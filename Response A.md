# Refactoring Architecture for PairJump Spawners

## Analysis of Current Problems

Looking at your scripts, I can identify several architectural issues:

1. **God Class (Spawner.cs)** — handles spawning, pooling, intro, color picking, variant picking, layout, boosters, and lifecycle all in one
2. **Tight Coupling** — Spawner directly knows about `BoosterSpawner`, `PlatformPool`, `PlatformIntro`, `PlatformVariantPicker`, `PlatformColorPicker`, `PlatformLayoutGenerator`
3. **Duplicated Pooling Logic** — `PlatformPool` and `EnemySpawner` both implement nearly identical pool patterns
4. **Mixed Responsibilities** — `EnemySpawner` does pooling + spawning + visibility + color logic
5. **Static/Instance Mix** — `ReflectX` is duplicated as static passthrough
6. **Magic Strings/Numbers** — `"Platform_{0}_{1:F0}"`, `0.45f`, etc.
7. **State Scattered** — `spawned`, `gameplayVisible`, `visible` tracked in multiple places
8. **Manual `ResolveRefs()` calls** everywhere instead of proper DI

---

## Proposed Architecture

I'll use a combination of patterns:

| Pattern | Purpose |
|---|---|
| **Generic Object Pool** | Unify `PlatformPool` + `EnemySpawner` pooling |
| **Strategy** | Layout, color pick, variant pick as interchangeable strategies |
| **Factory** | `IPlatformFactory` / `IEnemyFactory` for creation |
| **Mediator / Event Bus** | Decouple spawners from `GameManager` |
| **Facade** | `Spawner` becomes a thin coordinator |
| **Service Locator / DI Container** | Resolve dependencies cleanly |
| **State Machine** | Explicit lifecycle (Idle → Spawning → Playing → Cleanup) |
| **Observer** | Renderer visibility via events |

---

## 1. Generic Object Pool

```csharp
// Pooling/IGenericPool.cs
public interface IGenericPool<T> where T : Component
{
    T Get(GameObject prefab);
    void Release(T instance);
    void Prewarm(GameObject prefab, int count);
    void Clear();
    int TotalCreated { get; }
}

// Pooling/GenericComponentPool.cs
using System.Collections.Generic;
using UnityEngine;

public class GenericComponentPool<T> : IGenericPool<T> where T : Component
{
    private readonly Dictionary<GameObject, Queue<T>> _pools = new();
    private readonly Dictionary<T, GameObject> _sourcePrefab = new();
    private readonly int _maxPerPrefab;
    private readonly System.Func<GameObject, T> _factory;

    public int TotalCreated { get; private set; }

    public GenericComponentPool(int maxPerPrefab, System.Func<GameObject, T> factory)
    {
        _maxPerPrefab = maxPerPrefab;
        _factory = factory;
    }

    public void Prewarm(GameObject prefab, int count)
    {
        for (int i = 0; i < count; i++)
        {
            var instance = Create(prefab);
            instance.gameObject.SetActive(false);
            For(prefab).Enqueue(instance);
        }
    }

    public T Get(GameObject prefab)
    {
        var q = For(prefab);
        while (q.Count > 0)
        {
            var item = q.Dequeue();
            if (item != null) return item;
        }
        return Create(prefab);
    }

    public void Release(T instance)
    {
        if (instance == null) return;
        _sourcePrefab.TryGetValue(instance, out var key);
        instance.gameObject.SetActive(false);
        var q = For(key);
        if (q.Count < _maxPerPrefab) q.Enqueue(instance);
        else
        {
            _sourcePrefab.Remove(instance);
            Object.Destroy(instance.gameObject);
        }
    }

    public void Clear()
    {
        foreach (var kv in _pools)
            while (kv.Value.Count > 0)
            {
                var item = kv.Value.Dequeue();
                if (item != null) Object.Destroy(item.gameObject);
            }
        _sourcePrefab.Clear();
    }

    private Queue<T> For(GameObject prefab)
    {
        if (!_pools.TryGetValue(prefab, out var q) || q == null)
        {
            q = new Queue<T>();
            _pools[prefab] = q;
        }
        return q;
    }

    private T Create(GameObject prefab)
    {
        var instance = _factory(prefab);
        _sourcePrefab[instance] = prefab;
        TotalCreated++;
        return instance;
    }
}
```

