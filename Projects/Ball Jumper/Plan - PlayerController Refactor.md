# 🔧 Plan PlayerController Refactor (Ball Jumper)

#plan #architecture #unity #ball-jumper

## 🎯 Tujuan

Pecah `PlayerController.cs` (608 baris, `Assets/Scripts/Player/PlayerController.cs`) jadi modul SRP <160 baris/file dengan facade tipis — tambah varian tanpa edit file lama (OCP).
Tanpa ubah feel: spring 1.8, apex snap, wrap 9:16, `rb.position` teleport (bukan `MovePosition`/`transform`).

Referensi: [[Skills/03-Game-Architecture/SOLID Principles (Unity)]], [[Skills/03-Game-Architecture/Strategy Pattern (Unity Ability)]], [[Skills/03-Game-Architecture/State Pattern (Unity FSM)]], [[Skills/03-Game-Architecture/Layered Architecture Stabilization]], [[Skills/03-Game-Architecture/Runtime State Separation]], [[Skills/03-Game-Architecture/Single Source of Truth (SSOT)]], [[Skills/03-Game-Architecture/Observer Pattern Events]], [[Skills/03-Game-Architecture/Flyweight Pattern (Unity Shared Data)]].
Lanjutan dari: [[Projects/Ball Jumper/Plan - Multi Booster|Plan - Multi Booster]] (gate di player, efek via `Booster.ApplyEffect`). Konteks engine: [[Projects/Ball Jumper/TDD - Ball Jumper|TDD - Ball Jumper]].

## 🗺️ Peta Coupling Sekarang

- `PlayerController`: input queue (`pendingDragPx` + `HandleDrag`) + `FixedUpdate` move + `Wrap` + `jumpVelocity = hang*g/2` + dash (`HandleDashRequest`, coyote 0.05s, `dashTimeout` 3s) + `TryLand` + `TryBoost` + mode/streak (`CurrentMode`, `Streak`, `lastPlatformId/ref`) + lifecycle (`HandleGameState`, `IsPlaying`, `StartBallIntro/KillBallIntro` DOTween, `Die/DieFrom`, `ForceBounce/ForceToggleMode`) + `RefreshAllPlatforms` via `FindAnyObjectByType` + `SetGameplayVisible` + sandbox/debug.
- Bau SRP: 1 class >300 baris (target skill SOLID: <200-300). Nama API dipakai luar: `Platform` → `TryLand`, `BoosterSpawner`/`Booster` → `TryBoost/SnapToSurface/LaunchUp`, `GameManager` → `HandleGameState/Die`, juice (`PlayerSquashStretch`, `PlayerSplashBurst`, `PlayerSfx`) → `OnModeChanged/OnDashChanged/OnStreakChanged/OnLanded/OnBoosted`.
- Yang sudah bagus (jangan disentuh logikanya): `PlayerSfx`, `PlayerSquashStretch`, `PlayerSplashBurst`, `Booster : MonoBehaviour` abstract + `ApplyEffect`.

## 🗂️ Struktur Baru

