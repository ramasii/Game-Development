# Skill: Manage State Separation (Runtime Separation)
#architecture #state

## Kapan Harus Menggunakan
Gunakan skill ini saat merancang arsitektur data game (terutama tipe game roguelike atau modifier-heavy) untuk menentukan batas siklus hidup variabel agar proses reset data mudah, mencegah bug kebocoran efek, dan mempermudah debugging.

## Deskripsi
Arsitektur pengelolaan data dengan memisahkan state berdasarkan durasi hidupnya (*lifecycle*), kepemilikan (*ownership*), serta ruang lingkup mutasinya (*mutation scope*).

## Alur Kerja (Workflow)
1. **Identify System Variables:** Daftarkan semua variabel yang dibutuhkan oleh core gameplay loop.
2. **Layer Lifecycle Classification:** Kelompokkan variabel ke dalam 3 layer: Persistent (satu run penuh), Runtime (satu babak/blind), atau Temporary (satu aksi/kalkulasi instan).
3. **Enforce Boundary Rule:** Buat pembatas ketat agar logika skor instan (Temporary) tidak memodifikasi langsung data progresi makro (Persistent) untuk menghindari *hidden coupling*.
4. **Implement Unified Session Structure:** Susun struktur data terpadu seperti `RunSessionState` yang memisahkan objek sub-state secara modular.

## 🔗 Hubungan Keterkaitan
- [[Identify Core Loops]] — Menentukan kapan state runtime di-reset dan kapan data temporary dihancurkan.
- [[Layered Architecture Stabilization]] — Membantu memisahkan elemen baku (invariant) dari elemen yang terus bermutasi sepanjang runtime.