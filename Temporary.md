# Mini Game Design Document: Aether Conduit

Project Status: Concept Locked / Pre-Production Lead Designer: (Anda) Date: [Hari Ini]

---

## 1. Executive Summary

- Genre: Tower Defense (TD) / Roguelite / Puzzle Strategy
- Target Audiens: Pemain dewasa (18+) yang menyukai tantangan strategis, _deep mechanics_, dan genre _puzzle solving_ yang membutuhkan perencanaan jangka panjang (Misalnya: Pemain yang menyukai _Into the Breach_, _Slay the Spire_, dan _Factorio_).
- High Concept: Anda adalah konduktor energi dalam jaringan antar-dimensi, dipaksa untuk mempertahankan sumber daya vital dari ancaman entropi dengan cara memanipulasi aliran waktu itu sendiri.
- Unique Selling Point (USP): Penggantian fokus pertahanan dari sekadar penempatan _tower_ menjadi manajemen kondisi waktu (mengatur kapan, di mana, dan bagaimana energi harus berinteraksi). Pemain harus berpikir seperti ahli fisika yang mengatasi _bug_ di kode semesta.

## 2. Gameplay & Mechanics

- Core Loop:
    1. Preparation (Setup): Pemain menempatkan berbagai _Conduits_ dan _Utility_ di jalur pertahanan.
    2. Absorption (Build-Up): Pemain mengumpulkan _Stabilizer Charge_ dan mengamati pola serangan musuh menggunakan Echo.
    3. Crisis (Intervention): Ketika gelombang serangan besar datang, pemain harus menggunakan Phase Shifting untuk bertahan dan mengisolasi ancaman.
    4. Climax (Resolution): Menggunakan **Resonance Cascade** untuk melipatgandakan kerusakan dan membersihkan gelombang musuh. 5. Advance: Jika pemain berhasil bertahan, mereka akan maju ke _node_ berikutnya, menerima peningkatan _meta-progress_ permanen, dan memulai _run_ baru dengan tantangan baru.

- Player Verbs Utama: Shift, Link, Sense
- Rincian Mekanik:
    - Phase Shifting: (Defense) Memungkinkan pemain mengisolasi _conduit_ dari ancaman temporer. Menggunakan _Stabilizer Charge_. Risiko: _Cooldown_ Utility.
    - Resonance Cascade: (Offense) Melipatgandakan efektivitas pertahanan dengan menautkan utilitas yang berbeda. Mekanisme kunci: _Multiplier_ berdasarkan jumlah _link_ unik.
    - Echo Reading: (Utility/Information) Membaca jejak waktu untuk mendapatkan pengetahuan krusial tentang pola serangan musuh yang akan datang. Sumber informasi utama di setiap _run_.
- Win / Lose Condition:
    - Win: Berhasil melewati gelombang musuh dengan _Disruption Level_ total yang terlampaui, mengaktifkan _exit conduit_.
    - Lose: Semua _conduit_ utama rusak total atau _Stabilizer Charge_ habis total sebelum gelombang berikutnya tiba.

## 3. World & Entities

- Latar Belakang Cerita: Semesta energi vital (Aether) yang menopang eksistensi berbagai dimensi berada dalam keadaan entropi. Pemain adalah Konduktor terlatih yang ditempatkan di Jaringan Arus Energi (The Great Conduit), yang kini dipenuhi oleh retakan temporal. Tugas mereka adalah menjaga integritas jaringan dari _The Void Recursion_—kekuatan yang ingin menghapus konsep dan waktu itu sendiri—dengan memulihkan dan menyinkronkan aliran energi.
- Profil Entitas & Karakter:

|Entitas|Peran|Stat Utama|Mekanik Kunci|
|---|---|---|---|
|Echo (NPC Pendamping)|Utility Support / Guide|Wawasan Temporal (Temporal Insight)|Echo Reading: Memproyeksikan pola ancaman masa depan, memberikan informasi kritis yang mengubah strategi pertahanan.|
|The Void Recursion (Boss)|Existential Threat|Tingkat Disrupsi (Disruption Level)|Causality Collapse: Tidak menyerang secara fisik, melainkan merusak aturan sistem itu sendiri (membatalkan _Resonance_ atau mengubah _Phase Shift_), memaksa pemain _adapt_ secara total.|

## 4. Technical Scope & Engine Recommendation

- Platform Utama: PC (Steam)
- Rekomendasi Game Engine: Unity
- Alasan:
    1. Visual Complexity: Karena desain kita melibatkan interaksi sistem yang kompleks (fisika energi, _particle effects_ untuk _Resonance_, visualisasi _time stream_), Unity memiliki _toolset_ yang lebih matang untuk _VFX_ (Visual Effects) dan _Shader Programming_ yang kompleks.
    2. Cross-Platform: Unity menawarkan dukungan ekosistem yang sangat luas, memastikan kemudahan adaptasi ke platform lain jika diperlukan di masa depan.
    3. Community Support: Jumlah aset, tutorial, dan _developer_ siap pakai di Unity sangat besar, mempercepat fase prototipe untuk sistem mekanik yang rumit.

## 5. Persiapan - Game Brief & Visual Lock (Praktikum Minggu 3 - disesuaikan dengan GDD)

| Elemen         | Keputusan Desain (disesuaikan dengan GDD Aether Conduit)                                                              |
| -------------- | --------------------------------------------------------------------------------------------------------------------- |
| Game Brief     | Aether Conduit                                                                                                        |
| World          | The Great Conduit - jaringan arus energi antar-dimensi yang retak temporal, Aether vs The Void Recursion              |
| Main Character | Konduktor - teknisi / kurir energi muda, pemelihara aliran waktu, pembawa Stabilizer Charge dan Echo Reader           |
| Visual Style   | Stylized 2D game illustration; clean shapes; readable silhouette; mystical-tech hand-painted + glowing energy accents |
| Palette        | Void Indigo (navy), Stabilizer Gold, Aether Teal, Bone Cream, Entropic Terracotta                                     |
| Target         | Karakter mudah dikenali di tengah VFX ramai dan dapat diturunkan menjadi sprite 2D / portrait UI                      |
| Constraint     | Tidak menggunakan logo, teks, atau karakter tambahan pada character concept; kostum sederhana, no photoreal           |

## Visual Lock - Atribut yang tidak boleh berubah (sesuai GDD)
- rambut hitam pendek
- jaket mantle indigo dengan piping teal menyala (Phase Shift gear)
- scarf teal panjang (visualisasi time-stream / Echo Reading)
- tas selempang cokelat dengan selang conduit kecil (pembawa Stabilizer Charge)
- kompas kuningan kecil / stabilizer core pada belt
- sepatu boots cokelat tempur
- silhouette sederhana dan mudah dibaca
- proporsi karakter stylized ramping (5-6 kepala), cocok untuk sprite
