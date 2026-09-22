# AWAN // Tukang Hujan - Concept Keep (Revisi Sketsa)

> Update 21 Sep 2026 sesuai sketsa King. Fun 80% Edu 20%. No angka & huruf. No chocolate-covered broccoli.
> Opsi visual: A (pecah slot tanah). Plan dev di bagian 9.
> Revisi endless: no win, no HUD bunga. Lose = tidak ada tanaman hidup lagi.

## 1. Konsep & Identitas
- **Premis:** Game ini adalah 2D santai mobile endless di mana pemain menggeser awan, menggabungkan awan jadi besar, dan menghujani tanah biar tanaman tetap hidup selama mungkin, di atas planet kecil melingkar yang bisa diputar.
- **Genre:** 2D Merge-Drag / Ecosystem Survival Sandbox, Casual
- **Target Platform:** Mobile, 2D, LANDSCAPE, 1 jari (single control: drag/geser doang)
- **USP:** Siklus air dimainin langsung: Stay (evaporasi) > Merge (kondensasi) > Hujan (presipitasi) > Kecil lagi + dunia melingkar yang diputar dengan cara digeser. Main selama lu mampu, tidak ada tamat.
- **Referensi:** Seperti 2048 merge-feel + Tiny Wings chill + Tamagotchi ekosistem + Loop World ala Tiny Planet.

## 2. Core Loop (revisi endless)
Geser awan ke atas genangan > diem di atas air (evaporasi) > geser + overlap 2 awan buat merge (kondensasi jadi besar & gelap) > drag awan ke edge buat muter daratan ke area target > awan gelap hujan otomatis (presipitasi) > awan menyusut jadi kecil putih > merge lagi atau biarin regen > tanah basah > tanaman tumbuh & bertahan > tanah kering lagi > putar + hujan lagi > bertahan selama mungkin

- **Core Mechanic:** Cuma geser. Overlap = merge. Edge-drag = putar dunia.
- **Daya tarik 5 menit:** Merge pop satisfying + hujan deres + muter planet yang tactile.
- **Daya tarik panjang:** Endless survival — jaga semua tanaman jangan sampai habis. Makin lama makin banyak lahan kering bersamaan, rotasi prioritas makin panas.

## 3. Sistem Detail (sesuai sketsa)

### LANDSCAPE / Siklus
`Genangan -> (stay/evaporasi) -> Awan kecil -> MERGE (kondensasi) -> Awan besar gelap -> MERGED -> Presipitasi hujan -> jadi kecil lagi -> balik ke genangan`
- Layout landscape: kamera fixed di langit, daratan melengkung di bawah sebagai busur planet.

### WORLD / DARATAN MELINGKAR (baru)
- Player bisa menggeser daratan untuk pindah area.
- Daratan berbentuk lingkaran (planet kecil), sehingga ketika digeser terus menerus bisa kembali ke lokasi awal.
- Dari POV developer ini "memutar" daratan (rotasi angle planet), tapi bagi player ini terasa menggeser daratannya (parallax horizontal).
- Implementasi: planet = circle, kamera diam, putar container daratan di sumbu Z. Awan tetap di screen-space langit.
- Edukasi bonus: planet bulat + horizon melengkung kebaca visual tanpa teks.

### PLAYER (update)
- Player menggerakkan awan dengan cara menggeser awan (drag 1 jari).
- Player menggabungkan awan dengan cara menempatkan dua awan atau lebih secara overlap.
- Player bisa menggeser awan lalu didrag ke paling kanan atau kiri layar untuk otomatis menggeser daratan dan memindahkan awan (edge-scroll).
  - Drag awan ke edge kanan > planet muter kiri (area baru masuk dari kanan).
  - Drag awan ke edge kiri > planet muter kanan.
  - Awan ikut kebawa secara visual, tapi logikanya dunia yang muter.
- Player juga bisa geser daratan langsung (drag tanah kosong kiri/kanan) untuk eksplor tanpa bawa awan — tetap 1 jari, tidak nambah tombol.

