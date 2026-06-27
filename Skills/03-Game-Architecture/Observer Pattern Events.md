# Skill: Implement Observer Pattern for Event-Driven Mechanics
#architecture #design-pattern

## Kapan Harus Menggunakan
Gunakan skill ini ketika satu objek mengalami perubahan state atau menghasilkan sebuah event (seperti round dimenangi, kartu dimainkan, nyawa habis), dan banyak sistem lain (UI, audio, pencapaian/achievement, analitik) perlu merespons kejadian tersebut secara otomatis tanpa ketergantungan yang kaku (*tight coupling*).

## Deskripsi
Pola desain perilaku yang memisahkan antara produsen kejadian (*Subject*) dengan konsumen/pendengar kejadian (*Observer*) menggunakan sistem berlangganan (*subscription system*).

## Alur Kerja (Workflow)
1. **Establish Subject:** Buat kelas yang mengumumkan kejadian dan beri kemampuan untuk mengelola daftar pelanggan.
2. **Define Observer Interface:** Buat interface penerima notifikasi standar (misal fungsi `onNotify()`).
3. **Implement Concrete Observers:** Modifikasi sistem luar (UI, Sound, Achievement) agar mengimplementasikan interface observer tersebut.
4. **Register Subscriptions:** Daftarkan objek observer ke dalam subjek agar otomatis bereaksi saat event dipicu.

## 🔗 Hubungan Keterkaitan
- [[Advanced Architecture Patterns]] — Berperan sebagai infrastruktur utama penggerak pemicu (*triggers*) berbasis event - Week 1.docx].
- [[Decorator Pattern Modifiers]] — Digunakan bersama untuk memicu pemasangan atau pelepasan lapisan dekorator pada objek game secara dinamis.