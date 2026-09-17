# 🏭 Design Patterns & SOLID (Unity - Level Up Your Code)

#architecture #design-pattern #unity #solid #clean-code

## 🎯 Apa Ini?

Distilasi buku Unity `Level Up Your Code with Design Patterns and SOLID` (148 hlm, Unity 6 Preview): 5 prinsip SOLID + 11 pola siap pakai di Unity.
Pakai skill ini tiap kali kode mulai bau `switch` raksasa, `Instantiate/Destroy` tiap frame, Singleton beranak, atau UI nyampur sama logika.

## 🧠 Poin Penting

### SOLID dalam 1 menit
- **S**ingle Responsibility: 1 class = 1 alasan berubah. Contoh buku: `UnrefactoredPlayer` (input + gerak + sfx) → pecah jadi `PlayerInput`, `PlayerMovement`, `PlayerAudio`, `PlayerFX` + `Player` sebagai facade.
- **O**pen-Closed: buka untuk extension, tutup untuk modifikasi. `AreaCalculator.GetArea(Shape)` + `shape.CalculateArea()` — tambah shape baru tanpa sentuh calculator.
- **L**iskov Substitution: subclass harus bisa ganti base class. `Train: Vehicle` yang buang `TurnLeft()` = pelanggaran. Fix: `RoadVehicle: IMovable, ITurnable` vs `RailVehicle: IMovable`, favor composition.
- **I**nterface Segregation: jangan paksa client depend ke method yang gak dipakai. `IUnitStats` raksasa → pecah jadi `IMovable` + `IDamageable` + `IUnitStats` + `IExplodable`.
- **D**ependency Inversion: high-level jangan depend ke low-level konkret. `Switch → Door` → `Switch → ISwitchable ← Door/Trap/Light`.

> Interface = fleksibilitas (`has-a`, bisa multi), Abstract class = bagi kode bersama (`is-a`, cuma 1 parent). Paduannya: base class untuk inti + interface untuk kemampuan tempelan.

### Creational: Factory, Object Pool, Singleton
```csharp
// Factory — spawner yang declutter + extensible
public interface IProduct { string ProductName { get; set; } void Initialize(); }
public abstract class Factory : MonoBehaviour {
  public abstract IProduct GetProduct(Vector3 position);
}
```
> Tiap `ProductA/B` punya `Initialize()` sendiri (partikel vs sfx). Factory cuma panggil `Initialize()`, gak tahu detail.

```csharp
// Object Pool modern — jangan bikin manual kecuali buat belajar
using UnityEngine.Pool;
objectPool = new ObjectPool<RevisedProjectile>(
  CreateProjectile, OnGetFromPool, OnReleaseToPool,
  OnDestroyPooledObject, collectionCheck: true,
  defaultCapacity: 20, maxSize: 100);
```
> Pool di `Awake/loading screen`, `Get()` saat tembak, `Release()` saat nonaktif/offscreen. Pool kekecilan = alokasi liar, kegedean = mem bengkak.

```csharp
// Singleton generik — hemat, tapi jangan diumbar
public class Singleton<T> : MonoBehaviour where T : Component {
  private static T instance;
  public static T Instance {
    get {
      if (instance == null) {
        instance = FindFirstObjectByType<T>();
        if (instance == null) {
          var go = new GameObject(typeof(T).Name);
          instance = go.AddComponent<T>();
          DontDestroyOnLoad(go);
        }
      }
      return instance;
    }
  }
  public virtual void Awake() {
    if (instance == null) { instance = this as T; DontDestroyOnLoad(gameObject); }
    else Destroy(gameObject);
  }
}
// pakai: public class GameManager : Singleton<GameManager> {}
```
> Reserve Singleton cuma buat 2-3 manager (Game, Audio). Sisanya DI / reference Inspector / Event.

### Behavioral: Command, State, Observer, Strategy
```csharp
// Command — undo/redo, replay, combo buffer
public interface ICommand { void Execute(); void Undo(); }
public class MoveCommand : ICommand {
  PlayerMover mover; Vector3 dir;
  public MoveCommand(PlayerMover m, Vector3 d) { mover = m; dir = d; }
  public void Execute() => mover.Move(dir);
  public void Undo() => mover.Move(-dir);
}
// Invoker: Stack<ICommand> undoStack, + Stack redoStack, batasi size!
```
> Ganti koleksi sesuai kebutuhan: `Stack` = undo LIFO, `Queue` = playback FIFO, `List + index` = undo/redo cursor.

