# 🕹️ Centralized State Manager (GameManager Singleton & Event)
#architecture #state #singleton #event

## 🎯 Apa Ini?
Pola sentralisasi lalu lintas *game state* lewat satu kelas **Singleton** (`GameManager`) yang menjalankan logika spesifik tiap kali state berubah, lalu memancarkan **event global** agar sistem lain (UI, Audio, dll) bisa otomatis menyesuaikan diri tanpa polling manual setiap frame.

## 🧠 Poin Penting

**Singleton + state ter-enkapsulasi**
```csharp
public static GameManager Instance { get; private set; }
public GameState CurrentState { get; private set; }

private void Awake()
{
    if (Instance == null) Instance = this;
    else Destroy(gameObject);
}
```
> `CurrentState` punya *private setter* — satu-satunya jalan mengubahnya cuma lewat `UpdateState()`. Ini mencegah sistem lain mengubah state secara diam-diam tanpa lewat jalur resmi (yang artinya tanpa memicu event/handler).

**Switch-case sebagai titik kendali sentral**
```csharp
public void UpdateState(GameState newState)
{
    CurrentState = newState;
    switch (newState)
    {
        case GameState.Paused:
        case GameState.GameOver:
            Time.timeScale = 0f;
            break;
        default:
            Time.timeScale = 1f;
            break;
    }
    OnStateChanged?.Invoke(newState);
}
```
> `Time.timeScale` jadi cara murah untuk "pause" tanpa harus mematikan tiap-tiap script `Update()` satu per satu — taruh logikanya terpusat di sini supaya tidak ada sistem yang lupa di-pause.

**Broadcast event ke seluruh sistem**
```csharp
public static event Action<GameState> OnStateChanged;
```
> Event statis ini fungsinya mirip dengan [[Observer Pattern Events]] / `AudioEventChannel` di [[Decoupled Audio System (Event Channel & Pooling)]] — bedanya event ini "menempel" langsung di kelas Singleton, bukan *asset* ScriptableObject terpisah. Trade-off: lebih cepat ditulis untuk prototype, tapi lebih erat *coupling*-nya ke `GameManager` dibanding pakai Event Channel.

## 🧩 Properties (Inspector / Code)

| Anggota | Tipe | Keterangan |
|---|---|---|
| `Instance` | `static GameManager` | Titik akses tunggal ke manager dari skrip manapun |
| `CurrentState` | `GameState` (get-only) | State aktif saat ini, hanya bisa diubah lewat `UpdateState()` |
| `OnStateChanged` | `static event Action<GameState>` | Dipicu setiap kali state berubah, untuk dipakai sistem lain (UI/Audio) |

## 🔄 Alur Lengkap

```
Start() → UpdateState(MainMenu)
              │
   [trigger eksternal: tombol Play / Pause / Lose-condition]
              │
              ▼
      UpdateState(newState)
              │
   ┌──────────┼──────────────┐
   ▼          ▼               ▼
CurrentState  switch-case     OnStateChanged?.Invoke(newState)
diupdate      (HandleX())            │
                                      ▼
                          UI / Audio Subscriber bereaksi
```

## 🛠️ Cara Pasang & Pakai

1. Taruh `GameManager.cs` sebagai satu GameObject di scene pertama; tambahkan `DontDestroyOnLoad(gameObject)` di `Awake()` kalau game punya banyak scene, agar instance-nya tidak ikut hancur saat *load scene* baru.
2. Picu transisi state dari skrip lain lewat akses statis, contoh: `GameManager.Instance.UpdateState(GameState.Playing);` saat tombol Play ditekan, atau `GameManager.Instance.UpdateState(GameState.GameOver);` saat kondisi kalah terpenuhi.
3. Di skrip UI/Audio yang perlu bereaksi otomatis, *subscribe* ke event seperti pola di [[Observer Pattern Events]]:
   ```csharp
   private void OnEnable()  => GameManager.OnStateChanged += HandleStateChanged;
   private void OnDisable() => GameManager.OnStateChanged -= HandleStateChanged;
   ```
4. Jangan lupa selalu *unsubscribe* di `OnDisable`/`OnDestroy` untuk mencegah *memory leak* — sama persis seperti kewajiban di [[Decoupled Audio System (Event Channel & Pooling)]].

## 🔗 Lihat Juga
- [[Simple FSM Berbasis Enum (Game State Prototyping)]] — sumber enum `GameState` yang dikonsumsi kelas ini.
- [[Observer Pattern Events]] — pola dasar *publish-subscribe* yang dipakai `OnStateChanged`.
- [[Runtime State Separation]] — pelengkap untuk memisahkan data *persistent/runtime/temporary*, di luar sekadar status FSM ini.
