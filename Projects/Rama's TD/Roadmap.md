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

**Minggu 1 — Grid & Flow:**

- Grid System (Node/Graph) — tile placement di grid
- Conveyor Belt: lurus + belok, resource mengalir dari node ke node
- 1 Resource type (misal: Raw Iron)
- 1 Mesin: Smelter (input Iron → output Iron Bar)
- Visualisasi sederhana (bisa pakai ProBuilder yang udah ada di project lo)

**Minggu 2 — Loop Pertama:**

- 1 Turret yang consume output Smelter → shoot projectile
- Basic enemy: jalan lurus menuju Core, Core punya HP
- 1 Wave kecil (5–10 musuh)
- Bottleneck mechanic: turret berhenti kalau resource habis
- Object Pooling untuk resource items & projectiles

**✅ Kriteria Lulus Phase 1:**

- Conveyor terasa _satisfying_ disambung-sambungin
- Ada momen "oh shit bottleneck!" yang bikin panik
- Kalau belum fun → pivot ke Minesweeper Dungeon / Chess Physics sesuai GDD

---

### 🎮 Phase 2 — MVP `(~2 Bulan setelah Phase 1)`

Target: **satu run penuh yang bisa diplaytest orang lain.**

**Core Systems:**

- Blueprint Drafting: pilih 1 dari 3 tiap akhir wave
- Synergy Tag System (`[listrik]`, `[panas]`, `[waste]`)
- 3 jenis mesin (Smelter, Splitter, 1 mesin unik)
- 10 blueprint / perk
- 5 wave dengan scaling difficulty
- Reward Phase UI
- Permadeath + simpan koin (PlayerPrefs/JSON)
- Metaprogression dasar: unlock upgrade permanen

**FTUE Tutorial:**

- Sandbox room tanpa musuh (Kihon approach sesuai GDD)
- Contextual UI hint — muncul pas diperlukan aja
- Urutan: conveyor → smelter → turret → wave kecil

**Solo Dev Tips untuk Phase ini:**

- Playtesting diri sendiri tiap minggu, catat session
- Prioritas: **fun dulu, polish belakangan**
- Batasi diri: jangan tambah mesin baru sebelum 10 blueprint selesai

---

### 🚀 Phase 3 — Early Access `(4–6 Bulan total)`

Baru masuk sini kalau MVP udah diplaytest dan core loop validated.

- 20+ blueprint, 2 faksi teknologi (Steampunk / Cyberpunk)
- 15 wave
- Audio (lo punya FL Studio, manfaatin!)
- Polish VFX (GPU Instancing sudah disiapkan sejak MVP)
- Canvas splitting untuk UI performance
- Steam page & trailer

---

### 📅 Quick Timeline Overview

|Phase|Durasi|Output|
|---|---|---|
|0 - Setup|2–3 hari|Project structure siap|
|1 - Prototype|2 minggu|Go/No-Go decision|
|2 - MVP|~2 bulan|Playable full run|
|3 - Early Access|4–6 bulan total|Steam launch|

---

Mau mulai dari Phase 0 sekarang King? Gue bisa langsung bantu setup struktur folder di Unity project lo, atau bikin `GameManager` + `GameState` FSM pertama.