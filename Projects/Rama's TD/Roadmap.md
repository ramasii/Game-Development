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
	Started (9 July 2026)
	Finished (12 July 2026)

- ✅ Grid System (Node/Graph) — tile placement di grid
- ✅ Conveyor Belt: lurus + belok, resource mengalir dari node ke node
- ✅ 1 Resource type (Raw Iron)
- ✅ Ore Deposit — tile pre-placed di map, tandai lokasi bahan mentah
- ✅ Miner — ditempatkan di atas Ore Deposit, spawn resource ke conveyor secara berkala
- ✅ Object Pooling untuk resource items
- ✅ 1 Mesin: Smelter (input Iron → output Iron Bar)
- ✅ Visualisasi sederhana (ProBuilder primitives)
- ✅ Factorio-style resource queuing (berdempet, tidak despawn di ujung)

**⚙️ Minggu 2 — Loop Pertama (target: loop terasa lengkap):**
	Started (13 July 2026)
	Finished (20 July 2026)

- ✅ Turret — consume Iron Bar dari conveyor → shoot projectile ke enemy
- ✅ Basic enemy — spawn, jalan lurus ke Core, serang Core & Turret
- ✅ Core HP — kena damage saat enemy sampai, ada visual feedback
- ✅ Wave Manager — spawn enemy per wave dari multiple spawn points, auto-scaling
- ✅ **GameManager FSM integration** — transisi state lengkap:
	- `BuildPhase` → player build bebas, ada tombol "Start Wave" untuk skip
	- `WavePhase` → build dilock, enemy spawn, pabrik jalan otomatis
	- `RewardPhase` → placeholder "Wave selesai" sebelum loop ulang
	- `GameOver` → Core HP = 0, tampil screen sederhana
- ✅ Basic HUD — Core HP bar, wave counter, 4 panel state
- ✅ Object Pooling untuk projectile turret
- ✅ **Router** — distribusi resource 1→banyak, round-robin, backpressure, anti-clog

**🎨 Art Track — Phase 1 (Placeholder Only, jangan lebih dari ini!):**

- ✅ Semua objek pakai **ProBuilder primitives** (cube, cylinder) — tidak perlu Blender
- ✅ Tiap tipe objek beda warna Unlit material sesuai Color Language di GDD:
	- Conveyor → `#2C2C2C` | Iron → `#4A90D9` | Smelter → `#C0392B` | Turret → `#27AE60` | Enemy → `#8E44AD`
	- Ore Deposit → `#F1C40F` (kuning) | Miner → `#6B4F3A` (coklat tua) | Router → `#17A589` (teal)
- ✅ Core (Arcane Forge) → cube 2×2 dengan emissive cyan, placeholder saja
- ✅ Grid → flat plane + LineRenderer
- ✅ **Aturan keras: tidak ada sesi Blender selama Phase 1**

**✅ Kriteria Lulus Phase 1 (Go/No-Go Gate):**

- ✅ Conveyor terasa _satisfying_ disambung-sambungin
- ✅ Loop **Build → Wave → Reward → Build** berjalan penuh tanpa crash
- ✅ Ada momen "oh shit bottleneck!" yang bikin panik saat wave
- ✅ **LULUS — Game terasa fun! Lanjut ke Phase 2.**

### 🎮 Phase 2 — MVP `(~2 Bulan setelah Phase 1)`

Target: **satu run penuh yang bisa diposting ke Instagram Reels dalam ~1 bulan dari sekarang.**

Dipecah 4 blok berurutan. Fokus utama: **selesaikan loop dulu, baru content, baru polish.**

---

- [x] **⚙️ Blok A — Complete the Loop `(~2 minggu)`**

Prioritas tertinggi. Loop harus bisa jalan penuh sebelum apapun ditambahkan.

- RewardPhase proper: UI pilih 1 dari 3 blueprint (pool SO acak)
- Blueprint system dasar: minimal 3 blueprint yang bisa di-draft dan langsung berpengaruh
- Loop repeat: Build → Wave → Reward → Build dengan wave counter naik tiap round
- Wave scaling dasar: tiap wave musuh lebih banyak / lebih kuat

**⚙️ Blok B — Mini Art Pass `(~1 minggu)` — 🎥 Instagram Reel Milestone**

Cukup buat layak diposting, bukan full polish. Target: ~1 bulan dari sekarang.

