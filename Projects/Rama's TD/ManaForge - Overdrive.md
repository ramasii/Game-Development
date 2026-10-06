# 📋 GDD: ManaForge — Overdrive

---

## 🎮 1. Konsep & Identitas Game

- **Premis**: Game ini adalah Roguelite Factory di mana pemain membangun pabrik otomatis dari blueprint acak untuk memproduksi senjata magis dan mempertahankan inti energi (*Core*) dari serbuan monster yang datang bergelombang.
- **Genre**: Roguelite + Automation / Factory Defense
- **View**: 3D top-down isometric
- **Target Platform**: Website / Web Portal (browser, WebGL)
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
  Build Phase (2 mnt, bisa Skip) → Wave Phase → Reward Phase → (run berikutnya atau mati)

  Build Phase  : Susun conveyor, mesin, turret dari blueprint. Lihat preview arah wave berikutnya. Bisa hapus/jual/rotate.
  Wave Phase   : Monster menyerang — pabrik + turret jalan otomatis. Bangunan terkunci tidak bisa diubah.
  Reward Phase : Pilih 1 dari 3 blueprint (1 bangunan) sebagai hadiah.
  Mati         : Core HP 0 → run berakhir → koin tersimpan → upgrade permanen.
  ```
- **Core Mechanic**: Bangun jalur `Deposit → Miner → Conveyor → Smelter/Crafter → Turret`. Surplus bar yang masuk Core diubah jadi Energy.
- **Daya Tarik Jangka Pendek**: Combo 2–3 blueprint menghasilkan broken build. Ingin coba lagi tiap run.
- **Daya Tarik Jangka Panjang**: Blueprint langka, faksi Steampunk/Cyberpunk, upgrade Core permanen.

---

## ⚔️ 3. Mekanik Utama

- **Mekanik 1 — Grid & Build Rules**:
  - Map 100x100 tile, procedural generation untuk posisi deposit. Core 2x2 di tengah (tile 49-50).
  - Build Phase saja: bisa pasang, hapus, jual (refund 70%), rotate. Wave Phase terkunci.
  - Biaya bangun pakai resource mentah/jadi (lihat kolom Biaya). Start inventory: 30 Raw Iron.
  - Starter gratis saat run mulai: 1 Miner Basic, 5 Conveyor Mk.I, 1 Smelter Batu, 1 Turret Iron (pre-placed dekat Core, bisa dipindah saat Build).
  - Bangunan bisa hancur (HP 0). Bisa repair selama HP > 0, cost 50% biaya bangun untuk +50% max HP, kapan saja, instant.
- **Mekanik 2 — Blueprint Drafting (bangunan saja)**:
  - Tiap akhir wave pilih 1 dari 3 blueprint. 1 blueprint = 1 bangunan.
  - Blueprint TIDAK bisa jadi perk pasif. Perk/sinergy dibahas terpisah nanti, tidak masuk draft ini.
  - Pool tunggal di section 4. Rarity: Common/Uncommon/Rare/Epic untuk bobot draft.
- **Mekanik 3 — Ore Deposit & Miner**:
  - Deposit pre-placed, tidak bisa dihapus. Miner ditaruh di atasnya, bisa rotate output.
  - Penamaan konsisten:
    - Mentah: Raw Iron, Raw Copper, Raw Gold, Diamond
    - Olahan: Iron Bar, Copper Bar, Gold Bar, Diamond (tetap "Diamond", langsung pakai, tanpa smelt)
    - Craft: Alloy Pack (Iron Bar + Copper Bar), Rune Core (Gold Bar + Diamond)
  - Crusher TIDAK menghasilkan waste. Recycler dihapus dari GDD.
  - Miner tier terpisah (Basic / Fast / Multi), masing-masing 1 blueprint sendiri, bukan auto-upgrade. Upgrade manual dengan biaya.
  - Flow: `Deposit → Miner → Conveyor → Smelter/Crafter → Turret → (surplus → Core = Energy)`
- **Mekanik 4 — Wave & Spawn**:
  - Build Phase 2 menit, bisa Skip via tombol. Selama Build, UI tampilkan panah arah spawn wave berikutnya.
  - Maksimal 4 spawn point (Utara/Selatan/Timur/Barat edge). Maksimal 2 spawn aktif bersamaan.
  - Spawn bertahap dari pool, tidak sekaligus. Interval per tabel wave. Wave selesai saat semua pool habis + yang hidup mati.
  - Komposisi dibuat agar Wave 1-2 mudah (lihat tabel).
- **Mekanik 5 — Core HP & Ekonomi**:
  - Core HP 500. Repair 5 Iron Bar = +50 HP, hanya saat Build Phase.
  - Patokan: 2 Miner Basic cukup untuk 3 Turret Iron + surplus ke Core.
    - Miner Basic 20 raw/mnt x2 = 40 raw/mnt. 3x Smelter Batu 15/mnt = 45 kapasitas → output ~40 bar/mnt.
    - Turret Iron max 15 bar/mnt (1/4 detik), rata-rata tempur ~70% = ~10.5/mnt. 3 turret = ~31.5/mnt.
    - Surplus = 40 - 31.5 = ~8.5 bar/mnt → masuk Core = 8.5 x 5 = ~42 Energy/mnt.
    - 1 Miner → 1 Turret: 20 raw → 15 bar (bottleneck smelter) → turret 10.5/mnt → surplus ~4.5/mnt.
  - Konversi Core: 1 Iron/Copper Bar = 5 Energy, 1 Gold Bar = 12 Energy, 1 Diamond/Alloy = 20 Energy, 1 Rune Core = 50 Energy.

### Conveyor Tile Spec (7 Varian)

Mk.I dan Mk.II tier terpisah (2 blueprint berbeda).

| No | Nama | Size | I/O | Fungsi | Speed | Biaya | Rarity |
|---|---|---|---|---|---|---|---|
| 1 | Lurus Mk.I | 1x1 | 1 in -> 1 out lurus | Transport dasar | 1 item/s | 2 Raw Iron | Common |
| 2 | Lurus Mk.II | 1x1 | 1 in -> 1 out lurus | Transport cepat | 2.5 item/s | 2 Iron Bar | Uncommon |
| 3 | Belok | 1x1 | 1 in -> 1 out L | Belok 90°, ikut speed belt masuk +0.1s delay | - | 2 Raw Iron | Common |
| 4 | Splitter | 1x1 | 1 in -> 2 out | Bagi bergantian | round-robin | 3 Iron Bar | Common |
| 5 | Merger | 1x1 | 2 in -> 1 out | Gabung bergantian | - | 3 Iron Bar | Common |
| 6 | Balancer | 1x1 | 2 in -> 2 out | Seimbangkan load | balance | 5 Iron Bar | Uncommon |
| 7 | Filter | 1x1 | 1 in -> 2 out (lolos/tidak) | Blokir/izinkan per tipe | check/item | 5 Iron Bar + 2 Copper Bar | Uncommon |

### Ore Deposit & Resource (4 Varian)

| No | Deposit | Size | Resource Mentah | Hasil | Rarity Map |
|---|---|---|---|---|---|
| 1 | Iron Deposit | 1x1 | Raw Iron | Iron Bar (smelt) | Common, dekat Core |
| 2 | Copper Deposit | 1x1 | Raw Copper | Copper Bar (smelt) | Common, mid |
| 3 | Gold Deposit | 1x1 | Raw Gold | Gold Bar (smelt) | Uncommon, jauh/sisi |
| 4 | Diamond Deposit | 1x1 | Diamond | Diamond (langsung, bisa ke Crafter/Turret) | Rare, pojok high-risk |

### Miner Varian (3, tier terpisah)

| No | Nama | Size | Output | Interval | Biaya | Rarity |
|---|---|---|---|---|---|---|
| 1 | Miner Basic | 1x1 | 1 arah | 3.0s (20/mnt) | 10 Raw Iron | Common |
| 2 | Miner Fast | 1x1 | 1 arah | 1.5s (40/mnt) | 10 Iron Bar | Uncommon |
| 3 | Miner Multi | 1x1 | 2 arah round-robin | 2.0s total (30/mnt) | 10 Copper Bar | Rare |

### Enemy Varian (5, mudah → sulit)

| No | Nama | HP | Speed | Damage | Target | Ability | Bounty |
|---|---|---|---|---|---|---|---|
| 1 | Mite Crawler | 20 | 3.0 cepat | 5 ke Core/bangunan | Core langsung | Gerombol | 2 Raw Iron |
| 2 | Shell Brute | 120 | 1.2 lambat | 20 ke bangunan | Wall/Turret terdekat | Armor -50% dmg <10 | 4 Raw Iron |
| 3 | Spit Wisp | 45 | 2.0 sedang | 10 jarak 4 tile | Conveyor/Miner | Ranged | 3 Raw Copper |
| 4 | Phase Wraith | 70 | 3.5 sangat cepat | 15 | Tembus ke Core | Ignore Wall | 4 Raw Copper |
| 5 | Forge Titan | 800 | 0.8 sangat lambat | 100 AoE 3x3 | Core | Boss stomp | 10 Gold Bar + 5 Diamond |

### Wave Composition (7 Wave, 30-40 mnt/run)

Spawn bertahap, max 2 spawn aktif. Wave 1 dijamin 1 arah saja agar mudah.

| Wave | Komposisi pool | Total HP | Spawn aktif | Interval | Catatan |
|---|---|---|---|---|---|
| 1 | 6x Crawler | 120 | 1 (Utara) | 1/3s | 1 Turret Iron cukup (6 shot = 24s) |
| 2 | 10x Crawler + 2x Brute | 440 | 1 | 1/2.5s | Kenalkan Brute |
| 3 | 12x Crawler + 4x Brute + 2x Wisp | 810 | 2 | 1/2.5s | Kenalkan ranged, preview 2 arah |
| 4 | 8x Brute + 6x Wisp + 4x Wraith | 1510 | 2 | 1/2s | Wraith cepat, butuh Wall/Filter |
| 5 | 12x Wisp + 8x Wraith + 4x Brute | 1580 | 2 | 1/2s | Tekanan logistik, butuh Copper/Gold |
| 6 | 16x Brute + 8x Wisp + 8x Wraith | 2840 | 2 | 1/1.5s | Gear check Epic |
| 7 Final | 1x Titan + 10x Brute + 8x Wisp | 2360 | 2, Titan terakhir 30s | 1/1.5s, Titan tunggal | Boss + escort |

Core 500 HP cukup untuk 2-3 bocor kecil per wave awal (W1 total 30 dmg jika semua bocor), tapi lethal di W6-7 jika jebol.

### Building Stats (HP / Range / DPS)

Repair: 50% biaya = +50% max HP, HP harus >0.

| Bangunan | HP | Range | Damage / Rate / DPS | Konsumsi | Biaya |
|---|---|---|---|---|---|
| Turret Iron | 150 | 6 | 20 / 4s / 5 DPS | 1 Iron Bar/shot (15/mnt max) | 10 Iron Bar |
| Turret Copper | 120 | 5 | 8 / 2s / 4 DPS | 0.5 Copper/shot (15/mnt max) | 12 Copper Bar |
| Turret Gold | 150 | 5 | 30 AoE / 6s / 5 AoE | 2 Gold/shot (20/mnt max) | 20 Gold Bar |
| Turret Diamond | 120 | 8 | 100 / 8s / 12.5 | 1 Diamond/3 shot (~2.5/mnt) | 10 Diamond |
| Turret Tesla | 130 | 5 | 15 chain3 / 3s | 1 Alloy/2 shot | 15 Alloy Pack + 5 Diamond |
| Turret Mortar | 180 | 9 | 40 AoE / 7s | 2 Gold/shot | 20 Gold Bar |
| Smelter Batu | 200 | - | 1 Raw→1 Bar / 4s (15/mnt) | - | 8 Raw Iron |
| Smelter Arcane | 200 | - | 1 Raw→1 Bar / 2s (30/mnt) | - | 15 Iron Bar |
| Foundry Ganda 2x1 | 300 | - | 2 slot x 3s (40/mnt) | - | 25 Iron Bar + 10 Copper Bar |
| Crusher | 200 | - | 1 Raw→1 Bar / 1s (60/mnt) | - | 12 Iron Bar |
| Crafter Alloy 1x1 | 200 | - | 1 Iron+1 Copper→1 Alloy / 3s | - | 20 Iron Bar + 10 Copper Bar |
| Assembler Rune 2x2 | 400 | - | 1 Gold Bar+1 Diamond→1 Rune / 5s | - | 30 Gold Bar + 10 Diamond |
| Cooler Mist | 150 | - | hilangkan panas, +10% speed keluar | - | 10 Copper Bar |
| Coil Charger | 150 | - | tambah listrik ke bar lewat | - | 15 Copper Bar + 5 Iron Bar |
| Router | 150 | - | round-robin multi I/O | - | 5 Iron Bar |
| Storage Buffer | 200 | - | tahan 20 item | - | 8 Iron Bar |
| Wall Rune | 300 | - | blokir | - | 5 Raw Iron |
| Pylon Overdrive | 150 | buff 3x3 | +30% speed sekitar | 1 Gold/30s | 15 Gold Bar |

---

## 📦 4. Blueprint Pool Tunggal (26 Bangunan)

Satu pool. 1 draft = 1 bangunan. Tidak ada perk pasif di sini.

| No | Nama | Kategori | Rarity | Fungsi | Biaya |
|---|---|---|---|---|---|
| 1 | Miner Basic | Miner | Common | 20/mnt, 1 arah | 10 Raw Iron |
| 2 | Miner Fast | Miner | Uncommon | 40/mnt, 1 arah | 10 Iron Bar |
| 3 | Miner Multi | Miner | Rare | 30/mnt, 2 arah | 10 Copper Bar |
| 4 | Lurus Mk.I | Logistik | Common | 1 item/s | 2 Raw Iron |
| 5 | Lurus Mk.II | Logistik | Uncommon | 2.5 item/s | 2 Iron Bar |
| 6 | Belok | Logistik | Common | Belok 90° | 2 Raw Iron |
| 7 | Splitter | Logistik | Common | 1→2 | 3 Iron Bar |
| 8 | Merger | Logistik | Common | 2→1 | 3 Iron Bar |
| 9 | Balancer | Logistik | Uncommon | 2→2 balance | 5 Iron Bar |
| 10 | Filter | Logistik | Uncommon | 1→2 lolos/tidak | 5 Iron Bar + 2 Copper Bar |
| 11 | Router | Logistik | Uncommon | multi I/O | 5 Iron Bar |
| 12 | Smelter Batu | Smelter | Common | 4s/bar | 8 Raw Iron |
| 13 | Smelter Arcane | Smelter | Uncommon | 2s/bar | 15 Iron Bar |
| 14 | Foundry Ganda | Smelter | Rare | 2 slot paralel | 25 Iron Bar + 10 Copper Bar |
| 15 | Crusher | Smelter | Uncommon | 1s/bar, tanpa waste | 12 Iron Bar |
| 16 | Turret Iron | Turret | Common | 20 dmg /4s | 10 Iron Bar |
| 17 | Turret Copper | Turret | Uncommon | 8 dmg /2s | 12 Copper Bar |
| 18 | Turret Gold | Turret | Rare | 30 AoE /6s | 20 Gold Bar |
| 19 | Turret Diamond | Turret | Epic | 100 /8s sniper | 10 Diamond |
| 20 | Turret Tesla | Turret | Epic | chain3 | 15 Alloy Pack + 5 Diamond |
| 21 | Turret Mortar | Turret | Rare | 40 AoE jauh | 20 Gold Bar |
| 22 | Crafter Alloy | Crafter | Rare | Iron+Copper→Alloy | 20 Iron Bar + 10 Copper Bar |
| 23 | Assembler Rune | Crafter | Epic | Gold+Diamond→Rune | 30 Gold Bar + 10 Diamond |
| 24 | Cooler Mist | Utilitas | Uncommon | anti-panas +10% | 10 Copper Bar |
| 25 | Coil Charger | Utilitas | Rare | tambah listrik | 15 Copper Bar + 5 Iron Bar |
| 26 | Storage / Wall / Pylon | Mixed | Uncommon-Rare-Epic | Buffer 20 / HP300 / buff 3x3 | 8 Iron Bar / 5 Raw Iron / 15 Gold Bar |

Recycler dihapus. Crusher tanpa waste.

---

## 🏛️ 5. Desain FTUE

- **Pendekatan**: Contextual UI Hint + Sandbox Kihon, zero risk.
  1. Tempatkan conveyor lurus → resource mengalir
  2. Tambah Smelter → jadi bar
  3. Hubungkan Turret → menembak otomatis
  4. Wave kecil (6 Crawler, 1 arah Utara, info preview) → loop penuh
- Reward tutorial: 1 blueprint bangunan pilihan pemain → run pertama.

---

## 🎨 6. Visual Design & Art Direction

### Art Style
- Referensi Shapez 2 low-poly. Readable > pretty. Isometric 3D top-down.

### Color Language (Wajib Konsisten)

| Elemen | Warna |
|---|---|
| Conveyor Mk.I / Mk.II | Abu `#2C2C2C` / Abu terang + strip kuning |
| Iron Deposit / Raw Iron / Iron Bar | Biru `#4A90D9` / Biru muda / Oranye `#E8862A` |
| Copper Deposit / Raw Copper / Copper Bar | Oranye tembaga `#B87333` |
| Gold Deposit / Raw Gold / Gold Bar | Kuning emas `#F1C40F` |
| Diamond Deposit / Diamond | Cyan putih `#AFF8FF` |
| Miner | Coklat `#6B4F3A` |
| Router / Filter / Balancer | Teal `#17A589` |
| Smelter / Crusher / Foundry | Merah bata `#C0392B` |
| Turret | Hijau `#27AE60` |
| Enemy | Ungu `#8E44AD` |
| Core 2x2 | Cyan emissive + batu gelap `#1A1A2E` |

### The Core — "The Arcane Forge" 2x2
- Trapesium + 2-3 cerobong + pintu emissive + rune tipis.
- HP 500. Visual: Tinggi=asap aktif cyan terang, Sedang=oranye redup, Kritis=merah blink.