**Benefit:** One pool implementation serves platforms, enemies, and any future pooled component.

---

## 2. Game State Mediator (decouple from GameManager)

```csharp
// Core/IGameStateService.cs
public interface IGameStateService
{
    GameState Current { get; }
    event System.Action<GameState> StateChanged;
}

// Core/GameStateService.cs
public class GameStateService : IGameStateService
{
    public GameState Current { get; private set; } = GameState.MainMenu;
    public event System.Action<GameState> StateChanged;

    public void Set(GameState state)
    {
        if (Current == state) return;
        Current = state;
        StateChanged?.Invoke(state);
    }
}
```

Register it once; spawners subscribe to the interface, not the singleton.

---

## 3. Strategy Interfaces (Layout, Color, Variant)

```csharp
// Spawning/Strategies/IPlatformLayoutStrategy.cs
public interface IPlatformLayoutStrategy
{
    float NextY { get; }
    Vector2 NextPosition(Camera cam, GameObject prefab);
    void Reset();
}

// Spawning/Strategies/IPlatformColorStrategy.cs
public interface IPlatformColorStrategy
{
    PlatformColor Pick(float y, out bool wasBailout);
    void Reset();
}

// Spawning/Strategies/IPlatformVariantStrategy.cs
public interface IPlatformVariantStrategy
{
    GameObject Pick(float y, bool wasBailout, GameObject basePrefab);
    void Reset();
}
```

Your existing `PlatformLayoutGenerator`, `PlatformColorPicker`, `PlatformVariantPicker` should **implement** these interfaces (they already have the right shape — just add `: IPlatformLayoutStrategy` etc.).

---

## 4. Platform Factory

```csharp
// Spawning/Factories/IPlatformFactory.cs
public interface IPlatformFactory
{
    Platform Create(GameObject prefab);
}

// Spawning/Factories/PlatformFactory.cs
using UnityEngine;

public class PlatformFactory : IPlatformFactory
{
    private readonly IGenericPool<Platform> _pool;

    public PlatformFactory(IGenericPool<Platform> pool) => _pool = pool;

    public Platform Create(GameObject prefab)
    {
        var platform = _pool.Get(prefab);
        if (platform == null && prefab == null)
        {
            Debug.LogError("[PlatformFactory] No prefab and pool empty.");
            return null;
        }
        return platform;
    }
}
```

For the **procedural fallback**, inject a separate `IProceduralPlatformBuilder` so the factory stays clean.

---

## 5. Spawn Context (value object — replaces long parameter lists)

```csharp
// Spawning/SpawnContext.cs
public readonly struct PlatformSpawnContext
{
    public readonly Vector2 Position;
    public readonly PlatformColor Color;
    public readonly bool WasBailout;
    public readonly PlayerMode PlayerMode;
    public readonly bool IsDashing;
    public readonly bool GameplayVisible;
    public readonly int SpawnId;

    public PlatformSpawnContext(Vector2 position, PlatformColor color, bool wasBailout,
        PlayerMode mode, bool isDashing, bool gameplayVisible, int spawnId)
    {
        Position = position;
        Color = color;
        WasBailout = wasBailout;
        PlayerMode = mode;
        IsDashing = isDashing;
        GameplayVisible = gameplayVisible;
        SpawnId = spawnId;
    }
}
```

This kills the 6-argument methods and the `cachedPlayerCtrl != null ? ... : ...` duplication.

---

## 6. Platform Spawner (single responsibility)

