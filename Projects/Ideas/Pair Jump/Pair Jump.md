# 🎮 Pair Jump — GDD Mini (GT Jam Internal 2026)

> Jam 3 hari (sampai 13 Sep 2026) | Solo Dev | Unity 6 | Mobile Portrait

## 🎮 1. Konsep & Identitas Game

Game vertical platformer hyper-casual di mana 2 karakter tak terduga (Red & Blue) terjebak dalam 1 badan. Naik setinggi-tingginya dengan ganti mode di udara.

- **Premis**: Game ini adalah 2D vertical platformer di mana pemain drag untuk gerak dan tap untuk ganti mode Red/Blue agar bisa mendarat di platform warna yang cocok.
- **Genre**: 2D Hyper-casual Platformer (Score-attack)
- **Target Platform**: Mobile Android Portrait (primary), PC build untuk submit jam
- **USP**: Unexpected Pair dalam 1 badan + swap mid-air + wrap kiri-kanan. Beda dari Doodle Jump yang cuma variasi platform, di sini platformnya reaktif ke state pemain.
- **Referensi & Inspirasi**: Seperti Doodle Jump tapi dengan twist polarity Ikaruga + Duet Game

Mockup: lihat desain awal (kiri P1 Red bisa injak Red+Hijau, kanan P1 Blue bisa injak Blue+Hijau, warna lawan tampil ghost dashed 25%).

## 🔄 2. Core Gameplay Loop

**Loop Utama**: Auto-jump → Drag posisi di udara → Tap swap warna → Landing tepat → Kamera naik → Ulangi. Jatuh di bawah kamera = Game Over → Retry 1 tap.

- **Core Mechanic**: Tap-swap polarity mid-air untuk mengaktifkan / menonaktifkan collider platform.
- **Daya Tarik Jangka Pendek**: "Satu swap lagi!" — timing swap 0.2 detik sebelum landing, combo bonus bikin nagih 5 menit pertama.
- **Daya Tarik Jangka Panjang (versi Jam)**: Highscore height (m) + perfect landing streak untuk pamer ke juri.

## ⚔️ 3. Mekanik Utama

Maks 4, tidak tambah sebelum prototype fun:

- **Mekanik 1 — Auto-Jump**: Loncat otomatis saat sentuh platform valid. Jump height fixed, hang-time 0.7s (cukup untuk 1x swap di udara).
- **Mekanik 2 — Drag Move + Wrap**: Geser kanan-kiri relatif (sensitivitas 1:1.2). Tembus layar kanan → muncul di kiri dan sebaliknya. Multi-touch ready.
- **Mekanik 3 — Tap Swap Red/Blue**: Tap (<0.2s, <15px, abaikan UI) ganti mode. Mode Red = collider Red+Hijau aktif, Blue jadi ghost. Mode Blue sebaliknya. Ada buffer 0.1s + sfx pop + scale punch.
- **Mekanik 4 — Spawner Solvable**: Spawn procedural ke atas. Hijau = netral selalu solid. Red/Blue = butuh mode cocok. Aturan: selalu ada minimal 1 pijakan reachable dari lompatan terakhir.

Kontrol locked: drag = gerak, tap = switch, tombol kanan-atas = pause. Tanpa tilt, tanpa swipe gesture.

## 💻 4. Arsitektur Data & Design Pattern

Prioritas jam: simpel, anti-null, gampang debug solo.

- **Design Pattern Pilihan**:
  - *Simple FSM Berbasis Enum (PlayerMode: Red, Blue)* untuk state pemain — prototyping cepat.
  - *Observer / C# event (OnModeChanged)* untuk decoupling: Player tidak kenal Platform, Platform subscribe event lalu enable/disable collider sendiri.
  - *Object Pooling (UnityEngine.Pool)* untuk platform, hindari Instantiate/Destroy tiap naik.
- **Arsitektur & Penyimpanan Data**: Centralized State Manager (GameManager Singleton) sebagai SSOT untuk height, best score, dan state Play/Pause/GameOver. Save best via PlayerPrefs (cukup untuk jam).
- **Mermaid Diagram**:
    ```mermaid
    graph TD
      InputManager -->|Tap / Drag| PlayerController
      PlayerController -->|Set Mode| PlayerModeFSM[PlayerMode Red/Blue]
      PlayerModeFSM -->|OnModeChanged| RedPlatform
      PlayerModeFSM -->|OnModeChanged| BluePlatform
      PlayerModeFSM -->|OnModeChanged| PlayerVisuals
      PlayerController -->|OnLanded| GameManager
      PlatformPool -->|Spawn/Recycle| Spawner
      Spawner -->|Height| GameManager
      GameManager -->|Update| HeightUI
    ```

## 🏛️ 5. Desain FTUE

Pendekatan: *Contextual UI Hint + Safe Sandbox* (cocok untuk hyper-casual, bukan Kishoten penuh).

- 0-30m Zona Aman: cuma platform Hijau. Hint: "GESER untuk gerak" (panah kiri-kanan).
- 30-80m Kenalin Swap: Red+Hijau selang-seling. Hint: "TAP untuk ganti warna" muncul saat pertama ketemu Red. Ghost Blue ditampilkan 25% agar terbaca tapi jelas non-solid.
- 80m+ Bukti: campuran Red/Blue wajib swap 1x di udara. Tidak ada teks lagi, cuma ghost + sfx.

Gagal di FTUE = respawn di platform terakhir tanpa restart height (ramah jam).

## 🚀 6. Struktur Folder Modular & Optimisasi Performa

- **Struktur Folder (Feature-Based)**:
    ```
    Assets/
    ├── Art/ Sprites, Anim/
    ├── Audio/ SFX, BGM/
    ├── Settings/ Input, URP/
    └── _PairJump/
        ├── Core/ GameManager.cs, PlatformPool.cs, Spawner.cs, InputManager.cs
        ├── Features/Player/ PlayerController.cs, PlayerMode.cs, PlayerVisuals.cs
        ├── Features/Platform/ Platform.cs, RedPlatform.cs, BluePlatform.cs, NeutralPlatform.cs
        └── Features/UI/ HeightUI.cs, PauseUI.cs, GameOverUI.cs
    ```
- **Rencana Optimisasi**:
    - *CPU/Memori*: Object Pool untuk platform (max ~20 aktif), no alloc di Update.
    - *Rendering/UI*: Canvas Splitting — StaticCanvas (bg), DynamicCanvas (height text), OverlayCanvas (pause/gameover). Sprite sederhana kotak rounded sesuai mockup.

## 📏 7. Scope & Feasibility

- **Estimasi Durasi**: Prototype Day 1 (move+jump+wrap+swap), MVP Day 2 (spawner+score+gameover+pause), Polish+Build Day 3 (juice+sfx+icon).
- **Ukuran Tim**: Solo (Rama - Programmer + Art kotak + SFX FL Studio simpel).
- **Risiko Teknis**: Tap vs drag ketuker saat panik → mitigasi threshold 15px + multi-touch touchId terpisah + abaikan touch di atas UI. Wrap + kamera follow glitch → clamp kamera hanya naik, tidak turun.
- **Risiko Desain**: Platform unsolvable → mitigasi aturan spawn reachable + ghost visibility 25% (bukan invisible 0%).
- **Kriteria Go/No-Go**: Dalam 2 menit playtest: bisa paham tanpa tutorial teks panjang, bisa swap mid-air minimal 3x berturut tanpa frustrasi, tidak ada null saat restart cepat.

---
#usulan-jam #unexpected-pair #unity #mobile #platformer
