# ⚗️ Merancang Sistem Sinergi & Item Roguelite
#game-design #roguelite #item-design #synergy #broken-build

## 🎯 Apa Ini?
Framework untuk merancang sistem item/perk Roguelite yang bisa menghasilkan kombinasi kuat (*broken build*) secara tak terduga — fondasi dari Pilar 1 "Broken Build/Sinergi" di [[Roguelite Design Pillars (4 Pilar Utama)]]. Gunakan skill ini saat merancang kumpulan item game Roguelite dari nol, atau saat sistem item yang ada terasa membosankan dan tidak menghasilkan momen "WOW".

**Kapan harus digunakan:**
- Saat merancang pool item/perk untuk game Roguelite baru.
- Saat playtest menunjukkan bahwa pemain jarang menemukan kombo yang terasa "gila" atau OP.
- Saat ingin memperluas item pool yang sudah ada tanpa merusak keseimbangan.

---

## 🧠 Poin Penting

### Anatomi Item yang Bisa Bersinergi
Item yang baik dalam Roguelite punya **kondisi trigger** dan **efek** yang bisa dimanipulasi oleh item lain. Bukan hanya "+10% damage" yang berdiri sendiri.

```
[Kondisi Trigger] → [Efek] → [Amplifier dari item lain]

Contoh:
"Setiap besi yang melewati belokan 3x" → "bermuatan listrik" → "Turret bermuatan listrik memicu petir ke 5 musuh"
```

### Tipe Sinergi

**Tipe A — Chain Reaction (Reaksi Berantai)**
Item A mengubah properti suatu objek/resource → Item B bereaksi spesifik terhadap properti tersebut.
> Contoh: *Overclocker* (mesin lebih cepat tapi buang 10% resource) + *Recycle Bin* (resource terbuang → koin). Pemain sengaja membuat sistem "bocor" untuk menghasilkan koin tak terbatas.

**Tipe B — Multiplier Stack**
Dua item yang masing-masing memberi bonus kecil, tapi digabungkan menghasilkan pertumbuhan eksponensial karena satu mengalikan output yang lain.
> Contoh: Item "+20% damage per musuh di layar" + Item "musuh spawn 2x lebih banyak" = damage yang meledak saat arena penuh musuh.

**Tipe C — Role Reversal (Pembalikan Peran)**
Item yang mengubah *debuff* menjadi *buff* atau membalik mekanik yang biasanya merugikan.
> Contoh dari riset: kartu *Reverse* di UNO-Roguelite yang membalik semua *debuff* aktif menjadi *buff* — pemain justru *sengaja* kena debuff untuk kemudian membaliknya.

**Tipe D — Economy Exploit**
Sinergi yang mengeksploitasi sistem ekonomi internal game (resource, koin, energi) untuk menghasilkan loop yang sangat efisien atau bahkan infinite.
> Ini adalah tipe sinergi yang paling dibicarakan pemain di komunitas — tapi juga yang paling rentan merusak keseimbangan jika tidak dikontrol.

### Prinsip "Controlled Chaos"
Sinergi harus terasa *discovered*, bukan *obvious*. Kalau semua kombo jelas dan terpampang di tutorial, momen "broken build" hilang. Tapi kalau terlalu tersembunyi, pemain frustasi. **Sweet spot**: item punya deskripsi jelas, tapi efek kombinasinya baru terasa saat dimainkan.

### Skala Pool Item
- **Minimum viable**: 30–50 item untuk prototype pertama — cukup untuk menghasilkan variasi run yang terasa berbeda.
- **Early Access**: 80–120 item — mulai muncul sinergi 3-item yang kompleks.
- **Full Release**: 150+ item (contoh: CloverPit punya 150+ jimat) — pemain bisa ratusan jam tanpa kehabisan kombinasi baru.

---

## 🧩 Properties — Template Desain Item

| Field | Isi | Contoh |
|---|---|---|
| **Nama** | Nama item yang ikonik/memorable | *Recycle Bin* |
| **Tier** | Common / Uncommon / Rare / Legendary | Rare |
| **Kondisi Trigger** | Kapan efek aktif? | "Setiap resource yang terbuang" |
| **Efek Utama** | Apa yang terjadi? | "Menghasilkan 5 koin emas" |
| **Tag Sinergi** | Kata kunci untuk sinergi dengan item lain | `[waste]` `[economy]` `[passive]` |
| **Sinergi Diketahui** | Item lain yang combo bagus | *Overclocker*, *Broken Pipe* |
| **Counter** | Item/situasi yang melemahkan item ini | Run tanpa mesin yang membuang resource |

## 🔄 Alur Desain Item Baru

```
Tentukan "role" item: Trigger? Amplifier? Economy? Utility?
        │
        ▼
Tulis kondisi trigger yang spesifik (hindari "selalu aktif")
        │
        ▼
Beri tag sinergi (minimal 1-2 kata kunci)
        │
        ▼
Cek: apakah ada 2-3 item lain yang bisa bersinergi dengan tag ini?
        │
   Tidak ada ──▶ Tambah item amplifier baru yang bereaksi ke tag ini
        │
        ▼
Playtest: apakah kombinasinya bisa "break" game? Seberapa mudah ditemukan?
        │
   Terlalu mudah ──▶ Naikkan tier jadi Rare/Legendary
   Terlalu susah  ──▶ Turunkan tier atau tambah hint di deskripsi
```

## 🛠️ Cara Pakai
1. Mulai dengan merancang **3 "chain" sinergi** yang punya narasi jelas (pemain bisa menceritakan kombonya ke temannya). Ini jadi "showcase sinergi" yang pertama kali dirasakan pemain baru.
2. Gunakan **sistem tag** (`[listrik]`, `[waste]`, `[kecepatan]`) di setiap item — ini memudahkan pemrograman interaksi antar item tanpa harus hardcode setiap kombinasi.
3. Setiap item baru yang ditambahkan, tanya: "Apakah item ini berinteraksi dengan minimal 3 item lain yang sudah ada?" — jika tidak, item itu terlalu terisolasi.
4. Buat **spreadsheet sinergi** (baris = item A, kolom = item B, isi = efek kombinasi) — ini akan sangat berguna saat pool item mulai besar.
5. Playtest secara khusus dengan memaksa kombinasi ekstrem — jika tidak ada yang "gila", sistem sinerginya perlu diperkuat.

## 🔗 Lihat Juga
- [[Roguelite Design Pillars (4 Pilar Utama)]] — skill ini adalah implementasi konkret Pilar 1.
- [[Apply Balance Foundations]] — setelah sinergi dirancang, gunakan ini untuk mengatur keseimbangan agar tidak ada satu kombo yang terlalu dominan.
- [[Map Resource Flows]] — peta aliran resource membantu melihat celah di mana sinergi Economy Exploit bisa muncul.