### AWAN
- Awan yang sudah menjadi gelap, akan otomatis hujan.
- Hujan berlangsung sampai awan menjadi kecil dan berwarna putih (awan kecil).
- Awan kecil (putih kecil) memiliki waktu hidup sekian detik (pendek, harus cepat di-merge / dipakai).
- Awan normal (putih sedang) memiliki jangka waktu yang lama (awan kerja utama).
- Awan kecil bisa menjadi awan normal dengan cara digabungkan dengan awan lain.
- Rumus: Awan + Awan = Awan lebih besar.
- Awan tidak ikut rotasi planet (tetap di langit), hanya daratan yang muter — jadi pemain harus timing mindahin hujan ke atas lahan target.

### GENANGAN AIR
- Menghasilkan awan (spawner pasif).
- Fungsi: tempat evaporasi — stay in top of water = isi ulang / munculin awan baru.
- Genangan menempel di planet (ikut muter), jadi kadang harus muter dulu buat cari air.

### TANAH (Opsi A - Slot)
- Update: satu planet memiliki 80 slot (64 soil + 16 water), tiap slot 1 sprite Opsi A. Terlihat menyatu, logik per-slot.
- Soil (64) bisa kering dalam waktu tertentu.
- Soil bisa basah jika terkena hujan.
- Water (16) menempel di planet ikut muter, jadi cari air = tantangan rotasi.
- Visual only: kering (pucat/retak) vs basah (gelap). Tanpa angka, tanpa teks.
- Tanaman menempel di soil (maks 64, initial 32 tunable). Sumber tunggal kini di GDD + Planning folder project.

### TANAMAN + LOSE CONDITION
- Tanaman bisa kering dan mati dalam jangka tertentu (kalau tanah kering terus).
- Tanaman bisa tumbuh di tanah yang selalu basah.
- Tanaman bisa menumbuhkan bunga jika subur dalam waktu tertentu (murni reward visual/juice, bukan win, bukan skor).
- Tanaman menempel di planet, jadi jaga kelembaban = main rotasi + prioritas hujan.
- **Tidak ada win condition. Tidak ada target bunga. Biarkan player main selama dia mampu.**
- **Lose condition tunggal: ketika tidak ada lagi tanaman yang hidup (AliveCount == 0).** Semua slot plant Dead / hilang = GameOver.

## 4. Kenapa Fun Dulu (80%)
- Merge itu candu: geser-overlap-pop-besar.
- Hujan otomatis sebagai reward, bukan hukuman — awan gelap = saatnya panen.
- Muter planet itu tactile & satisfying (kayak putar globe), eksplorasi tanpa loading.
- Edge-drag mindahin awan + dunia sekaligus = 1 gesture 2 fungsi, tetap single control.
- Endless tension: tidak ada tamat, cuma "berapa lama lu bertahan?" — urgency alami dari tanah yang kering bergantian.

## 5. Edukasi 20% (muncul alami)
- Evaporasi: diem di atas air = dapat awan.
- Kondensasi: merge 2 awan = jadi besar gelap.
- Presipitasi: gelap = hujan sampe kecil lagi.
- Ekologi: tanah basah ↔ tanaman hidup, tanah kering ↔ mati, subur terus ↔ berbunga.
- Planet: dunia bulat, horizon melengkung, area beda butuh perjalanan (rotasi).

## 6. Anti Chocolate-Covered Broccoli Check
- BUKAN: kuis siklus air buat buka hujan.
- INI: siklusnya = cara mainnya. Cabut semua label edukasi, game tetap fun dimainin sebagai merge-hujan + putar planet.

## 7. FTUE (tanpa angka/huruf, full visual)
1. Awan kecil + panah jari: geser ke atas genangan → uap naik (evaporasi paham).
2. Dua awan deketan + hint overlap → merge jadi sedang → merge lagi jadi gelap.
3. Awan gelap digeser ke tanah kering → hujan otomatis → tanah gelap → tunas muncul.
4. Drag awan gelap ke edge kanan → daratan bergeser, lahan baru masuk → pemain paham edge-scroll muter dunia.
5. Biarin: tanah memucat lagi → pemain paham harus hujan rutin + muter prioritas. Tidak ada pesan menang, cuma disuruh bertahan.

