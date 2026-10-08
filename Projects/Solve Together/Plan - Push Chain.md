# Plan - Push Chain Berbobot (Solve Together)

> Mekanik dorong berantai: [P1]->[P2]->[P3]->[BOX], tiap player power=1, box punya requiredPower. Box gerak hanya jika totalPower >= requiredPower dan searah. Server-authoritative, arsitektur rapi ikut skill vault, tanpa merusak arsitektur existing.

## Konteks
- Project: [[Solve Together]] — Co-op Puzzle 2D, Unity 6 + NGO, max 4, listen server
- Unity: PBJ Project, scene `Level 1`, prefab `Player.prefab` (Rigidbody2D mass=1, NetworkObject + NetworkTransform owner-auth, PlayerMover set linearVelocity)
- GDD: [[GDD - Solve Together]] 4.1 / 4.3 server-authoritative, Client kirim ServerRpc, Server broadcast via NetworkTransform + ClientRpc
- Existing: [[Plan - Level Failed Completed]] — LevelManager SSOT levelState/deadIds/goalIds, PlayerController lock saat !IsPlaying
- Status: [[Timeline]] W2-4 Engine Integration

## Prinsip Arsitektur (wajib patuhi)
- [[Single Source of Truth (SSOT)]] — 1 pemilik per data, derived property, enum/constants
- [[Centralized State Manager (GameManager Singleton & Event)]] — LevelManager tetap satu-satunya otak level, max 2-3 manager, jangan bikin PushManager singleton baru
- [[Observer Pattern Events]] — subscribe/unsubscribe, tanpa polling
- [[SOLID Principles (Unity)]] — SRP <300 baris, OCP via interface, DIP via IPushable/IPushPowerProvider
- [[Runtime State Separation]] — Persistent (config) vs Runtime (currentPower/pusherIds) vs Temporary (kontak/intent per-frame)
- [[Simple FSM Berbasis Enum (Game State Prototyping)]] — enum ringan dulu, jangan State Pattern penuh
- [[Merancang Sistem Sinergi & Item Roguelite]] Tipe A Chain Reaction — Trigger [pushing+searah] -> Efek [power diteruskan] -> Amplifier [chain menambah total]
- [[Tutorial Level Building Blocks]] + [[Framework Kihon-Kata-Kumite (Learning Curve & Encounter Design)]] — Kihon 1-power zero-risk, Kata 2-power repetisi, Kumite 3-power high-stakes
- [[MVP Pattern (Unity UI)]] — View tampil doang, Presenter mediasi, Model = PushableBox

---

## 0. Batasan Non-Merusak (hasil inspeksi Unity)

- `Assets/Scripts/Level/LevelManager.cs` — SSOT `levelState`, `deadIds`, `goalIds`, server-write only. `IsPlaying` jadi gerbang semua gerak. PushableBox hanya baca, tidak pernah tulis.
- `Assets/Scripts/Player/PlayerController.cs` — lock input + `simulated=false` saat `!IsPlaying`. Push ikut pola lock ini, jangan bikin lock baru.
- `Assets/Prefabs/Player.prefab` — jangan ubah Mover/Health/NetworkObject. Hanya tambah PushDetector.
- Scene `Level 1` — jangan ubah HazardZone, GoalZone, LevelResultPresenter. Hanya tambah box uji.
- `NetConstants` tetap SSOT nama scene. Jangan hardcode string scene.

## 1. Desain Netcode (server-authoritative, anti-desync)

Player pakai owner-auth NetworkTransform. Kalau box didorong via fisika lokal client → pasti desync. Jadi:

```
Client: PushDetector lapor intent (dir + isPushing) via ServerRpc
  -> Server: PushChainResolver BFS tiap FixedUpdate:
     Box <- siapa sentuh box & dorong ke arah box?
         <- siapa sentuh pendorong itu dari belakang & searah? (rekursi max 4)
     sum power, filter mati (deadIds) + !IsPlaying
  -> Server: NetworkVariable currentPower, pushState (Everyone read, Server write)
  -> Server: gerakkan Rigidbody2D box (kinematic, MovePosition) hanya jika cukup
  -> Broadcast: OnPushStateChanged + ClientRpc untuk FX + hint "2/3"
```

- Box NetworkObject milik server, NetworkTransform server-auth.
- Client tidak pernah set posisi box langsung.
- Arah: 2D side-scroller sumbu X saja. Syarat valid: semua pendorong facing sama + moveInput.x searah ke box.
- Power: `IsMoving => currentPower >= requiredPower` (derived, bukan field ganda).

## 2. File Baru (tambah saja)

### 2.1 PushState.cs — Enum SSOT
**Skill:** [[Simple FSM Berbasis Enum (Game State Prototyping)]], [[Single Source of Truth (SSOT)]]

- Baru: `Assets/Scripts/Push/PushState.cs`
```csharp
public enum PushState { Idle, Strained, Moving }
```
- Jangan tambah state lain dulu.

### 2.2 IPushable.cs + IPushPowerProvider.cs — Abstraksi OCP/DIP
**Skill:** [[SOLID Principles (Unity)]]

