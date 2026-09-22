# Prompts - Eksekusi Awan Tukang Hujan

> File ini terpisah dari GDD & Planning. GDD: [[GDD - Awan Tukang Hujan]]. Planning: [[Planning - Awan Tukang Hujan]]. Hub: [[Awan Tukang Hujan]].
> Cara pakai: copy 1 prompt per sesi ke AI executor (opencode). Jangan loncat urutan. Setiap prompt sudah ngunci anti-melenceng + wajib MCP Unity.

## ATURAN GLOBAL (tempel di tiap eksekusi)

```
 second brain: Obsidian MCP obsidian-gamedev. SELALU baca ulang via MCP sebelum coding:
 - Projects/Awan Tukang Hujan/GDD - Awan Tukang Hujan.md
 - Projects/Awan Tukang Hujan/Planning - Awan Tukang Hujan.md
 Unity: SELALU pakai MCP Unity (unity_*). JANGAN tebak isi scene/file.
 - Mulai dengan unity_list_instances > unity_select_instance > unity_editor_state > unity_scene_hierarchy.
 - Cek script via unity_script_read, edit via unity_script_create/update, verifikasi via unity_get_compilation_errors + unity_console_log + unity_play_mode.
 Batasan keras GDD: Mobile Landscape 2D, 1 jari drag saja, no angka/huruf di gameplay & UI (full ikon), no win, no HUD bunga, lose tunggal AliveCount==0, Opsi A 12 SoilSlot, max 5 awan, Unity 6 + URP 2D.
 Arsitektur wajib: SSOT BalanceConfigSO, GameManager Singleton + GameState enum (Boot,MainMenu,Playing,Paused,GameOver), State Pattern untuk Cloud/Soil/Plant, Observer EventChannelSO, Factory + Object Pool, MVP UI, SOLID, Runtime State Separation, Dirty Flag, Flyweight.
 Larangan: JANGAN tambah mekanik/sistem di luar GDD. JANGAN hardcode timer di script. JANGAN buat scene baru selain Game. Jika ragu, baca vault dulu.
```

---

## P0 - Phase 0 Setup Core + Folder Modular

```
Konteks: baca ATURAN GLOBAL + GDD bab 4+6 + Planning bab 2+3 via Obsidian MCP.

Tugas: setup fondasi di Unity (MCP) tanpa gameplay dulu.

1. Via Unity MCP: cek instance, editor state, scene aktif. Buat struktur folder persis Planning:
Assets/_Project/Core/, Features/Cloud/, Features/Soil/, Features/Plant/, Features/Planet/, Features/Input/, Features/FX/, UI/, Audio/ + .asmdef per folder.
2. Buat: Singleton<T>.cs generic, GameState.cs enum (Boot,MainMenu,Playing,Paused,GameOver), GameManager.cs (Singleton + SetState + OnStateChanged event), 6 EventChannelSO (OnCloudMerged, OnRainTick, OnSoilChanged, OnPlantDied, OnPlantBloomedVisual, OnGameOver) sebagai ScriptableObject di Core/Channels/.
3. Buat BalanceConfigSO.cs + asset BalanceConfig.asset dengan anchors v0.1 Planning bab 4 (cloudSmallLife 8, cloudNormalLife 40, mergeHold 0.4, snap 0.6, evaporateTick 1.0, rainToWet 2.0, soilWet 25, grow 15, thirsty 10, death 12, bloomVisual 20, rotate 60 deg/dtk, edge 15%, start 2 Normal max 5).
4. Buat 1 scene Game (jika belum ada). Setup hierarki kosong: Boot, World/PlanetRoot, World/Sky, Systems, UI Canvas. Kamera Ortho fixed landscape.
5. Verifikasi: unity_get_compilation_errors = 0 error, Play mode Boot->MainMenu jalan tanpa null.

DoD: folder + asmdef lengkap, GameManager bisa SetState via Inspector button/context, BalanceConfig terbaca semua sistem (tidak ada angka hardcode), kompilasi bersih.
JANGAN lanjut ke planet/awan. Hanya fondasi.
```

