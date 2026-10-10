# GDD - Drift Master

> *Drift Boss-style — arcade endless 3D satu tombol.
> Hub proyek: [[Projects/Drift Master/Drift Master|Drift Master]]

## 1. Konsep

Game arcade endless 3D dengan kontrol satu tombol. Mobil jalan otomatis di atas jalur melayang berbelok-belok. Pemain harus ganti arah dengan timing tepat supaya tidak jatuh ke luar jalur. Makin jauh, makin tinggi skor.

**Genre:** Arcade / hyper-casual, endless runner  
**Platform:** WebGL dan Mobile  
**Sesi main:** 30 detik sampai 3 menit

## 2. Pilar Gameplay

- **Mudah dipelajari:** satu input, tanpa tutorial panjang.
- **Sulit dikuasai:** timing menentukan semuanya, dan ritme jalan makin cepat.
- **Retry instan:** mati langsung bisa main lagi dalam kurang dari 2 detik.

## 3. Kontrol

|Input|Aksi|
|---|---|
|Tahan (klik / spasi / sentuh)|Mobil drift ke **kanan**|
|Lepas|Mobil drift ke **kiri**|

Tidak ada gas dan rem. Kecepatan maju diatur sistem.

## 4. Core Mechanics

**a. Auto-forward + diagonal drift**

- Mobil bergerak maju konstan. Input hanya mengganti arah diagonal (kanan atau kiri), jadi jalurnya zig-zag.
- Ganti arah memicu animasi drift (body miring, efek asap, suara gesekan ban).

**b. Jalur melayang (platform track)**

- Jalur terdiri dari segmen/tile yang tersambung, dengan belokan tajam, tikungan beruntun, dan lebar jalur yang bervariasi.
- Tidak ada pagar pembatas. Kalau roda keluar dari jalur, mobil jatuh dan **game over**.

**c. Fail condition**

- Mobil keluar jalur lalu jatuh ke ruang kosong, kamera mengikuti sebentar, lalu muncul layar game over.

**d. Skor**

- Skor naik per jarak tempuh atau per segmen yang dilewati.
- Bonus kecil untuk koin yang diambil. Best score disimpan lokal.

**e. Koin**

- Koin diletakkan secara acak di sepanjang jalur, sebagian sengaja di dekat tepi sebagai risk/reward.
- Koin dipakai untuk unlock kendaraan.

**f. Kenaikan kesulitan**

- Kecepatan naik bertahap per jarak tertentu.
- Jalur makin sempit, dan belokan makin rapat dan beruntun.
- Ada jeda "napas" berupa segmen lurus panjang supaya tidak melelahkan.

## 5. Core Loop

```
Main → Jalan jauh & kumpulin koin → Jatuh (Game Over)
  → Skor + koin masuk → Unlock kendaraan → Main lagi
```

## 6. Progression & Meta

- **Kendaraan unlockable:** mobil biasa, taksi, polisi, truk es krim, dan lainnya. Pembedanya terutama visual, dan sebagian bisa punya handling berbeda (lebar drift, kecepatan belok).
- **Daily reward:** hadiah login harian yang naik tiap hari berturut-turut.
- **Booster:** double score, car insurance (revive sekali), coin rush.
- **Spin wheel:** hadiah acak setelah selesai main.

## 7. Level / Track Generation

- **Procedural endless:** segmen diambil dari pool prefab (lurus, belok kiri, belok kanan, zig-zag, jalur sempit) lalu disambung secara runtime.
- Bobot pemilihan segmen mengikuti kurva kesulitan: makin jauh, makin banyak segmen sulit.
- Segmen yang sudah dilewati dihapus atau di-recycle dengan object pooling.

## 8. Visual & Audio

- Low-poly dengan warna cerah, platform berpola kotak-kotak dan berwarna-warni di atas latar kosong (space/void).
- Kamera isometrik/third-person dari atas, mengikuti mobil dengan smoothing.
- SFX drift, koin, dan jatuh. BGM energik tapi tidak mengganggu.

## 9. UI Minimal

- **HUD:** skor, jumlah koin.
- **Game Over:** skor, best score, tombol Retry, tombol ke garage.
- **Garage:** pilih dan unlock kendaraan.

## 10. Catatan Implementasi (Unity)

- Gerak mobil: velocity maju konstan, arah diganti lewat rotasi/heading target dengan lerp supaya terasa drift.
- Deteksi jatuh: raycast ke bawah dari mobil atau cek posisi Y. Kalau tidak ada ground, trigger game over.
- Segmen jalur: prefab dengan titik sambung (entry/exit point) supaya mudah disusun.
- Object pooling untuk segmen dan koin, penting untuk performa WebGL/mobile.
- Parameter yang dibuat tweakable via ScriptableObject: kecepatan, sudut drift, kurva kesulitan.
