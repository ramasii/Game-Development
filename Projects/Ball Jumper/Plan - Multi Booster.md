# 🚀 Plan Multi Booster (Ball Jumper)

#plan #architecture #unity #ball-jumper

## 🎯 Tujuan

Ubah `BoosterSpawner.boosterPrefab` tunggal jadi **daftar entry per tipe** — siap tambah booster baru (jetpack, magnet, shield, ...) tanpa ubah `Spawner`/`PlayerController`/`Platform` (OCP).
Referensi: [[Skills/03-Game-Architecture/Strategy Pattern (Unity Ability)]], [[Skills/03-Game-Architecture/SOLID Principles (Unity)]], [[Skills/03-Game-Architecture/Object Pool Pattern (Unity)]], [[Skills/03-Game-Architecture/Single Source of Truth (SSOT)]].
Nilai tuning booster sekarang sudah dicatat di [[Temporary2]] (140/0.1/1.8/offset 0.1/tint OFF).

## 🗺️ Peta Coupling Sekarang

- `Platform.booster` bertipe `SpringBooster` (4 titik: `PrepareForSpawn`, `HasBoosterActive`, `RefreshVisual`, field).
- `BoosterSpawner` create/find `SpringBooster`, panggil `Setup/RefreshVisibility/PrepareForSpawn`, tint via `b.sr` langsung.
- `PlayerController.TrySpringBoost(SpringBooster)` isi gate (jatuh, top-crossing, solid) + streak + efek bounce + `ShowTriggered` + sfx — gate dan efek tercampur.
- 1 prefab + 1 chance/minY/multiplier global.

## 🗂️ Struktur Baru

| File | Tanggung jawab |
|---|---|
| `Booster.cs` (abstract, MonoBehaviour) | Kontrak semua tipe: `ParentPlatform`, `Visual`, `Setup(plat, mult)`, `RefreshVisibility(mode, dash)`, `PrepareForSpawn()`, `ApplyEffect(player)`, `PlayTriggerFeedback()` |
| `SpringBooster.cs` (`: Booster`) | Pindah logika pegas apa adanya; efek bounce jadi `ApplyEffect` |
| `BoosterSpawner.cs` | `boosterPrefab` → `List<BoosterEntry>`; roll per entry; attach generik via `Booster` |
| `BoosterEntry` (serializable) | `label`, `prefab` (wajib `Booster`), `minY`, `chance`, `multiplier` |
| `Platform.cs` | Field `booster`: `SpringBooster` → `Booster`; 4x `GetComponentInChildren<SpringBooster>` → `<Booster>` |
| `PlayerController.cs` | `TrySpringBoost(SpringBooster)` → `TryBoost(Booster)`: gate + streak tetap di player, efek via `b.ApplyEffect(this)` |

Keputusan: **maks 1 booster per platform** (entry pertama yang menang roll). Flag warna (`boosterOnGreen/Red/Blue`), offset, tint tetap **global** (inspector tidak meledak); per-tipe hanya `minY/chance/multiplier` (contoh: spring 140/0.1/1.8, jetpack 250/0.05/2.5).

## 📌 Keputusan Desain

- **Abstract class, bukan interface:** Unity tidak serialize interface — field `Platform.booster` runtime-assigned, tapi base class aman untuk inspector + `GetComponent<Booster>()` (skill Strategy versi MonoBehaviour).
- **Strategy, bukan switch di player:** tipe baru = class + prefab + entry, nol edit file lama (OCP). Factory belum perlu (1–3 tipe tanpa logika init khusus).
- **Tint tetap SSOT** via `Booster.Visual` (ganti akses `b.sr` langsung); whitebox fallback spring dipertahankan bila list kosong.
- **Pool tak berubah:** booster tetap child ikut recycle platform.

## 🐛 Bau yang Ikut Dibenahi

1. `b.sr` publik diakses spawner langsung → property `Visual` read-only di base.
2. `GetComponent<SpringBooster>()` tersebar 6 titik → `<Booster>` (sekali ganti, semua tipe ikut).
3. Gate vs efek tercampur di `TrySpringBoost` → pisah (gate di player, efek di tipe).

## ⚠️ Risiko

- Ganti tipe field `Platform.booster` bisa melepas ref di prefab → aman karena runtime selalu re-find via `GetComponentInChildren` (verifikasi saat playtest).
- `multiplier` global → per entry: isi ulang 1.8 untuk spring.
- Art + prefab tiap tipe baru tetap kerja King (kode tidak generate art).

## ✅ Urutan Kerja (tiap langkah dites dulu)

- [x] 0. Git bersih (tuning sudah tercatat)
- [x] 1. `Booster` abstract + `SpringBooster` warisi; `Platform.booster` → `Booster` (perilaku identik)
- [x] 2. `Player.TryBoost(Booster)` + `ApplyEffect` (spring bounce pindah; gate + streak tetap) + helper `SnapToSurface`/`LaunchUp`
- [x] 3. `BoosterSpawner` → `List<BoosterEntry>` + migrasi nilai spring; whitebox fallback tetap
- [x] 4. Re-wire inspector, playtest checklist bawah
- [ ] 5. (Nanti, per tipe baru) class + prefab + 1 entry — tanpa sentuh file lama

## 🧪 Cek Setelah Tiap Langkah

- 0 compile error; spring terasa identik (bounce 1.8, tint OFF, hanya >140, tidak di retak)
- Pool plateau; 1 platform maks 1 booster; mute tetap patuh
- Langkah 5 (simulasi): entry dummy ke-2 (prefab spring duplikat, minY tinggi) — spawn bergantian tanpa numpuk, tanpa ubah kode
