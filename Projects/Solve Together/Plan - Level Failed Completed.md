# Plan - Level Failed & Completed (Solve Together)

> Flow server-authoritative untuk 1 level playable. Failed = 1+ player mati kena hazard. Completed = semua player masuk GoalZone trigger. Arsitektur rapi ikut skill vault.

## Konteks
- Project: [[Solve Together]] — Co-op Puzzle 2D, Unity 6 + NGO, max 4, listen server
- Unity: PBJ Project, scene `Level 1`, `MainMenu` ada NetworkManager + ConnectionManager persistent
- GDD: [[GDD - Solve Together]] 4.2 / 4.3, Win = semua masuk pintu, Lose = 1 kena hazard
- Status: [[Timeline]] W1 prototyping movement + Host/Client connect sudah jalan, puzzle A+B belum ada

## Prinsip Arsitektur (wajib patuhi)
- [[Single Source of Truth (SSOT)]] — 1 pemilik per data, derived property, enum/constants, event broadcast
- [[Centralized State Manager (GameManager Singleton & Event)]] — LevelManager singleton per-scene + `OnLevelStateChanged`
- [[Simple FSM Berbasis Enum (Game State Prototyping)]] — enum ringan dulu, jangan State Pattern penuh sebelum stabil
- [[Observer Pattern Events]] — subscribe/unsubscribe, tanpa polling
- [[SOLID Principles (Unity)]] — SRP <300 baris, OCP via interface, DIP via `IDamageable`
- [[MVP Pattern (Unity UI)]] — View tampil doang, Presenter mediasi, Model = LevelManager
- [[Runtime State Separation]] — Persistent (playerIds Lobby) vs Runtime (deadIds/goalIds) vs Temporary (trigger enter)
- [[Singleton Pattern (Unity Generic)]] — max 2-3 manager, LevelManager per-scene bukan DontDestroy

---

## 1. LevelState.cs — Enum SSOT

**Skill:** [[Simple FSM Berbasis Enum (Game State Prototyping)]], [[Single Source of Truth (SSOT)]]

**Tujuan:** Satu-satunya daftar resmi state level.

**Aksi:**
- Baru: `Assets/Scripts/Level/LevelState.cs`
```csharp
public enum LevelState { Playing, Completed, Failed }
```
- Jangan tambah Paused/MainMenu di sini. Itu urusan ConnectionManager.

**Acceptance:**
- Compile 0 error, LevelManager bisa refer tanpa string hardcoded.

## 2. LevelManager.cs — Otak sentral server-authoritative

**Skill:** [[Centralized State Manager (GameManager Singleton & Event)]], [[Single Source of Truth (SSOT)]], [[Observer Pattern Events]]

**Tujuan:** Satu pemilik kebenaran. Server tulis, client lapor via Rpc.

**Aksi:**
- Baru: `Assets/Scripts/Level/LevelManager.cs` : `NetworkBehaviour`, `Instance` static per-scene
- Isi wajib:
```csharp
NetworkVariable<LevelState> levelState = new(Playing, Everyone, Server);
NetworkList<ulong> deadIds; goalIds;
static event Action<LevelState> OnLevelStateChanged;
bool IsPlaying => levelState.Value == Playing;
[ServerRpc(RequireOwnership=false)] ReportDeathServerRpc(ulong id)
[ServerRpc(RequireOwnership=false)] ReportEnterGoalServerRpc(ulong id)
[ServerRpc(RequireOwnership=false)] ReportExitGoalServerRpc(ulong id)
[ClientRpc] PlayFailedClientRpc() / PlayCompletedClientRpc()
void RestartLevel() // server only
```
- Logic server ReportDeath: `if(!IsPlaying) return; deadIds.Add(id); levelState.Value=Failed;`
- Logic server ReportEnter: `if(!IsPlaying||deadIds.Contains(id)) return; add goal; if(goalIds.Count == PlayerCount && deadIds.Count==0) levelState=Completed;`
- PlayerCount = `NetworkManager.Singleton.ConnectedClientsIds.Count` (live)
- Edit `PlayerController.cs`: kunci input + `simulated=false` saat `!IsPlaying`, mirip lock Lobby yang sudah ada
- Hierarchy Level 1: GameObject `LevelManager` + `NetworkObject` (scene object) + script

