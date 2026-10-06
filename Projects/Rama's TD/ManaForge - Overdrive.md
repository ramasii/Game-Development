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
- **Daya Tarik Jangka Panjang**: Membuka faksi teknologi baru (Steampunk / Cyberpunk), blueprint langka, dan upgrade Core permanen lewat metaprogression.

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

### Conveyor Tile Spec (6 Varian)

| No | Nama | Size | I/O | Fungsi | Speed | Rarity |
|---|---|---|---|---|---|---|
| 1 | Lurus | 1x1 | 1 in -> 1 out lurus | Transport linear | 1 item/s, Mk.II 2.5/s | Common |
| 2 | Belok | 1x1 | 1 in -> 1 out L | Belok 90° | sama spt lurus, +delay 0.1s | Common |
| 3 | Splitter | 1x1 | 1 in -> 2 out | Bagi 1 jalur ke 2 bergantian | 1:1 round-robin | Common |
| 4 | Merger | 1x1 | 2 in -> 1 out | Gabung 2 jalur ke 1 | prioritas bergantian | Common |
| 5 | Balancer | 1x1 | 2 in -> 2 out | Seimbangkan load 2 jalur | balance + round-robin | Uncommon |
| 6 | Filter | 1x1 | 1 in -> 2 out (lolos / tidak) | Blokir/izinkan resource per tipe, out1 = memenuhi kriteria, out2 = tidak | filter check tiap item | Uncommon |

### Ore Deposit & Resource (4 Varian)

| No | Deposit | Size | Resource Mentah | Hasil Smelter (Bar) | Rarity Map |
|---|---|---|---|---|---|
| 1 | Iron Deposit | 1x1 | Raw Iron | Iron Bar | Common, dekat Core |
| 2 | Copper Deposit | 1x1 | Raw Copper | Copper Bar | Common, mid |
| 3 | Gold Deposit | 1x1 | Raw Gold | Gold Bar | Uncommon, jauh / sisi map |
| 4 | Diamond Deposit | 1x1 | Rough Diamond | Diamond (langsung, tanpa smelt) | Rare, pojok / high-risk |

### Miner Varian (3)

| No | Nama | Size | Output | Interval | Upgrade Dari | Rarity |
|---|---|---|---|---|---|---|
| 1 | Miner Basic | 1x1 | 1 arah | 3.0s/item | - (default craft) | Common |
| 2 | Miner Fast | 1x1 | 1 arah | 1.5s/item | Basic + 10 Iron Bar | Uncommon |
| 3 | Miner Multi | 1x1 | 2 arah round-robin | 2.0s/item | Fast + 10 Copper Bar | Rare |

---

## 📦 4. Blueprint Pool (20 Varian)

Blueprint = bangunan placeable untuk Reward Draft. Perk pasif dibahas terpisah.

| No | Nama | Kategori | Rarity | Fungsi | Input -> Output / Konsumsi |
|---|---|---|---|---|---|
| 1 | Miner Basic | Miner | Common | Ekstraksi dasar, 1 output | Deposit -> mentah, 3s/item |
| 2 | Miner Fast | Miner | Uncommon | 2x lebih cepat | Deposit -> mentah, 1.5s/item |
| 3 | Miner Multi | Miner | Rare | 2 output round-robin | Deposit -> mentah ke 2 arah |
| 4 | Conveyor Mk.I | Logistik | Common | Transport dasar | 1 item/s |
| 5 | Conveyor Mk.II | Logistik | Uncommon | Transport cepat | 2.5 item/s |
| 6 | Splitter | Logistik | Common | 1 -> 2 bergantian | - |
| 7 | Merger | Logistik | Common | 2 -> 1 | - |
| 8 | Balancer | Logistik | Uncommon | Seimbangkan 2 jalur | 2 in -> 2 out |
| 9 | Router | Logistik | Uncommon | Auto-distribusi multi I/O | round-robin |
| 10 | Smelter Batu | Smelter | Common | Lebur lambat murah | mentah -> bar, 4s |
| 11 | Smelter Arcane | Smelter | Uncommon | Lebur cepat | mentah -> bar, 2s `[panas]` |
| 12 | Foundry Ganda | Smelter | Rare | 2 slot paralel | 2x mentah -> 2x bar |
| 13 | Turret Iron | Turret | Common | DPS standar | Iron Bar, 1/tembakan |
| 14 | Turret Copper | Turret | Uncommon | Fire-rate tinggi | Copper Bar, 0.5/tembakan |
| 15 | Turret Gold | Turret | Rare | Splash AoE | Gold Bar, 2/tembakan `[panas]` |
| 16 | Turret Diamond | Turret | Epic | Sniper dmg besar | Diamond, 1/3 tembakan |
| 17 | Crafter Alloy | Crafter | Rare | Gabung bar jadi mix | Iron + Copper -> Alloy |
| 18 | Storage Buffer | Utilitas | Uncommon | Tahan 20 item | anti-bottleneck |
| 19 | Wall Rune | Defensif | Uncommon | Blokir 1 tile | HP 200 |
| 20 | Pylon Overdrive | Utilitas | Epic | Buff 3x3 +30% speed | 1 Gold Bar/30s `[listrik]` |

---

## 🏭 5. Machine Varian (12)

| Nama | Kategori | Size | Fungsi | Rarity |
|---|---|---|---|---|
| Smelter Batu | Smelter | 1x1 | mentah -> bar, 4s lambat murah | Common |
| Smelter Arcane | Smelter | 1x1 | mentah -> bar, 2s | Uncommon |
| Foundry Ganda | Smelter | 2x1 | 2 slot paralel | Rare |
| Crusher Scrap | Smelter | 1x1 | 1 mentah -> 2 shard 1s, 30% jadi waste | Uncommon |
| Crafter Alloy | Crafter | 1x1 | Iron Bar + Copper Bar -> Alloy Pack | Rare |
| Assembler Rune | Crafter | 2x2 | Gold Bar + Diamond -> Rune Core | Epic |
| Cooler Mist | Utilitas | 1x1 | hilangkan panas, +10% speed keluar | Uncommon |
| Coil Charger | Utilitas | 1x1 | tambah listrik ke bar lewat | Rare |
| Recycler Waste | Utilitas | 1x1 | 3 waste -> 1 bar acak | Rare |
| Turret Tesla | Turret | 1x1 | chain 3 musuh, butuh Alloy | Epic |
| Turret Mortar | Turret | 2x2 | AoE jauh, lambat, butuh Gold | Rare |
| Pylon Overdrive | Buffer | 1x1 | buff 3x3 +30% speed, makan Gold/30s | Epic |

---

## 🏛️ 6. Desain FTUE

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

## 🎨 7. Visual Design & Art Direction

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

