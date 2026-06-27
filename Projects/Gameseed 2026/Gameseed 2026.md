# 📋 Dokumen Proyek: TTT: Never Late Go to School

## 1. Konsep Utama & Narasi

- **Genre:** 3D Platformer Parkour dengan elemen komedi.
- **Objektif Utama:** Pemain harus mencapai gerbang sekolah tepat waktu sebelum bel masuk berbunyi pada pukul 07:00.
- **Tema Utama:** Mengangkat urgensi dan tekanan waktu yang diterjemahkan ke dalam mekanisme _gameplay_ yang cepat dan menantang.

## 2. Mekanik Gameplay (Core Mechanics)

- **Pergerakan Fisika:** Kontrol karakter berbasis _Rigidbody_ yang responsif untuk memastikan pergerakan terasa instan dan dinamis.
- **Sistem Stomp (Mario-Style):** Kemampuan menginjak kepala musuh (_furry_) untuk menyingkirkan rintangan sekaligus mendapatkan dorongan lompatan vertikal ekstra.
- **Wall Run:** Mekanik berlari di dinding untuk melewati celah atau area yang tidak bisa dilalui dengan jalan kaki biasa.
- **Floating Platforms:** Pijakan yang bersifat interaktif dan dinamis, di mana platform akan miring secara fisik sesuai dengan berat dan posisi karakter saat menginjaknya.

## 3. Estetika Visual & Karakter

- **Art Style:** Mengadopsi gaya **3D Flat Cartoon / Cel-shaded** dengan inspirasi utama dari estetika _Crowscape Zero_.
- **Arsitektur Dunia:** Lingkungan kota didesain dengan bentuk yang asimetris, miring, dan distorsi (_wonky_) untuk memperkuat kesan komikal dan hidup.
- **Desain Karakter:** Karakter utama dan musuh menggunakan proporsi **Chibi 3D** (kepala besar, tubuh mungil) dengan berbagai variasi kostum seperti _Punk Rocker_, _Nature Druid_, _Winter Frost_, dan _Cyborg Prototype_.
- **Teknik Rendering:** Menggunakan _Custom Toon Shader_ di Unity Shader Graph dan _Fullscreen Outline_ berbasis _depth_ serta _normals_ untuk menghasilkan garis tepi komik yang tegas.

## 4. Desain Level (Alley/Gang Sempit)

Level pertama disusun secara linear dengan pembagian segmen kesulitan yang progresif:

- **Segmen Pemanasan:** Pengenalan lompatan dasar melalui rintangan tumpukan sampah dan got terbuka.
- **Segmen Intensitas Tinggi:** Menghadirkan tantangan parkour di atas mobil angkot yang parkir melintang dan refleks menghindari motor kurir dari arah depan.
- **Segmen Panic Zone:** Area klimaks dekat gerbang sekolah yang penuh dengan gerombolan musuh, tantangan _wall-run_ miring, dan rintangan kabel rendah.

## 5. Arsitektur Teknis (Unity 6)

- **Kamera:** Menggunakan _Cinemachine Free Look_ yang dioptimalkan dengan _Smart Update_ untuk mencegah _stuttering_ serta fitur _Recomposer_ untuk efek miring (_tilt_) kamera saat _wall-run_.
- **Struktur Kode:** Menerapkan prinsip _Clean Code_ dengan _Singleton GameManager_, _Observer Pattern_ (Event-driven) untuk sistem menang/kalah, dan pemisahan logika audio menggunakan _ScriptableObjects_.
- **VFX & Audio:** Implementasi partikel debu saat mendarat (_stomp_) dan sistem SFX modular yang memiliki variasi _pitch_ serta volume acak untuk meningkatkan _game feel_.

## 6. Antarmuka Pengguna (UI/HUD)

- **Gaya UI:** Mengusung tema **Comic Pop** dengan bentuk-bentuk panel asimetris dan warna kontras solid.
- **Fitur Spesial:** Jam weker panik yang bergetar saat waktu kritis dan teks pop-up on-screen seperti "STOMP!" atau "LATE!" untuk memperkuat elemen narasi komedi.