| File | Tanggung jawab (1 alasan berubah) | Estimasi |
|---|---|---|
| `PlayerController.cs` (ubah jadi facade) | `RequireComponent`, wiring `Awake`, expose API lama + event tetap. Nol logika, hanya delegasi. | 110-140 |
| `PlayerConfig.cs` (SO, baru) | SSOT + Flyweight tuning: `normalGravity, dashGravityMult, hangTime, moveSensitivity, dashCooldown, dashSlamVelocity, dashTimeout, landDebounce, landTol, landSnapEps, knockbackX/Y, ballPopDuration`. | 60-80 |
| `PlayerRuntimeState.cs` (baru) | `Runtime State Separation`: Persistent (mode/streak), Runtime (`canDash, isDead, introActive, savedVel, lastPlatform`), Temporary (`pendingDragPx, prevBottomY`). Temporary dilarang tulis Persistent langsung. | 50-70 |
| `PlayerLocomotion.cs` (baru, Core) | `HandleDrag` queue + `FixedUpdate` px→world + `Wrap` 9:16 via `rb.position`. | 80-100 |
| `PlayerBounce.cs` (baru, Core) | `jumpVelocity`, `SnapToSurface`, `LaunchUp`, `ForceBounce`. Apex konsisten by construction. | 60-80 |
| `PlayerDash.cs` (baru, Strategy) | `HandleDashRequest`, slam-cut, coyote, timeout cancel. Tipe dash baru = class baru. | 90-110 |
| `PlayerLandingResolver.cs` (baru) | `ShouldLand(prevBottom,platTop,tol)` + gate `TryLand/TryBoost`. Efek via `ApplyEffect`, streak via tracker. | 120-150 |
| `PlayerStreakTracker.cs` (baru) | `spawnId` pool-safe + fallback ref, `ResetStreak`. Kalkulasi murni. | 50-60 |
| `PlayerModeSwitcher.cs` (baru) | `Red<->Blue`, `UpdateColor`, `ForceToggleMode`, `RefreshAllPlatforms` via registry. | 50-70 |
| `PlayerLifecycle.cs` (baru, State+Observer) | Listen `GameManager.OnStateChanged`, `IsPlaying/sandbox`, `StartBallIntro/KillBallIntro`, `Die/DieFrom`, pause `savedVel`, `SetGameplayVisible`. | 130-160 |
| `IPlayerContracts.cs` (baru, ISP) | `IDamageable, IBounceable, IDashable, ILandable` @ 2-4 method. `Booster/Platform` depend ke interface. | 30-40 |
| `PlayerSfx/SquashStretch/SplashBurst` (tetap) | Sudah SRP, hanya re-wire event ke facade. | 0 |

Total: **~730-980 baris** (naik dari 608 — overhead pemisah, tiap file <160).

```
[Config SO] → [RuntimeState] → Core: Locomotion+Bounce (deterministik)
→ Computational: Streak+Mode → Transformation: Dash + Booster Strategy
→ Interaction: Lifecycle + Observer events → Facade: PlayerController
```

## 📌 Keputusan Desain

- **Facade pertahankan API:** `TryLand(Platform)`, `TryBoost(Booster)`, `SnapToSurface`, `LaunchUp`, `Die/DieFrom`, `ForceBounce/ForceToggleMode`, properti `CurrentMode/IsDashing/Streak/JumpVelocity` + 5 event — agar `Platform`, `BoosterSpawner`, `UIManager`, juice nol ubah.
- **Strategy, bukan switch:** dash/bounce sebagai strategy terpisah (ikut pola Booster di Plan Multi Booster). State pattern hanya untuk lifecycle (Intro/Playing/Dashing/Paused/Dying/Dead), bukan untuk tiap bounce.
- **Flyweight + SSOT:** semua angka tuning pindah ke `PlayerConfig` SO. Scene yang menang bila beda (ikut aturan TDD: `landTol` scene 0.5, bukan default kode).
- **DIP via interface:** `LandingResolver` pegang `IDashable/IBounceable`, bukan `PlayerController` konkret. Serialize konkret (`MonoBehaviour`), cast ke interface saat runtime (cara skill SOLID).
- **KISS:** bila 1 modul <2 state simpel, tetap enum — jangan maksa State penuh.

## 🐛 Bau yang Ikut Dibenahi

1. Class 608 baris → pecah SRP, facade tipis (skill SOLID).
2. `RefreshAllPlatforms` `FindAnyObjectByType` tersebar → sentral di `ModeSwitcher` via registry `PlatformSpawner` (no alloc), fallback `Find` hanya bila null.
3. Gate vs efek tercampur (sisa di `TryBoost`) → gate di `LandingResolver`, efek di `Booster.ApplyEffect` / `Bounce`.
4. `isDead` + `Health`-like ganda → computed `IsDead` dari state (SSOT derived data).
5. Magic number tersebar → `PlayerConfig` + `enum PlayerMode/GameState` (SSOT).

## ⚠️ Risiko

- Ganti field tuning ke SO bisa lepas ref inspector → migrasi nilai scene eksplisit (normalGravity 3, dashGravityMult 3.5, hang 0.85, sens 1.2, cooldown 0.15, slam -2, timeout 3, debounce 0.1, tol 0.5, eps 0.02).
- DOTween intro (`StartBallIntro/KillBallIntro`) sensitif `DOKill/SetLink` → pindah utuh ke `Lifecycle`, jangan pecah dulu.
- `rb.position` rule (TDD §3.4) wajib dipertahankan — review tiap modul Core yang sentuh `rb`.

## ✅ Urutan Kerja (tiap langkah dites dulu)

