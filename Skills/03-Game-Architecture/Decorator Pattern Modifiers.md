# Skill: Implement Decorator Pattern for Dynamic Modifiers
#architecture #design-pattern

## Kapan Harus Menggunakan
Gunakan skill ini ketika sistem game memiliki perilaku dasar yang stabil (misal: kalkulasi skor dasar atau damage senjata), tetapi membutuhkan penambahan banyak variasi efek/modifier opsional secara dinamis pada saat runtime tanpa menumpuk kondisi `if-else`.

## Deskripsi
Pola desain struktural yang membungkus objek inti dengan objek dekorator lain secara berlapis untuk menambahkan fungsionalitas baru secara modular tanpa mengubah kode kelas asli.

## Alur Kerja (Workflow)
1. **Declare Component Interface:** Buat interface bersama yang mendefinisikan fungsi kalkulasi dasar.
2. **Build Concrete Component:** Implementasikan kelas objek asli yang mengembalikan nilai dasar tanpa modifikasi.
3. **Create Base Decorator:** Buat kelas abstrak yang mengimplementasikan interface yang sama dan menampung referensi objek di dalamnya.
4. **Develop Specific Decorators:** Buat variasi kelas dekorator spesifik (misal `FireDecorator`) untuk memodifikasi atau menumpuk nilai komputasi objek di dalamnya secara kumulatif.

## 🔗 Hubungan Keterkaitan
- [[Advanced Architecture Patterns]] — Menjadi salah satu komponen structural pattern utama dalam mengelola mesin pengubah nilai (modifier engine) - Week 1.docx].
- [[Observer Pattern Events]] — Sering dikombinasikan agar dekorator tertentu terpasang otomatis saat event spesifik dipicu.