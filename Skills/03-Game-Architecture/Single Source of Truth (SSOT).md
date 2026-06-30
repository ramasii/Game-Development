# 🔑 Single Source of Truth (SSOT)
#architecture #clean-code #state #anti-pattern

## 🎯 Apa Ini?
Prinsip arsitektur yang menyatakan setiap elemen data atau logika bisnis spesifik hanya boleh dibuat, disimpan, dan diedit di **satu tempat saja**. Bagian lain dari program yang butuh data/logika tersebut harus merujuk ke satu sumber utama ini, bukan membuat salinan atau representasi baru.

**Kapan harus dipakai:**
- Saat ada 2+ variabel/field yang sebenarnya bisa saling menyiratkan satu sama lain (misal `Health` dan `IsDead`).
- Saat beberapa komponen (UI, Player, Audio) butuh tahu status yang sama (misal state game: Paused/Playing).
- Saat string/angka yang sama (nama scene, ID animasi, key database) ditulis ulang manual di banyak file.
- Sebelum mulai *refactor* besar — cek dulu apakah bug yang muncul disebabkan data yang sama tersimpan di lebih dari satu tempat.

## 🧠 Poin Penting

**Masalah tanpa SSOT**
- **Duplikasi Data (Redundansi)** — data yang sama disimpan di banyak variabel, harus diupdate manual semuanya; lupa satu = bug sinkronisasi.
- **Sulit Melacak Bug** — saat hasil kalkulasi salah, susah cari fungsi mana yang menghitungnya kalau logikanya tersebar.
- **Efek Samping Tak Terduga** — ubah kode di satu modul tiba-tiba merusak modul lain karena ketergantungan data yang tidak terpusat.

**A. Data Turunan (Derived Data / Computed Properties)**
Jangan simpan state yang sebenarnya bisa dihitung langsung dari data yang sudah ada.
```csharp
// ❌ Salah (Non-SSOT): duplikasi kebenaran
public class Player {
    public int Health = 100;
    public bool IsDead = false; // Kalau Health = 0 tapi lupa diubah, sistem rusak
}

// ✅ Benar (SSOT): status dihitung dinamis dari satu sumber
public class Player {
    public int Health = 100;
    public bool IsDead => Health <= 0;
}
```

**B. Sentralisasi State (Centralized State Management)**
Pusatkan status utama aplikasi di satu manajer khusus (pola Singleton untuk komponen manajerial) — jangan biarkan UI, Player, dan Audio melacak status `Paused`/`Gameplay` masing-masing secara terpisah. Lihat [[Centralized State Manager (GameManager Singleton & Event)]] untuk implementasi lengkapnya.

**C. Sistem Event/Signals (Decoupling)**
Agar komponen lain tahu perubahan dari sumber kebenaran tanpa ikut campur memodifikasi datanya, pakai pendekatan *event-driven*:
```
GameManager mengubah state miliknya
        │
        ▼
GameManager memicu event OnStateChanged
        │
        ▼
UI, Player, Audio merespons event secara mandiri
```
> Kebenaran perubahan state tetap dipegang satu objek saja (`GameManager`) — pola ini sama persis dengan yang dipakai di [[Observer Pattern Events]] dan [[Decoupled Audio System (Event Channel & Pooling)]].

**D. Hindari Hardcoded Strings (Gunakan Enums/Constants)**
String yang ditulis manual di banyak tempat (nama scene, nama animasi, ID database) melanggar SSOT — kalau nama berubah, harus diganti manual di seluruh file.
```csharp
// Sumber kebenaran tunggal untuk nama-nama State
public enum GameState {
    MainMenu,
    Gameplay,
    Paused,
    GameOver
}
```
> Ini juga fondasi dari [[Simple FSM Berbasis Enum (Game State Prototyping)]] — enum jadi satu-satunya daftar resmi nama state.

## 🧩 Properties / Ringkasan Teknik

| Teknik | Masalah yang Diatasi | Implementasi |
|---|---|---|
| Derived/Computed Property | Data yang saling menyiratkan disimpan ganda | Getter dihitung dari field lain (`IsDead => Health <= 0`) |
| Centralized State Manager | Status yang sama dilacak banyak komponen | Singleton pemilik tunggal status |
| Event/Signal System | Komponen lain butuh tahu perubahan tanpa modifikasi langsung | `event Action<T>` / Event Channel |
| Enum/Constants | String/angka yang sama ditulis ulang manual | Satu `enum` atau kelas `static const` |

## 🔄 Alur Diagnosis (Kapan Curiga Ada Pelanggaran SSOT)

```
Ketemu bug aneh / data tidak sinkron?
        │
        ▼
Cek: apakah data ini disimpan di lebih dari 1 tempat?
        │
   ┌────┴────┐
   Ya         Tidak → bukan masalah SSOT, cari penyebab lain
   │
   ▼
Pilih perbaikan:
   - Bisa dihitung dari data lain? → jadikan Computed Property
   - Dibutuhkan banyak komponen?   → Sentralisasi di satu Manager
   - String/angka berulang?        → Ganti ke Enum/Constants
   - Komponen lain perlu notifikasi? → Tambahkan Event/Signal
```

## 🛠️ Cara Pakai
1. Sebelum nulis variabel baru, tanya dulu: "apakah nilai ini bisa dihitung dari data yang sudah ada?" — kalau ya, jadikan *computed property*, bukan field tersimpan.
2. Kalau beberapa sistem (UI, Audio, Player) butuh tahu status yang sama, jangan biarkan masing-masing nyimpen sendiri — pusatkan di satu manager dan broadcast lewat event.
3. Ganti semua string/angka magic yang ditulis berulang (nama scene, tag, key) jadi `enum` atau `static class Constants` satu sumber.
4. Saat refactor, cari dulu "siapa pemilik sah data ini?" sebelum mengubah logikanya di banyak tempat sekaligus.

## 🔗 Lihat Juga
- [[Centralized State Manager (GameManager Singleton & Event)]] — contoh konkret penerapan SSOT lewat Singleton + Event.
- [[Simple FSM Berbasis Enum (Game State Prototyping)]] — penerapan SSOT lewat enum untuk nama-nama state.
- [[Observer Pattern Events]] — pola event/signal yang menjaga SSOT tetap *decoupled*.
- [[Runtime State Separation]] — pelengkap soal pemisahan kepemilikan data persistent/runtime/temporary.