## 8. Scope Prototype
- **Solo Unity 6, 1-2 minggu:** 1 scene landscape, drag awan (raycast 2D), overlap check buat merge, state awan: kecil/normal/besar-gelap (scale + color), timer hidup, rain particle + soil wet/dry timer, plant state: benih/tumbuh/kering/mati/bunga (bunga = visual only).
- **Tambahan planet:** 1 empty PlanetRoot (rotasi Z), slot tanah/genangan/tanaman jadi child di sekeliling lingkaran. Edge-drag: jika awan x > 0.85*halfWidth atau < -0.85*halfWidth → rotate PlanetRoot dengan kecepatan proporsional. Drag tanah kosong → rotate langsung.
- **Lose check:** PlantManager hitung AliveCount tiap OnPlantDied. Jika 0 → GameOver.
- **Risiko:** overlap merge terasa adil (magnet snap), edge-scroll tidak ke-trigger tidak sengaja (deadzone + indikator panah edge), balancing timer jangan frustasi.
- **Go/No-Go:** Playtest 5 menit tanpa teks, pemain bisa merge + hujan + muter + bertahan hidup >3 menit tanpa semua tanaman mati = lanjut.

---
# 9. DEV PLAN - Opsi A (Slot Tanah) - Detail Flow + Arsitektur

> Tujuan: prototype rapi, modular, siap scale ke MVP. 1 scene, no angka/huruf di UI (full ikon). Endless, no win.

## 9.1 Flow: Main Menu -> In Game -> Pause -> Main Menu + GameOver

```mermaid
graph TD
  Boot[Boot - Init Managers] --> MainMenu[MAIN MENU - Idle Planet]
  MainMenu -- Tap Awan-Main -icon- --> FTUECek{Cek Save FTUE?}
  FTUECek -- Belum --> InGame_FTUE[IN GAME - FTUE Scripted]
  FTUECek -- Sudah --> InGame_Free[IN GAME - Endless Survival]
  InGame_FTUE --> InGame_Free
  InGame_Free -- Tap Pause-icon --> Paused[PAUSED - TimeScale 0]
  Paused -- Resume-icon --> InGame_Free
  Paused -- Restart-icon --> InGame_Free
  Paused -- Home-icon --> MainMenu
  InGame_Free -- App Pause / Back HP --> Paused
  InGame_Free -- AliveCount == 0 --> GameOver[GAMEOVER - Planet Kering]
  GameOver -- Restart-icon --> InGame_Free
  GameOver -- Home-icon --> MainMenu
```

**Detail tiap state (GameState enum: Boot, MainMenu, Playing, Paused, GameOver):**

1. **Boot (1-2 detik)**
   - Init: GameManager, Pool, Factory, Audio, Load BalanceConfig (SSOT), Load FTUE flag PlayerPrefs.
   - Tampilkan splash ikon awan doang, no teks.

2. **MAIN MENU**
   - Visual: planet muter pelan otomatis, 2 awan dummy drift, tanaman demo hidup. Kamera sama kayak in-game (no load scene baru).
   - UI (ikon only): [Awan-Main besar], [Speaker on/off kecil], [Ulang FTUE kecil - ikon tangan].
   - Input: drag planet boleh (eksplor), tapi awan tidak bisa di-drag (locked).
   - Transisi: tap Main > fade + pop scale > GameManager.SetState(Playing).

3. **IN GAME - Playing (Endless, no win)**
   - HUD: cuma 1 tombol kiri-atas ikon Pause. Tidak ada HUD bunga, tidak ada skor, tidak ada progress win.
   - Input aktif penuh:
     - Drag awan bebas di langit (clamp Y atas garis horizon).
     - Overlap 2 awan >0.4 detik + magnet snap > merge pop.
     - Drag awan ke edge (x > 85% layar) > PlanetRoot rotate + indikator panah edge muncul.
     - Drag tanah kosong horizontal > rotate planet manual.
     - Stay di atas genangan 1 detik > evaporasi tick (uap particle + awan tumbuh).
   - Bunga = juice visual doang saat subur, bukan tujuan, tidak dihitung di HUD.
   - Lose check tiap frame/event: `PlantManager.AliveCount`. Jika 0 → `SetState(GameOver)` + efek planet mengering (desaturate) + awan sisa fade out.
   - Tidak ada celebration menang. Reward = bertahan lebih lama + lihat ekosistem hidup.

