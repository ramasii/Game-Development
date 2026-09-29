Here is a modular, clean, and scalable refactored architecture for your spawning and pooling systems.

---

### Key Architectural Improvements

1. **Generic Object Pool Pattern**: Replaced duplicated pooling structures in `PlatformPool` and `EnemySpawner` with a single, type-safe `GenericObjectPool<T>` wrapper.


2. **Factory Pattern**: Separated entity creation logic (whitebox fallback sprites, booster instantiation, and enemy spawning) from lifecycle management components.


3. **Single Responsibility Principle (SRP)**:
* **`BoosterSpawner`**: Split into rolling evaluation, visual tinting, and positioning strategy instead of handling all three in a single method.


* **`Spawner` & `EnemySpawner**`: Stripped out manual queue management; components now strictly handle layout/positioning calculations and camera triggers.




4. **Strategy & Query Abstractions**: Encapsulated camera bound calculations and off-screen despawn logic into reusable helper methods to remove copy-pasted camera Y-checks.



---

### 1. Reusable Generic Object Pool (`GenericObjectPool.cs`)

A standardized, type-safe object pool that handles prewarming, capacity limits, and object destruction without duplicating queue logic.

```csharp
using System;
using System.Collections.Generic;
using UnityEngine;

public class GenericObjectPool<T> where T : Component
{
    private readonly Func<T> _createFunc;
    private readonly Action<T> _onGet;
    private readonly Action<T> _onRelease;
    private readonly Queue<T> _pool = new Queue<T>();
    private readonly int _maxSize;

    public int TotalCreated { get; private set; }
    public int CountInactive => _pool.Count;

    public GenericObjectPool(Func<T> createFunc, Action<T> onGet = null, Action<T> onRelease = null, int maxSize = 40)
    {
        _createFunc = createFunc ?? throw new ArgumentNullException(nameof(createFunc));
        _onGet = onGet;
        _onRelease = onRelease;
        _maxSize = maxSize;
    }

    public void Prewarm(int count)
    {
        for (int i = 0; i < count; i++)
        {
            T item = CreateNew();
            item.gameObject.SetActive(false);
            _pool.Enqueue(item);
        }
    }

    public T Get()
    {
        T item = _pool.Count > 0 ? _pool.Dequeue() : CreateNew();
        if (item == null) item = CreateNew();
        
        item.gameObject.SetActive(true);
        _onGet?.Invoke(item);
        return item;
    }

    public void Release(T item)
    {
        if (item == null) return;

        item.gameObject.SetActive(false);
        _onRelease?.Invoke(item);

        if (_pool.Count < _maxSize)
        {
            _pool.Enqueue(item);
        }
        else
        {
            UnityEngine.Object.Destroy(item.gameObject);
        }
    }

    public void Clear()
    {
        while (_pool.Count > 0)
        {
            T item = _pool.Dequeue();
            if (item != null) UnityEngine.Object.Destroy(item.gameObject);
        }
    }

    private T CreateNew()
    {
        TotalCreated++;
        return _createFunc();
    }
}

```

---

### 2. Refactored `PlatformPool.cs`

Uses key-based generic pools to eliminate GC allocations and procedural fallback sprite creation duplication.

