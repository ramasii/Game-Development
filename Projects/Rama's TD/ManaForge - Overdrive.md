# 📋 GDD: ManaForge — Overdrive

---

## 🎮 1. Konsep & Identitas Game

- **Premis**: Game ini adalah Roguelite Factory di mana pemain membangun pabrik otomatis dari blueprint acak untuk memproduksi senjata magis dan mempertahankan inti energi (*Core*) dari serbuan monster yang datang bergelombang.
- **Genre**: Roguelite + Automation / Factory Defense
- **Target Platform**: PC / Steam (Early Access)
- **USP**: Rasa candu *Factorio* dikemas dalam run 30–45 menit seperti *Vampire Survivors* — setiap run pabrikmu berbeda total karena blueprint mesin yang kamu dapat selalu acak. Bukan kamu yang menembak musuh, tapi pabrik yang kamu bangun.
- **Referensi & Inspirasi**:
  - *Factorio* — loop otomasi input→proses→output
  - *Vampire Survivors* — wave defense, run pendek, metaprogression
  - *Balatro* — sistem sinergi item yang menghasilkan broken build tak terduga
  - *Shapez 2* — visual minimalist low-poly untuk konveyor dan mesin

---

## 🔄 2. Core Gameplay Loop

- **Loop Utama**:
  ```
  Build Phase → Wave Phase → Reward Phase → (run berikutnya atau mati)

  Build Phase  : Susun conveyor belt, mesin, dan turret dari blueprint yang tersedia
  Wave Phase   : Monster menyerang — pabrik bekerja otomatis, turret menembak sendiri
  Reward Phase : Pilih 1 dari 3 blueprint/perk acak sebagai hadiah bertahan hidup
  Mati         : Core hancur → koin/komponen tersimpan → buka upgrade permanen di menu
  ```
- **Core Mechanic**: Membangun jalur logistik (conveyor belt) yang mengalirkan bahan mentah → mesin pemroses → turret otomatis. Jika jalur macet (*bottleneck*), turret kehabisan peluru dan Core bisa hancur.
- **Daya Tarik Jangka Pendek**: Momen ketika sinergi 2–3 blueprint menghasilkan combo tak terduga — misalnya pabrik yang sengaja "bocor" justru menghasilkan koin tak terbatas. Pemain ingin langsung coba lagi setelah menemukan hint kombo baru.
- **Daya Tarik Jangka Panjang**: Membuka faksi teknologi baru (Steampunk / Cyberpunk), blueprint langka, dan upgrade Core permanen lewat metaprogression — plus rasa penguasaan sejati saat pabrik yang dibangun berjalan sempurna tanpa bottleneck.

---

## ⚔️ 3. Mekanik Utama

- **Mekanik 1 — Grid Placement**: Arena berbentuk grid. Pemain menempatkan potongan conveyor belt (lurus, belok, splitter, merger) dan mesin di atas grid untuk membangun jalur logistik. Posisi tidak bisa diubah saat wave sedang berjalan.
- **Mekanik 2 — Blueprint Drafting**: Setiap akhir wave, pemain memilih 1 dari 3 blueprint/perk acak. Blueprint bisa berupa mesin baru, upgrade conveyor, atau perk pasif yang mengubah aturan sistem (misal: "besi yang melewati belokan 3x bermuatan listrik").
- **Mekanik 3 — Sinergi Item**: Perk dan mesin berinteraksi satu sama lain lewat sistem tag (`[listrik]`, `[panas]`, `[waste]`, dll). Kombinasi tag yang tepat menghasilkan efek berantai (*chain reaction*) yang jauh lebih kuat dari jumlah bagian-bagiannya.
- **Mekanik 4 — Bottleneck & Permadeath**: Jika aliran resource ke turret tersumbat, turret berhenti menembak. Monster yang mencapai Core akan merusaknya. Core hancur = run berakhir, tapi koin dan komponen langka tetap tersimpan.
- **Mekanik 5 — Ore Deposit & Miner**: Sumber daya mentah tidak langsung tersedia — pemain harus menemukan **Ore Deposit** yang sudah ada di map (pre-placed oleh level designer) dan menempatkan **Miner** di atasnya untuk mulai mengekstraksi resource secara otomatis.
  - **Ore Deposit**: Tile khusus pre-placed di map. Tidak bisa dihapus. Menandai lokasi bahan mentah (`iron`, `copper`, dst).
  - **Miner**: Ditempatkan player tepat di atas Ore Deposit. Spawn resource item secara berkala (interval ditentukan `MinerData` ScriptableObject) ke satu arah output. Player bisa rotate arah output sebelum/sesudah placement.
  - **Upgrade Path**: `MinerData` mendukung chain upgrade (Basic → Fast → Multi-Output). Miner level tinggi bisa punya lebih dari satu output direction (round-robin per spawn).
  - **Flow lengkap**: `Ore Deposit → Miner → Conveyor Belt → Mesin/Turret`
