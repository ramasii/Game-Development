# 📐 Architectural Drawing Toolkit untuk Level Design
#level-design #architecture #drawing #non-digital-tools

## 🎯 Apa Ini?
Sebelum buka Unity/Blender, Totten nyaranin pakai 5 jenis gambar arsitektural standar buat mikirin ruang level secara cepat & murah di atas kertas dulu. Tiap jenis gambar punya fungsi beda buat "membaca" ruang — dan karena kamu udah biasa gambar di Blender, toolkit ini harusnya kerasa familiar.

## 🧠 Poin Penting

**1. Plan (Tampak Atas)**
Plan digambar dari bidang horizontal, menunjukkan pandangan dari atas — seolah ruang dipotong secara horizontal lalu dilihat dari atas.
> Paling kepake buat layout level: posisi ruangan, jalur pemain, penempatan musuh/item — ini "peta" dasar sebelum masuk ke detail visual.

**2. Section (Potongan Vertikal)**
Section digambar dari bidang vertikal yang memotong ruang, menunjukkan apa yang ada di dalamnya (seolah kamu memotong ruang lalu berdiri di depannya).
> Krusial buat level dengan banyak ketinggian/lantai — misal cek apakah lompatan parkour kamu di Gameseed 2026 secara vertikal masuk akal sebelum dibangun di Unity.

**3. Elevation (Tampak Depan/Samping)**
Elevation digambar dari bidang vertikal yang menunjukkan tampak langsung ke sebuah fasad atau permukaan, tanpa ada pemotongan ruang.
> Beda dari Section: Elevation cuma "muka"-nya doang, nggak nunjukin isi di baliknya. Bagus buat desain landmark/fasad yang jadi penanda arah (mirip prospect di skill sebelumnya).

**4. Axonometric (3D Tanpa Distorsi)**
Axonometric adalah gambar yang merepresentasikan objek 3D dalam 2D tanpa distorsi perspektif — semua garis digambar sesuai skala asli, beda dari perspective yang punya vanishing point.
> Ini "sweet spot" buat komunikasi ke tim: tetap kebaca 3D, tapi ukurannya akurat — cocok buat dokumentasi level/modular kit sebelum di-build di Blender.

**5. Perspective (Sudut Pandang Manusia)**
Perspective adalah konstruksi gambar di mana objek 3D direpresentasikan seperti yang kita lihat di kehidupan nyata — garis horizontal sejajar menyatu di vanishing point.
> Ini yang paling "kerasa" buat mockup mood/atmosfer — gimana pemain bakal ngeliat ruangnya secara langsung, bukan cuma data teknis.

## 🧩 Properties — Kapan Pakai yang Mana

| Jenis Gambar | Bidang Potong | Info yang Didapat | Kapan Dipakai |
|---|---|---|---|
| Plan | Horizontal | Layout, jalur, posisi objek | Awal blocking level, desain rute |
| Section | Vertikal (potong) | Hubungan ketinggian antar ruang | Level berlantai/vertikal, parkour |
| Elevation | Vertikal (tanpa potong) | Tampak fasad/landmark | Desain visual penanda arah |
| Axonometric | 3D non-perspektif | Bentuk 3D akurat skala | Dokumentasi modular kit ke tim |
| Perspective | 3D dengan vanishing point | Pengalaman visual pemain | Mockup mood & atmosfer |

## 🔄 Alur Penerapan

```
Ide level / gameplay goal
        │
        ▼
   PLAN (layout & rute kasar)
        │
        ▼
  Ada elemen vertikal? ──Ya──▶ SECTION (cek hubungan lantai/ketinggian)
        │ Tidak
        ▼
ELEVATION (desain landmark/fasad penanda arah)
        │
        ▼
AXONOMETRIC (dokumentasi 3D akurat buat tim/modular kit)
        │
        ▼
PERSPECTIVE (cek mood & "rasanya" main sebelum masuk Blender/Unity)
```

## 🛠️ Cara Pakai di Workflow Kamu
1. Mulai selalu dari **Plan** — sketsa cepat di kertas/tablet, jangan langsung 3D.
2. Kalau levelnya punya banyak ketinggian (parkour, platform), tambahin **Section** biar nggak salah hitung jarak lompat sebelum dibangun.
3. Pakai **Elevation** buat landmark yang kepake sebagai prospect (lihat [[Prospect & Refuge Spatial Design]]) — fasad ikonik yang kelihatan dari jauh.
4. Sebelum brief ke tim atau bikin modular asset di Blender, rapikan jadi **Axonometric** — biar ukuran antar piece konsisten.
5. Terakhir, gambar 1-2 **Perspective** dari sudut pandang karakter buat ngecek mood sebelum produksi aset final.

## 🔗 Lihat Juga
- [[Prospect & Refuge Spatial Design]] — Elevation/landmark di sini sering berperan sebagai titik prospect.
- [[Level Design Workflow (Whiteblocking & Modular)]] — lanjutan natural dari sketsa manual ke prototipe digital.
