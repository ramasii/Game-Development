# 🚦 State Pattern (Unity FSM)

#architecture #design-pattern #state #fsm #unity

## 🎯 Apa Ini?

Bungkus tiap state jadi objek (`Enter/Execute/Exit`) + 1 `StateMachine` yang urus transisi, biar gak ada `switch` raksasa.
Pakai saat player/enemy/game punya ≥3 state yang saling pindah (Idle/Walk/Jump, Patrol/Chase/Attack, Menu/Play/Pause).

## 📚 Konsep Utama

Bau yang diganti:
```csharp
// Bau: enum + switch di 1 class, nambah state = bongkar file
enum S { Idle, Walk, Jump }
void Update() { switch (state) { case S.Idle: Idle(); break; /* ... */ } }
```

```csharp
// Bersih ala buku:
public interface IState { void Enter(); void Execute(); void Exit(); }

[Serializable]
public class StateMachine {
  public IState CurrentState { get; private set; }
  public WalkState walkState; public JumpState jumpState; public IdleState idleState;
  public StateMachine(PlayerController p) {
    walkState = new WalkState(p); jumpState = new JumpState(p); idleState = new IdleState(p);
  }
  public void Initialize(IState s) { CurrentState = s; s.Enter(); }
  public void TransitionTo(IState n) { CurrentState.Exit(); CurrentState = n; n.Enter(); }
  public void Execute() => CurrentState?.Execute();
}

public class IdleState : IState {
  readonly PlayerController player;
  public IdleState(PlayerController p) => player = p;
  public void Enter() { /* ... */ }
  public void Execute() {
    // cek velocity/jump → player.StateMachine.TransitionTo(walk/jump)
  }
  public void Exit() { /* ... */ }
}
```
> Aturan GoF yang dipenuhi: objek ganti perilaku saat state internal berubah + tambah state baru tanpa ganggu state lama. `StateMachine` Serializable biar kebaca Inspector via `PlayerController`.

Versi lanjut buku: `AbstractState : IState` + `State` umum + `DelayState` (tunggu/load bar) + `LoadSceneState/UnloadLastSceneState` + `ILink / EventLink / EventSOLink` buat transisi via C# event / ScriptableObject event. `GameManager` pakai ini buat alur Menu → demo, tombol raise event → transisi + load scene aditif.

## 🧩 Properties (Inspector)

| Bagian | Isi | Catatan |
|---|---|---|
| `CurrentState` | read-only | Jangan diset manual, via `Initialize/TransitionTo` |
| state objects | `walk/jump/idle` (+ custom) | 1 class 1 state, bawa dependensi via constructor |
| transisi | di dalam `Execute()` tiap state | Bukan di `Update()` raksasa |
| event | `OnEnter/OnExit` opsional | Notify UI/audio/anim saat pindah state |

## 🔄 Alur Lengkap

```
State ≥3? → Ya: IState per state → StateMachine pegang semua → Initialize(start) → tiap frame Execute()
→ kondisi kena? → TransitionTo(next) → butuh event luar? → EventLink/SO → butuh hierarki? → SuperState (Grounded → Walk/Run)
→ anim? → 1 state = 1 clip Animator → AI? → Patrol/Attack/Flee sebagai state
```

## 🛠️ Cara Pakai di Unity

1. List state + diagram transisi di kertas dulu (jangan langsung code).
2. Bikin `IState` + 1 class per state, `StateMachine` sebagai field di controller.
3. Pindah logika `switch` ke `Execute()` masing-masing, transisi via `TransitionTo`.
4. Tambah event enter/exit kalau UI/anim/sfx perlu tahu. Hierarki kalau ada state mirip (Grounded).
5. Kalau cuma 2 state simpel, tetap pakai enum — State pattern = overkill.

## 🔗 Lihat Juga

- [[Books/Level Up Your Code With Design Pattern.pdf]] — sumber (hlm. 80-92)
- [[Design Patterns & SOLID (Unity - Level Up Your Code)]] — hub master
- [[Simple FSM Berbasis Enum (Game State Prototyping)]] — versi ringan sebelum naik ke penuh
- [[Observer Pattern Events]] — transisi via event
- [[Command Pattern (Unity Undo)]] — batasi command per state
- [[Skills]] — indeks kategori
