# 🪶 Flyweight Pattern (Unity Shared Data)

#architecture #design-pattern #flyweight #unity #performance #memory

## 🎯 Apa Ini?

Pisah data shared (intrinsic, immutable) vs data unik (extrinsic) biar ratusan objek sejenis gak duplikat data.
Pakai saat crowd, unit strategy, pohon hutan, skin senjata — banyak objek, sebagian field-nya sama persis.

## 📚 Konsep Utama

Bau: `UnrefactoredUnitInstance` simpan `factionName/icon/baseHealth...` + `health/attack...` per unit → `SetFactionData()` copy semua → makin banyak unit makin boros + susah sync.

```csharp
// Bersih: shared di ScriptableObject (flyweight), unik di instance (context)
[CreateAssetMenu]
public class FactionData : ScriptableObject {
  public string factionName; public Sprite factionIcon;
  public int baseHealth, baseAttack, baseDefense, baseMovement;
}
public class UnitInstance : MonoBehaviour {
  public FactionData factionData; // shared
  public int health, attack, defense, movement; // unik
  public Vector3 position; // unik
  void Start() => RefreshUnitStats();
  void RefreshUnitStats() {
    health = factionData.baseHealth; attack = factionData.baseAttack; /* ... */
  }
}
```
> Contoh buku: `ShipData` (UnitName/Desc/Speed/Atk/Def shared) + `Ship` (`m_SharedData` + `m_Health` unik) + `ShipFactory.GenerateShips(rows, cols)` instantiate prefab + `Initialize(sharedData, 100)`.

Prefab vs flyweight: prefab = share seluruh struktur GameObject, flyweight = share field tertentu. Kombo keduanya: prefab buat struktur, flyweight buat data dalam. Cek hemat via `Window > Analysis > Memory Profiler`. Ribuan entitas + butuh multithread? Pakai DOTS, bukan flyweight manual.

## 🧩 Properties (Inspector)

| Field | Taruh di | Aturan |
|---|---|---|
| nama/desc/speed/atk/def base, icon | `ScriptableObject` (shared) | Jangan diubah runtime per-instance |
| hp/current, posisi, buff temp | `MonoBehaviour` instance | Unik per objek |
| `sharedData` ref | tiap instance | 1 asset dipakai N instance |

## 🔄 Alur Lengkap

```
Banyak objek sejenis? → field sama berulang? → Ya: split intrinsic→SO, extrinsic→instance → factory inject SO saat spawn
→ masih boros? → Memory Profiler before/after → ribuan + CPU bound? → DOTS → objek dikit/heterogen? → skip, overhead
```

## 🛠️ Cara Pakai di Unity

1. Audit field: tandai mana shared vs unik.
2. Bikin SO (`FactionData/ShipData/UnitConfig`), buat asset per faksi/tipe.
3. Instance pegang 1 ref SO + field unik, `Refresh/Initialize(data, hp)` saat spawn.
4. Spawn via factory yang lempar SO yang sama ke semua anak.
5. Override unik ala prefab-variant kalau 1-2 unit mau beda, jangan duplikat SO.

## 🔗 Lihat Juga

- [[Books/Level Up Your Code With Design Pattern.pdf]] — sumber (hlm. 128-136)
- [[Single Source of Truth (SSOT)]] — 1 pemilik data, anti duplikat
- [[Factory Pattern (Unity)]] — factory yang inject shared data
- [[Object Pool Pattern (Unity)]] — reuse instance + share data = dobel hemat
- [[Skills]] — indeks kategori