**Acceptance:**
- Host ubah levelState → semua client terima OnValueChanged sama. Client tulis langsung ditolak.

## 3. PlayerHealth + Hazard — Jalur Failed

**Skill:** [[SOLID Principles (Unity)]], [[Single Source of Truth (SSOT)]]

**Spec:** 1 atau lebih mati = Failed. Mati cuma dari hazard.

**Aksi:**
- Baru: `Assets/Scripts/Player/PlayerHealth.cs` : `NetworkBehaviour, IDamageable`
```csharp
interface IDamageable { void TakeDamage(); bool IsDead {get;} }
NetworkVariable<bool> isDead;
IsDead => isDead.Value;
TakeDamage() => RequestDieServerRpc();
[ServerRpc(RequireOwnership=false)] RequestDieServerRpc()
```
- Baru: `Assets/Scripts/Level/Hazard.cs` : `MonoBehaviour`
```csharp
OnTriggerEnter2D(Collider2D other) {
  if(!NetworkManager.Singleton.IsServer) return;
  if(other.TryGetComponent(out IDamageable h)) h.TakeDamage();
}
```
- Edit `Player.prefab`: tambah PlayerHealth, pastikan Rigidbody2D + BoxCollider2D IsTrigger=false
- Level 1: `Hazards/Spike` Box + BoxCollider2D IsTrigger + Hazard script

**Test Failed:**
- MPPM 2 instance: Host ke spike → dua-duanya Failed, freeze, deadIds==1. Client injak = hasil sama.

## 4. GoalZone — Jalur Completed

**Skill:** [[SOLID Principles (Unity)]], [[Observer Pattern Events]]

**Spec:** Semua player masuk area trigger = Completed.

**Aksi:**
- Baru: `Assets/Scripts/Level/GoalZone.cs` : `NetworkBehaviour`
```csharp
OnTriggerEnter2D → if IsServer → LevelManager.ReportEnterGoalServerRpc(playerId)
OnTriggerExit2D → ReportExitGoalServerRpc
playerId = other.GetComponent<NetworkObject>().OwnerClientId
```
- Level 1: `GoalZone` + BoxCollider2D IsTrigger 3x3 + warna hijau FTUE
- Prioritas: Failed > Completed. Kalau deadIds>0, ReportEnter di-ignore.

**Test Completed:**
- 1/2 di goal → tetap Playing + hint "Tunggu temanmu! (1/2)". 2/2 masuk → Completed semua. 1 mati pas 1 di goal → Failed.

## 5. LevelResult UI (MVP) — Tampil + Restart

**Skill:** [[MVP Pattern (Unity UI)]], [[Runtime State Separation]]

**Aksi:**
- View (UGUI di UICanvas): `ResultPanel (inactive)` → TitleText + SubText + RestartButton + LobbyButton
- Baru: `Assets/Scripts/UI/LevelResultPresenter.cs`
```csharp
OnEnable: LevelManager.OnLevelStateChanged += UpdateUI
UpdateUI(state): Failed merah, Completed hijau
RestartButton → LevelManager.RestartLevel() // server only
```
- Model = LevelManager.levelState/deadIds/goalIds langsung, jangan copy ke UI
- RestartLevel: `if(!IsServer) return; NetworkManager.SceneManager.LoadScene(NetConstants.Level1Scene, Single);`
- Edit `GameManager.cs` Level 1: hapus MultiplayerUI StartHost/Client lama, ganti ke LevelManager biar tidak dobel SSOT

**Acceptance:**
- Host Restart → semua reload Level 1 bareng, dead/goal kosong, Playing lagi. Client Restart di-ignore.

## 6. Bukti + Log Ethol Mingguan

**Skill:** [[PBL Brief - NGO]]

**Test matrix wajib:**
1. Host Failed → client ikut Failed
2. Client Failed → host ikut Failed
3. 1/2 goal → belum menang
4. 2/2 goal → Completed semua
5. Restart setelah Failed/Completed → sync Playing

**Log:**
- Milestone + git log + file Netcode diubah + bug desync + solusi
- Centang [[Timeline]] W1 prototyping movement + Host/Client + Failed/Completed base

## 🔗 Lihat Juga
- [[Solve Together]]
- [[GDD - Solve Together]]
- [[Timeline]]
- [[PBL Brief - NGO]]