```csharp
// Spawning/PlatformSpawner.cs
using System.Collections.Generic;
using UnityEngine;

public class PlatformSpawner : MonoBehaviour
{
    [Header("Config")]
    [SerializeField] private PlatformSpawnerConfig _config;

    private IPlatformFactory _factory;
    private IPlatformLayoutStrategy _layout;
    private IPlatformColorStrategy _colorStrategy;
    private IPlatformVariantStrategy _variantStrategy;
    private IBoosterAttacher _booster;
    private IPlatformVisibility _visibility;
    private IGameStateService _gameState;
    private PlayerContext _playerContext;

    private readonly List<Platform> _live = new();
    private int _nextSpawnId = 1;
    private bool _hasSpawned;

    public IReadOnlyList<Platform> Live => _live;

    public void Construct(
        IPlatformFactory factory,
        IPlatformLayoutStrategy layout,
        IPlatformColorStrategy color,
        IPlatformVariantStrategy variant,
        IBoosterAttacher booster,
        IPlatformVisibility visibility,
        IGameStateService gameState,
        PlayerContext playerContext)
    {
        _factory = factory;
        _layout = layout;
        _colorStrategy = color;
        _variantStrategy = variant;
        _booster = booster;
        _visibility = visibility;
        _gameState = gameState;
        _playerContext = playerContext;

        _gameState.StateChanged += OnStateChanged;
    }

    private void OnDestroy() => _gameState.StateChanged -= OnStateChanged;

    private void OnStateChanged(GameState state)
    {
        switch (state)
        {
            case GameState.Playing:
                if (!_hasSpawned) SpawnInitial();
                _visibility.ShowAll(_live);
                break;
            case GameState.MainMenu:
                ClearAll();
                break;
        }
    }

    private void SpawnInitial()
    {
        _hasSpawned = true;
        _layout.Reset();
        _colorStrategy.Reset();
        _variantStrategy.Reset();

        SpawnAt(Vector2.zero, PlatformColor.Green, false);
        for (int i = 0; i < _config.InitialPlatforms; i++)
            SpawnNext();
    }

    private void Update()
    {
        if (!_hasSpawned || Camera.main == null) return;
        SpawnAheadOfCamera();
        RecycleBelowCamera();
    }

    private void SpawnAheadOfCamera()
    {
        var cam = Camera.main;
        float top = cam.transform.position.y + cam.orthographicSize;
        int guard = 0;
        while (_layout.NextY < top && guard++ < _config.MaxSpawnsPerFrame)
            SpawnNext();
    }

    private void RecycleBelowCamera()
    {
        var cam = Camera.main;
        float bottom = cam.transform.position.y - cam.orthographicSize - _config.RecycleMargin;
        for (int i = _live.Count - 1; i >= 0; i--)
        {
            var p = _live[i];
            if (p == null) { _live.RemoveAt(i); continue; }
            if (p.transform.position.y < bottom)
            {
                _live.RemoveAt(i);
                _factory.Release(p);
            }
        }
    }

    private void SpawnNext()
    {
        var cam = Camera.main;
        Vector2 pos = _layout.NextPosition(cam, _config.BasePrefab);
        SpawnAt(pos, _colorStrategy.Pick(pos.y, out bool bailout), bailout);
    }

    private void SpawnAt(Vector2 pos, PlatformColor color, bool wasBailout)
    {
        var prefab = _variantStrategy.Pick(pos.y, wasBailout, _config.BasePrefab);
        var platform = _factory.Create(prefab);
        if (platform == null) return;

        var ctx = new PlatformSpawnContext(
            pos, color, wasBailout,
            _playerContext.Mode, _playerContext.IsDashing,
            _visibility.GameplayVisible, _nextSpawnId++);

        ApplyContext(platform, ctx);
        _booster?.Attach(platform, ctx);
        _visibility.OnSpawned(platform, ctx.GameplayVisible);

        _live.Add(platform);
    }

    private void ApplyContext(Platform platform, in PlatformSpawnContext ctx)
    {
        platform.transform.position = new Vector3(ctx.Position.x, ctx.Position.y, 0f);
        platform.name = $"Platform_{ctx.Color}_{ctx.Position.y:F0}";
        platform.color = ctx.Color;
        platform.OnSpawned(ctx.SpawnId);

        if (!platform.gameObject.activeSelf)
            platform.gameObject.SetActive(true);

        if (platform.TryGetComponent<PlatformMover>(out var mover))
            mover.SetAnchor(platform.transform.position);

        platform.RefreshVisual(ctx.PlayerMode, ctx.IsDashing, true);
    }

    private void ClearAll()
    {
        foreach (var p in _live) if (p != null) _factory.Release(p);
        _live.Clear();
        _hasSpawned = false;
    }
}
```

