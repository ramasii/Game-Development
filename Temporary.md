Membuat musik untuk game (_Background Music_ atau BGM) dari nol itu sangat bisa dilakukan! Teori musik untuk game sedikit berbeda dengan musik konvensional karena fokus utamanya adalah membangun **atmosfer** dan **looping (putaran lagu tanpa putus)**, bukan sekadar memamerkan teknik yang rumit.

Sebagai pemula, kamu tidak perlu langsung menghafal seluruh buku teori klasik. Cukup kuasai 4 pilar teori musik yang paling krusial untuk kebutuhan game berikut:

## 4 Pilar Teori Musik untuk Game BGM

### 1. Tempo & Rhythm (Pengatur Detak Jantung Game)

Tempo diukur dalam **BPM (Beats Per Minute)** atau jumlah ketukan dalam satu menit. Ini adalah fondasi utama yang menentukan seberapa cepat adrenalin pemain berpacu.

- **60 – 80 BPM (Lambat):** Cocok untuk area eksplorasi, menu utama, atau momen sedih/tenang.
    
- **100 – 120 BPM (Sedang):** Bagus untuk level platformer santai, teka-teki (_puzzle_), atau kota hub.
    
- **140 – 180+ BPM (Cepat):** Wajib untuk _boss fight_, kejar-kejaran, atau fase aksi intens.
    

### 2. Harmoni & Skala (Sakelar Emosi Pemain)

Skala musik (_scale_) adalah kumpulan nada yang berkerabat dekat. Pilih skala yang tepat untuk langsung mengubah psikologis pemain:

- **Major Scale (Tangga Nada Mayor):** Terdengar cerah, bahagia, heroik, dan aman. Cocok untuk desa pertama atau layar kemenangan.
    
- **Minor Scale (Tangga Nada Minor):** Terdengar sedih, misterius, tegang, atau epik. Cocok untuk _dungeon_, malam hari, atau area musuh.

![[Chords Relationships.png]]

Lihat tabel di atas: menggabungkan jenis akor tertentu bisa langsung menciptakan spektrum emosi spesifik, mulai dari _Evil/Ominous_ untuk area bos hingga _Heroic_ untuk sang protagonis.

### 3. Motif atau Leitmotif (KTP dalam Bentuk Suara)

Kamu tidak butuh melodi panjang yang rumit. Cukup buat **Motif**, yaitu rangkaian 3 sampai 5 nada pendek yang sangat mudah diingat.

- _Contoh:_ Coba ingat melodi 5 nada pembuka game _Super Mario Bros_ atau tema _Zelda_. Ketika motif itu diulang-ulang di sepanjang game dengan instrumen berbeda, pemain akan langsung tahu bahwa itu adalah identitas karakter atau area tersebut.
    

### 4. Struktur Looping (Transisi Tanpa Akhir)

BGM game harus bisa berputar terus-menerus tanpa membuat pemain sadar kapan lagu itu berakhir dan mulai kembali. Kuncinya ada pada **Resolusi Nada**:

- Jangan akhiri loop kamu dengan nada dasar (nada _Tonic_) yang terlalu final. Biarkan nada terakhir menggantung agar telinga pemain "menuntut" untuk kembali ke baris pertama lagu.
    

## Alur Membuat Loop BGM Pertama Kamu

Gunakan langkah-langkah terstruktur ini untuk merancang satu lingkaran musik pendek (biasanya 4 atau 8 _bar_ / birama) di dalam software musik pilihanmu:

**1.Tentukan Emosi & Kecepatan:**Langkah 1.

Pilih satu area di game-mu. Misal: "Dungeon Lembab". Atur tempo ke **90 BPM** (agak lambat) agar terasa misterius.

**2.Pilih Skala 'A Minor':**Langkah 2.

Sebagai pemula, gunakan skala **A Minor**. Kenapa? Karena skala ini _hanya_ menggunakan tuts putih pada keyboard/piano (A, B, C, D, E, F, G). Kamu dijamin tidak akan menekan nada yang sumbang!

**3.Buat Progresi Akor (Akor Dasar):**Langkah 3.

Buat 4 kotak atau _bar_. Di setiap kotak, bunyikan 3 nada bersamaan secara konstan sebagai latar belakang (disebut _Chord Pad_). Coba urutan ini: **Am – F – C – G** (masing-masing bunyikan selama 4 ketukan). Ini adalah progresi standar yang sangat kuat.

**4.Tambahkan Melodi dan Motif Sederhana:**Langkah 4.

Di atas akor tadi, buat melodi tipis-tipis menggunakan instrumen utama (misal: suling atau synth). Gunakan prinsip _Leitmotif_: buat 4 nada unik di bar pertama, lalu variasikan sedikit di bar ketiga.

**5.Uji Sambungan Loop:**Langkah 5.

Pastikan ketukan terakhir di bar ke-4 mengalir mulus saat berputar kembali ke bar ke-1. Jika terdengar patah atau mengagetkan, geser nada terakhir agar nadanya lebih dekat dengan nada pembuka di bar 1.

---

