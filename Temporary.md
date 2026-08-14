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

### 🔧 Komponen yang Perlu Dibangun

#### **1. `RunModifiers.cs`** — Static Subject + Event
```csharp
Responsibility:
  - Simpan state modifier aktif selama satu run (static fields)
  - Trigger event "OnModifiersChanged" saat ada perubahan
  - Provide Reset() saat game over

Static Fields:
  - float MinerSpeedMultiplier = 1f
  - float TurretRangeBonus = 0f
  - float ResourceSpeedBonus = 0f

Static Event:
  - public static event System.Action OnModifiersChanged;

Static Methods:
  - void ApplyModifier(BlueprintEffectType type, float value)
    └─ Update field → Invoke OnModifiersChanged
  - void Reset()
    └─ Kembalikan semua field ke default → Invoke OnModifiersChanged
```

**Anti-Bug Checklist:**
- ✓ Invoke event HANYA JIKA value benar-benar berubah (guard: if newValue != oldValue)
- ✓ OnModifiersChanged call di akhir apply, bukan di awal
- ✓ Reset() dipastikan dipanggil di GameManager.RestartGame() — jangan lupa!
- ✓ Static init di LoadTime, bukan RuntimeInit

---

#### **2. `BlueprintData.cs`** — ScriptableObject (per blueprint)
```csharp
Serialized Fields:
  - string displayName = "Iron Miner Mk.II"
  - string description = "Miner spawn resource 30% faster"
  - Sprite icon
  - BlueprintEffectType effectType = FasterMiner
  - float effectValue = 0.3f  ← ADJUSTABLE VIA INSPECTOR

Enum BlueprintEffectType:
  - FasterMiner = 0
  - ExtendedRange = 1
  - ConveyorSpeed = 2

Validation (OnValidate):
  - Warn jika effectValue < 0 atau > reasonable limit
```

**3 Blueprint Assets (di Assets/ScriptableObjects/Blueprints/):**
1. **Iron Miner Mk.II** — effectType: FasterMiner, effectValue: 0.3
2. **Extended Range** — effectType: ExtendedRange, effectValue: 2.0
3. **Overclocked Belt** — effectType: ConveyorSpeed, effectValue: 1.0

**Anti-Bug Checklist:**
- ✓ SOs buat langsung di Project, assign di Inspector, jangan instantiate di code
- ✓ effectValue harus adjustable (public field, bukan hardcode)
- ✓ Validate effectValue di OnValidate agar tidak negatif saat edit SO

---

#### **3. `BlueprintDraftManager.cs`** — Draft & Selection Manager
```csharp
Responsibility:
  - Pool semua blueprint SO
  - Pilih 3 acak tanpa duplikat
  - Apply pilihan → trigger next wave

Fields:
  - [SerializeField] BlueprintData[] _allBlueprints  (isi via Inspector)
  - [SerializeField] int _draftSize = 3
  - BlueprintData[] _currentDraft = new BlueprintData[3]
  - bool _draftShown = false

Methods:
  - void OnEnable() → Subscribe GameManager.OnRewardPhaseStart
  - void OnDisable() → Unsubscribe
  - void DrawThree()
    └─ Random.Range pilih 3 tanpa duplikat
    └─ Invoke HUDManager.ShowBlueprints(_currentDraft)
    └─ Set _draftShown = true
  - void SelectBlueprint(int index)
    └─ Guard: if (!_draftShown) return  (anti double-click)
    └─ BlueprintData selected = _currentDraft[index]
    └─ RunModifiers.ApplyModifier(selected.effectType, selected.effectValue)
    └─ Set _draftShown = false
    └─ Invoke GameManager.NextWave()

Random Without Duplicate:
  - Use Fisher-Yates atau manual shuffle + take first 3
  - Guard: if (_allBlueprints.Length < 3) Warn & clamp
```

**Anti-Bug Checklist:**
- ✓ OnEnable/OnDisable subscribe/unsubscribe observer pattern
- ✓ Guard _draftShown di SelectBlueprint → anti double-submit
- ✓ Jangan hardcode index, gunakan array index dari panel card onclick
- ✓ Clear _currentDraft sebelum draw baru (reset array atau set to null)
- ✓ Cek _allBlueprints.Length >= 3 saat DrawThree()

---

#### **4. Update `HUDManager.cs`** — Blueprint Card Display
```csharp
Added Fields:
  - [SerializeField] Image[] _cardIcons = new Image[3]
  - [SerializeField] TMP_Text[] _cardNames = new TMP_Text[3]
  - [SerializeField] TMP_Text[] _cardDescriptions = new TMP_Text[3]
  - [SerializeField] Button[] _cardButtons = new Button[3]

New Methods:
  - void Start()
    └─ Setup button onClick → BlueprintDraftManager.SelectBlueprint(index)
      └─ Card 0 → SelectBlueprint(0)
      └─ Card 1 → SelectBlueprint(1)
      └─ Card 2 → SelectBlueprint(2)

  - void ShowBlueprints(BlueprintData[] drafts)
    └─ Guard: if (drafts == null || drafts.Length != 3) Log.Error return
    └─ Loop i=0..2:
       └─ _cardNames[i].text = drafts[i].displayName
       └─ _cardDescriptions[i].text = drafts[i].description
       └─ _cardIcons[i].sprite = drafts[i].icon
       └─ _cardButtons[i].interactable = true

  - void HideBlueprints()
    └─ Loop disable semua button
    └─ Opsional: set text to ""
```

