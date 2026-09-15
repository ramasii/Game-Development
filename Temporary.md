# my-rimworld-mcp — eksperimen AI main RimWorld 1.6 via MCP

  

## Struktur

```

RimWorldMCP/        -> mod C# (bridge HTTP 127.0.0.1:8765)

  About/About.xml   -> packageId pagani.rimworldmcp, supports 1.6, loadAfter Harmony

  Source/*.cs       -> MCPMod, MCPGameComponent, StateSnapshot, Actions

mcp-server/         -> MCP server Python (FastMCP)

  server.py         -> 9 tools MVP

scenarios/          -> skenario + metrik eksperimen

```

  

## Cara build mod (sekali saja)

1. Install RimWorld 1.6 + Harmony (`brrainz.harmony`) dari Workshop.

2. Set env var `RIMWORLD_DIR`, contoh:

   `C:\Program Files (x86)\Steam\steamapps\common\RimWorld`

3. Build:

   ```

   cd RimWorldMCP\Source

   dotnet build -c Release

   ```

   Hasil `RimWorldMCP.dll` taruh di `RimWorldMCP\Assemblies\RimWorldMCP.dll`

4. Copy folder `RimWorldMCP` ke:

   `C:\Users\%USERNAME%\AppData\LocalLow\Ludeon Studios\RimWorld by Ludeon Studios\Mods\`

   atau Steam `...\RimWorld\Mods\`

5. Aktifkan di menu Mods: Harmony di atas, RimWorldMCP di bawahnya. Restart.

6. Load save, cek log (`Ctrl+F12`): harus ada `[RimWorldMCP] listening on 127.0.0.1:8765`

7. Test: buka browser `http://127.0.0.1:8765/ping`

  

## Cara jalanin MCP server

```

cd mcp-server

pip install -r requirements.txt

python server.py

```

Lalu tambah ke Claude / opencode via `mcp.example.json`.

  

Urutan wajib: **RimWorld jalan + save loaded + unpaused sekali** baru MCP tools dipanggil. Kalau `/snapshot` kosong `{}`, tunggu 250 ticks in-game.

  

## Loop eksperimen

`get_colony_status -> list_alerts -> act (max 3-4 actions) -> advance_ticks(5000) -> log`

  

Jangan kirim full map grid ke LLM, pakai snapshot simbolik dulu biar context tidak jebol.

  

## Kompatibel mod lain?

Ya, selama tidak ganti `GameComponent` atau `WorkTypeDef` secara drastis. Untuk aman, test baseline tanpa mod lain dulu (lihat `scenarios/crashlanded_15d.md`). Kalau pakai mod besar (VE, SOS2, Combat Extended), Def lookup `bench/recipe` di `Actions.cs` mungkin perlu alias tambahan.

  

## Troubleshooting

- `HttpListener Access Denied` -> jalankan RimWorld sekali sebagai admin, atau `netsh http add urlacl url=http://127.0.0.1:8765/ user=Everyone`

- Port bentrok -> ganti di Mod Settings > RimWorldMCP, samakan `RIMWORLD_BRIDGE`

- Snapshot `{}` terus -> belum ada `Find.CurrentMap` (masih di menu utama)