## P1 - PlanetRoot + 20 SoilSlot Whitebox + Rotasi Drag

```
Konteks: baca ATURAN GLOBAL + GDD bab 3 Mekanik 4+5 + Planning bab 2 via MCP.

Tugas: dunia melingkar bisa diputar (Unity MCP only).

1. Cek scene Game via unity_scene_hierarchy. Buat PlanetRotator.cs di Features/Planet/: rotate PlanetRoot di sumbu Z, maxSpeed dari BalanceConfigSO, easing + clamp, method Rotate(deltaX) + AutoRotate lambat untuk MainMenu.
2. Buat SoilSlot.prefab whitebox (SpriteRenderer kotak, beda warna Dry pucat vs Wet gelap) + SoilStateMachine.cs (State Pattern: Dry <-> Wet, timer soilWetDuration, rainToWet 2 dtk untuk jadi Wet, Dirty Flag untuk visual). Spawn 20 slot melingkar sebagai child PlanetRoot (radius konsisten, terlihat menyatu).
3. Buat WaterSpot.cs 2 biji (child PlanetRoot, zona evaporasi + visual biru) + EvaporasiZone detection (stay 1 dtk = tick).
4. InputManager.cs tahap 1: drag tanah kosong horizontal = PlanetRotator.Rotate. Bedakan via raycast layer Tanah vs Awan (awan belum ada, siapkan saja). Support mouse (editor) + touch 1 jari.
5. Verifikasi: Play mode, drag tanah muter 360° balik ke awal, kamera diam, slot ikut muter. Cek via unity_graphics_scene_capture jika perlu.

DoD: 20 slot + 2 air muter mulus, tidak mabuk (clamp+easing), soil bisa Wet/Dry via debug hujan dummy, tidak ada awan dulu.
```

## P2 - Cloud Drag + Merge Overlap + Evaporasi + Hujan Placeholder

```
Konteks: baca ATURAN GLOBAL + GDD bab 3 Mekanik 1-3 + Planning bab 3 Feature.Cloud via MCP.

Tugas: awan inti fun (placeholder kotak dulu, belum art).

1. Buat CloudVisualSO (Flyweight: scale/color Small putih kecil, Normal putih sedang, Dark gelap besar) + CloudStateMachine (State Pattern Small 8 dtk -> Normal 40 dtk -> Dark auto-rain -> Small). Timer dari BalanceConfigSO.
2. Buat CloudController.cs (drag bebas di Sky, clamp Y di atas horizon, tidak ikut PlanetRoot) + CloudManager (max 5, start 2 Normal).
3. Merge: overlap check tiap frame antar awan (distance < snapRadius 0.6 + hold 0.4 dtk) + magnet snap → merge pop (scale punch) → tier naik → fire OnCloudMerged. Small+Small=Normal, Normal+Normal=Dark, dst.
4. Evaporasi: stay di atas WaterSpot 1 dtk → growth tick + uap placeholder (lingkaran naik) → Small bisa jadi Normal.
5. Hujan placeholder: Dark auto-rain (box biru jatuh via Debug particle/line) → kirim OnRainTick ke SoilSlot di bawahnya (worldX → slot index terdekat) → soil jadi Wet. Hujan sampai menyusut jadi Small.
6. InputManager tahap 2: drag awan prioritas atas drag tanah (raycast awan dulu). Single touch saja.

DoD: bisa geser 2 awan → merge → gelap → hujan → tanah basah dalam 2 menit playtest. Lifetime Small hilang dengan puff. Tidak ada edge-scroll dulu. Kompilasi 0 error.
```

## P3 - Soil Final + Plant States + AliveTracker + GameOver Trigger

