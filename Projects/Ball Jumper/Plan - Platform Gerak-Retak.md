# Plan — Platform Gerak & Retak masuk keluarga Platform

> Hub: [[Projects/Ball Jumper/Ball Jumper|Ball Jumper]] | Status: 🟡 kode + prefab DONE, runtime test PENDING (editor wedge, perlu restart Unity)
> Prinsip: komposisi (Platform = inti), SSOT tanpa variabel ganda, satu jalur landing via `Platform.TryLand`.

## Temuan awal (dari prefab YAML)
- `PlatformGerak`/`PlatformRetak`: Rigidbody2D Dynamic + SimpleHazard (bounce=1, toggle=0) + FlyingPatrolAI. **Tanpa script Platform** → streak gak keitung, gak ikut ghost, gak masuk pool.
- `Retak` belum bisa retak (belum ada script crumble).

## Yang sudah dikerjakan (via Unity MCP port 7890 + edit file)
1. `Platform.cs`: `event OnStepped` + `NotifyStepped()` (+ `using System`).
2. `PlayerController.cs`: `p.NotifyStepped()` di `TryLand` + `TrySpringBoost`.
3. Baru `PlatformMover.cs` (sine patrol X, anchor di OnEnable, pause ikut timeScale) + `PlatformCrumbler.cs` (Intact→Shake→Shrink→`ReleasePlatform()`, reset di OnEnable).
4. `Spawner.cs`: struct `PlatformVariant` + list inspector, pool per prefab (`Dictionary` + `sourcePrefab` map), `ReleasePlatform()` publik, `PickVariant()` (bailout selalu normal, retak anti-beruntun), larang booster di Retak. Fix sempat error brace ganda di OnValidate.
5. Bedah prefab via MCP: Gerak = [Transform, SpriteRenderer, BoxCollider2D, Platform, PlatformMover] (mover range 2/speed 1), Retak = [..., Platform, PlatformCrumbler]. SimpleHazard/FlyingPatrolAI/Rigidbody2D/Sensor dibuang.
6. Spawner di-wire: Gerak minY 100/10%, Retak minY 150/8%. Scene di-save.
7. Verifikasi statis: compile 0 error; distribusi PickVariant 2000 roll @200m = G 179 (~9%) / R 135 (~6.75%, sesuai teori 7.2% + anti-beruntun) / N 1686; @50m steril 0 varian.

## PENDING — butuh restart Unity dulu
- Playtest fungsional (mover gerak, retak hilang pasca-injak) KEGAGALAN lingkungan: player loop stall — `Time.frameCount=1`, `Time.time` beku, ts=1, tidak pause, console bersih dari error game. Bukan salah kode (compile 0, tidak ada loop tak terbatas di kode). Dugaan: sesi editor wedge setelah 37+ menit + banyak hot reload hari ini.
- Setelah restart: re-run tes spawn Gerak/Retak runtime + cek pool plateau, lalu update TDD.

## Update — debug log varian + anomali player loop
- Tambah `Spawner.logVariantSpawns` (default ON): log `[Spawner] Varian X spawn di (x, y)` tiap Gerak/Retak spawn. `PlatformCrumbler`: log `[Crumbler] ... diinjak` + `... rontok -> balik pool`. Compile 0 error.
- Anomali: `Time.frameCount` stuck 1-2 + `Time.time` beku (ts=1, tidak pause, console bersih, uptime editor 57 mnt). Runtime test TETAP pending. Minta King restart Unity.
- Hipotesis backlog kalau gap terbukti varian: (a) Retak rontok wajar meninggalkan lubang (by design, perlu konfirmasi rasa), (b) Mover range 2 + maxGapX 2.5 = gap efektif 4.5 → pertimbangkan kecilkan moveRange default.
