# 🧱 SOLID Principles (Unity)

#architecture #solid #clean-code #design-pattern #unity

## 🎯 Apa Ini?

5 aturan dasar OOP dari buku `Level Up Your Code` biar kode Unity gampang dibaca, gampang ditambah, gak gampang jebol.
Pakai tiap kali mau bikin class baru, refactor, atau review kode yang mulai bau.

## 📚 Konsep Utama

**S — Single Responsibility:** 1 class = 1 alasan berubah.
```csharp
// Bau: input + gerak + sfx numpuk
public class UnrefactoredPlayer : MonoBehaviour { /* Input.GetAxis + Move + bounceSfx.Play() */ }
// Bersih: pecah + facade
[RequireComponent(typeof(PlayerAudio), typeof(PlayerInput), typeof(PlayerMovement))]
public class Player : MonoBehaviour {
  PlayerAudio audio; PlayerInput input; PlayerMovement move;
}
```
> Target <200-300 baris per class. Kecil = gampang diwarisi, gampang reuse.

**O — Open-Closed:** buka buat extension, tutup buat modifikasi.
```csharp
public abstract class Shape { public abstract float CalculateArea(); }
public class Rectangle : Shape {
  public float width, height;
  public override float CalculateArea() => width * height;
}
public class AreaCalculator {
  public float GetArea(Shape s) => s.CalculateArea(); // tambah shape baru tanpa sentuh ini
}
```
> Contoh buku di Unity: `AreaOfEffect.CalculateArea()` → `CircleEffect / HexEffect / RectEffect` masing-masing override sendiri.

**L — Liskov Substitution:** subclass harus bisa ganti base class tanpa rusak.
```csharp
// Rusak: Train: Vehicle tapi buang TurnLeft() -> throw NotImplemented
// Benar: pisah interface + composition
public interface IMovable { void GoForward(); void Reverse(); }
public interface ITurnable { void TurnLeft(); void TurnRight(); }
public class RoadVehicle : IMovable, ITurnable { /* ... */ }
public class RailVehicle : IMovable { /* ... */ }
```
> Bau LSP: `NotImplementedException`, method kosong, `if (x is Train)` bertebaran. Fix: jangan paksa hierarki dunia nyata jadi hierarki class.

**I — Interface Segregation:** jangan paksa client depend ke method yang gak dipakai.
```csharp
// Bau: IUnitStats raksasa (Health + Move + Strength + Explode)
// Bersih:
public interface IDamageable { float Health { get; set; } void Die(); void TakeDamage(); }
public interface IMovable { float MoveSpeed { get; set; } void GoForward(); }
public interface IExplodable { void Explode(); }
public class ExplodingBarrel : MonoBehaviour, IDamageable, IExplodable { /* ... */ }
```
> Contoh buku: `IEffectTrigger / IDamageable / IExplodable` — Projectile gak perlu tahu target itu apa.

**D — Dependency Inversion:** high-level jangan depend ke konkret low-level, dua-duanya depend ke abstraksi.
```csharp
public interface ISwitchable { bool IsActive { get; } void Activate(); void Deactivate(); }
public class Switch : MonoBehaviour {
  public ISwitchable client; // bukan Door konkret
  public void Toggle() { if (client.IsActive) client.Deactivate(); else client.Activate(); }
}
public class Door : MonoBehaviour, ISwitchable { /* ... */ }
public class Trap : MonoBehaviour, ISwitchable { /* ... */ }
```
> Hasil: 1 Switch bisa nyalain pintu, jebakan, lampu, robot — tanpa ubah kode Switch.

**Interface vs Abstract:** interface = fleksibel multi (`has-a`), abstract = bagi kode bersama (`is-a`, cuma 1 parent). Padu: base class buat inti + interface buat tempelan (`NPC : Robot + ISwitchable`).

## 🧩 Properties (Inspector)

| Prinsip | Bau khas | Fix cepat |
|---|---|---|
| SRP | Class >300 baris, nama pake `And/Manager/Util` | Pecah per tanggung jawab, facade tipis |
| OCP | Tambah fitur = edit file lama + `switch` nambah | Abstract/interface + override |
| LSP | `NotImplemented`, method kosong, `is/as` bertebaran | Pisah base, favor composition |
| ISP | 1 interface >6 method, class implement tapi separuh dibuang | Pecah jadi 2-4 method per interface |
| DIP | `new Door()` di dalam `Switch`, using konkret di mana-mana | Depend ke `ISwitchable`, inject via Inspector |

## 🔄 Alur Lengkap

```
Mau bikin class? → Tulis dulu 1 tanggung jawab (SRP) → Butuh varian? → abstract/interface (OCP)
→ Warisan aneh? → cek bisa substitusi gak (LSP) → Interface kegemukan? → pecah (ISP)
→ Depend ke konkret? → selipin abstraksi (DIP) → Masih ribet? → KISS: revert, jangan maksa
```

## 🛠️ Cara Pakai di Unity

1. Audit 1 file bau, tandai pelanggarannya (S/O/L/I/D yang mana).
2. Pecah dulu (SRP), baru abstraksi (OCP/ISP/DIP). Jangan lompat ke pola canggih sebelum SRP beres.
3. Serialize yang konkret (`MonoBehaviour`), cast ke interface saat runtime:
```csharp
[SerializeField] MonoBehaviour interactableObject;
if (interactableObject is IInteractable i) i.Interact();
```
4. Uji: tambah 1 varian baru tanpa edit file lama. Kalau masih edit lama = OCP gagal.
5. Jangan over-engineer. Buku tekankan KISS — pola = alat, bukan tujuan.

## 🔗 Lihat Juga

- [[Books/Level Up Your Code With Design Pattern.pdf]] — sumber (hlm. 12-47)
- [[Design Patterns & SOLID (Unity - Level Up Your Code)]] — hub master semua pola
- [[Single Source of Truth (SSOT)]] — 1 pemilik sah per data
- [[Factory Pattern (Unity)]] — OCP praktis buat spawning
- [[Strategy Pattern (Unity Ability)]] — OCP + composition buat behavior swap
- [[Skills]] — indeks kategori