```csharp
using System.Collections.Generic;
using UnityEngine;

public class PlatformPool : MonoBehaviour
{
    [Header("Pooling Configuration")]
    [Tooltip("Objek nonaktif disiapkan saat Start.")]
    public int prewarmPool = 15;
    [Tooltip("Batas antrian pool per prefab.")]
    public int maxPoolSize = 40;

    private readonly Dictionary<GameObject, GenericObjectPool<Platform>> _pools = new Dictionary<GameObject, GenericObjectPool<Platform>>();
    private readonly Dictionary<Platform, GameObject> _sourceMap = new Dictionary<Platform, GameObject>();
    
    private static Sprite _squareCache;
    private bool _warnedNoScript;

    public int TotalCreated
    {
        get
        {
            int total = 0;
            foreach (var pool in _pools.Values) total += pool.TotalCreated;
            return total;
        }
    }

    public void Prewarm(GameObject platformPrefab)
    {
        GetPool(platformPrefab).Prewarm(prewarmPool);
    }

    public Platform Get(GameObject prefab)
    {
        return GetPool(prefab).Get();
    }

    public void Release(Platform platform)
    {
        if (platform == null) return;

        if (_sourceMap.TryGetValue(platform, out GameObject prefabKey) && prefabKey != null)
        {
            GetPool(prefabKey).Release(platform);
        }
        else
        {
            Destroy(platform.gameObject);
        }
    }

    public void ClearPools()
    {
        foreach (var pool in _pools.Values) pool.Clear();
        _pools.Clear();
        _sourceMap.Clear();
    }

    private GenericObjectPool<Platform> GetPool(GameObject prefab)
    {
        GameObject key = prefab != null ? prefab : transform.gameObject;
        if (!_pools.TryGetValue(key, out var pool))
        {
            pool = new GenericObjectPool<Platform>(
                createFunc: () => CreatePlatformInstance(prefab),
                onGet: p => p.PrepareForSpawn(),
                maxSize: maxPoolSize
            );
            _pools[key] = pool;
        }
        return pool;
    }

    private Platform CreatePlatformInstance(GameObject prefab)
    {
        GameObject go;
        Platform plat;

        if (prefab != null)
        {
            go = Instantiate(prefab, Vector3.zero, Quaternion.identity);
            plat = go.GetComponent<Platform>();
            if (plat == null)
            {
                if (!_warnedNoScript)
                {
                    _warnedNoScript = true;
                    Debug.LogWarning("[PlatformPool] Prefab missing Platform script — adding automatically.");
                }
                plat = go.AddComponent<Platform>();
            }
        }
        else
        {
            go = new GameObject("Platform_Pooled_Procedural");
            var sr = go.AddComponent<SpriteRenderer>();
            sr.sprite = GetOrCreateSquareSprite();
            go.transform.localScale = new Vector3(2.2f, 0.4f, 1f);
            
            var col = go.AddComponent<BoxCollider2D>();
            col.isTrigger = true;
            plat = go.AddComponent<Platform>();
        }

        _sourceMap[plat] = prefab;
        return plat;
    }

    private static Sprite GetOrCreateSquareSprite()
    {
        if (_squareCache != null) return _squareCache;
        var tex = new Texture2D(4, 4);
        for (int x = 0; x < 4; x++)
            for (int y = 0; y < 4; y++)
                tex.SetPixel(x, y, Color.white);
        tex.Apply();
        _squareCache = Sprite.Create(tex, new Rect(0, 0, 4, 4), new Vector2(0.5f, 0.5f), 4f);
        _squareCache.name = "PairJumpSquare";
        return _squareCache;
    }
}

```

---

### 3. Refactored `BoosterSpawner.cs`

De-coupled probability evaluation, procedural creation, positioning, and color tinting into discrete methods.

