# 🎮 IFEST 2026 — Dukun Chain Reaction

> *Gamejam IFEST 2026 (Tema: Chain Reaction) — Player sebagai dukun yang membuat barang dari beberapa bahan yang harus urut, diberikan ke customer yang memesan, lalu mendapat uang untuk beli bahan lagi.*

---

## 📋 Dokumen Proyek

- [[GDD - Dukun Chain Reaction]] — GDD versi CONFIRMED (hanya mekanik dari screenshot)
- [[Roadmap]] — Timeline gamejam, task breakdown & Go/No-Go gate

---

## 🔗 Skill Vault yang Relevan

### 🎮 Game Design
- [[Player Persona & Motivasi (Quantic Foundry)]] — Persona target: Achiever & Explorer

### 🏗️ Game Architecture
- [[Observer Pattern Events]] — Juice & order bubble decoupled via event
- [[Single Source of Truth (SSOT)]] — CustomerOrderPool & InventoryManager sebagai SSOT

---

## 📊 Status Proyek

| Info | Detail |
|------|--------|
| **Nama** | Dukun Chain Reaction |
| **Event** | IFEST 2026 Game Jam |
| **Tema** | Chain Reaction |
| **Genre** | Crafting Puzzle |
| **Platform** | PC (WebGL optional) |
| **Engine** | Unity 6 |
| **Tim** | Team IFEST |
| **Status** | ✅ Selesai — Submitted 04 Sep 2026 |

### 🚦 Progress

- [x] Ide & Core Loop (Combine 5 slot urut -> Order -> Output (Sepatu Roda) -> Objective Money -> Shop) — CONFIRMED
- [x] Moodboard (Nusantara Modern Urban, Kramat Mantra Dukun Sesajen Ramuan — Vibes Modern Misterius Fun Mistis Profesional JOy)
- [x] GDD dibuat (versi confirmed only, appendix pending belum di-merge)
- [x] Roadmap dibuat
- [x] Prototype Combine 5-slot + validasi urutan
- [x] Implementasi Order System + Customer
- [x] Ekonomi Shop (Money -> Bahan)
- [x] Polish + Build & Submit IFEST

### ✨ Polish Terakhir (Sesi 04 Sep 2026)

- [x] Item juice DOTween (`Assets/Scripts/Rama/UIDraggableItemJuice.cs`) — drag lift + custom curve, drop boink, hover boink, wrong-return tween
- [x] Fix hover spam membesar (reset scale + kill punch/scale sebelum tween baru)
- [x] Fix stuck wrong-drop (guard double-call SellSpot + OnEndDrag, idempotent return tween)
- [x] Fix split-stack count hilang (capture `WasSplitDrag` sebelum `Initialize`)
- [x] Fix warna putih saat merge split-stack (copy `Image.color`)
- [x] Fix stack wrong-drop teleport (tween + merge di `onComplete`)
- [x] Order bubble (`Assets/Scripts/Rama/CustomerOrderBubble.cs`) — Square jadi icon `ItemData` order, putih, fixed scale, statis

> Proyek dilanjutkan: [[Rama's TD]] (🔧 Aktif kembali setelah IFEST selesai)