- **Mekanik 6 — Router (Distribusi Logistik)**: Tile 1×1 yang mendistribusikan resource dari satu atau banyak input ke banyak output secara otomatis. Terinspirasi dari Router di Mindustry.
  - **Auto-detect I/O**: Tidak perlu set arah manual — Router otomatis mendeteksi side mana yang jadi input (conveyor outputnya menuju Router) dan side mana yang jadi output.
  - **Round-robin**: Resource didistribusikan bergantian ke semua output yang valid (tidak blocked).
  - **Backpressure handling**: Kalau satu output penuh/blocked → skip ke output berikutnya. Kalau semua blocked → resource tunggu di Router.
  - **Anti-clog**: Router tidak boleh output ke sesama Router (mencegah loop tak terbatas).
  - **Use case utama**: Membagi resource dari satu Smelter ke beberapa Turret, atau menggabungkan dua jalur conveyor menjadi satu.

> Mekanik 7 (Underground Conveyor, multi-lantai, dsb.) ditambahkan setelah prototype mekanik 1–6 terbukti fun.

## 💻 4. Arsitektur Data & Design Pattern

- **Design Pattern Pilihan**:
  - **Node/Graph System** — setiap tile grid adalah node; conveyor belt adalah edge; resource mengalir dari node ke node. Ini fondasi utama sistem pabrik.
  - **Observer Pattern (Event Channel)** — mesin dan turret subscribe ke event resource-flow. Saat resource tiba, event dipicu otomatis tanpa polling tiap frame. Lihat [[Decoupled Audio System (Event Channel & Pooling)]] sebagai referensi implementasi pattern serupa.
  - **Object Pooling** — item/resource di conveyor di-pool agar tidak ada GC spike saat ratusan objek bergerak bersamaan. Lihat [[Decoupled Audio System (Event Channel & Pooling)]].
  - **ScriptableObject sebagai SSOT** — setiap blueprint/mesin/perk didefinisikan sebagai ScriptableObject. Satu asset = satu sumber kebenaran. `MinerData` adalah contoh pertama pattern ini. Lihat [[Single Source of Truth (SSOT)]].
  - **State Pattern** — GameState (BuildPhase / WavePhase / RewardPhase / GameOver) dikelola lewat FSM terpusat. Lihat [[Simple FSM Berbasis Enum (Game State Prototyping)]] dan [[Centralized State Manager (GameManager Singleton & Event)]].
  - **Factory Pattern** — spawning mesin dan resource menggunakan Factory agar tidak ada hardcode tipe objek di luar satu tempat.

- **Arsitektur & Penyimpanan Data**:
  - Blueprint dan Perk → `ScriptableObject` dengan sistem tag sinergi
  - `MinerData` → `ScriptableObject` per tier Miner (interval, output count, upgrade chain)
  - Resource flow data → runtime-only, tidak perlu disimpan ke disk
  - Metaprogression (koin, unlock permanen) → `JSON` lokal atau `PlayerPrefs` untuk MVP
  - Game state → Singleton `GameManager` dengan event broadcast

- **Mermaid Diagram**:
    ```mermaid
    graph TD
        GM[GameManager\nState Machine] -->|OnStateChanged| UI[UI Manager]
        GM -->|OnStateChanged| WM[Wave Manager]
        GM -->|OnStateChanged| BM[Build Manager]

        BM -->|PlaceTile| Grid[Grid System\nNode Graph]
        BM -->|PlaceMiner| Miner[Miner\nMinerData SO]
        BM -->|PlaceRouter| Router[Router\nAuto-detect I/O]

        OreDeposit[Ore Deposit\npre-placed di map] -->|DepositTag| Miner
        Miner -->|Get dari pool| Pool[Object Pool\nResource Items]
        Pool -->|ResourceItem bergerak| Conv[Conveyor Belt]
        Conv -->|masuk| Router
        Router -->|round-robin output| Conv
        Router -->|round-robin output| Machine[Machine Node\nScriptableObject]
        Conv -->|OnResourceArrived| Machine
        Machine -->|OnOutputReady| Turret[Turret Node]
        Turret -->|OnFire| ProjPool[Projectile Pool]

        WM -->|SpawnWave| EnemyPool[Enemy Object Pool]
        EnemyPool -->|OnEnemyReachCore| GM

        RM[Reward Manager] -->|DraftBlueprint| SO[Blueprint\nScriptableObject]
        SO -->|ApplyPerk| Grid
    ```