```csharp
// State — ganti switch-enum raksasa
public interface IState { void Enter(); void Execute(); void Exit(); }
public class StateMachine {
  public IState CurrentState { get; private set; }
  public void Initialize(IState s) { CurrentState = s; s.Enter(); }
  public void TransitionTo(IState n) { CurrentState.Exit(); CurrentState = n; n.Enter(); }
  public void Execute() => CurrentState?.Execute();
}
```
> Tiap state tentukan sendiri kapan `TransitionTo`. Cocok buat player controller, enemy AI (Patrol → Chase → Attack → Flee), GameManager (Menu → Load → Play → Pause). Tambah `EventLink` biar transisi via event.

```csharp
// Observer — one-to-many tanpa coupling
public class Subject : MonoBehaviour {
  public event Action ThingHappened;
  public void DoThing() => ThingHappened?.Invoke();
}
// Observer: OnEnable += , OnDisable/OnDestroy -= . WAJIB unsubscribe!
```
> Butuh skala besar? `static EventManager` sentral atau `ScriptableObject Event Channel`. `UnityEvent` enak buat designer tapi lebih lambat dari `Action`. Gabung Observer + Command = event queue anti-kakofoni.

```csharp
// Strategy — behavior interchangeable saat runtime
public abstract class Ability : ScriptableObject {
  public string abilityName;
  public abstract void Use(GameObject go);
}
// AbilityRunner.currentAbility bisa diganti tiap streak / power-up
public class AbilityRunner : MonoBehaviour {
  public Ability currentAbility;
  void Update() { if (Input.GetKeyDown(KeyCode.Space)) currentAbility.Use(gameObject); }
}
```
> Contoh lain: `MeleeAttack / RangedAttack / AreaAttack`, `AStar / Dijkstra`, `AdaptiveDifficulty / FixedDifficulty`. Tiap strategy jaga komunikasi lepas via event, jangan coupling ke sistem lain.

### Structural & Optimasi: Flyweight, Dirty Flag
```csharp
// Flyweight — pisah intrinsic (shared) vs extrinsic (unik)
// Shared: ScriptableObject | Unik: MonoBehaviour instance
[CreateAssetMenu] public class FactionData : ScriptableObject {
  public string factionName; public Sprite icon;
  public int baseHealth, baseAttack, baseDefense;
}
public class UnitInstance : MonoBehaviour {
  public FactionData factionData; // shared
  public int health, attack;      // unik
  public Vector3 position;        // unik
}
```
> Prefab = share seluruh GameObject, Flyweight = share field tertentu. Combo keduanya buat crowd, skin senjata, hutan pohon. Ribuan objek + butuh multithread? Lirik DOTS, bukan Flyweight manual.

```csharp
// Dirty Flag — skip kalkulasi mahal sampai beneran kotor
public class Sector : MonoBehaviour {
  public bool IsLoaded { get; private set; }
  public bool IsDirty { get; private set; }
  public void MarkDirty() => IsDirty = true;
  public void Clean() => IsDirty = false;
  public void LoadContent() { IsLoaded = true; /* SceneManager.LoadSceneAdditive */ }
  public void UnloadContent() { IsLoaded = false; /* Unload */ }
}
// loop: if (isPlayerClose != sector.IsLoaded) sector.MarkDirty();
//       if (sector.IsDirty) { Load/Unload; sector.Clean(); }
```
> Pakai buat sector streaming, pathfinding (recalc cuma jika obstacle/target pindah), UI layout (`MarkDirtyRepaint`), transform hierarchy anak.

### Arsitektural UI: MVP & MVVM (Unity 6)
- **MVC**: Model (data) — View (tampil) — Controller (logika, pegang input).
- **MVP (pilihan Unity)**: View pegang input → event → Presenter → update Model → event `HealthChanged` → Presenter `UpdateUI()`. Model = `ScriptableObject HealthModel`, View = UXML/USS, Presenter = `MonoBehaviour HealthPresenter`.
- **MVVM (Unity 6 + UI Toolkit data binding)**: sama kayak MVP tapi sinkronisasi otomatis. `ConverterGroup "Int to HealthBar"` ubah int → color/string, `DataBinding { dataSourcePath, BindingMode.ToTarget }` + `SetBinding("style.backgroundColor", binding)`.

> MVP/MVVM baru worth it buat UI gede + tim gede + butuh unit test tanpa Play Mode. Script kecil / MeshRenderer jangan dipaksa masuk pola.

## 🧩 Properties (Inspector)