```csharp
using System;
using System.Collections.Generic;
using UnityEngine;

public class BoosterSpawner : MonoBehaviour
{
    [Serializable]
    public struct BoosterEntry
    {
        public string label;
        public GameObject prefab;
        public float minY;
        [Range(0f, 1f)] public float chance;
        [Range(0.5f, 5f)] public float multiplier;
    }

    [Header("Booster Configurations")]
    public bool enableBooster = true;
    public List<BoosterEntry> entries = new List<BoosterEntry>();
    public bool boosterOnGreen = true;
    public bool boosterOnRed = true;
    public bool boosterOnBlue = true;
    public Vector2 boosterSpawnOffset = new Vector2(0f, 0.1f);
    public bool changeBoosterColor = false;

    private void OnValidate()
    {
        if (entries == null) return;
        for (int i = 0; i < entries.Count; i++)
        {
            var e = entries[i];
            e.minY = Mathf.Max(0f, e.minY);
            e.chance = Mathf.Clamp01(e.chance);
            e.multiplier = Mathf.Clamp(e.multiplier, 0.5f, 5f);
            entries[i] = e;
        }
    }

    public void Attach(Platform plat, float y, PlatformColor color, PlayerMode mode, bool isDashing, bool gameplayVisible)
    {
        if (plat == null) return;

        if (plat.GetComponent<PlatformCrumbler>() != null || !Roll(y, color, out BoosterEntry won))
        {
            HideBooster(plat);
            return;
        }

        if (plat.booster == null)
            plat.booster = plat.GetComponentInChildren<Booster>(true);

        Booster b = plat.booster;

        if (b != null && !MatchesEntry(b, won))
        {
            Destroy(b.gameObject);
            plat.booster = null;
            b = null;
        }

        if (b == null)
            b = CreateBoosterObject(plat, won);

        if (b == null) return;

        b.gameObject.SetActive(true);
        PositionBooster(plat, b);
        
        b.Setup(plat, won.multiplier);
        plat.booster = b;
        b.RefreshVisibility(mode, isDashing);
        
        ApplyTint(plat, b, color);
        SetBoosterVisibility(b, gameplayVisible);
    }

    private bool Roll(float y, PlatformColor color, out BoosterEntry won)
    {
        won = default;
        if (!enableBooster || entries == null) return false;

        bool isColorAllowed = (color == PlatformColor.Green && boosterOnGreen) ||
                             (color == PlatformColor.Red && boosterOnRed) ||
                             (color == PlatformColor.Blue && boosterOnBlue);
        if (!isColorAllowed) return false;

        foreach (var entry in entries)
        {
            if (entry.prefab == null || y < entry.minY || entry.chance <= 0f) continue;
            if (UnityEngine.Random.value < entry.chance)
            {
                won = entry;
                return true;
            }
        }
        return false;
    }

    private void PositionBooster(Platform plat, Booster b)
    {
        float topOffset = 0.45f;
        var pcol = plat.GetComponent<BoxCollider2D>();
        if (pcol != null)
            topOffset = pcol.size.y * 0.5f * Mathf.Abs(plat.transform.localScale.y);

        topOffset += boosterSpawnOffset.y;
        
        b.transform.SetParent(plat.transform, false);
        b.transform.localPosition = new Vector3(boosterSpawnOffset.x, topOffset, 0f);
        b.transform.localRotation = Quaternion.identity;
        b.transform.localScale = Vector3.one;
    }

    private void HideBooster(Platform plat)
    {
        if (plat.booster == null)
            plat.booster = plat.GetComponentInChildren<Booster>(true);

        if (plat.booster != null)
        {
            plat.booster.PrepareForSpawn();
            ResetTint(plat.booster);
            plat.booster.gameObject.SetActive(false);
        }
    }

    private bool MatchesEntry(Booster b, BoosterEntry entry)
    {
        if (b == null || entry.prefab == null) return false;
        var want = entry.prefab.GetComponent<Booster>();
        return want != null && b.GetType() == want.GetType();
    }

    private void ApplyTint(Platform plat, Booster b, PlatformColor color)
    {
        if (b == null || b.Visual == null) return;
        if (!changeBoosterColor) return;

        Color tint = Color.white;
        if (plat != null && plat.visualRenderer != null)
        {
            tint = plat.visualRenderer.color;
            tint.a = 1f;
        }
        else
        {
            tint = color switch
            {
                PlatformColor.Red => new Color(1f, 0.42f, 0.42f),
                PlatformColor.Blue => new Color(0.30f, 0.59f, 1f),
                _ => new Color(0.48f, 0.96f, 0.61f)
            };
        }
        b.Visual.color = tint;
    }

    private void ResetTint(Booster b)
    {
        if (b?.Visual != null) b.Visual.color = Color.white;
    }

    private void SetBoosterVisibility(Booster b, bool visible)
    {
        if (visible) return;
        foreach (var r in b.GetComponentsInChildren<Renderer>(true))
            r.enabled = false;
    }

    private Booster CreateBoosterObject(Platform parent, BoosterEntry entry)
    {
        GameObject go;
        if (entry.prefab != null)
        {
            go = Instantiate(entry.prefab, parent.transform, false);
            return go.GetComponent<Booster>() ?? go.AddComponent<SpringBooster>();
        }

        go = new GameObject("SpringBooster_Whitebox");
        go.transform.SetParent(parent.transform, false);
        
        var col = go.AddComponent<BoxCollider2D>();
        col.isTrigger = true;
        col.size = new Vector2(0.7f, 0.4f);

        var sr = go.AddComponent<SpriteRenderer>();
        sr.sortingOrder = 5;

        return go.AddComponent<SpringBooster>();
    }
}

```

---

### 4. Refactored `PlatformIntro.cs`

Cleaned up sequence targeting and DOTween animations with defensive null checks.

