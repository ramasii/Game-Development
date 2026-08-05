# 🗒️ Temporary Notes

## Plan Blok A — Complete the Loop

### Yang sudah ada:
- ✅ GameManager FSM (BuildPhase/WavePhase/RewardPhase/GameOver)
- ✅ HUDManager 4 panel
- ✅ Reward Panel UI (title + 3 blueprint card buttons + skip btn) — masih placeholder
- ✅ WaveManager auto-scaling

---

### Yang perlu dibangun:

**1. `RunModifiers.cs`** — Static class, simpan bonus aktif selama satu run
- Fields: `MinerSpeedMultiplier`, `TurretRangeBonus`, `ResourceSpeedBonus`
- `Reset()` dipanggil saat GameOver/restart
- Kenapa static: semua sistem bisa baca tanpa dependency inject

**2. `BlueprintData.cs`** — ScriptableObject per blueprint
- Fields: `displayName`, `description`, `icon`, `effectType` (enum), `effectValue` (float)
- Enum BlueprintEffectType: `FasterMiner`, `TurretRange`, `ConveyorSpeed`

**3 blueprint awal:**
| Blueprint | Effect |
|---|---|
| Iron Miner Mk.II | Miner spawn resource 30% lebih cepat (`MinerSpeedMultiplier += 0.3f`) |
| Extended Range | Turret range +2 unit (`TurretRangeBonus += 2f`) |
| Overclocked Belt | Resource bergerak +1 unit/s (`ResourceSpeedBonus += 1f`) |

**3. `BlueprintDraftManager.cs`** — Manager draft
- `_allBlueprints[]` — pool semua blueprint SO di Inspector
- `DrawThree()` — pick 3 acak dari pool, tidak duplikat dalam satu draft
- Subscribe RewardPhase → `DrawThree()`
- `SelectBlueprint(index)` → apply effect → `GameManager.NextWave()`

**4. Update `HUDManager.cs`**
- Tambah referensi 3 blueprint card (title TMP + desc TMP + button)
- `ShowBlueprints(data[])` → isi teks tiap card
- OnClick card → `BlueprintDraftManager.SelectBlueprint(index)`

**5. Update Miner/Turret/ResourceItem**
- Miner: `mineInterval / RunModifiers.MinerSpeedMultiplier`
- Turret: `_data.range + RunModifiers.TurretRangeBonus`
- ResourceItem: `_moveSpeed + RunModifiers.ResourceSpeedBonus`

**6. Reset saat restart**
- `RunModifiers.Reset()` dipanggil di `GameManager.RestartGame()`

---

### Urutan pengerjaan:
1. `RunModifiers` + `BlueprintData` + 3 SO assets
2. `BlueprintDraftManager`
3. Update `HUDManager` untuk blueprint cards
4. Update Miner/Turret/ResourceItem baca RunModifiers
5. Test loop penuh dengan blueprint aktif

### Pertanyaan yang belum dijawab Rama:
1. 3 blueprint di atas cocok, atau mau yang lain?
2. Efek blueprint apply ke instance yang **sudah ada di scene**, atau hanya yang **baru ditempatkan** setelah itu?


### Jawaban Rama:
1. 3 blueprint cocok. Value (range/speed/dll) harus bisa diubah lewat Inspector — pakai `effectValue` di BlueprintData SO, jangan hardcode.
2. Efek langsung apply ke SEMUA instance yang sudah ada di scene (bukan hanya yang baru ditempatkan). Artinya Miner/Turret/ResourceItem yang sudah exist harus ikut ke-update saat blueprint dipilih — butuh cara broadcast/refresh ke semua instance aktif.