Aksi pemain yang dinamis membuat musik di dalam game tidak boleh monoton. Jika pemain diam di tempat selama 10 menit, musiknya tidak boleh terasa membosankan. Begitu juga saat mendadak ada musuh menyerang, musik harus langsung berubah seketika mengikuti ketegangan.

Inilah yang disebut **Adaptive Audio** (atau _Dynamic Audio_), yaitu musik yang aransemen dan strukturnya berubah secara _real-time_ berdasarkan _state_ atau kondisi di dalam game.

## 2 Teknik Dasar Teori Musik Adaptif

Di industri game, ada dua teknik utama yang wajib kamu ketahui untuk memanipulasi file audio agar terdengar dinamis:

### 1. Vertical Remixing (Layering)

Teknik ini membagi satu lagu menjadi beberapa lapisan instrumen terpisah (_stems_) yang berjalan bersamaan dari awal sampai akhir secara sinkron. Game akan mengatur volume dari tiap lapisan ini sesuai situasi.

- **Cara kerja:**
    
    - _Layer 1 (Base):_ Hanya suara drum dan bass (berbunyi saat pemain menjelajah santai).
        
    - _Layer 2 (Melody):_ Suara gitar elektrik masuk/di-_fade in_ (saat musuh mulai mendekat).
        
    - _Layer 3 (Intensity):_ Suara orkestra/synth cepat masuk (saat darah pemain kritis atau _boss fight_ dimulai).
        

### 2. Horizontal Resequencing

Teknik ini memotong lagu secara horizontal menjadi segmen-segmen pendek (misalnya segmen Intro, Eksplorasi, Ketegangan, Pertarungan, dan Victory). Game akan menyambung potongan-potongan ini berdasarkan aksi pemain.

Agar perpindahan segmen tidak terdengar patah, komposer biasanya membuat **Transition Segment** (jembatan nada) atau memanfaatkan ketukan bar berikutnya (_next bar transition_) agar musik tersambung dengan mulus.

## Cara Implementasi di Sisi Game Engine

Kamu tidak perlu memotong audio secara manual lewat kode yang rumit jika memanfaatkan alat yang tepat. Secara umum, ada dua jalur implementasi:

### Jalur A: Menggunakan Audio Middleware (FMOD / Wwise)

Ini adalah standar industri. Kamu mengimpor aset musik ke software _middleware_ ini, lalu mengatur parameter logika di sana (misal: variabel `DangerLevel` dari 0 sampai 100). FMOD/Wwise akan menangani transisi ketukan (_beat-matching_) secara otomatis. Game engine tinggal mengirimkan nilai parameter tersebut lewat kode singkat.

### Jalur B: Menggunakan Fitur Bawaan Game Engine (Native Audio Mixer)

Jika kamu ingin mencoba langsung di dalam game engine tanpa software tambahan, kamu bisa menggunakan sistem **Audio Mixer**. Berikut alur dasarnya:

**1.Ekspor Stems yang Sinkron:**Persiapan Aset.

Saat membuat musik di DAW, ekspor semua layer (Base, Melody, Intensity) dengan **panjang file dan BPM yang persis sama**. Jika berdurasi 1 menit, semuanya harus pas 1 menit.

**2.Gunakan Audio Mixer & Grouping:**Setup Engine.

Buat satu `Audio Mixer` di dalam game engine. Buat beberapa _Group_ atau jalur volume (misal: Jalur_Eksplorasi dan Jalur_Pertarungan). Masukkan file audio masing-masing ke objek `Audio Source` terpisah yang mengarah ke jalur grup mixer tadi.

**3.Mainkan Bersamaan (Play on Awake):**Sinkronisasi.

Picu semua `Audio Source` untuk berjalan bersamaan (`Play()`) di waktu yang sama persis sejak scene dimulai. Setel volume Jalur_Pertarungan ke nilai paling rendah (-80 dB) agar tidak terdengar dulu.

**4.Gunakan Lerp untuk Transisi Volume:**Logika Kode.

Ketika mendeteksi musuh memasuki area pemain via kode (misalnya fungsi `OnTriggerEnter`), jalankan fungsi interpolasi seperti `Mathf.Lerp` atau fungsi transisi snapshot mixer untuk menaikkan volume Jalur_Pertarungan secara perlahan dan menurunkan Jalur_Eksplorasi.

> **Tips Penting:** Untuk pemula yang baru mencoba teknik _Vertical Layering_, pastikan semua instrumen di layer yang berbeda menggunakan **kunci nada (Key) yang sama**. Jika Layer 1 bermain di A Minor, Layer 2 juga wajib di A Minor agar saat volumenya dinaikkan tidak terjadi tabrakan nada sumbang.

---

Melakukan transisi audio yang pas dengan ketukan (_beat-matching_) tidak bisa menggunakan logika _coding_ biasa seperti fungsi `Update()` atau `deltaTime`. Kenapa? Karena _frame rate_ game bersifat dinamis (bisa naik-turun), sedangkan audio berjalan di sirkuitnya sendiri secara konstan pada tingkat sampel (_sample rate_ contohnya 44.100 sampel per detik).

