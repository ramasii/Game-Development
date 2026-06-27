# Skill: Establish Mathematical Anchors for Game Balancing
#game-balance #math

## Kapan Harus Menggunakan
Gunakan skill ini ketika pertama kali menginput data angka numerik untuk data senjata, HP musuh, atau kurva progresi kesulitan menggunakan metode Top-Down/Bottom-Up agar angka dasar game solid dan tidak asal tebak.

## Deskripsi
Proses menentukan angka acuan baku (*Anchor*) yang menjadi dasar dari seluruh kalkulasi matematika lainnya dalam sistem permainan.

## Alur Kerja (Workflow)
1. **Lock Primary Anchor:** Pilih satu variabel penting dan kunci nilainya sebagai patokan mati (misal: HP level 1 = 100).
2. **Choose Design Methodology:** Tentukan pendekatan Top-Down (mengejar target gameplay/waktu) atau Bottom-Up (menyusun dari komponen kecil).
3. **Calculate TTK & Stat Ratios:** Hitung *Time to Kill* (TTK) serta perbandingan Cost vs Utility.
4. **Apply Rule of 2x:** Jika angka terasa tidak pas saat testing, ubah nilai secara ekstrem sebesar 2x lipat untuk melihat kontrasnya, baru kemudian persempit.

## 🔗 Hubungan Keterkaitan
- [[Model Progress Curves]] — Angka jangkar ini akan dikembangkan ke level tinggi menggunakan kurva matematika.
- [[Spreadsheet Setup]] — Tempat mengunci rumus jangkar agar tabel bersifat dinamis tanpa *hardcoding*.