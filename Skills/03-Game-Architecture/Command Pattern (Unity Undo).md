# ↩️ Command Pattern (Unity Undo)

#architecture #design-pattern #command #unity

## 🎯 Apa Ini?

Bungkus aksi jadi objek (`Execute/Undo`) biar bisa antre, tunda, replay, atau undo/redo.
Pakai saat input butuh histori: puzzle undo, strategy plan-then-execute, combo fighting, replay.

## 📚 Konsep Utama

```csharp
public interface ICommand { void Execute(); void Undo(); }

public class CommandInvoker {
  static readonly Stack<ICommand> undoStack = new();
  public static void ExecuteCommand(ICommand c) { c.Execute(); undoStack.Push(c); }
  public static void UndoCommand() {
    if (undoStack.Count > 0) undoStack.Pop().Undo();
  }
}
```
> Invoker gak tahu isi aksi, cuma panggil `Execute/Undo`. Nambah command baru = class baru, lama aman.

```csharp
// Contoh buku: gerak undoable di maze
public class MoveCommand : ICommand {
  PlayerMover mover; Vector3 dir;
  public MoveCommand(PlayerMover m, Vector3 d) { mover = m; dir = d; }
  public void Execute() => mover.Move(dir);
  public void Undo() => mover.Move(-dir);
}
void RunPlayerCommand(PlayerMover m, Vector3 dir) {
  if (m == null || !m.IsValidMove(dir)) return;
  CommandInvoker.ExecuteCommand(new MoveCommand(m, dir)); // InputManager gak panggil Move langsung
}
```
> `PlayerMover` urus validasi (`Raycast` obstacle + `boardSpacing`), `MoveCommand` urus histori. Tombol UI panggil `RunPlayerCommand` + tombol Undo panggil `UndoCommand()`.

## 🧩 Properties (Inspector)

| Koleksi | Perilaku | Cocok buat |
|---|---|---|
| `Stack` | LIFO | Undo (tumpuk, pop terakhir) |
| `Stack` kedua | Redo | Undo pop → push ke redo; aksi baru = clear redo |
| `Queue` | FIFO | Playback berurutan / combo buffer |
| `List + index` | Cursor | Undo ke kiri, redo ke kanan dari index aktif |

Batasi size stack (mis. 50 terakhir) biar histori gak meledak.

## 🔄 Alur Lengkap

```
Input? → bungkus jadi ICommand (param via constructor) → Invoker.Execute + push → Mau batal? → Invoker.Undo (pop + Undo)
→ Mau ulangi? → redo stack → Buffer rame? → Queue/List + limit size → Event rame? → gabung [[Observer Pattern Events]] jadi event queue
```

## 🛠️ Cara Pakai di Unity

1. Bikin `ICommand`, 1 class command per aksi (`MoveCommand`, `AttackCommand`).
2. Bikin `CommandInvoker` statik (undo + opsional redo), kasih limit.
3. `InputManager` bikin command, jangan panggil logika langsung.
4. Wire tombol/keyboard/gamepad ke `RunXCommand`, tombol Undo/Redo ke invoker.
5. Jangan pakai buat aksi sekali jalan tanpa histori — over-structure.

## 🔗 Lihat Juga

- [[Books/Level Up Your Code With Design Pattern.pdf]] — sumber (hlm. 73-79)
- [[Design Patterns & SOLID (Unity - Level Up Your Code)]] — hub master
- [[Observer Pattern Events]] — gabungan jadi event queue tertib
- [[State Pattern (Unity FSM)]] — state tentukan kapan command boleh jalan
- [[Skills]] — indeks kategori