4. **PAUSED**
   - Trigger: tombol pause, tombol Back HP, OnApplicationPause(true). Tidak aktif saat GameOver.
   - Efek: Time.timeScale=0, semua timer & hujan & rotasi freeze, input drag di-ignore.
   - UI panel tengah: [Resume - ikon play], [Restart - ikon putar], [Home - ikon rumah/planet]. Background blur + planet berhenti.
   - Resume: langsung resume + 0.5 detik input grace (tanpa angka, pakai animasi dot membesar).
   - Restart: reset Run state doang (tanah kering ulang, tanaman hidup ulang, awan reset 2 normal), tidak balik ke Boot, tidak hapus FTUE flag.
   - Home: SetState(MainMenu), reset Run, planet balik ke rotasi 0 pelan-pelan (lerp).

5. **GAMEOVER (Lose)**
   - Trigger tunggal: tidak ada lagi tanaman yang hidup.
   - Visual: planet pucat/retak semua, tanaman gosong, awan hilang, musik menipis (adaptive layer off).
   - UI panel tengah (ikon only): [Restart - ikon putar], [Home - ikon planet]. Tidak ada skor, tidak ada teks "kalah" (pakai ikon awan sedih / planet kering).
   - Restart → reset Run langsung Playing. Home → MainMenu idle.

6. **Edge & Fail-safe**
   - Awan kecil habis lifetime > pop hilang + puff kecil (tidak ada penalti teks).
   - Semua awan hilang TAPI tanaman masih hidup > spawner genangan paksa spawn 1 awan normal di tengah setelah 2 detik (anti softlock, bukan gameover).
   - Rotasi planet clamp speed max biar tidak mabuk, ada easing.

## 9.2 Struktur Scene & Objek (1 Scene: `Game`)

```
Game (scene)
├── Boot (GameManager, BalanceConfig SO, EventChannels SO)
├── World
│   ├── PlanetRoot (PlanetRotator.cs - rotate Z)
│   │   ├── SoilSlots x12 (SoilSlot.prefab - SoilStateMachine + SpriteRenderer + Puddle child)
│   │   ├── WaterSpots x2 (WaterSpot.cs - spawner + evaporasi zone)
│   │   └── PlantSpots x6 (Plant.prefab - PlantStateMachine, parent ke SoilSlot biar ikut)
│   └── Sky (CloudManager, PlantManager-AliveTracker, RainArea)
│       ├── Clouds (pooled)
│       └── RainFX (pooled particle)
├── Systems (InputManager, CloudFactory, PoolManager, AudioManager)
└── UI (Canvas - Screen Space Camera)
    ├── HUD (PauseBtn saja)
    ├── MainMenuPanel (PlayBtn, MuteBtn, FTUEBtn)
    ├── PausePanel (ResumeBtn, RestartBtn, HomeBtn)
    └── GameOverPanel (RestartBtn, HomeBtn + planet kering visual)
```

- Kamera: Orthographic, fixed, tidak ikut muter. Horizon = garis referensi Y.
- Semua tanah/air/tanaman = child PlanetRoot. Awan/hujan = child Sky (tidak muter).

## 9.3 Arsitektur Kode - Wajib Rapi (Design Pattern)

Prinsip: [[SOLID Principles (Unity)]] + [[Single Source of Truth (SSOT)]] + [[Layered Architecture Stabilization]] + [[Runtime State Separation]].

