# 🎲 Roguelite Design Pillars (4 Pilar Utama)
#game-design #roguelite #player-psychology #replayability

## 🎯 Apa Ini?
Empat pilar desain inti yang secara kolektif menciptakan efek "satu run lagi" pada genre Roguelite. Gunakan skill ini sebagai **checklist evaluasi** saat merancang atau menganalisis game Roguelite — jika salah satu pilar lemah, game akan terasa kurang adiktif. Digunakan sejak tahap desain awal sampai playtest.

**Kapan harus digunakan:**
- Saat merancang core loop game baru dengan genre Roguelite/Roguelike.
- Saat melakukan playtest dan mencari tahu *kenapa* game terasa kurang menarik.
- Saat menganalisis kompetitor untuk menemukan kelemahan atau keunggulan mereka.

---

## 🧠 Poin Penting

### Pilar 1 — Broken Build / Sinergi (Dopamin Loop)
Pemain bisa mengombinasikan 3–4 item/perk acak untuk menciptakan karakter atau sistem yang sangat kuat (OP). Momen menemukan kombo tak terduga ini adalah **sumber kepuasan terbesar** dalam genre ini.
> Tanpa sinergi, game hanya terasa seperti grinding statistik biasa. Sinergi inilah yang membuat pemain membicarakan gamenya ke orang lain ("bro aku nemuin build gila kemarin...").

### Pilar 2 — High Stakes, Quick Restart (Permadeath yang Adil)
Kematian permanen (*permadeath*) membuat setiap keputusan terasa bermakna dan menegangkan. Tapi frustrasinya harus langsung diredam dengan tombol *Restart* instan — **tanpa loading screen panjang, tanpa animasi kematian yang lama**.
> Rasio ketegangan vs frustrasi ini adalah pisau bermata dua. Semakin cepat pemain bisa kembali beraksi setelah mati, semakin besar toleransi mereka terhadap permadeath.

### Pilar 3 — Sense of Mastery (Pertumbuhan Skill Nyata)
Meskipun dunianya acak, yang berkembang adalah **skill asli pemain** — bukan hanya angka karakter. Ketika pemain berhasil mengalahkan bos yang sebelumnya mustahil, itu karena mereka belajar pola, bukan karena beruntung.
> Ini yang membedakan Roguelite berkualitas dari "game RNG murni". Pemain harus bisa merasakan bahwa **kemenangan adalah hasil kejeniusan mereka sendiri**.

### Pilar 4 — Metaprogression (Waktu Tidak Terbuang Sia-sia)
Sumber daya/poin yang dikumpulkan selama *run* tetap tersimpan setelah mati dan bisa ditukar dengan *upgrade* permanen di menu utama. Ini membuat pemain tipe kasual tetap merasa maju meskipun gagal.
> **Roguelike** tidak punya ini (mati = mulai dari nol total). **Roguelite** punya ini. Untuk target pasar Steam yang lebih luas, Roguelite lebih disarankan karena lebih inklusif.

---

## 🧩 Properties — Perbedaan Roguelike vs Roguelite

| | Roguelike | Roguelite |
|---|---|---|
| **Kematian** | Permadeath murni — mulai dari nol | Permadeath + metaprogression |
| **Progresi** | Tidak ada perkembangan permanen | Unlock permanen antar-run |
| **Rasa** | Sangat menegangkan, semua taruhan | Lebih "adil", cocok pasar lebih luas |
| **Target Pemain** | Hardcore (The Ninja/Masochist) | Casual–Hardcore (The Professor) |

## 🔄 Alur Evaluasi (Checklist)

```
Game Roguelite kamu terasa kurang seru?
        │
        ▼
Cek Pilar 1: Ada kemungkinan sinergi OP yang bisa ditemukan pemain?
        │
        ▼
Cek Pilar 2: Seberapa cepat pemain bisa restart setelah mati?
        │
        ▼
Cek Pilar 3: Apakah kemenangan terasa hasil skill, bukan keberuntungan?
        │
        ▼
Cek Pilar 4: Apakah ada sesuatu yang tersimpan setelah mati?
        │
        ▼
Pilar yang lemah = titik fokus iterasi berikutnya
```

## 🛠️ Cara Pakai
1. Di awal desain, tentukan dulu mana dari 4 pilar yang jadi **identitas utama** game kamu (bukan semua harus setara — tapi semua harus hadir minimal).
2. Saat playtest, minta tester jawab: "Kapan kamu paling puas? Kapan paling frustrasi?" — lalu petakan ke pilar mana yang bermasalah.
3. Jika Pilar 1 lemah: tambah lebih banyak item dengan efek yang bisa berinteraksi satu sama lain (lihat [[Merancang Sistem Sinergi & Item Roguelite]]).
4. Jika Pilar 2 lemah: potong animasi kematian, percepat loading, buat restart semudah menekan satu tombol.
5. Jika Pilar 3 lemah: pastikan ada pola yang bisa dipelajari pemain (musuh punya *telegraph* serangan, bos punya fase yang konsisten).
6. Jika Pilar 4 lemah: tambah minimal 1 *unlock* permanen yang terasa berarti per run (bukan hanya kosmetik).

## 🔗 Lihat Juga
- [[Merancang Sistem Sinergi & Item Roguelite]] — implementasi konkret Pilar 1.
- [[Player Persona & Motivasi (Quantic Foundry)]] — memahami pemain yang dituju agar pilar yang ditonjolkan tepat sasaran.
- [[Hybrid Genre Design untuk Roguelite]] — cara menciptakan USP di atas fondasi 4 pilar ini.
- [[Identify Core Loops]] — selaraskan pilar-pilar ini dengan core loop yang sudah diidentifikasi.