- Model Blender low-poly untuk mesin utama: Conveyor, Miner, Smelter, Turret, Router
- Ore Deposit visual upgrade (crystal/rock cluster kuning)
- Particle VFX dasar: mining spark, turret muzzle flash, enemy hit effect
- Screen shake saat Core kena hit (DOTween)
- SFX dasar 3–5 suara paling impactful (FL Studio)
- **Record & upload Instagram Reel** ← milestone utama Blok ini

**⚙️ Blok C — Machines & Content `(~3 minggu)`**

Setelah reel diposting, fokus ke depth dan replayability.

- ✅ ~~Router~~ — sudah diimplementasi di Phase 1 (bonus!)
- Splitter machine (1 input → 2 output fixed, berbeda dari Router yang dynamic)
- 1 mesin unik sesuai GDD (desain dulu di GDD sebelum implement)
- 10 blueprint/perk didesain dan diimplementasi
- Synergy tag system dasar: `[panas]`, `[listrik]`, `[waste]`
- 5 wave dengan proper difficulty curve

**⚙️ Blok D — Metaprogression `(~1-2 minggu)`**

Hook agar player mau main lagi setelah run berakhir.

- Coin drop saat enemy mati
- Save system (PlayerPrefs untuk MVP)
- 2–3 unlock permanen sebagai hook "next run"
- Proper GameOver screen dengan coin summary

---

**🎨 Art Track — Phase 2:**

Urutan prioritas seni mengikuti blok:
- Blok A: warna material konsisten, UI RewardPhase sederhana
- Blok B: low-poly Blender models, VFX, SFX — fokus yang **keliatan di kamera reel**
- Blok C: asset mesin baru, blueprint UI polish
- Blok D: GameOver screen, UI metaprogression

**Solo Dev Tips — Phase 2:**

- Playtesting sendiri tiap akhir minggu — catat satu hal yang paling terasa kurang
- Blok B art pass: prioritaskan yang keliatan di 30 detik pertama reel
- Jangan tambah blueprint baru sebelum loop Blok A benar-benar stabil

### 🚀 Phase 3 — Early Access `(4–6 Bulan total)`

Baru masuk sini kalau Blok D MVP selesai dan ada traction dari Instagram Reel.

**⚙️ Programming:**

- 20+ blueprint, 2 faksi teknologi (Steampunk / Cyberpunk)
- 15 wave dengan enemy variety
- Polish VFX (GPU Instancing)
- Canvas splitting untuk UI performance
- Steam Integration (Steamworks SDK)

**🎨 Art Track — Phase 3 (Full Polish):**

- Full art pass dua faksi:
	- **Steampunk** — gear + tembaga + uap, warna coklat/emas/oranye
	- **Cyberpunk** — neon + chrome + hologram, warna biru/ungu/putih
- Conveyor belt animasi (UV scroll material di Shader Graph)
- UI animations & transitions via DOTween
- VFX polish: particle lebih detail, emissive glow, screen effects
- **Audio lengkap (FL Studio)**:
	- SFX: conveyor hum, mining drill, mesin kerja, turret tembak, enemy mati, Core kena hit
	- BGM: loop ambient industrial-magic per state (Build = calm, Wave = intense)
- Steam page screenshots & trailer

---

**📅 Quick Timeline Overview:**

| Phase | Durasi | Output Utama |
|---|---|---|
| 0 - Setup | 2–3 hari | ✅ Project structure, GameManager, EventChannels |
| 1 Minggu 1 | 1 minggu | ✅ Grid, Conveyor, Miner, Smelter, Resource queuing |
| 1 Minggu 2 | 1 minggu | ✅ Turret, Enemy, Wave, FSM, HUD, Router → **GO/NO-GO: LULUS!** |
| 2 Blok A | ~2 minggu | RewardPhase, Blueprint drafting, loop repeat |
| 2 Blok B | ~1 minggu | Mini art pass, VFX, SFX → **🎥 Instagram Reel** |
| 2 Blok C | ~3 minggu | Splitter, mesin baru, 10 blueprint, synergy tags |
| 2 Blok D | ~1–2 minggu | Metaprogression, save system, GameOver proper |
| 3 - Early Access | 4–6 bulan total | 2 faksi, 20+ blueprint, Steam launch |
