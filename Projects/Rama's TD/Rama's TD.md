# 🎮 Rama's TD — ManaForge: Overdrive

> *Roguelite Factory Defense — pemain membangun pabrik otomatis untuk mempertahankan Core dari serbuan monster.*

---

## 📋 Dokumen Proyek

- [[ManaForge - Overdrive]] — Game Design Document (GDD) lengkap: konsep, core loop, mekanik, arsitektur, FTUE, struktur folder, scope & feasibility.
- [[Roadmap]] — Solo dev roadmap fase per fase: Phase 0 (Setup) → Phase 1 (Prototype/Go-No-Go) → Phase 2 (MVP) → Phase 3 (Early Access).

---

## 🔗 Skill Vault yang Relevan

### 🏗️ Game Architecture
- [[Single Source of Truth (SSOT)]] — ScriptableObject sebagai SSOT untuk semua Blueprint & Perk.
- [[Observer Pattern Events]] — Event Channel untuk resource-flow antar mesin tanpa polling.
- [[Decoupled Audio System (Event Channel & Pooling)]] — Referensi implementasi Object Pooling & Event Channel.
- [[Simple FSM Berbasis Enum (Game State Prototyping)]] — FSM untuk BuildPhase / WavePhase / RewardPhase / GameOver.
- [[Centralized State Manager (GameManager Singleton & Event)]] — GameManager sebagai pusat kendali state.

### 🎮 Game Design
- [[Roguelite Design Pillars (4 Pilar Utama)]] — Checklist 4 pilar: Broken Build, Permadeath, Mastery, Metaprogression.
- [[Merancang Sistem Sinergi & Item Roguelite]] — Blueprint sistem tag sinergi (`[listrik]`, `[panas]`, `[waste]`).
- [[Player Persona & Motivasi (Quantic Foundry)]] — Target pemain: The Professor (analitis, suka optimasi sistem).

### 🏛️ Level & Spatial Design
- [[Tutorial Level Building Blocks]] — Desain FTUE: Kihon sandbox room + Constructivist onboarding.
- [[Framework Kihon-Kata-Kumite (Learning Curve & Encounter Design)]] — Struktur tutorial: isolasi mekanik → pola → tekanan dinamis.

### 🔬 Research
- [[Riset Pasar - Roguelite & Indie Steam 2026]] — Validasi pasar: niche Roguelite + Automation belum banyak diisi di Steam.

---

## 📊 Status Proyek

| Info | Detail |
|---|---|
| **Nama** | Rama's TD (ManaForge: Overdrive) |
| **Genre** | Roguelite + Automation / Factory Defense |
| **Platform** | PC / Steam |
| **Engine** | Unity 6 |
| **Tim** | Solo Dev |
| **Status** | 🔧 Phase 0 — Foundation Setup |

### 🚦 Phase Progress

- [x] GDD dibuat
- [x] Roadmap dibuat
- [ ] **Phase 0** — Struktur folder, GameManager, EventChannel, Git setup *(2–3 hari)*
- [ ] **Phase 1** — Grid System, Conveyor, Loop pertama *(2 minggu — GO/NO-GO Gate)*
- [ ] **Phase 2** — MVP: Blueprint Drafting, Sinergi, 5 Wave, FTUE *(~2 bulan)*
- [ ] **Phase 3** — Early Access: 20+ blueprint, 2 faksi, Steam page *(4–6 bulan total)*
