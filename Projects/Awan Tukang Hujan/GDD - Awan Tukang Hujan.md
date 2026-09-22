# GDD - Awan Tukang Hujan

> GDD murni (What & Why). Untuk eksekusi kapan & siapa (How & When) lihat [[Planning - Awan Tukang Hujan]] terpisah. Sumber sketsa: [[Temporary 2]].
> Update: satu planet memiliki 40 slot (34 soil + 6 water).

---

## 🎮 1. Konsep & Identitas Game

Game ini adalah 2D Merge-Drag Ecosystem Survival Sandbox endless di mana pemain menggeser awan, menggabungkan awan jadi lebih besar dan gelap, lalu menghujani tanah di planet kecil melingkar yang bisa diputar untuk menjaga tanaman tetap hidup selama mungkin.

- **Premis:** Game ini adalah game survival santai di mana pemain jadi tukang hujan untuk menjaga ekosistem planet muter dari kekeringan.
- **Genre:** 2D Casual Merge-Drag / Ecosystem Survival (Edukasi Fun 80% Edu 20%)
- **Target Platform:** Mobile Landscape, 2D, Single Control (drag 1 jari doang)
- **USP:** Siklus air = verb, bukan teori. Stay (evaporasi) > Merge (kondensasi) > Hujan otomatis (presipitasi) > Kecil lagi + dunia lingkaran yang dev-nya muter tapi player ngerasanya geser + edge-drag 1 gesture 2 fungsi.
- **Referensi & Inspirasi:** Seperti 2048 (merge pop) + Tiny Wings (chill tactile) + Tamagotchi ekosistem + Tiny Planet loop world.

Batasan keras: tanpa angka & huruf di gameplay (full visual/ikon), tanpa kuis (no chocolate-covered broccoli), tanpa win condition, lose tunggal = tidak ada tanaman hidup lagi.

---

## 🔄 2. Core Gameplay Loop

**Loop Utama:** Geser awan ke genangan > diem (evaporasi) > overlap 2 awan merge (kondensasi gelap) > drag ke edge buat muter planet ke lahan target > hujan otomatis (presipitasi) > awan menyusut kecil > merge lagi > tanah basah > tanaman tumbuh/bertahan > tanah kering lagi > putar + hujan lagi > bertahan selama mungkin.

- **Core Mechanic:** Cuma geser. Overlap = merge. Edge-drag = putar dunia.
- **Daya Tarik Jangka Pendek:** Merge pop satisfying + hujan deres langsung lihat tanah menggelap + tunas muncul + planet tactile diputar.
- **Daya Tarik Jangka Panjang:** Endless survival. Tidak ada tamat. Makin lama makin banyak slot kering bersamaan, prioritas rotasi makin panas, pemain kejar bertahan lebih lama + lihat bunga mekar sebagai juice (bukan skor).

---

## ⚔️ 3. Mekanik Utama

Maks 5, sesuai sketsa King. Detail angka di BalanceConfig (lihat Planning).

- **Mekanik 1 — Geser Awan (Drag):** Drag 1 jari mindahin awan di langit (clamp Y di atas horizon). Magnet snap ringan saat dekat.
- **Mekanik 2 — Merge Overlap (Kondensasi):** Tempatkan 2+ awan overlap >0.4 dtk → merge jadi tier lebih besar. Rumus: Awan + Awan = Awan lebih besar. Kecil (putih kecil, hidup pendek) bisa jadi Normal (putih sedang, hidup lama) → Besar Gelap (auto-hujan).
- **Mekanik 3 — Hujan Otomatis (Presipitasi):** Awan gelap otomatis hujan sampai menyusut jadi kecil putih. Pemain tidak pencet hujan, cuma atur posisi + timing.
- **Mekanik 4 — Putar Planet + Edge-Scroll:** Daratan lingkaran (planet). Dev muter rotasi Z, player ngerasa geser. Cara: drag tanah kosong horizontal, atau drag awan ke 15% edge kanan/kiri → planet auto-rotate + panah indikator. Awan tetap di langit, genangan/tanah/tanaman ikut muter.
- **Mekanik 5 — Basah/Kering + Hidup/Mati (Opsi A Slot):** Opsi A dipilih: satu planet memiliki 40 slot (34 soil + 6 water), tiap slot 1 sprite (terlihat menyatu). Soil (34) kering sendiri dalam waktu tertentu, basah jika kena hujan 2 dtk. Tanaman (menempel di soil, maks 34, initial 17 tunable via BalanceConfig) tumbuh jika basah terus, kering/mati jika kering terus, berbunga visual jika subur terus (bukan win). Water (6) hasilkan awan + zone evaporasi (stay 1 dtk = tick uap).

Lose: `AliveCount == 0` → GameOver. Tidak ada HUD bunga, tidak ada skor, HUD cuma tombol Pause.

---

## 💻 4. Arsitektur Data & Design Pattern