## 🏛️ 5. Desain FTUE

- **Pendekatan FTUE**: **Contextual UI Hint + Sandbox Room (Kihon)**
  - Sebelum run pertama, pemain masuk ke ruang tutorial terisolasi tanpa musuh dan tanpa batas waktu (*Kihon* — zero risk, zero pressure).
  - UI hint muncul hanya di atas elemen yang relevan saat giliran pemain berinteraksi dengannya (bukan wall of text di awal).
  - Urutan pengenalan mekanik mengikuti prinsip **Constructivist** (tiap mekanik baru menumpuk di atas yang sudah dikuasai):
    1. Tempatkan satu conveyor lurus → resource mengalir sendiri (*"oh, begini cara kerjanya"*)
    2. Tambahkan Smelter → resource berubah jadi output baru
    3. Hubungkan ke Turret → turret mulai menembak otomatis
    4. Wave kecil datang → pemain merasakan loop penuh untuk pertama kali
  - Reward tutorial: 1 blueprint gratis pilihan pemain → langsung masuk run pertama yang sesungguhnya.
  - Lihat [[Tutorial Level Building Blocks]] dan [[Framework Kihon-Kata-Kumite (Learning Curve & Encounter Design)]].

---

## 🚀 6. Struktur Folder Modular & Optimisasi Performa

- **Struktur Folder (Feature-Based)**:
  ```
  Assets/
  ├── _Project/
  │   ├── Scripts/
  │   │   ├── Core/            ← GameManager, GameState, EventChannels
  │   │   ├── Grid/            ← GridSystem, TileNode, ConveyorBelt
  │   │   ├── Machines/        ← BaseMachine, Smelter, Turret, Splitter
  │   │   ├── Resources/       ← ResourceItem, ObjectPool
  │   │   ├── Waves/           ← WaveManager, EnemySpawner, EnemyAI
  │   │   ├── Blueprints/      ← BlueprintDraft, PerkSystem, SynergyTags
  │   │   ├── Metaprogression/ ← UnlockManager, SaveSystem
  │   │   └── UI/              ← HUD, BuildMenu, RewardPanel
  │   ├── ScriptableObjects/
  │   │   ├── Blueprints/
  │   │   ├── Machines/
  │   │   └── Events/
  │   ├── Prefabs/
  │   ├── Art/
  │   │   ├── Machines/        ← Low-poly geometric models
  │   │   ├── Environment/
  │   │   └── VFX/
  │   └── Audio/
  ```

- **Rencana Optimisasi**:
  - *CPU/Memori*: **Object Pooling** wajib untuk semua resource item di conveyor (bisa ratusan objek bergerak serentak). Pertimbangkan **Unity DOTS/ECS** jika jumlah tile mencapai 1000+.
  - *Grid Update*: Jangan update semua tile setiap frame — gunakan **event-driven dirty flag**: tile hanya update saat ada perubahan input/output.
  - *Rendering*: Semua mesin menggunakan **GPU Instancing** karena geometry sama, hanya posisi berbeda. Hemat draw call drastis.
  - *UI*: **Canvas Splitting** — HUD statis (wave counter, Core HP) dipisah dari UI dinamis (build menu, perk draft) agar tidak trigger rebuild canvas setiap frame.

---

## 📏 7. Scope & Feasibility

- **Estimasi Durasi**:
  - Prototype core mechanic (grid placement + conveyor flow): **2 minggu**
  - MVP playable (3 jenis mesin, 10 blueprint, 5 wave, metaprogression dasar): **2 bulan**
  - Early Access (20+ blueprint, 2 faksi, 15 wave, Steam page): **4–6 bulan**

- **Ukuran Tim**: Ideal **2–3 orang** (1 programmer, 1 artist/generalist, 1 game designer). Bisa dimulai solo untuk fase prototype.

- **Risiko Teknis**:
  - **Grid + Node flow system** adalah komponen paling kompleks — harus dirancang dengan benar di awal karena semua sistem lain bergantung padanya.
  - **Performa saat pabrik besar** — ratusan resource item bergerak bisa jadi bottleneck; Object Pooling + ECS harus disiapkan sejak MVP.
  - **Balance sinergi** — terlalu banyak perk yang berinteraksi bisa menciptakan combo yang completely break game; butuh spreadsheet sinergi dan playtesting intensif.

