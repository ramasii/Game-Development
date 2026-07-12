## 🗺️ ManaForge (Rama's TD) — Overdrive: Solo Dev Roadmap

### ⚠️ Prinsip Utama

GDD lo sendiri udah kasih **Go/No-Go Gate** di minggu ke-2. Kalau grid + conveyor belum terasa fun → pivot. Jadi Phase 1 adalah yang paling kritis.

---

### 🔧 Phase 0 — Foundation Setup `(2–3 hari)`
	Started (8 July 2026)
	Finished (8 July 2026)

Sebelum nulis satu baris gameplay, setup dulu biar gak nyesel belakangan.

**Checklist:**

- Buat struktur folder modular persis sesuai GDD (feature-based)
- Setup `EventChannel` ScriptableObjects (pakai pattern yang lo udah tau dari vault)
- Buat `GameManager` + `GameState` FSM (BuildPhase / WavePhase / RewardPhase / GameOver)
- Setup scene baru: `GameScene`, `TutorialScene`, `MainMenuScene`
- Commit pertama ke Git

> 💡 Project lo skrg cuma ada `SampleScene`, jadi ini tempat yang tepat untuk mulai rapi.

---

### 🧪 Phase 1 — Prototype `(2 Minggu)` — 🚦 GO/NO-GO GATE

Tujuan: **validasi apakah core loop terasa fun**, bukan bikin yang bagus dulu.

**⚙️ Minggu 1 — Grid & Flow:**

- ✅ Grid System (Node/Graph) — tile placement di grid
- ✅ Conveyor Belt: lurus + belok, resource mengalir dari node ke node
- ✅ 1 Resource type (Raw Iron)
- ✅ Ore Deposit — tile pre-placed di map, tandai lokasi bahan mentah
- ✅ Miner — ditempatkan di atas Ore Deposit, spawn resource ke conveyor secara berkala
- ✅ Object Pooling untuk resource items
- ✅ 1 Mesin: Smelter (input Iron → output Iron Bar)
- ✅ Visualisasi sederhana (ProBuilder primitives)

- BUG FIXING!
	1. ✅ seberapa banyak smelter bisa menyimpan bahan mentah (iron) ketika banyak pasokan sedangkan outputnya setiap sekian detik?
	2. ✅ resource bisa bisa mengantre seperti game factorio. seharusnya resource tidak destroy saat berada di ujung conveyor, melainkan menumpuk seperti factorio. ketika penyimpanan bahan mentah dari machine (smelter) penuh, maka resource juga bisa mengantre.
	3. ✅ sekarang resource mengantre dengan cara satu resource berhenti di atas satu conveyor. seharusnya resource antrenya berdempet seperti factorio dan game factory lain.
	4. ✅ ketika resource ke-2 mengantre membuat unitynya ngelag parah.
	5. ✅ posisi resource ke-3 yang mengantre sama dengan posisi resource ke-2, hasilnya yang keliatan antre cuma ada 2 resource.
	6. resource stuck ketika di persimpangan.
	7. miner tidak ngespawn resource padahal masih ada ruang di conveyor, cek juga untuk machine (smelter). mungkin conveyor di sebelahnya masih terisi resource dan resourcenya sedikit agak maju untuk antre sehingga menghasilkan ruang yang cukup luas.

**⚙️ Minggu 2 — Loop Pertama:**

- 1 Turret yang consume output Smelter → shoot projectile
- Basic enemy: jalan lurus menuju Core, Core punya HP
- 1 Wave kecil (5–10 musuh)
- Bottleneck mechanic: turret berhenti kalau resource habis
- Object Pooling untuk projectiles

**🎨 Art Track — Phase 1 (Placeholder Only, jangan lebih dari ini!):**

- Semua objek pakai **ProBuilder primitives** (cube, cylinder) — tidak perlu Blender
- Tiap tipe objek beda warna Unlit material sesuai Color Language di GDD:
	- Conveyor → `#2C2C2C` | Iron → `#4A90D9` | Smelter → `#C0392B` | Turret → `#27AE60` | Enemy → `#8E44AD`
	- Ore Deposit → `#F1C40F` (kuning) | Miner → `#6B4F3A` (coklat tua)
- Core (Arcane Forge) → cube 2×2 dengan emissive cyan, placeholder saja
- Grid → flat plane + LineRenderer
- **Aturan keras: tidak ada sesi Blender selama Phase 1**

**✅ Kriteria Lulus Phase 1:**

- Conveyor terasa _satisfying_ disambung-sambungin
- Miner → Conveyor → Turret flow berjalan tanpa bug
- Ada momen "oh shit bottleneck!" yang bikin panik
- Kalau belum fun → pivot ke Minesweeper Dungeon / Chess Physics sesuai GDD

### 🎮 Phase 2 — MVP `(~2 Bulan setelah Phase 1)`

Target: **satu run penuh yang bisa diplaytest orang lain.**

**⚙️ Core Systems:**