| Pola        | Pakai saat                                           | Jangan pakai jika                                       |
| ----------- | ---------------------------------------------------- | ------------------------------------------------------- |
| SOLID       | Selalu, sebagai kompas                               | Dipaksa mentah-mentah sampai over-engineer (ingat KISS) |
| Factory     | Spawn banyak varian produk + custom init             | Cuma 1-2 prefab tanpa logika khusus                     |
| Object Pool | Tembakan/partikel spawn-destroy tiap frame, GC spike | Objek jarang muncul, pool nganggur makan mem            |
| Singleton   | 1 manager global (Game/Audio)                        | Tiap sistem minta global — itu code smell               |
| Command     | Undo/redo, replay, input buffer, combo               | Aksi sekali jalan tanpa histori                         |
| State       | Player/enemy/game punya ≥3 state + transisi          | Cuma Idle/Walk — enum+switch cukup                      |
| Observer    | UI, achievement, analitik dengar event gameplay      | Relasi 1-ke-1 sederhana (direct call lebih murah)       |
| MVP / MVVM  | UI kompleks, tim besar, butuh test                   | UI 1 layar, prototyping cepat                           |
| Strategy    | Ability/senjata/AI/difficulty gonta-ganti runtime    | Behavior statis, gak pernah swap                        |
| Flyweight   | Ratusan unit sharing stat/faction/skin               | Objek sedikit & heterogen                               |
| Dirty Flag  | Kalkulasi mahal (load scene, pathfinding, layout)    | Update murah tiap frame                                 |

## 🔄 Alur Lengkap

```
Kode bau? → SRP pecah class → OCP (abstract/interface) → ISP pecah interface → DIP depend ke abstraksi
Spawn banyak? → Factory (varian) → + Object Pool (frekuensi tinggi, GC spike)
Butuh 1 global? → Singleton<T> (maks 2-3) → kalau lebih, Event Channel / DI
Aksi historis? → Command (undoStack + redoStack + limit)
Perilaku berubah? → State (IState + StateMachine) untuk fase internal → Strategy (ScriptableObject) untuk skill swap
Banyak pendengar? → Observer (Action) → skala besar: EventManager / SO Event → + Command queue biar tertib
UI gede? → MVP (event manual) → Unity 6: MVVM (data binding + converter)
Mem bengkak? → Flyweight (SO shared vs instance unik) → ribuan entitas: DOTS
Kalkulasi mahal? → Dirty Flag (MarkDirty → proses → Clean)
```

## 🛠️ Cara Pakai di Unity

1. **Pilih 1 masalah, 1 pola.** Jangan borong. Mulai dari bau paling sakit (GC spike = Pool, switch raksasa = State/Strategy).
2. **Taruh abstraksi dulu:** `IProduct / Ability / IState / ISwitchable / FactionData` sebagai `interface / abstract / ScriptableObject`.
3. **Implement konkret kecil-kecil:** 1 file 1 tanggung jawab, target <200-300 baris per class.
4. **Sambung via Inspector/event, bukan `Find` tiap frame:** Factory pegang prefab, Pool di `Awake`, Observer `+=` di `OnEnable` + `-=` di `OnDisable/OnDestroy`, MVVM `SetBinding` sekali.
5. **Uji trade-off:** Pool (size pas?), Singleton (masih ≤3?), Observer (leak karena lupa unsubscribe?), MVVM (binding bener ToTarget?).
6. **Iterasi KISS:** kalau pola nambah ribet tanpa manfaat nyata, revert. Pola = alat, bukan tujuan.

## 🔗 Lihat Juga

- [[Books/Level Up Your Code With Design Pattern.pdf]] — sumber utama buku ini
- [[Observer Pattern Events]] — versi decoupled event yang sudah ada
- [[Centralized State Manager (GameManager Singleton & Event)]] — Singleton GameManager + event
- [[Simple FSM Berbasis Enum (Game State Prototyping)]] — FSM ringan sebelum naik ke State pattern penuh
- [[Decorator Pattern Modifiers]] — disebut buku sebagai pasangan Observer (buff/perk dinamis)
- [[Decoupled Audio System (Event Channel & Pooling)]] — contoh Observer + Pool buat SFX scalable
- [[Single Source of Truth (SSOT)]] — lawan duplikasi data ala Flyweight
- [[Advanced Architecture Patterns]] — infrastruktur trigger berbasis event
- [[Skills]] — indeks semua kategori

- [[SOLID Principles (Unity)]] — pecahan detail 5 prinsip.
- [[Factory Pattern (Unity)]] — pecahan detail factory.
- [[Object Pool Pattern (Unity)]] — pecahan detail pool.
- [[Singleton Pattern (Unity Generic)]] — pecahan detail singleton.
- [[Command Pattern (Unity Undo)]] — pecahan detail command.
- [[State Pattern (Unity FSM)]] — pecahan detail state.
- [[Strategy Pattern (Unity Ability)]] — pecahan detail strategy.
- [[Flyweight Pattern (Unity Shared Data)]] — pecahan detail flyweight.
- [[Dirty Flag Pattern (Unity)]] — pecahan detail dirty flag.
- [[MVP Pattern (Unity UI)]] — pecahan detail MVP.
- [[MVVM Pattern (Unity 6 Binding)]] — pecahan detail MVVM.