- **SSOT:** `BalanceConfigSO` (semua timer: cloudSmallLife, cloudNormalLife, mergeHoldTime, evaporateTick, rainRate, soilDryTime, plantGrow/Thirsty/Death/BloomTime, planetRotateSpeed, edgeZone). Tidak ada angka hardcode di script. Flyweight: `CloudVisualSO`, `SoilVisualSO`, `PlantVisualSO` dipakai bareng.
- **Centralized State Manager:** `GameManager : Singleton<T>` + `GameState enum (Boot, MainMenu, Playing, Paused, GameOver)` + event `OnStateChanged`. Semua sistem dengerin ini buat lock input / freeze timer. Lihat [[Centralized State Manager (GameManager Singleton & Event)]] + [[Simple FSM Berbasis Enum (Game State Prototyping)]].
- **State Pattern (inti game ini):** `IState + StateMachine` untuk 3 entitas, bukan switch raksasa. Lihat [[State Pattern (Unity FSM)]].
  - `Cloud: SmallWhite (timer pendek) -> NormalWhite (lama) -> DarkHeavy (auto-rain) -> SmallWhite`
  - `Soil: Dry <-> Wet (timer kering ulang)`
  - `Plant: Seed -> Growing (butuh Wet terus) -> Thirsty -> Dead / Blooming-visual (subur terus, bukan win)`
- **Observer (decoupled):** `EventChannelSO` untuk `OnCloudMerged, OnRainTick, OnSoilChanged, OnPlantDied, OnPlantBloomed-visual-only, OnGameOver`. UI & Audio cuma subscribe, tidak reference langsung. Tidak ada `OnFlowerCountChanged` karena HUD bunga dihapus. Lihat [[Observer Pattern Events]] + [[Decoupled Audio System (Event Channel & Pooling)]].
- **Factory + Object Pool:** `CloudFactory (IProduct + Initialize)` + `UnityEngine.Pool ObjectPool` buat Cloud, RainDrop, Puddle, Uap. Anti GC spike di mobile. Lihat [[Factory Pattern (Unity)]] + [[Object Pool Pattern (Unity)]].
- **MVP untuk UI (no teks):** `MainMenuView+Presenter, PauseView+Presenter, GameOverView+Presenter, HudView+Presenter (PauseBtn saja)`. View cuma ikon + animasi. Lihat [[MVP Pattern (Unity UI)]].
- **Dirty Flag:** `SoilSlot` cuma update warna/sprite pas `isDirty`. Lihat [[Dirty Flag Pattern (Unity)]].
- **Singleton hemat:** cuma GameManager, PoolManager, AudioManager, PlantManager (AliveTracker) via `Singleton<T>`. Lihat [[Singleton Pattern (Unity Generic)]].

**Struktur Folder Modular + .asmdef:**

```
Assets/_Project/
├── Core/ (GameManager, GameState, EventChannels, BalanceConfigSO, Singleton) - Core.asmdef
├── Features/Cloud/ (CloudController, CloudStateMachine, CloudFactory, CloudVisualSO) - Feature.Cloud.asmdef
├── Features/Soil/ (SoilSlot, SoilStateMachine) - Feature.Soil.asmdef
├── Features/Plant/ (PlantController, PlantStateMachine, PlantManager-AliveTracker) - Feature.Plant.asmdef
├── Features/Planet/ (PlanetRotator, EdgeScrollDetector, EvaporasiZone) - Feature.Planet.asmdef
├── Features/Input/ (InputManager - drag awan vs drag tanah, edge detection) - Feature.Input.asmdef
├── Features/FX/ (RainPool, PuddlePool, UapPool) - Feature.FX.asmdef
├── UI/ (HUD-PauseOnly, MainMenu, Pause, GameOver - MVP) - UI.asmdef
└── Audio/ (AudioManager, Event->SFX map, Adaptive layer hujan) - Audio.asmdef
```

## 9.4 Skill List dari obsidian-gamedev yang Dipakai

