# 🎮 Ball Jumper — GDD Mini (GT Jam Internal 2026) v2 Dash Convert

> Jam 3 hari (sampai 13 Sep 2026) | Solo Dev | Unity 6 | Mobile Portrait | Revisi: Poinpy-style

## 🎮 1. Konsep & Identitas Game

Game vertical platformer hyper-casual di mana 2 karakter tak terduga (Red & Blue) terjebak dalam 1 badan. Untuk ganti mode, pemain harus ngedash ke bawah dan menyentuh platform apa pun — langsung toggle Red <-> Blue.

- **Premis**: Game ini adalah 2D vertical platformer di mana pemain drag untuk gerak dan swipe-down untuk dash ke bawah. Saat dash menyentuh platform apa pun (Red / Blue / Hijau), mode langsung switch ke warna sebaliknya.
- **Genre**: 2D Hyper-casual Platformer (Score-attack)
- **Target Platform**: Mobile Android Portrait (primary), PC build untuk submit jam
- **USP**: Swap bayar pakai height + aksi. Unexpected Pair yang tukerannya harus nabrak dulu. Beda dari Doodle Jump / Ikaruga yang swap-nya gratis.
- **Referensi & Inspirasi**: Doodle Jump (wrap + auto-jump) + Poinpy / Downwell inverse (swipe untuk momentum vertikal + stomp)

Contoh: lagi Red, di atas cuma ada Blue. Red bakal tembus Blue kalau jatuh biasa. Solusi: swipe down → dash → sentuh platform apa pun di bawah → jadi Blue → auto-bounce naik.

## 🔄 2. Core Gameplay Loop

**Loop Utama**: Auto-jump → Drag posisi di udara → Butuh ganti warna → Swipe-down dash → Sentuh platform apa pun → Toggle warna → Auto-bounce → Ulangi. Jatuh di bawah kamera = Game Over.

- **Core Mechanic**: Dash-down toggle. Normal fall = platform lawan ghost (tembus). Dash fall = semua platform solid, sentuh apa pun langsung switch Red <-> Blue.
- **Daya Tarik Jangka Pendek**: Dilema "korbanin height buat ganti warna sekarang atau cari hijau?" + feel dash yang nendang.
- **Daya Tarik Jangka Panjang (versi Jam)**: Highscore height + toggle streak tanpa jatuh.

## ⚔️ 3. Mekanik Utama

Maks 4, locked v2.1 toggle bebas:

- **Mekanik 1 — Auto-Jump**: Loncat otomatis saat sentuh platform valid. Jump height fixed, hang-time 0.85s.
- **Mekanik 2 — Drag Move + Wrap**: Geser kanan-kiri relatif (1:1.2). Tembus kanan → kiri. Saat dash, kontrol horizontal tetap 50% (fair steer).
- **Mekanik 3 — Swipe-Down Dash Toggle (pengganti tap)**: Flick bawah cepat (>60px dalam <0.3s, angle >60° dari horizontal, abaikan UI). Efek: gravity x3.5, trail, semua platform jadi solid selama dash. Saat sentuh platform apa pun (Red / Blue / Hijau) → langsung toggle Red <-> Blue + bounce. Miss semua = terus jatuh, bisa dash lagi. Cooldown 0.15s anti-spam.
- **Mekanik 4 — Spawner + Kamera Pemaaf**: Hijau netral selalu solid saat normal fall. Red/Blue butuh mode cocok saat normal fall, tapi selalu solid saat dash. Spawner jamin 1 jalur reachable. Death zone buffer 2.5m di bawah layar.

Kontrol locked v2.1: drag = gerak, swipe-down = dash toggle, tombol = pause. Tanpa tap, tanpa tilt.

## 💻 4. Arsitektur Data & Design Pattern