```csharp
using System.Collections.Generic;
using UnityEngine;
using DG.Tweening;

public class PlatformIntro : MonoBehaviour
{
    [Header("Intro Settings")]
    public float introStepDelay = 0.06f;
    public float introPopDuration = 0.35f;
    public float ballExtraDelay = 0.15f;

    private readonly Dictionary<Platform, Vector3> _introTargets = new Dictionary<Platform, Vector3>();

    public float GetBallDelay(int liveCount) => liveCount * introStepDelay + ballExtraDelay;

    public void PlayIntro(List<Platform> live)
    {
        KillIntro(false, live);
        _introTargets.Clear();

        var sorted = new List<Platform>(live);
        sorted.Sort((a, b) =>
        {
            if (a == null || b == null) return 0;
            return a.transform.position.y.CompareTo(b.transform.position.y);
        });

        for (int i = 0; i < sorted.Count; i++)
        {
            var p = sorted[i];
            if (p == null) continue;

            Vector3 target = p.transform.localScale == Vector3.zero ? Vector3.one : p.transform.localScale;
            _introTargets[p] = target;

            SetPlatformRenderersEnabled(p, true);
            p.transform.localScale = Vector3.zero;
            p.transform.DOScale(target, introPopDuration)
                .SetDelay(i * introStepDelay)
                .SetEase(Ease.OutBack)
                .SetLink(p.gameObject);
        }
    }

    public void KillIntro(bool snapToFinal, List<Platform> live)
    {
        if (live == null) return;

        foreach (var p in live)
        {
            if (p == null) continue;
            p.transform.DOKill();

            if (snapToFinal)
            {
                if (!_introTargets.TryGetValue(p, out Vector3 target) || target == Vector3.zero)
                    target = Vector3.one;

                p.transform.localScale = target;
            }
        }
        if (snapToFinal) _introTargets.Clear();
    }

    public void SetVisible(List<Platform> live, bool visible)
    {
        if (live == null) return;
        foreach (var p in live)
        {
            if (p != null) SetPlatformRenderersEnabled(p, visible);
        }
    }

    public void Reset() => _introTargets.Clear();

    private void SetPlatformRenderersEnabled(Platform p, bool enabled)
    {
        foreach (var r in p.GetComponentsInChildren<Renderer>(true))
            r.enabled = enabled;
    }
}

```

---

### 5. Refactored `Spawner.cs`

Encapsulates layout, pool delegation, and state management.