- Baru: `Assets/Scripts/Push/IPushable.cs`
```csharp
public interface IPushable { int RequiredPower { get; } int CurrentPower { get; } bool IsMoving { get; } }
```
- Baru: `Assets/Scripts/Push/IPushPowerProvider.cs`
```csharp
public interface IPushPowerProvider { int GetPushPower(); Vector2 PushDir { get; } bool IsPushing { get; } }
// default player: power 1
```
- Hasil: varian box baru (es/berat) tanpa edit resolver.

### 2.3 PushChainResolver.cs — Pure logic, testable
**Skill:** [[SOLID Principles (Unity)]], [[Merancang Sistem Sinergi & Item Roguelite]]

- Baru: `Assets/Scripts/Push/PushChainResolver.cs` static pure
```csharp
public static int ResolveTotalPower(IPushable box, List<IPushPowerProvider> chain Candidates)
// BFS max 4: box <- sentuh & searah <- sentuh belakang & searah, sum power
```
- SRP: hanya hitung total, tidak gerakkan fisika, tidak netcode.

### 2.4 PushableBox.cs — SSOT box, server only move
**Skill:** [[Single Source of Truth (SSOT)]], [[Centralized State Manager (GameManager Singleton & Event)]], [[Observer Pattern Events]]

- Baru: `Assets/Scripts/Push/PushableBox.cs` : `NetworkBehaviour, IPushable`
```csharp
[SerializeField] int requiredPower = 3;
[SerializeField] float moveSpeed = 2f;
NetworkVariable<int> currentPower (Everyone, Server);
NetworkVariable<PushState> pushState;
NetworkList<ulong> pusherIds; // runtime
static event Action<IPushable> OnPushStateChanged;
FixedUpdate(): if(!IsServer || !LevelManager.Instance.IsPlaying) return; Resolve + MovePosition
```
- Rigidbody2D kinematic, BoxCollider2D non-trigger, NetworkObject server-owned + NetworkTransform server-auth.
- Tidak pernah tulis levelState. Baca IsPlaying + deadIds untuk filter.

### 2.5 PushDetector.cs — Di player, lapor intent
**Skill:** [[SOLID Principles (Unity)]], [[Runtime State Separation]]

- Baru: `Assets/Scripts/Push/PushDetector.cs` di `Player.prefab`
```csharp
ReportPushIntentServerRpc(Vector2 dir, bool isPushing)
// Temporary: trigger enter/exit + BoxCast depan/belakang
// Filter: IsDead -> ignore, !IsPlaying -> disable
```
- Pushing = bergerak menekan ke objek (reuse MoveInput.x, tanpa input baru).

### 2.6 PushHintPresenter.cs — MVP UI
**Skill:** [[MVP Pattern (Unity UI)]], [[Observer Pattern Events]]

- Baru: `Assets/Scripts/UI/PushHintPresenter.cs`
- View: world-space Text `Butuh 3 (2/3)`, merah Strained, hijau Moving, hide saat Idle.
- Model = PushableBox.currentPower/requiredPower langsung, jangan copy.
- OnEnable: `PushableBox.OnPushStateChanged += UpdateUI`.

## 3. Edit Kecil (terisolasi)

- `Player.prefab`: + PushDetector saja.
- `PlayerController.cs UpdateLobbyState()`: + `PushDetector.enabled = !locked` (1 baris, ikut pola lock existing).
- `Level 1`: tambah `PushBox_1 (req 1)`, `PushBox_2 (req 2)`, `PushBox_3 (req 3)`. Jangan ubah Hazard/Goal.

## 4. Staging Level FTUE
**Skill:** [[Tutorial Level Building Blocks]], [[Framework Kihon-Kata-Kumite (Learning Curve & Encounter Design)]]

- **Kihon (zero-risk):** koridor datar, box req 1, tanpa hazard. Dorong 1 orang langsung gerak.
- **Kata (low-stakes, repetisi):** box req 2, butuh chain 2 orang searah. Spawn/checkpoint dekat.
- **Kumite (high-stakes):** box req 3 dekat HazardZone / sebelum GoalZone. Gagal ikut aturan existing (1 mati = Failed). Reward = Access (pintu/jalan terbuka).

## 5. Acceptance + Test MPPM

1. 1 orang dorong box req 3 → Strained, diam, hint 1/3.
2. Chain 3 searah → Moving, jalan sinkron host+client.
3. Arah beda → tidak dihitung, tetap diam.
4. 1 lepas tengah jalan → Strained <0.2s, berhenti.
5. 1 pendorong mati → power dicoret, berhenti.
6. Failed/Completed → freeze (ikut IsPlaying), Restart → currentPower=0, box kembali spawn.
7. Host vs client dorong hasilnya identik.

## 6. Urutan Kerja

1. Core offline: PushState + interface + Resolver + Box kinematic + Detector tanpa Netcode, test 1-player editor.
2. Netcode-kan: NetworkVariable + ServerRpc intent + server FixedUpdate + event + hint UI.
3. Staging + polish: prefab 1/2/3, susun Kihon→Kata→Kumite di Level 1, MPPM 2-4 instance, log Ethol.

## 🔗 Lihat Juga
- [[Solve Together]]
- [[GDD - Solve Together]]
- [[Plan - Level Failed Completed]]
- [[PBL Brief - NGO]]
- [[Timeline]]
