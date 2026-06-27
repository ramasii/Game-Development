# Skill: Spreadsheet Setup for Game Balancing
#game-balance #spreadsheet

## Kapan Harus Menggunakan
Gunakan skill ini saat mulai mengorganisasi ribuan data monster, senjata, atau level di Google Sheets atau Excel agar data terstruktur dengan rumus dinamis dan mudah dibaca oleh tim.

## Deskripsi
Praktik terbaik mengatur anatomi spreadsheet keseimbangan game agar terpusat pada variabel utama dan menghindari kesalahan input manual.

## Alur Kerja (Workflow)
1. **Map Table Anatomy:** Letakkan daftar Objek/Level pada Baris (Rows) dan Atribut/Stat pada Kolom (Columns).
2. **Apply The Golden Rule:** Jangan pernah melakukan *hardcode* angka di dalam sel kalkulasi! Semua harus mereferensikan sel Anchor secara dinamis.
3. **Inject Progress Curves:** Gunakan formula matematika untuk mengisi otomatis pertumbuhan nilai dari Level 1 hingga Max Level.
4. **Simulate Ecosystem:** Lakukan visualisasi grafik atau jalankan simulasi live (misal via Machinations) untuk memastikan ekonomi game stabil.

## 🔗 Hubungan Keterkaitan
- [[Apply Balance Foundations]] — Wadah utama eksekusi kalkulasi numerik framework roda gigi.
- [[Model Progress Curves]] — Menyediakan formula matematis untuk otomatisasi pengisian kolom progres.