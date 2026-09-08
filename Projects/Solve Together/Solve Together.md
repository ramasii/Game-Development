# 🎮 Solve Together — Co-op Puzzle 2D (PBL Kuliah)

> *Co-op Puzzle 2D Side Scroller max 4 orang — selesaikan puzzle bareng untuk buka pintu ke level berikutnya. Tugas kuliah Kelompok 4, Unity 6 + NGO.*

---

## 📋 Dokumen Proyek

- [[GDD - Solve Together]] — GDD ringkas: konsep, core loop, arsitektur sinkronisasi, roles, referensi
- [[Timeline]] — Timeline 6 minggu: Ideation → Prototyping → Assets → Level → Integration → Polishing
- [[PBL Brief - NGO]] — Panduan PBL: spesifikasi Unity 6 NGO, komponen wajib Netcode, deliverables Ethol

---

## 🔗 Skill Vault yang Relevan

### 🏗️ Game Architecture
- [[Centralized State Manager (GameManager Singleton & Event)]] — LevelManager + NetworkManager sebagai pusat state
- [[Observer Pattern Events]] — Event puzzle solved tanpa polling
- [[Single Source of Truth (SSOT)]] — Server sebagai SSOT untuk buttonPressed, skor, timer

### 🎮 Game Design
- [[Merancang Sistem Sinergi & Item Roguelite]] — Desain puzzle co-op yang butuh timing & komunikasi
- [[Framework Kihon-Kata-Kumite (Learning Curve & Encounter Design)]] — Tutorial puzzle 1 → 2 tombol → multi-pemain

### 🏛️ Level & Spatial Design
- [[Tutorial Level Building Blocks]] — FTUE: START → injak tombol → pintu buka → FINISH

---

## 📊 Status Proyek

| Info | Detail |
|------|--------|
| **Nama** | Solve Together |
| **Kelompok** | Kelompok 4 (4 orang) |
| **Genre** | Co-op Puzzle 2D Side Scroller |
| **Model** | Kooperatif, max 4 pemain, LAN / Direct IP |
| **Engine** | Unity 6 (wajib seragam sekelompok) |
| **Netcode** | Unity Netcode for GameObjects (NGO) + Unity Transport |
| **Artstyle** | Flat Rounded Cartoons, Chibi beda warna per player |
| **Tim** | Ahmad Ramadhani (5224600032) - Programmer, Satya Bagus Kenantaka (5224600038) - Level Designer, Syahrandy Waskito (5224600035) - 2D Environment Artist, Fisyahbilillah Mayrizqy Nurhanifah (5224600053) - 2D Character Artist |
| **Status** | 🔧 Aktif — PBL Kuliah, Week 1 Inisiasi |

### 🚦 Progress

- [x] Ide & Concept (Co-op Puzzle ala Pico Park + artstyle Poinpy)
- [x] GDD ringkas + diagram core loop + rencana arsitektur jaringan
- [x] Timeline 6 minggu
- [ ] Week 1: Prototyping movement + NetworkManager Host/Client connect
- [ ] Week 2-3: Assets Production + Level Production (1 level playable)
- [ ] Week 2-4: Engine Integration (NetworkTransform, NetworkVariable, ServerRpc/ClientRpc, Ownership)
- [ ] Week 4-6: Polishing, anti-desync, laporan mingguan Ethol
