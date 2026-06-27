
## 📐 Standar Format File Skill (Template)

Setiap file skill baru wajib mengikuti anatomi struktur **Modular Technical Documentation** berikut agar ekosistem *second brain* tetap konsisten:

### 1. Title Header (H1)
*   **Format**: `# [Emoji] [Nama Komponen/Materi] ([Konteks/Framework])`
*   *Contoh*: `# 📝 HLSL Outline Shader (Depth & Normal Edge Detection)`

### 2. Deskripsi Singkat (`## 🎯 Apa Ini?`)
*   Section wajib di awal catatan yang selalu menggunakan emoji target (`🎯`).
*   Berisi 1–2 kalimat rangkuman mengenai fungsi utama komponen atau materi tersebut.

### 3. Komponen & Arsitektur Teknis (H2 - Menyesuaikan Materi)
Gunakan sub-section modular di bawah ini untuk membedah isi materi:
*   `## 📚 Konsep Utama` atau `## 🧠 Poin Penting`: Untuk menaruh potongan kode (*code snippets*) atau teori dasar desain.
*   `## 🧩 Properties (Inspector)`: Gunakan format **Markdown Table** untuk memetakan variabel inspector atau data spreadsheet.
*   `## 🔄 Alur Lengkap`: Gunakan diagram teks/ASCII sederhana (`→`) jika materi memiliki alur logika data loop.
*   *Catatan Tambahan*: Gunakan fitur Blockquotes (`>`) tepat di bawah blok kode untuk memberikan anotasi atau penjelasan instan.

### 4. Panduan Eksekusi/Integrasi (`## 📦 Cara Pasang...` atau `## 🛠️ Cara Pakai...`)
*   Menggunakan emoji paket (`📦`) atau perkakas (`🛠️`).
*   Berisi urutan langkah konkret (Numbered List `1, 2, 3`) untuk mengimplementasikan skill tersebut di Unity maupun spreadsheet.

### 5. Jaringan Otak Kedua (`## 🔗 Lihat Juga`)
*   Section penutup wajib di bagian paling bawah catatan dengan emoji link (`🔗`).
*   Berisi daftar **Obsidian WikiLinks (`[[Nama Catatan]]`)** untuk menghubungkan file ini dengan pengetahuan atau skill relevan lainnya.