**Anti-Bug Checklist:**
- ✓ Array bounds check (Length == 3)
- ✓ Null check untuk drafts & setiap field di drafts
- ✓ Button.interactable = true HANYA saat ShowBlueprints, false saat HideBlueprints
- ✓ Assign card buttons di Start(), not OnEnable (agar tidak override)
- ✓ Sprite bisa null? Guard: if (drafts[i].icon != null) assign, else log warn

---

#### **5. Update Systems Untuk Subscribe ke RunModifiers Event**

##### **5a. Miner.cs**
```csharp
Added:
  - OnEnable() → RunModifiers.OnModifiersChanged += RefreshMiningInterval
  - OnDisable() → RunModifiers.OnModifiersChanged -= RefreshMiningInterval
  
  - void RefreshMiningInterval()
    └─ _mineInterval = _data.baseInterval / RunModifiers.MinerSpeedMultiplier
    └─ Guard: if (_mineInterval <= 0) _mineInterval = _data.baseInterval (fallback)
    └─ Reset timer ke 0 agar effect terasa langsung

Changed:
  - MineCoroutine() → Gunakan _mineInterval (bukan hardcode)
```

**Anti-Bug Checklist:**
- ✓ Guard _mineInterval > 0
- ✓ OnDisable wajib unsubscribe (anti memory leak)
- ✓ Fallback ke baseInterval jika hasil negative (safety)
- ✓ Reset timer saat refresh agar tidak menunggu full cycle

##### **5b. Turret.cs**
```csharp
Added:
  - OnEnable() → RunModifiers.OnModifiersChanged += RefreshRange
  - OnDisable() → RunModifiers.OnModifiersChanged -= RefreshRange
  
  - void RefreshRange()
    └─ _effectiveRange = _data.baseRange + RunModifiers.TurretRangeBonus
    └─ Guard: if (_effectiveRange < 0) _effectiveRange = _data.baseRange (fallback)

Changed:
  - AcquireTarget() → Gunakan _effectiveRange (bukan _data.range)
```

**Anti-Bug Checklist:**
- ✓ Guard _effectiveRange >= 0
- ✓ OnDisable wajib unsubscribe
- ✓ Fallback ke baseRange
- ✓ Target search akan auto-re-evaluate saat event fired

##### **5c. ResourceItem.cs**
```csharp
Added:
  - OnEnable() → RunModifiers.OnModifiersChanged += RefreshSpeed
  - OnDisable() → RunModifiers.OnModifiersChanged -= RefreshSpeed
  
  - void RefreshSpeed()
    └─ _effectiveSpeed = _data.baseSpeed + RunModifiers.ResourceSpeedBonus
    └─ Guard: if (_effectiveSpeed <= 0) _effectiveSpeed = _data.baseSpeed (fallback)

Changed:
  - Movement logic → Gunakan _effectiveSpeed (bukan _data.speed)
```

**Anti-Bug Checklist:**
- ✓ Guard _effectiveSpeed > 0
- ✓ OnDisable wajib unsubscribe
- ✓ Existing items di scene akan langsung move faster

---

#### **6. Update `GameManager.cs`** — Reset Modifiers
```csharp
Added di RestartGame():
  - Call RunModifiers.Reset() SEBELUM spawn wave pertama
  - Pastikan urutan: Reset() → Clear scene → LoadWave(1)
```

**Anti-Bug Checklist:**
- ✓ Reset call SEBELUM spawn, bukan sesudah
- ✓ Pastikan GameOver → RestartGame flow jelas
- ✓ Semua observer akan receive OnModifiersChanged saat Reset()

---

### 📋 Urutan Implementasi (Anti-Stall)

1. **`RunModifiers.cs`** (5 min)
   - Buat static class + static event + ApplyModifier() + Reset()
   - Test: Console.Log saat ApplyModifier/Reset

2. **`BlueprintData.cs` + 3 SOs** (10 min)
   - Buat SO class, tambah OnValidate
   - Create 3 assets di Inspector, isi nilai

3. **`BlueprintDraftManager.cs`** (15 min)
   - Buat manager, subscribe RewardPhase
   - DrawThree() + SelectBlueprint()
   - Test: Console.Log saat select

4. **Update `HUDManager.cs`** (10 min)
   - Add card fields + ShowBlueprints()
   - Connect button onClick → SelectBlueprint

5. **Update Miner/Turret/ResourceItem** (20 min)
   - OnEnable/OnDisable subscribe/unsubscribe
   - RefreshXXX() method + guard checks
   - Test in-game: place a miner, then select blueprint (should speed up)

6. **Update `GameManager.cs`** (2 min)
   - Add RunModifiers.Reset() di RestartGame()

7. **Integration Test** (10 min)
   - Play full loop: Build → Wave → Reward (select blueprint) → repeat

---

### 🎯 Design Decisions (Sudah Confirmed Rama)

✅ **3 blueprint cocok**
✅ **effectValue di Inspector (SO field)**
✅ **Effect apply ke SEMUA instance aktif** (via Observer Pattern)

---

### ⚠️ Known Pitfalls & Mitigations

| Pitfall | Mitigation |
|---------|-----------|
| Double-submit blueprint | Guard `_draftShown` di SelectBlueprint |
| Event not fired | Check OnModifiersChanged += bukan = |
| Instance tidak subscribe (spawn terlambat) | OnEnable subscribe, jangan LoadTime static init |
| Null reference di HUDManager | Array bounds check + null guard setiap access |
| Loop di RefreshXXX | Gunakan simple update field, bukan coroutine |
| Negative value → crash | Guard: effectValue > 0 di OnValidate, fallback ke base di RefreshXXX |
| Memory leak | OnDisable WAJIB unsubscribe semua event |
| Timer tidak reset saat blueprint apply | Di RefreshMiningInterval, reset _currentDelay = 0f |

---

### 📊 Testing Checklist

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
