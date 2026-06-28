# 🧪 Simple FSM Berbasis Enum (Game State Prototyping)
#architecture #state #prototyping

## 🎯 Apa Ini?
Pendekatan paling cepat dan paling minim *over-engineering* untuk mengelola kondisi inti game (Main Menu, Playing, Paused, Game Over) selama fase *prototyping* — saat mekanik masih sering berubah arah dan arsitektur harus mudah dibongkar pasang.

## 🧠 Poin Penting

**Aturan Emas Prototyping**
> Prioritaskan kecepatan iterasi dan fleksibilitas, hindari *over-engineering*. Arsitektur state di fase ini harus mudah dibongkar total, bukan dirancang untuk tahan selamanya.

**Core States — jangan bikin yang belum tentu dipakai**
```csharp
public enum GameState
{
    MainMenu,
    Playing,
    Paused,
    GameOver
}
```
> Kenapa `enum` + `switch-case`, bukan *class-based state machine* (interface `IState`) atau Animator-based state machine? Karena untuk prototyping, ini yang paling cepat ditulis, paling mudah dibaca, dan paling gampang dihapus total saat arah mekanik berubah drastis (sesuai aturan emas di atas). Class-based/Animator-based baru relevan kalau jumlah state & transisi sudah kompleks dan stabil (lihat [[Layered Architecture Stabilization]]).

## 🧩 Properties / Komponen Inti

| State | Kapan Aktif | Catatan |
|---|---|---|
| `MainMenu` | Halaman depan & inisialisasi awal | State default saat game pertama dibuka |
| `Playing` | Pemain aktif mengontrol game | `Time.timeScale = 1f` |
| `Paused` | Game berhenti sementara | Opsional, tapi bagus untuk mengetes interaksi UI saat `Time.timeScale = 0f` |
| `GameOver` | Kondisi kalah/menang | Tempat menaruh logika *reset* level |

## 🔄 Alur Lengkap

```
[MainMenu] ──Tekan Play──▶ [Playing] ──Tekan Pause──▶ [Paused]
                              │  ▲                         │
                              │  └────────Resume────────────┘
                              │
                       Kondisi Lose/Win
                              │
                              ▼
                         [GameOver] ──Reset/Restart──▶ [MainMenu] / [Playing]
```

## 🛠️ Cara Pasang & Pakai

1. Definisikan `enum GameState` dengan 4 *core states* di atas — jangan tambah state baru sebelum benar-benar dibutuhkan prototype.
2. Gunakan enum ini sebagai tipe data `CurrentState` di [[Centralized State Manager (GameManager Singleton & Event)]].
3. Kalau prototipe pivot ke mekanik baru, jangan ragu hapus/ubah total isi enum-nya — itu justru tujuan utama pendekatan ini.
4. Begitu game sudah stabil dan transisi antar-state mulai rumit (banyak kondisi bersyarat), pertimbangkan migrasi ke state machine berbasis class/interface.

## 🔗 Lihat Juga
- [[Centralized State Manager (GameManager Singleton & Event)]] — implementasi pengelola state terpusat yang mengonsumsi enum ini.
- [[Identify Core Loops]] — menentukan ritme inti permainan sebelum memutuskan state apa saja yang perlu dibuat.