---

## 7. Visibility Service (moves renderer toggling out)

```csharp
// Spawning/Visibility/IPlatformVisibility.cs
public interface IPlatformVisibility
{
    bool GameplayVisible { get; }
    void ShowAll(IReadOnlyList<Platform> live);
    void OnSpawned(Platform platform, bool gameplayVisible);
}

// Spawning/Visibility/PlatformVisibility.cs
using UnityEngine;

public class PlatformVisibility : IPlatformVisibility
{
    public bool GameplayVisible { get; private set; }

    public void ShowAll(IReadOnlyList<Platform> live)
    {
        GameplayVisible = true;
        foreach (var p in live)
            SetRenderers(p, true);
    }

    public void OnSpawned(Platform platform, bool gameplayVisible)
    {
        SetRenderers(platform, gameplayVisible);
    }

    private static void SetRenderers(Platform p, bool enabled)
    {
        if (p == null) return;
        foreach (var r in p.GetComponentsInChildren<Renderer>(true))
            r.enabled = enabled;
    }
}
```

---

## 8. Booster Attacher Interface

```csharp
// Spawning/Boosters/IBoosterAttacher.cs
public interface IBoosterAttacher
{
    void Attach(Platform platform, in PlatformSpawnContext ctx);
}

// BoosterSpawner implements it:
public class BoosterSpawner : MonoBehaviour, IBoosterAttacher
{
    public void Attach(Platform plat, in PlatformSpawnContext ctx)
    {
        AttachInternal(plat, ctx.Position.y, ctx.Color, ctx.PlayerMode, ctx.IsDashing, ctx.GameplayVisible);
    }
    // ... existing logic
}
```

---

## 9. Composition Root (wires everything once)

```csharp
// Bootstrap/SpawnerInstaller.cs
using UnityEngine;

public class SpawnerInstaller : MonoBehaviour
{
    [SerializeField] private SpawnerConfig _config;

    private void Awake()
    {
        var gameState = new GameStateService();
        // Publish via a simple locator (or use VContainer/Zenject)
        ServiceLocator.Register<IGameStateService>(gameState);
        // Bridge existing GameManager events → service
        GameManager.OnStateChanged += gameState.Set;

        var platformPool = new GenericComponentPool<Platform>(
            _config.MaxPoolSize, prefab => BuildPlatform(prefab));
        platformPool.Prewarm(_config.BasePrefab, _config.PrewarmCount);

        var factory = new PlatformFactory(platformPool);
        var visibility = new PlatformVisibility();
        var playerContext = new PlayerContext(_config.Player);

        var spawner = GetComponent<PlatformSpawner>();
        spawner.Construct(
            factory,
            _config.LayoutStrategy,
            _config.ColorStrategy,
            _config.VariantStrategy,
            GetComponent<BoosterSpawner>(),
            visibility,
            gameState,
            playerContext);
    }

    private static Platform BuildPlatform(GameObject prefab)
    {
        if (prefab != null)
        {
            var go = Instantiate(prefab);
            return go.GetComponent<Platform>() ?? go.AddComponent<Platform>();
        }
        return ProceduralPlatformBuilder.Build();
    }
}
```

---

## 10. Player Context (replaces cached `PlayerController`)