```csharp
using System.Collections.Generic;
using UnityEngine;

public class Spawner : MonoBehaviour
{
    public Transform player;

    [Header("Art Prefabs")]
    public GameObject platformPrefab;
    public int initialPlatforms = 12;

    [Header("Generators & Pickers")]
    public PlatformLayoutGenerator layout = new PlatformLayoutGenerator();
    public PlatformColorPicker colorPicker = new PlatformColorPicker();
    public PlatformVariantPicker variantPicker = new PlatformVariantPicker();
    public bool logVariantSpawns = true;

    [Header("Component References")]
    public BoosterSpawner boosterRef;
    public PlatformPool poolRef;
    public PlatformIntro introRef;

    private Camera _cam;
    private PlayerController _cachedPlayerCtrl;
    private BoosterSpawner _booster;
    private PlatformPool _pool;
    private PlatformIntro _intro;

    private readonly List<Platform> _livePlatforms = new List<Platform>();
    private int _nextSpawnId = 1;
    private bool _spawned;
    private bool _gameplayVisible;

    public int TotalCreated => _pool != null ? _pool.TotalCreated : 0;
    public int TotalSpawned { get; private set; }

    private void OnEnable() => GameManager.OnStateChanged += HandleGameState;
    private void OnDisable() => GameManager.OnStateChanged -= HandleGameState;

    private void Start()
    {
        _cam = Camera.main;
        ResolveReferences();

        if (player != null)
            _cachedPlayerCtrl = player.GetComponent<PlayerController>();

        if (platformPrefab == null)
            Debug.LogWarning("[Spawner] platformPrefab is unassigned. Using fallback sprite.");
    }

    private void Update()
    {
        if (_cam == null || player == null) return;

        if (!_spawned)
        {
            if (GameManager.Instance != null && GameManager.Instance.CurrentState == GameState.Playing)
            {
                EnsureSpawn();
                _gameplayVisible = true;
                _intro?.PlayIntro(_livePlatforms);
            }
            else return;
        }

        float topBoundary = _cam.transform.position.y + _cam.orthographicSize;
        int maxLoopGuard = 0;
        while (layout.NextY < topBoundary && maxLoopGuard++ < 10)
        {
            SpawnNext();
        }

        RecycleOffscreenPlatforms();
    }

    private void HandleGameState(GameState st)
    {
        ResolveReferences();
        switch (st)
        {
            case GameState.Playing:
                if (!_spawned) EnsureSpawn();
                if (!_gameplayVisible)
                {
                    _gameplayVisible = true;
                    _intro?.PlayIntro(_livePlatforms);
                }
                else if (_intro != null)
                {
                    _intro.KillIntro(true, _livePlatforms);
                    _intro.SetVisible(_livePlatforms, true);
                }
                break;

            case GameState.MainMenu:
                ClearAll();
                break;

            default:
                _intro?.KillIntro(true, _livePlatforms);
                break;
        }
    }

    private void EnsureSpawn()
    {
        if (_spawned) return;
        _spawned = true;

        ResolveReferences();
        if (_pool != null) _pool.Prewarm(platformPrefab);

        ResetRunState();
        variantPicker.normalPrefab = platformPrefab;

        SpawnAt(0f, 0f, PlatformColor.Green, false);
        for (int i = 0; i < initialPlatforms; i++) SpawnNext();
    }

    private void SpawnNext()
    {
        ResolveReferences();
        Vector2 pos = layout.NextPosition(_cam, platformPrefab);
        PlatformColor color = colorPicker.Pick(pos.y, out bool wasBailout);
        SpawnAt(pos.x, pos.y, color, wasBailout);
    }

    private void SpawnAt(float x, float y, PlatformColor color, bool wasBailout)
    {
        ResolveReferences();
        GameObject wantPrefab = variantPicker.Pick(y, wasBailout);
        
        if (logVariantSpawns && wantPrefab != null && wantPrefab != platformPrefab)
            Debug.Log($"[Spawner] Spawning variant {variantPicker.LabelOf(wantPrefab)} at ({x:F1}, {y:F1})");

        Platform plat = _pool != null ? _pool.Get(wantPrefab) : null;
        if (plat == null)
        {
            Debug.LogError("[Spawner] PlatformPool missing on Spawner!");
            return;
        }

        GameObject go = plat.gameObject;
        go.transform.position = new Vector3(x, y, 0f);
        go.name = $"Platform_{color}_{y:F0}";

        plat.color = color;
        plat.OnSpawned(_nextSpawnId++);

        var mover = plat.GetComponent<PlatformMover>();
        mover?.SetAnchor(go.transform.position);

        PlayerMode mode = _cachedPlayerCtrl != null ? _cachedPlayerCtrl.CurrentMode : PlayerMode.Red;
        bool isDashing = _cachedPlayerCtrl != null && _cachedPlayerCtrl.IsDashing;

        plat.RefreshVisual(mode, isDashing, true);
        _booster?.Attach(plat, y, color, mode, isDashing, _gameplayVisible);

        if (!_gameplayVisible)
            SetRenderersEnabled(go, false);

        _livePlatforms.Add(plat);
        TotalSpawned++;
    }

    private void RecycleOffscreenPlatforms()
    {
        float bottomBoundary = _cam.transform.position.y - _cam.orthographicSize - 6f;
        for (int i = _livePlatforms.Count - 1; i >= 0; i--)
        {
            Platform plat = _livePlatforms[i];
            if (plat == null)
            {
                _livePlatforms.RemoveAt(i);
                continue;
            }

            if (plat.transform.position.y < bottomBoundary)
            {
                _livePlatforms.RemoveAt(i);
                ReleasePlatformInternal(plat);
            }
        }
    }

    public void ReleasePlatform(Platform p)
    {
        if (p == null) return;
        _livePlatforms.Remove(p);
        ReleasePlatformInternal(p);
    }

    private void ReleasePlatformInternal(Platform p)
    {
        ResolveReferences();
        if (_pool != null) _pool.Release(p);
        else Destroy(p.gameObject);
    }

    private void ClearAll()
    {
        ResolveReferences();
        _intro?.KillIntro(false, _livePlatforms);

        foreach (var p in _livePlatforms)
            if (p != null) Destroy(p.gameObject);

        _livePlatforms.Clear();
        _pool?.ClearPools();
        _intro?.Reset();

        _spawned = false;
        _gameplayVisible = false;
        ResetRunState();
    }

    public float GetBallIntroDelay() => _intro != null ? _intro.GetBallDelay(_livePlatforms.Count) : 0f;

    public void RefreshAllPlatformVisuals(PlayerMode mode, bool isDashing)
    {
        for (int i = _livePlatforms.Count - 1; i >= 0; i--)
        {
            _livePlatforms[i]?.RefreshVisual(mode, isDashing);
        }
    }

    private void ResolveReferences()
    {
        if (_intro == null) _intro = introRef != null ? introRef : GetComponent<PlatformIntro>();
        if (_booster == null) _booster = boosterRef != null ? boosterRef : GetComponent<BoosterSpawner>();
        if (_pool == null) _pool = poolRef != null ? poolRef : GetComponent<PlatformPool>();

        layout ??= new PlatformLayoutGenerator();
        colorPicker ??= new PlatformColorPicker();
        variantPicker ??= new PlatformVariantPicker();
        if (variantPicker.normalPrefab == null) variantPicker.normalPrefab = platformPrefab;
    }

    private void ResetRunState()
    {
        layout?.Reset();
        colorPicker?.Reset();
        variantPicker?.Reset();
        _nextSpawnId = 1;
    }

    private void SetRenderersEnabled(GameObject go, bool enabled)
    {
        foreach (var r in go.GetComponentsInChildren<Renderer>(true))
            r.enabled = enabled;
    }

    private void OnValidate()
    {
        layout?.Validate();
        colorPicker?.Validate();
        variantPicker?.Validate();
    }
}

```

