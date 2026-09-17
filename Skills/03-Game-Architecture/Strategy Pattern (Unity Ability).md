# 🎲 Strategy Pattern (Unity Ability)

#architecture #design-pattern #strategy #unity #clean-code

## 🎯 Apa Ini?

Bungkus tiap algoritma/behavior jadi objek yang bisa ditukar saat runtime tanpa ubah class pemakai.
Pakai saat ability, senjata, AI, pathfinding, atau difficulty perlu gonta-ganti dinamis.

## 📚 Konsep Utama

Bau yang diganti: `AbilityRunner` + `enum + switch` di `ActivateAbility()` — tambah ability = bongkar file, langgar OCP.

```csharp
// Bersih ala buku: strategy sebagai ScriptableObject biar assignable di Inspector
public abstract class Ability : ScriptableObject {
  public string abilityName;
  public abstract void Use(GameObject go);
}
[CreateAssetMenu(fileName = "RadarPulseAbility", menuName = "Abilities/RadarPulse")]
public class RadarPulse : Ability {
  public override void Use(GameObject go) { Debug.Log("Activating Radar Pulse"); /* ... */ }
}
[CreateAssetMenu(fileName = "AirSupportAbility", menuName = "Abilities/AirSupport")]
public class AirSupport : Ability {
  public override void Use(GameObject go) { Debug.Log("Calling in Air Support"); /* ... */ }
}
public class AbilityRunner : MonoBehaviour {
  public Ability currentAbility; // tukar runtime / Inspector / streak logic
  void Update() {
    if (Input.GetKeyDown(KeyCode.Space)) currentAbility.Use(gameObject);
  }
}
```
> Contoh buku: streak → tombol ganti ability → klik = `Use()`. Tiap strategy dikemas sendiri: tambah baru tanpa sentuh yang lama. Rancang komunikasi via event biar strategy gak coupling ke sistem lain.

Contoh lain dari buku: movement (Walk → DoubleJump/Dash/Fly), AI (Offensive/Defensive/Patrol), pathfinding (A*/Dijkstra swap), attack (`Melee/Ranged/Area`, boss ganti mode by HP), difficulty (Adaptive vs Fixed).

## 🧩 Properties (Inspector)

| Field | Isi | Catatan |
|---|---|---|
| `currentAbility` | Asset `Ability` (Radar/Air/FirstAid) | Bisa diganti code saat streak naik |
| asset per ability | `CreateAssetMenu` | Designer bisa bikin varian tanpa programmer |
| client | `AbilityRunner`/tombol/enemy | Pegang 1 ref `Ability`, bukan switch |

## 🔄 Alur Lengkap

```
Behavior perlu swap runtime? → Ya: abstract Ability + Use() → 1 SO per behavior → client pegang 1 ref
→ trigger (streak/input/HP/state)? → ganti ref → Use() → butuh data bersama? → event, jangan direct ref silang
→ behavior statis? → jangan Strategy, overkill + overhead
```

## 🛠️ Cara Pakai di Unity

1. Bikin `Ability` abstract (SO), 1 file per behavior konkret.
2. Refactor client: hapus `enum+switch`, ganti 1 field `Ability`.
3. Buat asset via `CreateAssetMenu`, assign di Inspector / ganti via streak/AI/state logic.
4. Jaga strategy lepas: input data via `Use(go)` + event, bukan `GetComponent` silang.
5. Test: tambah ability baru = 0 edit file lama.

## 🔗 Lihat Juga

- [[Books/Level Up Your Code With Design Pattern.pdf]] — sumber (hlm. 121-127)
- [[SOLID Principles (Unity)]] — OCP di balik Strategy
- [[State Pattern (Unity FSM)]] — sepupu dekat (State = fase internal, Strategy = behavior tukar)
- [[Flyweight Pattern (Unity Shared Data)]] — SO shared buat data, Strategy buat behavior
- [[Skills]] — indeks kategori
