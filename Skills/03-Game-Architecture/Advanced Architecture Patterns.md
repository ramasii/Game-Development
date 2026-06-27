# Skill: Apply Software Design Patterns in Game Architecture
#architecture #design-pattern

## Kapan Harus Menggunakan
Gunakan skill ini ketika mengelola arsitektur game berskala besar yang memiliki banyak aturan mid-run kompleks (seperti mekanik toko pengubah aturan, penumpukan modifier, log history aksi) agar kode tidak runtuh ke dalam percabangan kondisional yang berantakan - Week 1.docx].

## Deskripsi
Pendekatan disiplin menggunakan tiga kategori pola desain (Creational, Structural, Behavioral) untuk mengendalikan ekosistem permainan sebagai *controlled transformation engine* - Week 1.docx].

## Alur Kerja (Workflow)
1. **Identify Conditional Choke-points:** Temukan tumpukan logika `if-else` atau percabangan flag fase yang rumit - Week 1.docx].
2. **Encapsulate Algorithms (Strategy):** Pisahkan variasi logika kalkulasi skor atau damage ke dalam kelas strategi terpisah - Week 1.docx].
3. **Represent Actions as Objects (Command):** Kemas tindakan pemain (Play, Buy, Reroll) sebagai objek mandiri untuk mempermudah sistem undo, replay, dan simulasi - Week 1.docx].
4. **Encapsulate Game Phases (State):** Kelompokkan perilaku spesifik fase (Playing, Shopping, Boss Phase) ke dalam kelas state terisolasi - Week 1.docx].

## 🔗 Hubungan Keterkaitan
- [[Decorator Pattern Modifiers]] — Pola struktural yang diintegrasikan untuk manajemen komposisi modifier.
- [[Observer Pattern Events]] — Pola perilaku yang diandalkan untuk broadcast pemicu aksi.
- [[Layered Architecture Stabilization]] — Berperan sebagai petunjuk kapan pola arsitektur ini layak mulai diimplementasikan ke dalam kode produksi.