```csharp
// Player/PlayerContext.cs
using UnityEngine;

public class PlayerContext
{
    private readonly PlayerController _ctrl;
    public PlayerContext(Transform player)
    {
        if (player != null) _ctrl = player.GetComponent<PlayerController>();
    }
    public PlayerMode Mode => _ctrl != null ? _ctrl.CurrentMode : PlayerMode.Red;
    public bool IsDashing => _ctrl != null && _ctrl.IsDashing;
}
```

---

## 11. Enemy Spawner — split into Pool + Spawner + Visibility

`EnemySpawner` becomes:

```csharp
public class EnemySpawner : MonoBehaviour
{
    [SerializeField] private EnemySpawnerConfig _config;

    private IGenericPool<EnemyPatrol> _pool;
    private IEnemySpawnStrategy _spawnStrategy;
    private IGameStateService _gameState;
    private readonly List<EnemyPatrol> _live = new();

    // same shape as PlatformSpawner: subscribe, spawn ahead, recycle below
}
```

Pooling code disappears — it uses `GenericComponentPool<EnemyPatrol>`. The `TrySpawn` anti-streak logic becomes an `IEnemySpawnStrategy` (testable in isolation).

---

## 12. File/Folder Layout

```
Scripts/
├── Core/
│   ├── IGameStateService.cs
│   ├── GameStateService.cs
│   ├── ServiceLocator.cs
│   └── PlayerContext.cs
├── Pooling/
│   ├── IGenericPool.cs
│   └── GenericComponentPool.cs
├── Spawning/
│   ├── PlatformSpawner.cs
│   ├── PlatformSpawnerConfig.cs
│   ├── PlatformSpawnContext.cs
│   ├── Factories/
│   │   ├── IPlatformFactory.cs
│   │   ├── PlatformFactory.cs
│   │   └── ProceduralPlatformBuilder.cs
│   ├── Strategies/
│   │   ├── IPlatformLayoutStrategy.cs
│   │   ├── IPlatformColorStrategy.cs
│   │   ├── IPlatformVariantStrategy.cs
│   │   └── Implementations/...
│   ├── Visibility/
│   │   ├── IPlatformVisibility.cs
│   │   └── PlatformVisibility.cs
│   └── Boosters/
│       ├── IBoosterAttacher.cs
│       └── BoosterSpawner.cs
├── Enemies/
│   ├── EnemySpawner.cs
│   ├── EnemySpawnerConfig.cs
│   └── Strategies/...
└── Bootstrap/
    └── SpawnerInstaller.cs
```

---

## Summary of Improvements

| Before | After |
|---|---|
| `Spawner` ~230 lines, 9 responsibilities | `PlatformSpawner` ~120 lines, 1 responsibility |
| 2 duplicate pool implementations | 1 generic `GenericComponentPool<T>` |
| `ResolveRefs()` called ~15 times | Single `Construct()` at bootstrap |
| 6-param `SpawnAt` | `PlatformSpawnContext` struct |
| Tight coupling to `GameManager.OnStateChanged` | `IGameStateService` interface |
| Renderer toggle logic in 4 places | `IPlatformVisibility` |
| Hard to unit-test | Strategies & factories are pure, injectable |
| Adding new spawn type = edit Spawner | Add new `IStrategy` / register in installer |

### Migration Path (incremental)

1. **Extract `GenericComponentPool<T>`** — replace both pools, no behavior change.
2. **Add `IGameStateService`** — wrap `GameManager` events.
3. **Make existing pickers implement the strategy interfaces** (they already match).
4. **Introduce `PlatformSpawnContext`** — replace long arg lists.
5. **Extract `PlatformVisibility` and `PlayerContext`**.
6. **Rename `Spawner` → `PlatformSpawner`**, strip pooling/visibility.
7. **Add `SpawnerInstaller`** as the composition root.
8. **Repeat for `EnemySpawner`**.

Each step ships independently and is fully backwards compatible with your scene until step 7.