**Wajib (prototype):**
- [[Deconstruct Mechanics]] - bedah verb geser/merge/hujan
- [[Identify Core Loops]] - kunci loop evaporasi>merge>hujan>bertahan (endless, bukan kejar menang)
- [[Designing FTUE (First-Time User Experience)]] - FTUE 5 langkah tanpa teks
- [[Tutorial Level Building Blocks]] + [[Level Design Workflow (Whiteblocking & Modular)]] - layout planet 12 slot + 2 air + 6 tanaman
- [[Single Source of Truth (SSOT)]] - BalanceConfigSO
- [[Centralized State Manager (GameManager Singleton & Event)]] + [[Simple FSM Berbasis Enum (Game State Prototyping)]] - flow MainMenu/Playing/Paused/GameOver
- [[State Pattern (Unity FSM)]] - Cloud/Soil/Plant state machine + AliveTracker
- [[Observer Pattern Events]] - EventChannel decoupled (termasuk OnGameOver)
- [[Factory Pattern (Unity)]] + [[Object Pool Pattern (Unity)]] - spawn awan/hujan/uap
- [[SOLID Principles (Unity)]] + [[Runtime State Separation]] + [[Layered Architecture Stabilization]]
- [[MVP Pattern (Unity UI)]] - UI ikon-only (Pause, GameOver tanpa skor)
- [[Dirty Flag Pattern (Unity)]] + [[Flyweight Pattern (Unity Shared Data)]]
- [[Apply Balance Foundations]] + [[Map Resource Flows]] + [[Establish Math Anchors]] + [[Spreadsheet Setup]] - kunci timer survival (lihat 9.5)
- [[Decoupled Audio System (Event Channel & Pooling)]] + [[Adaptive Audio (Vertical Layering & Horizontal Resequencing)]] - SFX merge/hujan/mati, musik menipis pas GameOver
- [[Teori Musik Dasar untuk Game BGM]] - BGM chill loop

**Pendukung:**
- [[Prospect & Refuge Spatial Design]] - area aman vs lahan jauh
- [[Framework Kihon-Kata-Kumite (Learning Curve & Encounter Design)]] - Kihon merge, Kata hujan tepat, Kumite bertahan + rotasi multi-lahan

## 9.5 Balancing Awal (Math Anchors - endless survival)

`BalanceConfigSO` v0.1 (detik):
- cloudSmallLife=8, cloudNormalLife=40, mergeHold=0.4, magnetSnapRadius=0.6
- evaporateTick=1.0, autoRainRate=1.0
- soilWetDuration=25, rainToWet=2.0
- plantGrow=15, thirsty=10, death=12, bloomVisual=20 (hiasan, bukan win)
- planetRotateSpeed=60 deg/dtk max, edgeZone=15% layar
- spawner: start 2 Normal, max 5 awan hidup, emergency spawn 2 dtk jika 0 dan masih ada tanaman hidup
- lose: AliveCount==0 → GameOver. Tidak ada target menang.

Flow resource endless: `Air (tak terbatas tapi perlu muter) -> Awan (terbatas lifetime) -> Basah (sementara 25 dtk) -> Hidup (butuh basah rutin)` - tantangan = banyak slot kering bersamaan, bukan kejar skor.

## 9.6 Task Plan 2 Minggu (Solo) - update endless

**Minggu 1 - Core Fun:**
1. Setup Core.asmdef + GameManager enum (tambah GameOver) + EventChannel + BalanceConfigSO
2. PlanetRoot + 12 SoilSlot whitebox + rotasi drag tanah
3. Cloud drag + overlap merge + auto-hujan placeholder
4. Soil Dry/Wet timer + Dirty Flag visual
5. Plant Seed/Growing/Thirsty/Dead + PlantManager.AliveCount + GameOver trigger placeholder
6. Playtest: bisa merge-hujan-tumbuh dalam 2 menit?

**Minggu 2 - Flow + Juice:**
7. Factory + Pool ganti placeholder ke sprite awan lucu
8. Edge-scroll + indikator panah + clamp + easing
9. UI MVP: MainMenu + HUD Pause-only + Pause + GameOver (Restart/Home ikon, tanpa skor/bunga)
10. FTUE 5 langkah (PlayerPrefs flag)
11. Audio decoupled: pop merge, hujan loop, mati, menipis pas GameOver + BGM loop
12. Balancing survival + build APK + playtest 5 menit tanpa teks (Go/No-Go 9.7)

## 9.7 Go / No-Go Prototype (endless)
- Lolos jika: pemain baru tanpa teks bisa merge + hujan + muter + bertahan >3 menit sebelum GameOver, dan mau retry lagi.
- Gagal jika: edge-scroll kepencet tidak sengaja >3x, atau merge random, atau mati terlalu cepat (<1 menit) / terlalu lama tidak mati (>10 menit bosen) → tuning BalanceConfigSO dulu, jangan tambah fitur.

---
Next eksekusi: mulai dari Core + Planet whitebox + PlantManager AliveTracker dulu, baru Cloud merge.