- **Design Pattern Pilihan**:
  - *Simple FSM Berbasis Enum*: PlayerMode (Red, Blue) + MoveState (Normal, Dashing). Pisah agar visual dan fisika tidak kecampur.
  - *Observer / C# event*: OnModeChanged (platform update collider + visual), OnDashStart/End (trail + kamera shake kecil + sfx).
  - *Object Pooling* untuk platform.
- **Arsitektur & Penyimpanan Data**: GameManager Singleton SSOT untuk height, best, state Play/Pause/GameOver. Save best via PlayerPrefs.
- **Mermaid Diagram**:
    ```mermaid
    graph TD
      InputManager -->|Drag| PlayerController
      InputManager -->|SwipeDown| DashController
      DashController -->|IsDashing?| Platform
      Platform -->|OnLanded + Convert?| PlayerModeFSM[Red/Blue]
      PlayerModeFSM -->|OnModeChanged| PlayerVisuals
      PlayerModeFSM -->|OnModeChanged| RedPlatform
      PlayerModeFSM -->|OnModeChanged| BluePlatform
      PlayerController -->|OnLanded| GameManager
      PlatformPool -->|Spawn/Recycle| Spawner
    ```

## 🏛️ 5. Desain FTUE

Pendekatan: *Contextual UI Hint + Safe Sandbox*.

- 0-30m: cuma Hijau. Hint: "GESER untuk gerak".
- 30-80m: Red+Hijau, tidak butuh toggle. Biar paham ghost (lawan samar 25%).
- 80-130m: tutorial toggle: taruh pola Red di atas Blue yang mustahil tanpa toggle. Hint: "SWIPE BAWAH untuk dash & ganti warna". Tekankan sentuh platform apa pun langsung toggle, tidak harus warna tujuan. Zona ini buffer kamera dilebarin, respawn di platform terakhir.
- 130m+: campuran acak wajib toggle berantai.

## 🚀 6. Struktur Folder Modular & Optimisasi Performa

- **Struktur Folder**:
    ```
    Assets/
    ├── Art/ Sprites, Anim/
    ├── Audio/ SFX, BGM/
    ├── Settings/ Input, URP/
    └── _PairJump/
        ├── Core/ GameManager.cs, PlatformPool.cs, Spawner.cs, InputManager.cs, CameraFollow.cs
        ├── Player/ PlayerController.cs, PlayerMode.cs, DashController.cs, PlayerVisuals.cs
        ├── Platform/ Platform.cs, RedPlatform.cs, BluePlatform.cs, NeutralPlatform.cs
        └── UI/ HeightUI.cs, PauseUI.cs, GameOverUI.cs
    ```
- **Rencana Optimisasi**:
    - *CPU/Memori*: Pool ~20 platform, no alloc di Update, dash pakai gravity scale bukan AddForce tiap frame.
    - *Rendering/UI*: Canvas Splitting (Static/Dynamic/Overlay). Trail dash pakai TrailRenderer 1 saja.

## 📏 7. Scope & Feasibility

- **Estimasi Durasi**: Day 1 (move+auto-jump+wrap+dash convert), Day 2 (spawner solvable+kamera buffer+score+pause), Day 3 (juice dash/convert+sfx+build).
- **Ukuran Tim**: Solo.
- **Risiko Teknis**: Drag horizontal vs swipe-down ketuker saat diagonal → mitigasi direction-lock 0.1s pertama + threshold angle 60°. Dash miss langsung mati → mitigasi death buffer 2.5m + coyote convert (landing 0.05s setelah dash habis masih dihitung convert).
- **Risiko Desain**: Swap jadi mahal (korban height) → game melambat. Mitigasi: jump height sedikit ditinggikan vs v1 (0.7s → 0.85s) agar 1 convert bisa nutup 2-3 platform. Playtest 2 menit wajib bisa 5x convert tanpa frustrasi.
- **Kriteria Go/No-Go**: Pemain paham dash-convert dalam 3x coba tanpa teks panjang, tidak ada misinput >10%, restart cepat tanpa null.

---
#usulan-jam #unexpected-pair #unity #mobile #platformer #poinpy-like