- [ ] 0. Git bersih, catat tuning scene (inspector menang)
- [ ] 1. `PlayerConfig` SO + migrasi nilai (perilaku identik)
- [ ] 2. `PlayerRuntimeState` (pindah field, tanpa ubah logika)
- [ ] 3. `PlayerLocomotion` + `PlayerBounce` (Core deterministik stabil dulu)
- [ ] 4. `PlayerDash` sebagai Strategy (gate + timeout dipindah utuh)
- [ ] 5. `PlayerLandingResolver` + `StreakTracker` + `ModeSwitcher` (gate vs efek pisah)
- [ ] 6. `PlayerLifecycle` + `IPlayerContracts` + tipiskan `PlayerController` jadi facade
- [ ] 7. Re-wire inspector, playtest checklist bawah

## 🧪 Cek Setelah Tiap Langkah

- 0 compile error; bounce/spring terasa identik (1.8, tint OFF, hanya >140, tidak di retak)
- Apex identik (snap `platTop+radius+eps`), wrap pillarbox 9:16 benar di layar lebar
- Dash: slam-cut, coyote 0.05s, timeout 3s, toggle warna hanya saat dash-landing
- Streak Plan A (beda +1 / sama 0, pool-safe `spawnId`), `Paused` tidak reset, `GameOver/MainMenu` reset
- `Dying` jatuh tembus, input/land mati; mute patuh; pool plateau
- Uji OCP: tambah dash/booster dummy = 0 edit file lama

## 🧩 Dampak Prefab & Assign Ulang

> `Player` di `Main` = instance `Assets/Prefabs/Player/Player 1.prefab` (`Connected`, `hasOverrides:false`). Duplikat nganggur: `Assets/Prefabs/Player/Player.prefab` — scene pakai yang `Player 1`.

### Wajib assign 1x

- Tambah 6-7 komponen baru ke `Player` = jadi override vs prefab sampai `Apply to Prefab` (`Player 1.prefab`). Tentukan 1 prefab kanonis, sinkronkan/hapus satunya.
- `PlayerController.sr -> Player/Ball Sprite` pindah ke `ModeSwitcher/Lifecycle` — assign ulang ke `Ball Sprite` yang sama.
- `PlayerConfig` SO baru — assign 1x ke facade + migrasi nilai scene (menang vs default kode): `3 / 3.5 / 0.85 / 1.2 / 0.15 / -2 / 3 / 0.1 / 0.5 / 0.02 / 6 / 7 / 0.6 / 0.35`.

### Aman tanpa assign (selama facade `PlayerController` tetap di `Player`)

- `PlayerSquashStretch.player/visual/rb` + `PlayerSplashBurst.player/splash/sfx` — sudah wired + fallback `if null GetComponent` + `RequireComponent`.
- `PlatformSpawner.player: Transform Player` wired; `playerCtrl` (`PlatformSpawner.cs:34`) private non-serialized, resolve via `player.TryGetComponent` — aman.
- `Enemy.cs:95`, `FlyingPatrolAI.cs:62`, `UIManager.cs:61`, `ComboFxText.cs:51,66`, `SimpleHazard.cs:41`, `Platform.cs:91`, `BoosterBase.cs:78` — semua `GetComponent/FindAnyObjectByType` runtime, aman.
- `Platform.prefab`, `Booster Spring.prefab`, `PlatformSpawner.prefab`, `UIManager.prefab` tidak simpan `PlayerController` serialized.

### Sebaiknya dibetulkan (rapuh sekarang)

- `StreakText/ComboFxText.followTarget = null` — jalan via auto-find (`ComboFxText.cs:37`). Assign explicit ke `Player`.
- `UIManager.player = null` (private, auto-find di `Awake`) — aman tapi rapuh bila ada 2 player.

### Aturan nol prefab-break

1. `PlayerController` tetap dengan nama sama di `Player` sebagai facade.
2. Modul baru: `[RequireComponent]` + `if null GetComponent`; serialize `MonoBehaviour`, cast ke `IDashable/IBounceable` saat runtime (Unity tidak serialize interface).
3. `Booster.ApplyEffect(PlayerController,...)` (`Booster.cs:21`) bila ganti ke interface = edit kode (`Booster.cs`, `BoosterBase.cs`, `SpringBooster.cs` + pemanggil `DieFrom`), bukan reassign prefab.
