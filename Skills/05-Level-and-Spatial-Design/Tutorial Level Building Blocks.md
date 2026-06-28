# 🚪 Tutorial Level Building Blocks
#level-design #tutorial #onboarding #teaching-theory

## 🎯 Apa Ini?
Kerangka khusus Totten buat desain level pertama/tutorial — gabungan building block **spasial** (gimana ruang disusun) dan building block **perilaku** (gimana pemain "dibentuk" lewat reward & teori belajar), plus metode mengajar mekanik tanpa teks lewat 3 gaya "advertising" (demonstrative/illustrative/associative).

## 🧠 Poin Penting

### A. Spatial Building Blocks

**Scenes**
Level tutorial dipecah jadi scene-scene kecil, masing-masing fokus mengajarkan 1 hal saja — biar tidak overload pemain di menit pertama.

**Portals & Thresholds**
Portal = gerbang/bukaan fisik yang menandai pemain masuk ke area/fase baru. Threshold = momen/garis transisi itu sendiri — secara arsitektural, melewati threshold itu psikologis: pemain "merasa" sudah pindah konteks (dari menu ke gameplay, dari tutorial ke level sungguhan).
> Threshold yang jelas (pintu besar, perubahan cahaya, perubahan musik) bikin pemain otomatis siap-siap mode baru tanpa perlu teks "Selamat datang di level 1".

**Controlled Approaches**
Teknik arsitektur klasik: jalur menuju sebuah ruang penting dibuat bertahap & terarah (menyempit lalu melebar, reveal sebagian dulu) untuk membangun antisipasi sebelum pemain benar-benar masuk — bukan langsung "melempar" pemain ke tengah ruang besar.

**Meeting Spaces**
Titik di mana jalur-jalur bertemu — biasanya menjadi tempat checkpoint, NPC pemberi info, atau hub kecil sebelum pemain lanjut ke cabang berikutnya.

### B. Behavioral Building Blocks

**Rewards in Tutorials & Access sebagai Reward**
Di level tutorial, reward paling kuat sering bukan item, tapi **akses** — dibukanya area/kemampuan baru sebagai hasil belajar mekanik. "Sekarang kamu bisa lompat lebih jauh" itu sendiri sudah jadi reward.

**Montessori Building Blocks**
Diturunkan dari Metode Montessori dalam pendidikan: pemain diberi "lingkungan yang disiapkan" (*prepared environment*) dan objek yang bisa dimanipulasi, lalu dibiarkan menemukan mekanik sendiri lewat eksplorasi bebas — tanpa instruksi teks langsung.
> Contoh: taruh satu objek yang jelas "mengundang" untuk diinteraksi (tombol menyala, kotak yang goyang) di ruang aman tanpa tekanan waktu/musuh, biar pemain coba-coba sendiri.

**Constructivist Building Blocks**
Diturunkan dari teori belajar Konstruktivisme: pemain membangun pemahaman baru dengan menyambungkannya ke pengetahuan yang sudah dikuasai sebelumnya — jadi tiap mekanik baru diajarkan dengan **menumpuk** di atas mekanik yang sudah dikuasai, bukan berdiri sendiri.
> Beda dengan Montessori (eksplorasi bebas dari nol), constructivist lebih ke "kamu sudah bisa A, sekarang coba A+B" — progressive scaffolding.

**Proximity of Checkpoints**
Checkpoint diletakkan dekat titik di mana pemain kemungkinan besar gagal saat belajar mekanik baru — biar kegagalan tidak mahal dan tidak membuat frustrasi belajar.

### C. Teaching Gameplay Through Advertising Method
Diadaptasi dari kerangka iklan yang dibahas Ian Bogost di buku *Persuasive Games* — 3 cara komunikasi visual untuk mengajarkan mekanik tanpa teks instruksi:

- **Demonstrative** — memakai *scripted event/trigger* yang secara eksplisit memperagakan mekanik (misal: NPC kelihatan melakukan aksi yang sama yang nanti pemain lakukan).
- **Illustrative** — menampilkan elemen yang relevan lewat narasi lingkungan, tapi tanpa menjelaskan langsung cara pakai atau fiturnya — pemain menyimpulkan sendiri dari konteks visual.
- **Associative** — paling minim: objek yang diajarkan bahkan tidak ditampilkan secara langsung, sistemnya dibangun sepanjang permainan lewat pengulangan simbol/suara/objek hingga pemain otomatis paham "bahasa" visual & audio game tersebut.

> Demonstrative paling eksplisit & cepat dipahami tapi paling "kentara" instruksinya. Associative paling halus & immersive tapi butuh waktu lebih lama untuk "nempel" ke pemain.

## 🧩 Properties / Ringkasan Building Block

| Kategori | Building Block | Fungsi |
|---|---|---|
| Spasial | Scenes | Pecah tutorial jadi unit ajar kecil |
| Spasial | Portal & Threshold | Tandai transisi fase/konteks |
| Spasial | Controlled Approach | Bangun antisipasi sebelum reveal ruang penting |
| Spasial | Meeting Space | Titik temu jalur, checkpoint/hub info |
| Perilaku | Reward & Access | Akses baru sebagai reward belajar |
| Perilaku | Montessori | Eksplorasi bebas, temukan mekanik sendiri |
| Perilaku | Constructivist | Mekanik baru menumpuk di atas yang lama |
| Perilaku | Proximity of Checkpoints | Checkpoint dekat titik gagal saat belajar |
| Komunikasi | Demonstrative | Trigger eksplisit memperagakan mekanik |
| Komunikasi | Illustrative | Narasi lingkungan, tanpa instruksi langsung |
| Komunikasi | Associative | Bangun asosiasi simbol/suara lewat repetisi |

## 🔄 Alur Penerapan

```
Pemain masuk Threshold (transisi ke tutorial)
        │
        ▼
Scene 1: 1 mekanik diajarkan via Montessori (eksplorasi bebas, aman)
        │
        ▼
Meeting Space (checkpoint kecil, opsional info)
        │
        ▼
Scene 2: Mekanik baru ditumpuk di atas Scene 1 (Constructivist)
        │
        ▼
Controlled Approach ke Reward/Access baru
        │
        ▼
Reward = Access (membuka kemampuan/area baru)
        │
        ▼
Checkpoint ditaruh dekat titik gagal yang paling mungkin
```

## 🛠️ Cara Pakai
1. Tandai threshold yang jelas (visual/audio) tiap kali pemain pindah konteks (menu→tutorial, tutorial→game sungguhan).
2. Pecah tutorial jadi scene kecil — 1 scene = 1 mekanik, jangan digabung.
3. Mekanik pertama, kasih ruang aman buat eksplorasi bebas ala Montessori (tanpa teks, tanpa tekanan).
4. Mekanik berikutnya, desain biar "menumpuk" di atas mekanik pertama (constructivist) — bukan independen.
5. Pilih gaya komunikasi sesuai kebutuhan: demonstrative kalau mekaniknya kompleks & harus jelas cepat, illustrative/associative kalau mau immersive dan tidak terasa "tutorial banget".
6. Taruh reward utama sebagai **access** (kemampuan/area baru), bukan cuma item.
7. Letakkan checkpoint tepat sebelum titik di mana pemain paling mungkin gagal saat baru belajar.

## 🔗 Lihat Juga
- [[Level Design Workflow (Whiteblocking & Modular)]] — tutorial level tetap mengikuti workflow whiteblocking & parti yang sama.
- [[Centralized State Manager (GameManager Singleton & Event)]] — bisa dipakai untuk trigger transisi state MainMenu→Tutorial yang selaras dengan konsep Threshold di sini.