- **Design Pattern Pilihan:**
  - `SSOT` — `BalanceConfigSO` (termasuk soilCount=34, waterCount=6, initialPlantCount=17) + `Cloud/Soil/PlantVisualSO` (Flyweight). Tidak ada hardcode timer di script.
  - `Centralized State Manager + Enum FSM` — `GameManager : Singleton<T>` + `GameState (Boot, MainMenu, Playing, Paused, GameOver)` + `OnStateChanged`.
  - `State Pattern` — `IState + StateMachine` untuk Cloud (Small/Normal/Dark), Soil (Dry/Wet), Plant (Seed/Growing/Thirsty/Dead/Blooming-visual).
  - `Observer` — `EventChannelSO`: `OnCloudMerged, OnRainTick, OnSoilChanged, OnPlantDied, OnPlantBloomed-visual, OnGameOver`. UI/Audio subscribe doang.
  - `Factory + Object Pool` — `CloudFactory` + `UnityEngine.Pool` untuk Cloud/Rain/Uap/Puddle. Anti GC spike mobile. Wajib untuk 40 slot (Dirty Flag + pool, jangan Instantiate per frame).
  - `MVP` — UI ikon-only: MainMenu / Pause / GameOver / HUD-PauseOnly. View animasi, Presenter dengar event.
  - `Dirty Flag` — SoilSlot (34 biji) update warna cuma pas berubah, bukan tiap frame.
  - `Singleton hemat` — hanya GameManager, PoolManager, AudioManager, PlantManager (AliveTracker).
- **Arsitektur & Penyimpanan Data:** ScriptableObject-based SSOT untuk balance + visual + event channel. Runtime split: Persistent (FTUE flag PlayerPrefs) vs Run (soil wet timer x34, plant state, cloud tier, planet angle). Nanti save cukup PlayerPrefs untuk FTUE + best survival (waktu, tanpa tampilkan angka ke pemain? simpan internal saja).
- **Mermaid Diagram:**
    ```mermaid
    graph TD
      BalanceConfigSO --> GameManager
      GameManager -- OnStateChanged --> InputManager
      GameManager --> UIManager
      InputManager -- drag/overlap/edge --> CloudManager
      CloudManager -- Factory/Pool --> Clouds
      Clouds -- OnRainTick --> SoilSlots
      SoilSlots -- OnSoilChanged --> PlantManager
      PlantManager -- OnPlantDied/OnGameOver --> GameManager
      PlantManager --> UIManager
      EventChannels --> AudioManager
    ```

---

## 🏛️ 5. Desain FTUE

Pendekatan: Contextual visual hint + Tutorial Building Blocks spasial, tanpa teks/angka. 5 langkah:

1. Panah jari: geser awan kecil ke atas genangan → uap naik (paham evaporasi).
2. Dua awan didekatkan + hint overlap → merge pop jadi Normal → Gelap (paham kondensasi).
3. Geser awan gelap ke tanah kering → hujan otomatis → tanah menggelap → tunas (paham presipitasi).
4. Drag awan gelap ke edge kanan → planet bergeser + panah edge → lahan baru (paham putar dunia).
5. Diamkan 10 dtk → tanah memucat → paham harus hujan rutin untuk bertahan. Tidak ada pesan menang.

---

## 🚀 6. Struktur Folder Modular & Optimisasi Performa

- **Struktur Folder (Feature-Based + .asmdef):**
```
Assets/_Project/
├── Core/ (GameManager, GameState, EventChannels, BalanceConfigSO)
├── Features/Cloud/ (Controller, StateMachine, Factory, VisualSO)
├── Features/Soil/ (SoilSlot, StateMachine)
├── Features/Plant/ (Controller, StateMachine, PlantManager-AliveTracker)
├── Features/Planet/ (PlanetRotator, EdgeScrollDetector, EvaporasiZone)
├── Features/Input/ (InputManager drag vs tanah)
├── Features/FX/ (RainPool, PuddlePool, UapPool)
├── UI/ (HUD-PauseOnly, MainMenu, Pause, GameOver - MVP)
└── Audio/ (AudioManager, Event-SFX map)
```
- **Rencana Optimisasi (penting untuk 40 slot mobile):**
  - *CPU/Memori:* Object Pool semua spawn berulang, Dirty Flag untuk 34 soil (jangan update tiap frame), Flyweight VisualSO shared, planet layout via data (loop spawn, bukan drag manual 40x).
  - *Rendering/UI:* 1 scene, kamera ortho fixed, sprite atlas untuk 40 slot + tanaman, Canvas split statis/dinamis nanti, sprite Unlit + SpriteMask untuk busur planet.

---

## 📏 7. Scope & Feasibility

- **Estimasi Durasi:** Prototype 2 minggu (solo). MVP 1-2 bulan jika lolos Go/No-Go.
- **Ukuran Tim:** Solo Dev (Rama - Programmer, Unity 6 + Blender cadangan + FL Studio BGM).
- **Risiko Teknis:** Merge overlap terasa adil (perlu magnet snap + hold 0.4 dtk), edge-scroll kepencet tidak sengaja (perlu deadzone + indikator), rotasi planet 40 slot bikin mabuk/berat (clamp 60 deg/dtk + easing + atlas + pool).
- **Risiko Desain:** Core bertahan >3 menit harus fun tanpa teks dengan 40 slot (jangan terlalu sepi/susah, air cuma 6 jadi rebutan). Validasi via playtest buta 5 menit.
- **Kriteria Go/No-Go:** Lolos jika pemain baru tanpa teks bisa merge + hujan + muter + bertahan >3 menit dan mau retry. Gagal jika edge-scroll salah trigger >3x, merge random, mati <1 menit (terlalu susah) atau >10 menit bosen (terlalu gampang) → tuning BalanceConfig dulu, jangan tambah fitur.

---
Lihat eksekusi di [[Planning - Awan Tukang Hujan]].
