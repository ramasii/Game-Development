# 🎮 Designing FTUE (First-Time User Experience)
#game-design #ftue #onboarding #user-experience

## 🎯 Apa Ini?
Kerangka kerja dan metodologi desain untuk merancang **First-Time User Experience (FTUE)** yang intuitif, mengajarkan mekanik tanpa membuat pemain merasa digurui, serta menahan tingkat retensi pemain di menit-menit kritis pertama.

**Kapan harus menggunakan skill ini:**
- Saat merancang alur tutorial, sekuens pembuka (prologue/intro), dan pengenalan mekanik utama (*core gameplay*) game baru.
- Ketika game memiliki *cognitive overload* tinggi (banyak tombol UI, menu, atau mekanik) yang butuh pembatasan info (*scaffolding*).
- Untuk meningkatkan retensi pemain di awal sesi bermain pertama.

## 🧠 Konsep Utama

### 1. Flow State (Mihaly C.)
Menjaga keseimbangan dinamis antara tingkat kesulitan rintangan (*challenge*) dan keterampilan pemain (*skill*).
*   **Zona Flow**: Sesi di mana pemain tidak merasa bosan (tantangan terlalu mudah) dan tidak frustrasi (tantangan terlalu sulit).
*   **Implementasi**: Tingkat kesulitan naik secara bertahap seiring berjalannya sesi FTUE.

### 2. Show, Don't Tell (Just-in-Time Information)
Manusia belajar lebih cepat melalui aksi kinestetik langsung daripada membaca manual teks yang panjang (*infodump*).
*   **Just-in-Time Info**: Memberikan informasi tepat saat pemain membutuhkannya.
*   **Penerapan**: Daripada memunculkan pop-up teks, letakkan rintangan (misal: jurang) di depan pemain dan munculkan ikon prompt tombol kecil hanya saat pemain mendekatinya.

### 3. Hook, Line, & Sinker Framework
Struktur psikologis terarah untuk memikat perhatian emosional pemain dalam hitungan menit pertama.
*   **Hook (30 Detik Pertama)**: Umpan visual yang memukau, misteri cerita yang kuat, atau sekuens aksi instan tanpa hambatan.
*   **Line (Core Loop Introduction)**: Mengenalkan core loop utama lewat tantangan kecil yang menarik dan intuitif.
*   **Sinker (Retention Loop)**: Memberikan hadiah bernilai (*reward*), memicu rasa penasaran cerita (*cliffhanger*), atau membuka akses gameplay baru yang membuat pemain ingin terus bermain.

---

## ⚙️ Strategi Praktis Desain

### 1. Core Loop Focus
*   Sembunyikan atau kunci fitur-fitur kompleks (misalnya menu perdagangan makro, kustomisasi tingkat lanjut) di awal.
*   Fokus pada *putaran utama* (contoh: di *city-builder*, cukup ajarkan cara membangun satu rumah dan memanen uang dari rumah tersebut).

### 2. Power Fantasy (Prolog Kilas Balik)
*   Pinjamkan karakter/senjata dengan level kekuatan tertinggi di 5 menit pertama game.
*   Rampas kekuatan tersebut karena tuntutan narasi (misal: diserang bos, atau kehilangan ingatan). Ini memberi bayangan seberapa keren karakter pemain jika mereka melanjutkan game.

### 3. Scaffolding (Alat Bantu UI Bertahap)
*   Matikan atau nonaktifkan tombol-tombol UI yang belum diperlukan pemain baru agar tidak memicu kebingungan visual (*cognitive overload*).
*   Aktifkan tombol satu per satu sesuai dengan progress tutorial pemain.

### 4. Skip Button (Tombol Lewati)
*   Sediakan opsi skip tutorial. Selalu hargai pemain veteran atau pemain yang sedang membuat akun cadangan (*alt accounts*).

---

## 🧩 Matriks Desain FTUE

| Elemen FTUE | Tujuan | Contoh Penerapan |
|---|---|---|
| **Core Loop Focus** | Mengurangi beban kognitif di awal | Sembunyikan tombol UI perdagangan, fokus ke tombol buat & panen saja |
| **Power Fantasy** | Memberikan aspirasi jangka panjang | Pemain memainkan karakter level 100 di prolog sebelum kembali ke level 1 |
| **Scaffolding** | Memandu fokus secara visual | Mengunci menu opsi/shop selama tutorial berjalan |
| **Skip Button** | Mencegah frustrasi veteran | Menampilkan tombol "Skip Tutorial" di sudut kanan atas layar |

---

## 🔄 Alur Penerapan (FTUE Flowchart)

```
Sesi Pertama Dimulai (Hook) -> Aksi Instan / Visual Memukau
                                 │
                                 ▼
Prolog / Power Fantasy (Opsional) -> Kenalkan kekuatan penuh
                                 │
                                 ▼
Kehilangan Kekuatan (Narrative Reset) -> Kembali ke level dasar
                                 │
                                 ▼
Fokus Core Loop + Scaffolding -> UI dikunci, hanya tampilkan tombol krusial
                                 │
                                 ▼
Show, Don't Tell -> Beri tantangan kinestetik dengan Just-in-Time Info
                                 │
                                 ▼
Sinker -> Berikan Reward pertama / Cliffhanger cerita -> Unlock full UI
```

---

## 🛠️ Langkah Eksekusi (Cara Pakai)

**Langkah 1: Tentukan Hook & Pembuka Sesi**
1. Buat pemain langsung melakukan aksi dalam 30 detik pertama (misal: melarikan diri dari reruntuhan, atau bertarung dengan kontrol dasar).
2. Hindari halaman registrasi panjang, logo developer yang tak bisa di-skip, atau cutscene membosankan di detik pertama.

**Langkah 2: Terapkan Scaffolding pada UI**
1. Identifikasi tombol-tombol yang tidak diperlukan pada 10 menit pertama.
2. Di Unity, buat fungsi untuk menonaktifkan (*SetActive(false)*) atau memburamkan panel-panel tersebut secara dinamis.

**Langkah 3: Rancang Sekuens Pengenalan Core Loop**
1. Tulis diagram alur sederhana berisi aksi-reaksi dasar game Anda.
2. Ajarkan aksi paling dasar dahulu. Jangan kenalkan mekanik B sebelum mekanik A dipraktikkan langsung oleh pemain.

**Langkah 4: Integrasikan Tombol Skip**
1. Letakkan tombol "Skip Tutorial" di area yang mudah diakses namun tidak mudah tertekan secara tidak sengaja.
2. Pastikan ketika tombol ditekan, status pemain langsung dialihkan ke status "Post-Tutorial" dan seluruh UI terbuka.

---

## 🔗 Lihat Juga
- [[Deconstruct Mechanics]] — gunakan untuk mendefinisikan kontrol dasar sebelum mengajarkannya di FTUE.
- [[Identify Core Loops]] — gunakan untuk memastikan core loop yang diajarkan di FTUE adalah loop yang tepat.
- [[Tutorial Level Building Blocks]] — gabungkan dengan konsep Montessori dan Constructivist untuk menyusun ruangan fisik/level tutorialnya.