- Blueprint Drafting: pilih 1 dari 3 tiap akhir wave
- Synergy Tag System (`[listrik]`, `[panas]`, `[waste]`)
- 3 jenis mesin (Smelter, Splitter, 1 mesin unik)
- 10 blueprint / perk
- 5 wave dengan scaling difficulty
- Reward Phase UI
- Permadeath + simpan koin (PlayerPrefs/JSON)
- Metaprogression dasar: unlock upgrade permanen

**⚙️ FTUE Tutorial:**

- Sandbox room tanpa musuh (Kihon approach sesuai GDD)
- Contextual UI hint — muncul pas diperlukan aja
- Urutan mekanik (Constructivist — tiap mekanik numpuk di atas yang sudah dikuasai):
	1. Ore Deposit sudah ada di map → tempatkan Miner di atasnya
	2. Sambungkan Conveyor dari output Miner
	3. Tambahkan Smelter di ujung conveyor → resource berubah jadi output baru
	4. Hubungkan ke Turret → turret mulai menembak otomatis
	5. Wave kecil datang → rasakan loop penuh pertama kali

**🎨 Art Track — Phase 2 (Basic Art, paralel sama coding):**

Urutan prioritas pengerjaan aset:

1. **Color System & Materials** *(Minggu 1 MVP)* — define semua material sesuai Color Language GDD sebelum bikin model apapun
2. **Low-Poly Machine Models (Blender)**:
	- Conveyor tile lurus + belok — paling sering keliatan, bikin duluan
	- Miner — bangunan industrial kecil di atas deposit
	- Smelter — kotak + cerobong kecil
	- Turret — base + barrel yang bisa rotate
	- Splitter — bentuk Y/T
	- Target: **masing-masing < 500 poly**
3. **Ore Deposit** — geometric crystal/rock cluster, warna kuning `#F1C40F`, tampil di bawah Miner
4. **The Arcane Forge (Core)** — trapezoid + 2–3 cerobong + pintu emissive cyan. Lihat spesifikasi lengkap di GDD Section 8
5. **Resource Items** — geometric shape kecil, beda warna per tipe, tidak perlu detail
6. **Enemy (1 tipe)** — stylized creature sederhana, silhouette harus jelas beda dari mesin
7. **UI** — mockup di Figma dulu, baru implement di Unity. Screen yang dibutuhkan: HUD, Build Menu, Reward Panel
8. **VFX Basic**:
	- Partikel mining saat Miner aktif (debu/spark kecil)
	- Muzzle flash turret (Particle System)
	- Resource "pop" saat sampai di mesin
	- Hit effect enemy
	- Screen shake saat Core kena hit (DOTween)

**Solo Dev Tips — Phase 2:**

- Playtesting diri sendiri tiap minggu, catat session
- Prioritas: **fun dulu, polish belakangan**
- Batasi diri: jangan tambah mesin baru sebelum 10 blueprint selesai
- Pertimbangkan beli asset pack untuk environment tiles & enemy model — hemat waktu untuk fokus ke mesin & conveyor sebagai signature visual

### 🚀 Phase 3 — Early Access `(4–6 Bulan total)`

Baru masuk sini kalau MVP udah diplaytest dan core loop validated.

**⚙️ Programming:**

- 20+ blueprint, 2 faksi teknologi (Steampunk / Cyberpunk)
- 15 wave
- Polish VFX (GPU Instancing sudah disiapkan sejak MVP)
- Canvas splitting untuk UI performance
- Steam Integration (Steamworks SDK)

**🎨 Art Track — Phase 3 (Polish):**

- Full art pass dua faksi:
	- **Steampunk** — gear + tembaga + uap, warna coklat/emas/oranye
	- **Cyberpunk** — neon + chrome + hologram, warna biru/ungu/putih
- Conveyor belt animasi (UV scroll material di Shader Graph)
- UI animations & transitions via DOTween
- VFX polish: particle lebih detail, emissive glow, screen effects
- **Audio (FL Studio)** — ini prioritas utama Phase 3:
	- SFX: conveyor hum, mining drill, mesin kerja, turret tembak, enemy mati, Core kena hit
	- BGM: loop ambient industrial-magic per phase (Build = calm, Wave = intense)
- Steam page screenshots & trailer

**📅 Quick Timeline Overview (Updated):**

| Phase | Durasi | Output Coding | Output Art |
|---|---|---|---|
| 0 - Setup | 2–3 hari | Project structure, GameManager, EventChannels | — |
| 1 - Prototype | 2 minggu | Grid, Conveyor, OreDeposit, Miner, ObjectPool, Smelter, Turret, Wave | Placeholder ProBuilder + colored materials (termasuk Miner & Deposit) |
| 2 - MVP | ~2 bulan | Full run playable, Blueprint Draft, 10 perk, 5 wave | Low-poly models (Miner, Mesin, Deposit), UI Figma → Unity, basic VFX |
| 3 - Early Access | 4–6 bulan total | 2 faksi, 20+ blueprint, 15 wave, Steam | Full art pass, audio FL Studio, trailer |

### 📅 Quick Timeline Overview

*Sudah dipindahkan ke bawah section Phase 3.*
