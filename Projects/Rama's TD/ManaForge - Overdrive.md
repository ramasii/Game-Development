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

##### Conveyor Tile Spec (6 Varian)

| No | Nama | Size | I/O | Fungsi | Speed | Rarity |
|---|---|---|---|---|---|---|
| 1 | Lurus | 1x1 | 1 in -> 1 out lurus | Transport linear | 1 item/s, Mk.II 2.5/s | Common |
| 2 | Belok | 1x1 | 1 in -> 1 out L | Belok 90° | sama spt lurus, +delay 0.1s | Common |
| 3 | Splitter | 1x1 | 1 in -> 2 out | Bagi 1 jalur ke 2 bergantian | 1:1 round-robin | Common |
| 4 | Merger | 1x1 | 2 in -> 1 out | Gabung 2 jalur ke 1 | prioritas bergantian | Common |
| 5 | Balancer | 1x1 | 2 in -> 2 out | Seimbangkan load 2 jalur | balance + round-robin | Uncommon |
| 6 | Filter | 1x1 | 1 in -> 2 out (lolos / tidak) | Blokir/izinkan resource per tipe, out1 = memenuhi kriteria, out2 = tidak | filter check tiap item | Uncommon |

##### Ore Deposit & Resource (4 Varian)

| No | Deposit | Size | Resource Mentah | Hasil Smelter (Bar) | Rarity Map |
|---|---|---|---|---|---|
| 1 | Iron Deposit | 1x1 | Raw Iron | Iron Bar | Common, dekat Core |
| 2 | Copper Deposit | 1x1 | Raw Copper | Copper Bar | Common, mid |
| 3 | Gold Deposit | 1x1 | Raw Gold | Gold Bar | Uncommon, jauh / sisi map |
| 4 | Diamond Deposit | 1x1 | Rough Diamond | Diamond (langsung, tanpa smelt) | Rare, pojok / high-risk |

##### Miner Varian (3)

| No | Nama | Size | Output | Interval | Upgrade Dari | Rarity |
|---|---|---|---|---|---|---|
| 1 | Miner Basic | 1x1 | 1 arah | 3.0s/item | - (default craft) | Common |
| 2 | Miner Fast | 1x1 | 1 arah | 1.5s/item | Basic + 10 Iron Bar | Uncommon |
| 3 | Miner Multi | 1x1 | 2 arah round-robin | 2.0s/item | Fast + 10 Copper Bar | Rare |

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