- **Risiko Desain**:
  - Core loop belum divalidasi — apakah "tidak bisa menembak sendiri, hanya bisa membangun" terasa menyenangkan bagi pemain yang terbiasa action roguelite? Wajib diuji di playtest awal.
  - Build Phase vs Wave Phase timing perlu dibalance — terlalu banyak waktu build = boring, terlalu sedikit = stressful tanpa arah.

- **Kriteria "Go/No-Go"**:
  - ✅ Prototype conveyor grid terasa *satisfying* dibangun (mekanisnya "klik") dalam 2 minggu pertama.
  - ✅ Satu sinergi 2-perk menghasilkan momen "WOW/broken build" saat playtesting internal.
  - ✅ Rata-rata run 30–45 menit — tidak terlalu pendek (tidak berasa), tidak terlalu panjang (tidak bisa restart cepat).
  - ❌ Jika setelah 2 minggu mekanik grid belum fun → pivot ke konsep lain (Minesweeper Dungeon atau Chess Physics).

---

## 🎨 8. Visual Design & Art Direction

### Art Style
- **Referensi**: Shapez 2 — minimalist low-poly geometric
- **Prinsip**: *Readable dulu, pretty belakangan.* Pemain harus bisa baca alur resource dari conveyor → mesin → turret dalam sekejap. Clarity > Aesthetics.

### Color Language (Wajib Konsisten)

| Elemen              | Warna                       |
| ------------------- | --------------------------- |
| Conveyor Belt       | Abu-abu gelap `#2C2C2C`     |
| Ore Deposit         | Kuning `#F1C40F`            |
| Miner               | Coklat tua / besi `#6B4F3A` |
| Router              | Teal `#17A589`              |
| Resource: Iron      | Biru `#4A90D9`              |
| Resource: Iron Bar  | Oranye `#E8862A`            |
| Smelter             | Merah bata `#C0392B`        |
| Turret              | Hijau gelap `#27AE60`       |
| Enemy               | Ungu `#8E44AD`              |
| Core (Arcane Forge) | Cyan emissive + batu gelap  |

### The Core — "The Arcane Forge"
Core bukan crystal atau orb, melainkan **pabrik induk** tempat semua operasi berpusat. Secara tematik, pemain literally mempertahankan *The ManaForge* itu sendiri.

**Bentuk Dasar (Low-Poly, Blender):**
- Badan utama: trapezoid/kotak besar, sedikit lebih lebar di bawah
- 2–3 cerobong di atas dengan ukuran bervariasi
- Pintu forge di depan — glowing emissive cyan/oranye
- Detail rune geometris di dinding (bevel edge + emissive material, tanpa texture)
- Ukuran di grid: **2×2 tiles**

**Color Palette Core:**
| Bagian | Warna |
|---|---|
| Badan bangunan | Batu gelap `#1A1A2E` / abu tua |
| Cerobong | Besi tua `#4A4A5A` |
| Glow pintu | Cyan + putih emissive ("mana") |
| Asap cerobong | Particle — ungu ke putih |
| Rune | Emissive cyan tipis |

**Visual Feedback HP (3 State):**
- **HP Tinggi** → cerobong ngebul aktif, glow pintu terang
- **HP Sedang** → asap melambat, glow meredup, warna bergeser ke oranye
- **HP Kritis** → asap berhenti, glow merah berkedip (DOTween pulse)

### Art Asset Pipeline per Phase
| Phase | Target | Approach |
|---|---|---|
| Phase 1 (Prototype) | Placeholder 100% | ProBuilder primitives + colored Unlit materials. Tidak perlu Blender. |
| Phase 2 (MVP) | Basic art | Low-poly Blender models, UI mockup di Figma dulu, basic Particle VFX |
| Phase 3 (Early Access) | Polish | Full art pass 2 faksi, animated conveyor, UI DOTween, audio FL Studio |

### Tool Stack Art
| Kebutuhan | Tool |
|---|---|
| 3D Modeling | Blender (gratis) |
| In-engine geometry | ProBuilder (sudah ada) |
| UI Mockup | Figma (gratis) |
| VFX | Unity Particle System + Shader Graph (URP) |
| Audio | FL Studio (sudah ada) |

### Tips Solo Dev — Art
1. Beli asset pack untuk elemen non-core (environment tiles, enemy model) — fokus energi di mesin dan conveyor sebagai signature visual game.
2. Audio setelah prototype lulus Go/No-Go — sound effect + musik drastis meningkatkan game feel, dan FL Studio sudah tersedia.
3. Jangan perfectionist di Phase 1 & 2 — placeholder art cukup selama core loop belum validated.
