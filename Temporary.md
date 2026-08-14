# 🗒️ Temporary Notes

## 📋 Blok A — Complete the Loop (Detailed Anti-Bug Plan)

### ✅ Yang Sudah Ada
- GameManager FSM (BuildPhase/WavePhase/RewardPhase/GameOver)
- HUDManager 4 panel
- Reward Panel UI (title + 3 blueprint card buttons + skip btn) — masih placeholder
- WaveManager auto-scaling

---

### 🏗️ Arsitektur: Observer Pattern untuk Modifier Broadcast

**Prinsip Utama:**
- `RunModifiers` bertindak sebagai **Subject** (central store)
- Ketika blueprint dipilih → `RunModifiers` di-update → event "OnModifiersChanged" dipicu
- Semua **Observer** (Miner, Turret, ResourceItem) yang ada di scene subscribe ke event ini
- Saat event dipicu → semua instance yang aktif langsung update nilai mereka
- **Keuntungan**: Anti-duplikasi logika, scalable, instance baru otomatis subscribe saat spawn

---

## 🔧 Implementasi Status

### ✅ Completed (6/7 Major Steps)

| Step | Component | Status | Detail |
|------|-----------|--------|--------|
| 1 | `RunModifiers.cs` | ✅ Created | Static subject + event, ApplyModifier(), Reset() |
| 2a | `BlueprintData.cs` | ✅ Created | ScriptableObject + OnValidate guards |
| 2b | **3 Blueprint SOs** | ✅ Created | Iron Miner Mk.II, Extended Range, Overclocked Belt |
| 3 | `BlueprintDraftManager.cs` | ✅ Created | Draw 3 acak, SelectBlueprint(), Observer pattern |
| 4 | `HUDManager.cs` | ✅ Updated | ShowBlueprints(), HideBlueprints(), card button setup |
| 5a | `Miner.cs` | ✅ Updated | Subscribe RunModifiers, RefreshMiningInterval() |
| 5b | `Turret.cs` | ✅ Updated | Subscribe RunModifiers, RefreshRange() |
| 5c | `ResourceItem.cs` | ✅ Updated | Subscribe RunModifiers, RefreshSpeed() |
| 6 | `GameManager.cs` | ✅ Updated | RunModifiers.Reset() di RestartGame(), OnRewardPhaseStart event |

### ⏸️ Manual Setup Needed (Step 7a — Before Integration Test)

**Assign Blueprint Card References di HUDManager Inspector:**

Saat ini, HUDManager card arrays (`_cardIcons`, `_cardNames`, `_cardDescriptions`, `_cardButtons`) masih kosong di Inspector. Kamu perlu assign:

**Untuk Card 0 (Blueprint 1 Btn):**
- `_cardIcons[0]` → HUD Canvas / Reward Panel / Blueprint Btns / Blueprint 1 Btn (ambil Image component)
- `_cardNames[0]` → Blueprint 1 Btn / Text (TMP) (TextMeshProUGUI)
- `_cardDescriptions[0]` → Blueprint 1 Btn / Text (TMP) untuk description (atau buat child baru jika ada)
- `_cardButtons[0]` → Blueprint 1 Btn (Button component)

**Untuk Card 1 (Blueprint 1 Btn (1)):**
- `_cardIcons[1]` → HUD Canvas / Reward Panel / Blueprint Btns / Blueprint 1 Btn (1) / Image
- `_cardNames[1]` → Blueprint 1 Btn (1) / Text (TMP)
- `_cardDescriptions[1]` → Blueprint 1 Btn (1) / Text (TMP)
- `_cardButtons[1]` → Blueprint 1 Btn (1) / Button

**Untuk Card 2 (Blueprint 1 Btn (2)):**
- `_cardIcons[2]` → HUD Canvas / Reward Panel / Blueprint Btns / Blueprint 1 Btn (2) / Image
- `_cardNames[2]` → Blueprint 1 Btn (2) / Text (TMP)
- `_cardDescriptions[2]` → Blueprint 1 Btn (2) / Text (TMP)
- `_cardButtons[2]` → Blueprint 1 Btn (2) / Button

**Also:**
- `_blueprintDraftManager` → Blueprint Draft Manager (reference GameObject yang punya BlueprintDraftManager component)
- Assign 3 Blueprint SOs ke `BlueprintDraftManager._allBlueprints[]` (sudah ada di scene, cek di inspector)

---

### 📊 Step 7 — Integration Test (Ready untuk run)

Setelah card references di-assign, coba run dengan checklist:

- [ ] Play mode → tidak ada error di console
- [ ] BuildPhase aktif, "Start Wave" button visible
- [ ] Click "Start Wave" → WavePhase dimulai, wave counter naik
- [ ] Wait untuk wave finish → RewardPhase trigger
- [ ] **RewardPhase: 3 blueprint cards appear** dengan nama, deskripsi, dan button
- [ ] Click blueprint card → RunModifiers.ApplyModifier() log muncul
- [ ] Existing Miner/Turret/ResourceItem speed/range meningkat langsung (log: "Speed refreshed", "Range refreshed", dll)
- [ ] Click "Next Wave" → kembali ke BuildPhase, modifiers tetap active
- [ ] Repeat: Start Wave lagi → wave selesai → pilih blueprint lain
- [ ] Modifiers cumulative (pilih 2 blueprint → bonus stack)
- [ ] Game Over → Restart → RunModifiers.Reset() dipanggil (log: "Reset all modifiers"), bonus hilang

---

## ⚠️ Known Pitfalls & Mitigations

| Pitfall | Mitigation |
|---------|-----------|
| Double-submit blueprint | Guard `_draftShown` di SelectBlueprint |
| Event not fired | Check OnModifiersChanged += bukan = |
| Instance tidak subscribe | OnEnable subscribe, jangan LoadTime static init |
| Null reference di HUDManager | Array bounds check + null guard setiap access |
| Negative value → crash | Guard: effectValue > 0 di OnValidate, fallback ke base di RefreshXXX |
| Memory leak | OnDisable WAJIB unsubscribe semua event |
| Timer tidak reset saat blueprint apply | Di RefreshMiningInterval, reset _currentDelay = 0f |
| Card buttons tidak respond | Check array di HUDManager properly assigned |

---

## 📊 Testing Checklist

- [ ] RunModifiers.ApplyModifier() correctly updates field + fires event
- [ ] RunModifiers.Reset() clears all modifiers + fires event
- [ ] BlueprintDraftManager draws 3 unique blueprints per draft
- [ ] HUDManager shows correct card text/icon
- [ ] Clicking card → SelectBlueprint → RunModifiers updated
- [ ] Miner speed increases immediately after blueprint select
- [ ] Turret range increases immediately after blueprint select
- [ ] Resource speed increases immediately after blueprint select
- [ ] Wave progresses correctly after selecting blueprint
- [ ] Restarting game → RunModifiers reset → values back to base
- [ ] No memory leaks (Observer unsubscribe on OnDisable)
- [ ] No double-submit (draft button disabled after first click until next RewardPhase)

---

### 📌 Reference: Architectural Patterns Applied

- **Observer Pattern**: RunModifiers (Subject) + Miner/Turret/ResourceItem (Observers)
  → [[Observer Pattern Events]]
- **Single Source of Truth (SSOT)**: RunModifiers adalah satu-satunya pemilik modifier state
  → [[Single Source of Truth (SSOT)]]
