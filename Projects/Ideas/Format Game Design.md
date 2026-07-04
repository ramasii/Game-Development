# 📋 Format Game Design Document (GDD)

Gunakan format standar ini untuk mendokumentasikan ide game baru sebelum masuk ke tahap produksi. Format ini dirancang agar setiap proyek memiliki struktur kode yang rapi, modular, dan menerapkan design pattern yang tepat sejak awal.

---

## 🎮 1. Konsep & Identitas Game
*Ringkasan ide game dalam satu paragraf — apa game-nya, kenapa menarik, dan apa yang membuatnya beda.*

- **Premis**: *(Satu kalimat: "Game ini adalah [genre] di mana pemain [aksi utama] untuk [tujuan].")*
- **Genre**: *(Contoh: 2D Action Roguelite, Turn-based Strategy, Physics Puzzle)*
- **Target Platform**: *(Contoh: PC/Steam, Mobile, WebGL)*
- **USP (Unique Selling Point)**: *(Apa yang membedakan game ini dari yang sudah ada di pasar?)*
- **Referensi & Inspirasi**: *(Contoh: "Seperti X tapi dengan twist Y" — bisa disertai link/gambar)*

---

## 🔄 2. Core Gameplay Loop
*Rancang loop inti yang membuat pemain terus bermain.*

- **Loop Utama**: *(Apa yang dilakukan pemain setiap sesi? Aksi → Feedback → Reward → Kembali ke Aksi)*
- **Core Mechanic**: *(Satu mekanik paling inti yang membedakan cara bermain game ini)*
- **Daya Tarik Jangka Pendek**: *(Apa yang bikin pemain nggak bisa berhenti dalam 5 menit pertama?)*
- **Daya Tarik Jangka Panjang**: *(Apa yang bikin pemain balik lagi setelah 10 jam?)*

---

## ⚔️ 3. Mekanik Utama
*Daftar mekanik inti yang mendukung Core Gameplay Loop.*

- **Mekanik 1**: *(Nama & deskripsi singkat)*
- **Mekanik 2**: *(Nama & deskripsi singkat)*
- **Mekanik 3**: *(dst.)*

> Batasi maksimal 3–5 mekanik utama di tahap ini. Tambahkan hanya setelah prototype terbukti fun.

---

## 💻 4. Arsitektur Data & Design Pattern *(Prioritas Utama)*
*Rancang arsitektur data dan pola desain (design pattern) agar struktur kode rapi, modular, dan scalable.*

- **Design Pattern Pilihan**: Tentukan pattern yang akan digunakan sesuai dengan kebutuhan game (contoh: *State Pattern* untuk AI/Player state, *Observer/Signals Pattern* untuk decoupling event, *Factory Pattern* untuk spawning item).
- **Arsitektur & Penyimpanan Data**: *(Menyesuaikan jenis game)* Tentukan bagaimana data disimpan dan dikomunikasikan (contoh: ScriptableObject-based SSOT untuk Unity, Singleton untuk manager tertentu, atau local JSON saving).
- **Mermaid Diagram**: Visualisasikan alur hubungan antar class dan sistem di bawah ini:
    ```mermaid
    graph TD
        %% Bagan visualisasi design pattern dan alur data
    ```

---

## 🏛️ 5. Desain FTUE *(First-Time User Experience)*
*Rancang pengenalan mekanik utama secara intuitif agar pemain langsung paham cara bermain.*

- **Pendekatan FTUE**: *(Menyesuaikan jenis game)* Tentukan metode tutorial yang cocok untuk genre game ini (contoh: struktur *Kishōtenketsu* 4 langkah untuk linear action-platformer, *Contextual UI Hint* untuk game strategi/sandbox, atau *Sandbox Room* untuk roguelite).

---

## 🚀 6. Struktur Folder Modular & Optimisasi Performa
*Rancang struktur project agar scalable dan tentukan fokus optimisasi performa sejak dini.*

- **Struktur Folder (Feature-Based/Modular)**: Kelompokkan asset dan script berdasarkan fitur (misal: Player, Enemy, UI) menggunakan Assembly Definitions (`.asmdef`) agar mudah dikelola dan waktu kompilasi cepat.
- **Rencana Optimisasi**:
    - *CPU/Memori*: (Contoh: Object Pooling untuk spawning objek berulang).
    - *Rendering/UI*: (Contoh: Canvas Splitting untuk UI statis & dinamis).

---

## 📏 7. Scope & Feasibility
*Pastikan ide ini realistis untuk dikerjakan sebelum masuk produksi.*

- **Estimasi Durasi**: *(Contoh: Prototype 2 minggu, MVP 2 bulan)*
- **Ukuran Tim**: *(Contoh: Solo, 2 orang, tim kecil 4 orang)*
- **Risiko Teknis**: *(Apa bagian yang paling sulit secara teknis? Ada teknologi baru yang belum dikuasai?)*
- **Risiko Desain**: *(Apakah core loop-nya sudah terbukti fun? Kapan akan divalidasi lewat playtest?)*
- **Kriteria "Go/No-Go"**: *(Apa kondisi minimum yang harus terpenuhi agar ide ini layak dilanjutkan ke produksi penuh?)*
