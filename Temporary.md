### 1. Router di Game _Mindustry_ (Sistem Distribusi 1x1 Terpusat)

Di _Mindustry_, **Router** adalah _tile_ logistik paling mendasar berukuran 1x1. Berbeda dengan game lain yang memisahkan antara blok konveyor lurus dan blok pembagi, Router di _Mindustry_ berfungsi sebagai _hub_ serbaguna.

#### ⚙️ Cara Kerja Teknis & Logika:

- **Multi-Input & Multi-Output:** Router tidak peduli dari arah mana _resource_ datang. Sisi mana pun yang dialiri oleh konveyor masuk akan dianggap sebagai **Input Port**. Sisi lainnya yang terhubung dengan konveyor keluar atau mesin (seperti turret) akan otomatis menjadi **Output Port**.
    
- **Logika Distribusi Sekuensial (Round-Robin):** Router memiliki internal pointer/counter untuk melacak antrean output.
    
    - _Contoh:_ Jika Router terhubung ke 3 jalur keluar (Kiri, Depan, Kanan), item pertama akan dikirim ke Kiri, item kedua ke Depan, item ketiga ke Kanan, dan item keempat kembali ke Kiri.
        
- **Logika _Backpressure_ (Anti-Macet):** Jika salah satu jalur output penuh (misalnya turret di sisi Kiri sudah penuh peluru), Router tidak akan berhenti memproses. Logikanya akan melakukan _skipping_: jika target output `Kiri` mendeteksi _inventory_ penuh, pointer akan langsung melompat mencari output berikutnya yang kosong (`Depan` atau `Kanan`).
    

#### ⚠️ Kelemahan Desain yang Harus Kamu Hindari (_Router Chains_):

Di _Mindustry_, ada fenomena terkenal bernama _Router Clogging/Chaining_. Jika pemain menempatkan beberapa Router secara berderet tanpa konveyor pembatas (Router menempel dengan Router lain), item berpotensi mengalir **mundur** atau berputar-putar di dalam deretan Router tersebut. Hal ini terjadi karena Router mendeteksi Router di belakangnya sebagai "Output Port" yang valid.

- _Solusi untuk ManaForge:_ Di kode game kamu, pastikan _node_ berjenis Router hanya boleh mengalirkan item ke _node_ yang memiliki tipe `Conveyor` (berarah menjauh) atau `Turret`, dan melarang transfer antar sesama Router secara langsung.