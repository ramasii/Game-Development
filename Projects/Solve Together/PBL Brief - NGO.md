# 📚 PBL Brief - NGO (Unity Netcode for GameObjects)

> Panduan penugasan PBL: Multiplayer lokal, listen server (1 Host + 3 Client), server-authoritative.

## 1. Deskripsi

Tiap kelompok 4 orang merancang + membangun game multiplayer lokal. Fokus: arsitektur server-authoritative, sinkronisasi state, interaksi objek real-time.

## 2. Spesifikasi Teknis

- **Engine:** Unity 6 wajib seragam sekelompok
- **Mode Jaringan:** LAN / Direct IP via Unity Transport
- **Genre:** Pick-up and Deliver (ambil → bawa lewati rintangan → antar ke zona target)
- **Model:** Kooperatif (target skor/waktu bareng) atau Kompetitif (FFA / 2v2). Solve Together pilih Kooperatif.
- **Visual:** 2D atau 3D (Solve Together: 2D)
- **Level:** Tepat 1 level playable, poles, bebas desync
- **Aset:** Boleh eksternal (Asset Store, itch.io, Kenney) wajib kredit + lisensi

## 3. Komponen Wajib Netcode

1. **NetworkManager & Unity Transport:** Host + Client via IP lokal
2. **NetworkTransform / ClientNetworkTransform:** sync posisi + rotasi, interpolated
3. **NetworkObject & Server Spawning:** spawn/despawn pickable terpusat oleh server
4. **NetworkVariable:** skor individual/tim, timer, status item
5. **RPC:**
   - **ServerRpc:** validasi server (misal validasi jangkauan sebelum ambil item)
   - **ClientRpc:** event global (partikel, audio, pengumuman pemenang, reset ronde)
6. **Network Ownership & Parenting:** pindah ownership / parenting saat dipegang-dilepas

## 4. Deliverables Ethol

### A. Hari Ini (Inisiasi)
1. GDD Ringkas: konsep, visual, win/lose, core loop, arsitektur sinkronisasi, roles, lisensi aset
2. Timeline 6 minggu (Gantt / WBS)

### B. Mingguan (W1-6, PDF)
- Milestone minggu berjalan
- Log commit Git (kontribusi merata)
- Dokumentasi skrip Netcode + bukti host-client test
- Kendala jaringan (bug/desync) + solusi

## 5. Materi Referensi NGO

- Part 1 – NetworkManager & Setup — https://youtu.be/nD_K_diocV0?si=-JWghsGin1f8paBb
- Part 2 – Player Spawning & Movement — https://youtu.be/bfM7sKjisJQ?si=hcllbVz3J2TwnNGf
- Part 3 – NetworkTransform & Sync — https://youtu.be/O3IktYv45Rs?si=6Cla7ggofhx8qeWI
- Part 4 – RPCs Implementation — https://youtu.be/c6r1yzZRUzQ?si=_mv1sz-HbRl4sNmj
- Part 5 – Tool/Item Pick-up Mechanic — https://youtu.be/MDdcrD50iwM?si=hR4ylf6odzKLRVSY
- Part 6 – NetworkVariables & State — https://youtu.be/KjUDpPX-C3E?si=d5kEnPgJjhsv1NOT
- Part 7 – Gameplay Synchronization — https://youtu.be/N-LTpAhquHI?si=_MEMyhxy0xtwBvW2
- Part 8 – Polish & Multiplayer Flow — https://youtu.be/JzXDn3qaLgA?si=XjBGBfVpFbqsWiPX

## 🔗 Lihat Juga

- [[Solve Together]]
- [[GDD - Solve Together]]
- [[Timeline]]
