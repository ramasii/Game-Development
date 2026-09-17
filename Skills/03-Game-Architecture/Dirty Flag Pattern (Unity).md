# 🚩 Dirty Flag Pattern (Unity)

#architecture #design-pattern #dirty-flag #unity #performance #optimasi

## 🎯 Apa Ini?

Boolean penanda "kotor" biar kalkulasi mahal cuma jalan saat state beneran berubah, sisanya di-skip.
Pakai saat load sector, pathfinding, procedural regen, layout UI kompleks — mahal kalau tiap frame.

## 📚 Konsep Utama

```csharp
public class Sector : MonoBehaviour {
  [Tooltip("Minimum distance to load")] public float m_LoadRadius;
  public bool IsLoaded { get; private set; }
  public bool IsDirty { get; private set; }
  void Awake() { Clean(); IsLoaded = false; }
  public void MarkDirty() => IsDirty = true;
  public void Clean() => IsDirty = false;
  public void LoadContent() { IsLoaded = true; /* SceneManager.LoadSceneAdditive */ }
  public void UnloadContent() { IsLoaded = false; /* Unload */ }
  public bool IsPlayerClose(Vector3 p) =>
    Vector3.Distance(p, transform.position) <= m_LoadRadius;
}

public class GameSectors : MonoBehaviour {
  public PlayerController player; public Sector[] sectors;
  void Update() {
    foreach (var s in sectors) {
      bool close = s.IsPlayerClose(player.transform.position);
      if (close != s.IsLoaded) s.MarkDirty(); // state perlu berubah?
      if (s.IsDirty) { // mahal cuma di sini
        if (close) s.LoadContent(); else s.UnloadContent();
        s.Clean();
      }
    }
  }
}
```
> Contoh buku: dunia dibagi sector, top-down camera buktiin load/unload cuma saat pemain dekat batas. Istilah beda: dirty flag = strategi high-level (skip update), dirty bit = indikator low-level memory page, caching = simpan copy biar baca cepat (pakai flag/bit biar fresh).

Contoh lain buku: transform hierarchy anak (update cuma jika parent dirty), fisika (recalc cuma jika pos/vel/gaya berubah), pathfinding (recalc cuma jika obstacle/target pindah), procedural terrain (regen on trigger), UI Toolkit (`MarkDirtyRepaint`, `EditorUtility.SetDirty/ClearDirty`).

## 🧩 Properties (Inspector)

| Field | Isi | Catatan |
|---|---|---|
| `m_LoadRadius` | jarak load per sector | Tiap sector bisa beda threshold |
| `IsLoaded / IsDirty` | read-only state | Ubah hanya via `Load/Unload/MarkDirty/Clean` |
| cek | `IsPlayerClose()` / event / physics / anim | Taruh di titik yang memang sering berubah |

## 🔄 Alur Lengkap

```
Operasi mahal? → Ya: kasih IsDirty → tiap frame cek murah (jarak/flag/event) → beda? → MarkDirty
→ IsDirty? → eksekusi mahal → Clean → tidak dirty? → skip → state ngelag? → wajar (tunda sampai dirty), kalau kritis jangan pakai pola ini
```

## 🛠️ Cara Pakai di Unity

1. Identifikasi 1 operasi mahal (Scene load, pathfinding, regen, layout).
2. Bungkus state di `IsLoaded/IsDirty` + method `MarkDirty/Clean/Load/Unload`.
3. Loop cek murah tiap frame, eksekusi mahal hanya saat dirty. Log `MarkDirty` saat develop biar kelihatan.
4. Waspada coupling + state basi sementara. Kalau butuh real-time ketat, jangan tunda.
5. Versi lanjut: hilangkan `Update()` sama sekali, full event-driven (dirty via event, bukan poll jarak).

## 🔗 Lihat Juga

- [[Books/Level Up Your Code With Design Pattern.pdf]] — sumber (hlm. 137-144)
- [[Object Pool Pattern (Unity)]] — hemat GC, Dirty Flag hemat CPU — kombo optimasi
- [[State Pattern (Unity FSM)]] — state yang tentukan kapan flag dikotori
- [[Skills]] — indeks kategori