```
Konteks: baca ATURAN GLOBAL + GDD bab 3 Mekanik 5 + lose condition + Planning bab 3 Feature.Plant via MCP.

Tugas: ekosistem hidup/mati + lose tunggal.

1. Finalisasi SoilSlot: Dry/Wet penuh + Puddle child (kotak biru muda fade in/out, Dirty Flag). rainToWet 2 dtk, wet tahan 25 dtk.
2. Buat Plant.prefab (whitebox tunas: scale + warna) + PlantStateMachine (Seed -> Growing jika Wet terus 15 dtk -> Thirsty jika kering 10 dtk -> Dead jika kering lanjut 12 dtk / BloomVisual jika subur 20 dtk, bunga = visual only bukan win). Parent ke SoilSlot (6 tanaman di slot genap, ikut muter).
3. Buat PlantManager.cs (Singleton AliveTracker): subscribe OnPlantDied/Bloom, hitung AliveCount, jika 0 → fire OnGameOver → GameManager.SetState(GameOver). Tidak ada HUD bunga, tidak ada skor.
4. Anti-softlock: jika awan 0 tapi Alive>0 → force spawn 1 Normal 2 dtk dari genangan terdekat.
5. Verifikasi Unity MCP: bunuh semua tanaman via test (keringkan) → GameOver kepicu. Siram rutin → bertahan.

DoD: loop merge-hujan-tumbuh-mati jalan, GameOver hanya saat tanaman habis, bukan saat awan habis. Event decoupled, tidak ada reference langsung Plant→UI.
```

## P4 - Factory + Object Pool + Ganti Placeholder ke Sprite Lucu

```
Konteks: baca ATURAN GLOBAL + skill Object Pool + Factory + Flyweight via vault_read sebelum coding.

Tugas: rapikan spawn + juice tanpa ubah gameplay.

1. Buat CloudFactory.cs (IProduct + Initialize dari VisualSO) + PoolManager (UnityEngine.Pool ObjectPool<Cloud>, RainDrop, Puddle, Uap). Ganti semua Instantiate/Destroy ke Pool.Get/Release.
2. Ganti whitebox: awan jadi sprite blob lucu (mata 2 titik, no teks), rain = particle streak biru, uap = puff putih, puddle = blob pipih. Warna via VisualSO (Flyweight). Tetap Unlit 2D, sorting Sky > Planet.
3. Tambah juice minimal: merge pop scale, hujan splash kecil, tumbuh punch, mati gosong (desaturate). Tidak ada screen shake dulu.
4. Verifikasi: profile Play 2 menit, tidak ada GC spike tiap merge/hujan (cek console). Pool max sesuai BalanceConfig.

DoD: gameplay sama persis P2+P3 tapi visual awan/hujan/tanah enak, tidak ada Instantiate di Update. Larangan: JANGAN tambah partikel berat / shader custom.
```

## P5 - Edge-Scroll + Flow UI MainMenu/HUD-Pause/GameOver (MVP Ikon)

```
Konteks: baca ATURAN GLOBAL + GDD bab 5 FTUE (langkah 4) + Planning bab 1 flow + skill MVP via MCP.

Tugas: navigasi planet + state flow lengkap (ikon only).

1. EdgeScrollDetector: jika awan di-drag ke 15% edge kanan/kiri → PlanetRotator auto-rotate proporsional + tampilkan panah edge (sprite segitiga fade). Deadzone jelas biar tidak kepencet tidak sengaja. Drag tanah tetap manual.
2. UI MVP (Canvas 1, 4 panel, full ikon tanpa teks/angka):
 - HUD: PauseBtn kiri-atas saja.
 - MainMenuPanel: PlayBtn (ikon awan besar), MuteBtn, FTUEBtn (ikon tangan). Background planet idle auto-rotate + 2 awan dummy.
 - PausePanel: Resume (play), Restart (putar), Home (planet). timeScale 0, blur.
 - GameOverPanel: Restart + Home + ikon planet kering. Trigger OnGameOver.
 Buat View (ikon+animasi) + Presenter (subscribe GameManager/EventChannel). Tidak ada FlowerProgress, tidak ada skor.
3. Wire GameManager: Boot→MainMenu→Playing↔Paused→MainMenu, Playing→GameOver→Playing/MainMenu. Handle Back HP + OnApplicationPause → Paused (kecuali GameOver).
4. Restart reset Run saja (slot kering ulang, tanaman hidup ulang, 2 Normal). Home lerp rotasi ke 0.

DoD: flow mermaid Planning jalan penuh via klik ikon, edge-drag muter + mindahin awan 1 gesture, tidak ada teks/angka di UI. Test: Main→Pause→Resume→Home→Main→Play→matikan semua tanaman→GameOver→Restart.
```

