# Audit God-Class & Coupling — Ball Jumper
#architecture #refactor #ball-jumper #plan

Tanggal: 2026-10-02
Sumber: MCP Unity live-read (83 scripts, ~70 custom) + second brain Skills/03-Game-Architecture
Konteks: Lanjutan dari [[Plan - PlayerController Refactor]] — facade PlayerController 608 → 153 baris DONE.

## Ringkasan
- Tidak ada lagi monster 500+ baris. Player refactor sukses.
- Sisa 5 file prioritas + 2 coupling halus.
- Ambang SOLID vault: <200-300 baris per class.

## P1 — Wajib
### 1. `Assets/Scripts/Enemy/AI/FlyingPatrolAI.cs` — 318 baris, GOD-CLASS
Bau: patrol + idle + FSM + fade/death + respawn + color-match + debug toggle + collider mgmt + raycast sensor numpuk 1 file.
Coupled ke: PlayerController, DebugEventManager, InputSystem, Rigidbody2D, SpriteRenderer, Collider2D[].
Duplikat: sistem baru `Enemy.cs` + `EnemyAIBrain.cs` + `GroundPatrolAI.cs` + `FlyingChaseAI.cs`. Enum `EnemyColor` dobel vs `PlatformColor`.
Refactor:
- Deprecate file ini, pindah ke `Enemy` abstract + `EnemyAIBrain` composition.
- Unifikasi warna ke 1 SSOT (hapus `EnemyColor`, pakai `PlatformColor`).
- Ref: [[Skills/03-Game-Architecture/SOLID Principles (Unity)]] SRP/OCP, [[Skills/03-Game-Architecture/Single Source of Truth (SSOT)]]

### 2. `Assets/Scripts/UI/UIManager.cs` — 247 baris
Bau: panels + HUD text + mute Animator/sprite fallback + wiring 2 canvas + click SFX + `Update()` polling height.
Coupled: `FindAnyObjectByType<CameraFollow, PlayerController, PlayerSfx>`, `GameObject.Find("OverlayCanvas/WorldspaceCanvas/DynamicCanvas")`, `PlayerPrefs MuteKey`, `GameManager.Instance.SubmitHeight` tiap frame.
Refactor (MVP):
- `MenuPresenter` (wiring tombol saja), `HudPresenter` (subscribe event, bukan Update), `MuteService` (SSOT bisu).
- HUD jangan polling — subscribe `CameraFollow.HeightChanged` / `GameManager.OnStateChanged`.
- AudioSystem satu-satunya yang baca `MuteKey`, UIManager cuma tulis + refresh visual.
- Ref: [[Skills/03-Game-Architecture/MVP Pattern (Unity UI)]], [[Skills/03-Game-Architecture/Observer Pattern Events]]

### 3. `Assets/Scripts/Enemy/Enemy.cs` — 225 baris (abstract base gemuk)
Bau: color/solid + tint/animator + stomp gate + death DOTween + subscribe `OnModeChanged` semua di base.
Refactor:
- Extract `EnemyDeathFx` (DOTween jump+fade), `EnemySolidVisual` (tint + `isSolid` param).
- Base tinggal `TryHit` + abstract `Tick`.
- Ref: [[Skills/03-Game-Architecture/SOLID Principles (Unity)]] SRP/ISP

### 4. `Assets/Scripts/Player/PlayerLifecycle.cs` — 217 baris
Bau: `HandleGameState` 4 branch + intro `DOTween.Sequence` + `Die/DieFrom` + `Camera.main.GetComponent<CameraFollow>().Shake()` + `FindAnyObjectByType<PlatformSpawner>` + sandbox flag.
Refactor:
- Intro pindah ke `PlayerIntroPlayer` sendiri.
- Death/shake via event `Died` → CameraFollow subscribe, hapus `Camera.main` lookup.
- Hapus `FindAnyObjectByType`, inject spawner via inspector/facade.
- Ref: [[Skills/03-Game-Architecture/Runtime State Separation]], [[Skills/03-Game-Architecture/Observer Pattern Events]]

### 5. `Assets/Scripts/Core/CameraFollow.cs` — 164 baris, 3 job
Bau: follow highestY + fall/gameover trigger (`SubmitHeight` + `UpdateState(GameOver)`) + shake + `PositionAnchor` world panel + `Find("WorldspaceCanvas")`.
Refactor:
- `FallDirector` emit `OnFallFinished`, GameManager yang transisi. CameraFollow murni follow + shake.
- Inject `gameOverAnchor` via inspector, hapus Find.
- Ref: [[Skills/03-Game-Architecture/Centralized State Manager (GameManager Singleton & Event)]]

## P2 — Coupling halus
### 6. `Assets/Scripts/Player/PlayerLandingResolver.cs` — Bind 8 param
`Bind(cfg, state, rb, dash, bounce, streak, mode, sfx)` kenal konkret semua → DIP violation.
Refactor: pegang `IBounceable/IDashable/IStreakTracker`, serialize `MonoBehaviour` + cast runtime (cara skill SOLID).

### 7. `Assets/Scripts/Enemy/AI/EnemyAIBrain.cs` + `FlyingChaseAI.cs`
`MonoBehaviour` enable/disable langsung (rapuh), `FindGameObjectWithTag("Player")` di Awake/Start.
Refactor: interface `IEnemyBehaviour { Enter/Exit }`, inject `playerTarget` dari `EnemySpawner`, bukan Find.

## Yang BAGUS — jangan disentuh logikanya
- `Assets/Scripts/Core/GameManager.cs` — 92 baris, SSOT + event bersih.
- `Assets/Scripts/Player/PlayerController.cs` — 153 baris facade, DONE dari 608.
- `Assets/Scripts/Spawning/SpawnSystem.cs` — 68 baris orchestrator bersih.
- `Assets/Scripts/Audio/AudioSystem.cs` — 61 baris, contoh decoupled terbaik (Event Channel + Pool).
- `Assets/Scripts/Platform/Platform.cs` — 95 baris abstract bersih.
- `Assets/Scripts/Spawning/PlatformSpawner.cs` (192) + `EnemySpawner.cs` (183) + `BoosterSpawner.cs` (136) — Strategy + Factory + Attacher udah bener. Next: ekstrak `Recycler` bareng biar margin recycle ga duplikat.

## Urutan gas
1. Hapus/deprecate `FlyingPatrolAI` (ROI gede, hapus duplikasi)
2. Tipisin `UIManager` (MVP split)
3. Pecah `Enemy.cs` + `PlayerLifecycle.cs` + `CameraFollow.cs`
4. Betulin `Bind` 8 param + `EnemyAIBrain` interface

## Verifikasi tiap langkah
- 0 compile error, playtest Main scene identik feel (spring 1.8, apex snap, wrap 9:16).
- Tambah varian musuh/UI baru = 0 edit file lama (OCP lolos).
- Tidak ada lagi `GameObject.Find` / `FindAnyObjectByType` di Update/path panas.
