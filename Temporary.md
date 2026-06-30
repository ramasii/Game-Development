# 🧠 Prinsip Single Source of Truth (SSOT) dalam Clean Code

Dalam dunia _software engineering_ dan praktik _Clean Code_, **Single Source of Truth (SSOT)** adalah prinsip arsitektur yang menyatakan bahwa setiap elemen data atau logika bisnis yang spesifik hanya boleh dibuat, disimpan, dan diedit di **satu tempat saja**.

Ketika bagian lain dari program membutuhkan data atau logika tersebut, mereka harus merujuk ke satu sumber utama ini, bukan membuat salinan (_copy_) atau representasi baru.

### 1. Mengapa SSOT Diperlukan dalam Clean Code?

_Clean Code_ berfokus pada kode yang mudah dibaca, mudah dirawat (_maintainable_), dan mudah diubah. Tanpa penerapan SSOT, kode Anda akan mengalami masalah berikut:

- **Duplikasi Data (Redundansi):** Jika data yang sama disimpan di tiga variabel berbeda, Anda harus memperbarui ketiganya saat terjadi perubahan. Jika lupa memperbarui salah satu saja, akan terjadi bug sinkronisasi.
    
- **Sulit Melacak Bug:** Saat hasil kalkulasi salah, Anda akan kesulitan mencari tahu fungsi mana yang menghitungnya jika logika tersebut tersebar di banyak tempat.
    
- **Efek Samping (Side Effects) Tak Terduga:** Mengubah kode di satu modul tiba-tiba merusak modul lain karena adanya ketergantungan data yang tidak terpusat.
    

### 2. Cara Menerapkan Prinsip SSOT dalam Kode

#### A. Gunakan Data Turunan (_Derived Data / Computed Properties_)

Jangan menyimpan data state yang sebenarnya bisa dihitung langsung dari data yang sudah ada.

_❌ **Salah (Non-SSOT):** Menyimpan status kelulusan/kematian secara manual._

C#

```
public class Player {
    public int Health = 100;
    public bool IsDead = false; // Duplikasi kebenaran. Jika Health = 0 tapi IsDead lupa diubah, sistem rusak.
}
```

- can **Benar (Menerapkan SSOT):** Menjadikan status sebagai properti yang dihitung secara dinamis.*
    

C#

```
public class Player {
    public int Health = 100;
    
    // Sumber kebenaran tunggal untuk status kematian adalah nilai Health itu sendiri
    public bool IsDead => Health <= 0; 
}
```

#### B. Sentralisasi State (_Centralized State Management_)

Pusatkan status utama aplikasi pada satu manajer khusus (misalnya menggunakan pola _Singleton_ untuk komponen manajerial).

Sebagai contoh, jika Anda membuat sistem _Game State_:

- Jangan biarkan komponen UI, Player, dan Audio melacak sendiri-sendiri apakah game sedang `Paused` atau `Gameplay`.
    
- Pusatkan status tersebut di satu kelas bernama `GameManager`. Komponen lain hanya berhak membaca dari satu sumber tersebut.
    

#### C. Gunakan Sistem Event / Signals (_Decoupling_)

Agar komponen lain bisa mengetahui perubahan dari sumber kebenaran tanpa harus ikut campur memodifikasi datanya, gunakan pendekatan _Event-Driven_.

- **Alur Logika:** `GameManager` mengubah state miliknya $\rightarrow$ `GameManager` memicu event `OnStateChanged` $\rightarrow$ UI, Player, dan Audio merespons event tersebut secara mandiri.
    
- Dengan cara ini, kebenaran perubahan state tetap dipegang oleh satu objek saja (`GameManager`).
    

#### D. Hindari _Hardcoded Strings_ (Gunakan Enums atau Constants)

String yang ditulis manual di banyak tempat (misalnya nama scene, nama animasi, atau ID database) sangat melanggar SSOT. Jika nama scene berubah, Anda harus menggantinya di seluruh file kode secara manual.

- **Penerapan SSOT:** Kumpulkan semua teks statis ke dalam sebuah struktur data terpusat (seperti `Enum` atau kelas `Static Constants`).
    

C#

```
// Sumber kebenaran tunggal untuk nama-nama State
public enum GameState {
    MainMenu,
    Gameplay,
    Paused,
    GameOver
}
```

### 3. Keuntungan Menerapkan SSOT

1. **Refactoring yang Aman:** Jika Anda perlu mengubah struktur data atau logika perhitungan, Anda hanya perlu mengubahnya di satu file atau satu fungsi saja.
    
2. **Satu Sumber untuk Debugging:** Jika nilai data menyimpang, Anda langsung tahu persis ke kelas mana Anda harus pergi untuk memperbaikinya.
    
3. **Meningkatkan Keterbacaan (Readability):** Developer lain tidak akan bingung memilih variabel mana yang harus digunakan, karena hanya ada satu variabel resmi untuk data tersebut.
    

> 💡 **Kesimpulan:** Single Source of Truth dalam Clean Code mengajarkan kita untuk tidak malas mendesain arsitektur di awal. Dengan memastikan setiap informasi penting hanya memiliki **satu pemilik sah**, kode Anda akan terhindar dari _spaghetti code_ dan siap menghadapi perubahan skala proyek yang lebih besar.