Jika kamu memicu lagu baru menggunakan deteksi waktu _frame_ biasa, jeda sekecil mili-detik pun akan membuat telinga pemain mendengar ketukan yang "patah" atau meleset (_off-beat_).

Untuk mengatasinya, kita menggunakan prinsip **Matematika Waktu Musik** dan **DSP (Digital Signal Processing) Scheduling**. Berikut adalah cara kerja logika dan matematikanya.

## 1. Prinsip Matematika Waktu Musik

Komputer membaca waktu dalam satuan **detik**, sedangkan musisi membaca waktu dalam satuan **BPM (Beats per Minute)** dan **Bar (Birama)**. Kita harus menjembatani keduanya.

Misalkan lagu game kamu memiliki tempo **120 BPM** dengan birama **4/4** (artinya ada 4 ketukan dalam 1 bar).

### Rumus 1: Menghitung Durasi per Ketukan (Beat)

$$\text{Duration per Beat (detik)} = \frac{60}{\text{BPM}}$$

- _Contoh (120 BPM):_ $\frac{60}{120} = 0.5$ detik per ketukan.
    

### Rumus 2: Menghitung Durasi per Bar (Birama)

$$\text{Duration per Bar (detik)} = \text{Duration per Beat} \times \text{Jumlah Ketukan per Bar}$$

- _Contoh (Birama 4/4):_ $0.5 \times 4 = 2.0$ detik per bar.
    

Artinya, setiap kelipatan 2 detik (detik ke-0, 2, 4, 6, dst.), lagu tersebut berada di posisi awal bar baru. Di sinilah waktu terbaik untuk memotong atau menyambung musik.

## 2. Logika Koding: Kuantisasi Waktu (Quantization)

Ketika pemain menekan tombol atau memicu _state_ bertarung di tengah-tengah permainan, game engine tidak boleh langsung memutar musik baru saat itu juga. Game engine harus menunggu hingga ketukan berikutnya tiba. Konsep ini disebut **Kuantisasi**.

### Logika Menghitung Ketukan Terdekat

Misalkan lagu sudah berjalan selama $t = 3.3$ detik. Di manakah bar berikutnya agar kita bisa berpindah musik dengan mulus?

1. **Hitung bar yang sudah terlewat:** $\frac{3.3 \text{ detik}}{2.0 \text{ detik per bar}} = 1.65 \text{ bar}$.
    
2. **Bulatkan ke atas (Ceil):** $\text{ceil}(1.65) = 2 \text{ bar}$.
    
3. **Target waktu transisi:** $2 \text{ bar} \times 2.0 \text{ detik} = 4.0 \text{ detik}$.
    

Jadi, kode kamu akan menjadwalkan musik pertempuran untuk mulai berbunyi tepat pada detik ke-4.0, menciptakan transisi mulus tanpa menginterupsi ritme yang sedang didengar pemain.

## 3. Implementasi Kode (Contoh Kasus: Unity C#)

Untuk akurasi audio yang absolut, gunakan sistem waktu DSP engine. Di Unity, kita menggunakan `AudioSettings.dspTime` (bukan `Time.time`) bersama dengan fungsi `PlayScheduled()`. Fungsi ini memberi tahu kartu suara komputer untuk memutar audio tepat pada waktu spesifik di masa depan, melewati sistem _frame rate_ game.

Berikut adalah cetak biru (_blueprint_) logika kodingnya:

C#

```
using UnityEngine;

public class BeatManager : MonoBehaviour
{
    public AudioSource explorationAudio;
    public AudioSource combatAudio;

    public float bpm = 120f;
    private float beatsPerBar = 4f;
    
    private float barDuration;
    private double songStartTime;

    void Start()
    {
        // 1. Hitung durasi satu bar dalam detik
        barDuration = (60f / bpm) * beatsPerBar;

        // 2. Catat waktu DSP saat lagu pertama dimulai
        songStartTime = AudioSettings.dspTime;
        
        explorationAudio.Play();
    }

    // Fungsi ini dipanggil ketika area berubah menjadi penuh musuh
    public void SwitchToCombat()
    {
        // 3. Ambil waktu DSP saat ini
        double currentTime = AudioSettings.dspTime;
        double timeElapsed = currentTime - songStartTime;

        // 4. Hitung kapan bar selanjutnya akan tiba (Matematika Kuantisasi)
        double barsPlayed = System.Math.Floor(timeElapsed / barDuration);
        double nextBarTime = songStartTime + ((barsPlayed + 1) * barDuration);

        // 5. Jadwalkan lagu pertempuran tepat di ketukan bar baru tersebut
        combatAudio.PlayScheduled(nextBarTime);

        // 6. Matikan atau fade-out lagu eksplorasi tepat di waktu yang sama
        explorationAudio.SetScheduledEndTime(nextBarTime);
    }
}
```

### Mengapa Logika ini Sangat Ampuh?

Dengan metode `PlayScheduled(nextBarTime)`, komputer sudah memproses data audio beberapa milidetik sebelum lagu berbunyi. Ketika waktu tepat menyentuh target, instrumen baru akan masuk seirama dengan ketukan drum dari lagu sebelumnya, membuat perpindahan sekstrem apa pun terasa natural di telinga pemain.