---

### 6. Refactored `EnemySpawner.cs`

Refactored to utilize `GenericObjectPool<EnemyPatrol>` instead of custom dictionary pools.

```csharp
using System.Collections.Generic;
using UnityEngine;

public class EnemySpawner : MonoBehaviour
{
    public enum SpawnColor { Random, Red, Blue }

    [System.Serializable]
    public struct EnemyEntry
    {
        public string label;
        public GameObject prefab;
        public float minY;
        [Range(0f, 1f)] public float chance;
        public SpawnColor spawnColor;
    }

    [Header("Enemy Entries")]
    public List<EnemyEntry> entries = new List<EnemyEntry>();

    [Header("Spawn Heights")]
    public float startY = 180f;
    public float stepY = 8f;
    public float spawnAhead = 2f;
    public float recycleMargin = 6f;
    public float xMargin = 0.5f;

    [Header("Pooling Limits")]
    public int prewarmPerEntry = 2;
    public int maxPoolSize = 20;
    public int maxAlive = 6;

    private Camera _cam;
    private float _nextY;
    private int _lastColorIndex = -1;
    private bool _visible;
    private bool _spawned;

    private readonly List<EnemyPatrol> _liveEnemies = new List<EnemyPatrol>();
    private readonly Dictionary<GameObject, GenericObjectPool<EnemyPatrol>> _pools = new Dictionary<GameObject, GenericObjectPool<EnemyPatrol>>();
    private readonly Dictionary<EnemyPatrol, GameObject> _sourceMap = new Dictionary<EnemyPatrol, GameObject>();

    private void OnEnable() => GameManager.OnStateChanged += HandleState;
    private void OnDisable() => GameManager.OnStateChanged -= HandleState;

    private void Start() => _cam = Camera.main;

    private void Update()
    {
        if (_cam == null) return;

        if (!_spawned)
        {
            if (GameManager.Instance != null && GameManager.Instance.CurrentState == GameState.Playing)
                EnsureSpawn();
            else return;
        }

        float top = _cam.transform.position.y + _cam.orthographicSize;
        while (_nextY < top + spawnAhead)
        {
            TrySpawn(_nextY);
            _nextY += stepY;
        }

        RecycleOffscreenEnemies();
    }

    private void HandleState(GameState st)
    {
        if (st == GameState.Playing)
        {
            if (!_spawned) EnsureSpawn();
            _visible = true;
        }
        else if (st == GameState.MainMenu)
        {
            ClearAll();
        }
    }

    private void EnsureSpawn()
    {
        if (_spawned) return;
        _spawned = true;
        _nextY = startY;

        foreach (var e in entries)
        {
            if (e.prefab == null) continue;
            GetPool(e.prefab).Prewarm(prewarmPerEntry);
        }
    }

    private void TrySpawn(float y)
    {
        if (_liveEnemies.Count >= maxAlive || entries == null) return;

        foreach (var e in entries)
        {
            if (e.prefab == null || y < e.minY || e.chance <= 0f) continue;
            if (Random.value >= e.chance) continue;

            int colorIndex = e.spawnColor switch
            {
                SpawnColor.Red => 0,
                SpawnColor.Blue => 1,
                _ => Random.value < 0.5f ? 0 : 1
            };

            if (colorIndex == _lastColorIndex) return;

            _lastColorIndex = colorIndex;
            SpawnAt(e.prefab, y, colorIndex == 0 ? PlatformColor.Red : PlatformColor.Blue);
            return;
        }
    }

    private void SpawnAt(GameObject prefab, float y, PlatformColor color)
    {
        float bound = GetBound();
        var proto = prefab.GetComponent<EnemyPatrol>();

        float offMin = proto != null ? Mathf.Min(proto.offsetA.x, proto.offsetB.x) : 0f;
        float offMax = proto != null ? Mathf.Max(proto.offsetA.x, proto.offsetB.x) : 0f;

        float lo = -bound - offMin;
        float hi = bound - offMax;
        float x = (lo > hi) ? 0f : Random.Range(lo, hi);

        EnemyPatrol enemy = GetPool(prefab).Get();
        enemy.transform.position = new Vector3(x, y, 0f);

        enemy.SetColor(color);
        enemy.SetAnchor(enemy.transform.position);

        if (!_visible)
        {
            foreach (var r in enemy.GetComponentsInChildren<Renderer>(true))
                r.enabled = false;
        }

        _liveEnemies.Add(enemy);
    }

    private void RecycleOffscreenEnemies()
    {
        float bottom = _cam.transform.position.y - _cam.orthographicSize - recycleMargin;
        for (int i = _liveEnemies.Count - 1; i >= 0; i--)
        {
            var e = _liveEnemies[i];
            if (e == null)
            {
                _liveEnemies.RemoveAt(i);
                continue;
            }

            if (e.transform.position.y < bottom)
            {
                _liveEnemies.RemoveAt(i);
                ReleaseEnemy(e);
            }
        }
    }

    private GenericObjectPool<EnemyPatrol> GetPool(GameObject prefab)
    {
        if (!_pools.TryGetValue(prefab, out var pool))
        {
            pool = new GenericObjectPool<EnemyPatrol>(
                createFunc: () => FactoryCreateEnemy(prefab),
                onGet: e => { },
                onRelease: e => { },
                maxSize: maxPoolSize
            );
            _pools[prefab] = pool;
        }
        return pool;
    }

    private EnemyPatrol FactoryCreateEnemy(GameObject prefab)
    {
        var go = Instantiate(prefab, new Vector3(0f, -1000f, 0f), Quaternion.identity);
        var p = go.GetComponent<EnemyPatrol>() ?? go.AddComponent<EnemyPatrol>();
        p.SetAnchor(go.transform.position);
        _sourceMap[p] = prefab;
        return p;
    }

    private void ReleaseEnemy(EnemyPatrol enemy)
    {
        if (enemy == null) return;
        if (_sourceMap.TryGetValue(enemy, out GameObject prefabKey) && prefabKey != null)
        {
            GetPool(prefabKey).Release(enemy);
        }
        else
        {
            Destroy(enemy.gameObject);
        }
    }

    private void ClearAll()
    {
        foreach (var e in _liveEnemies)
            if (e != null) Destroy(e.gameObject);

        _liveEnemies.Clear();

        foreach (var pool in _pools.Values)
            pool.Clear();

        _pools.Clear();
        _sourceMap.Clear();

        _spawned = false;
        _visible = false;
        _lastColorIndex = -1;
    }

    private float GetBound()
    {
        float halfW = 4f;
        if (_cam != null)
        {
            float w = _cam.orthographicSize * _cam.aspect;
            float design = _cam.orthographicSize * (9f / 16f);
            halfW = Mathf.Min(w, design);
        }
        return Mathf.Max(0.5f, halfW - xMargin);
    }
}

```