## P6 - FTUE 5 Langkah + Audio Decoupled + BGM

```
Konteks: baca ATURAN GLOBAL + GDD bab 5 + skill FTUE + Decoupled Audio + Adaptive Audio via vault_read.

Tugas: tutorial tanpa teks + suara tanpa coupling.

1. FTUEManager (PlayerPrefs flag ftue_done): 5 step highlight + panah jari (sprite tangan) sesuai GDD: 1) geser ke air → uap, 2) overlap merge, 3) hujan ke kering → tunas, 4) edge-drag muter, 5) diamkan → memucat (paham rutin). Tiap step cek event (OnCloudMerged/RainTick/SoilChanged) baru lanjut. Bisa di-skip via FTUEBtn, bisa diulang dari MainMenu.
2. AudioManager (Singleton + Event subscribe, tidak dipanggil langsung): SFX pool (pop merge, hujan loop, uap, tumbuh, layu/mati, klik UI, gameover thin). BGM chill 2-bar loop (buat di FL Studio, import sebagai loop) + adaptive layer: hujan layer on saat Dark raining, mekar chime saat BloomVisual, musik menipis saat GameOver.
3. MuteBtn fungsional (ikon speaker coret). Semua audio via Pool, tidak ada AudioSource hardcode di gameplay script.

DoD: pemain baru tanpa teks paham merge-hujan-muter dalam 5 menit. Mute + FTUE flag persist. Tidak ada narasi teks/suara berhuruf.
```

## P7 - Balancing Survival + Build APK + Validasi Go/No-Go

```
Konteks: baca ATURAN GLOBAL + Planning bab 4+6 + skill Balance (Anchors, Resource Flows, Spreadsheet) via MCP. Ini prompt terakhir, JANGAN tambah fitur.

Tugas: kunci fun endless + buktikan lolos.

1. Buat spreadsheet balancing (atau tabel di Planning): uji 3 set (gampang v0.1, sedang, susah) untuk soilWet 25, death 12, cloudLife 8/40. Target: bertahan 3-8 menit ideal. Mati <1 menit = terlalu susah, >10 menit bosen = terlalu gampang. Tuning hanya via BalanceConfigSO asset, JANGAN ubah kode.
2. Via Unity MCP: switch platform Android, cek Player Settings landscape, package name, icon awan. Build APK via unity_build target Android. Catat error jika ada, fix via unity_get_compilation_errors.
3. Playtest buta 5 menit (atau minta user test): checklist Go/No-Go:
 - Bisa merge + hujan + muter tanpa teks? ya/tidak
 - Bertahan >3 menit + mau retry? ya/tidak
 - Edge salah trigger >3x? harus tidak
 - Merge terasa random? harus tidak
4. Jika gagal: tuning SO dulu, tulis hasil di Planning bab 6 sebagai v0.2. Jika lolos: tandai Phase 1 DONE di Awan Tukang Hujan.md dan ajukan MVP.

DoD: APK terinstall jalan 60fps HP mid, tidak crash MainMenu→GameOver loop 3x, laporan Go/No-Go tertulis. STOP, jangan tambah konten baru.
```
