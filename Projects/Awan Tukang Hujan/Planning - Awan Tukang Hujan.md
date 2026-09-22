# Planning - Awan Tukang Hujan

> Planning eksekusi terpisah dari GDD. GDD (What/Why) di [[GDD - Awan Tukang Hujan]]. Di sini hanya How/When. Hub: [[Awan Tukang Hujan]].

---

## 1. Flow Full: Main Menu → In Game → Pause → Main Menu + GameOver

```mermaid
graph TD
  Boot[Boot - Init] --> MainMenu[MAIN MENU - Idle Planet]
  MainMenu -- Tap Main-icon --> FTUECek{FTUE flag?}
  FTUECek -- Belum --> InGameFTUE[IN GAME - FTUE]
  FTUECek -- Sudah --> InGameFree[IN GAME - Endless]
  InGameFTUE --> InGameFree
  InGameFree -- Pause-icon / Back HP --> Paused[PAUSED - timeScale 0]
  Paused -- Resume --> InGameFree
  Paused -- Restart --> InGameFree
  Paused -- Home --> MainMenu
  InGameFree -- AliveCount==0 --> GameOver[GAMEOVER]
  GameOver -- Restart --> InGameFree
  GameOver -- Home --> MainMenu
```

**GameState enum:** `Boot, MainMenu, Playing, Paused, GameOver` + `OnStateChanged`. Semua sistem lock input / freeze timer berdasarkan ini.

- **Boot (1-2 dtk):** Init GameManager, Pool, Factory, Audio, load BalanceConfigSO (SSOT) + FTUE flag. Splash ikon awan saja.
- **MainMenu:** Planet auto-rotate pelan, 2 awan dummy drift, tanaman demo. UI ikon: [Main-awan besar], [Mute], [Ulang FTUE]. Drag planet boleh, drag awan locked. Tap Main → fade → Playing.
- **Playing (Endless, no win):** HUD cuma Pause kiri-atas. Input: drag awan (clamp Y), overlap merge hold 0.4 dtk + snap, edge 15% → rotate planet + panah, drag tanah → rotate manual, stay atas air 1 dtk → evaporasi tick. Bunga = visual only. Lose check `PlantManager.AliveCount==0` → GameOver (planet desaturate + awan fade + musik menipis). Tidak ada celebration menang.
- **Paused:** Trigger pause btn / Back HP / OnApplicationPause. timeScale 0, freeze semua. Panel: Resume (play), Restart (putar), Home (planet). Resume +0.5 dtk grace. Restart reset Run saja (12 slot kering ulang, 6 tanaman hidup ulang, 2 awan Normal). Home lerp rotasi ke 0.
- **GameOver:** Trigger tunggal tanaman habis. Panel ikon: Restart + Home + ikon planet kering (tanpa skor/teks). Restart langsung Playing, Home ke MainMenu.
- **Fail-safe:** Awan kecil habis → puff hilang. Semua awan hilang tapi tanaman hidup → force spawn 1 Normal 2 dtk (anti softlock).

## 2. Scene Breakdown (1 Scene: `Game`)

```
Game
├── Boot (GameManager, BalanceConfigSO, EventChannels)
├── World
│   ├── PlanetRoot (PlanetRotator rotate Z)
│   │   ├── SoilSlots x12 (SoilSlot.prefab + Puddle child)
│   │   ├── WaterSpots x2 (spawner + evaporasi zone)
│   │   └── PlantSpots x6 (parent ke SoilSlot)
│   └── Sky (CloudManager, PlantManager-AliveTracker)
│       ├── Clouds (pooled) + RainFX (pooled)
├── Systems (InputManager, CloudFactory, PoolManager, AudioManager)
└── UI Canvas (HUD-PauseOnly, MainMenuPanel, PausePanel, GameOverPanel - MVP)
```

Kamera ortho fixed. Tanah/air/tanaman child PlanetRoot (ikut muter). Awan/hujan child Sky.

## 3. Arsitektur Implementasi Checklist

- Core.asmdef: GameManager Singleton, GameState, EventChannelSO 6 biji, BalanceConfigSO, Singleton<T>.
- Feature.Cloud: CloudController + CloudStateMachine (Small 8 dtk / Normal 40 dtk / Dark auto-rain) + Factory + VisualSO.
- Feature.Soil: SoilSlot + State Dry/Wet + Dirty Flag visual lerp.
- Feature.Plant: PlantController + State Seed/Growing/Thirsty/Dead/BloomVisual + PlantManager AliveCount.
- Feature.Planet: PlanetRotator (max 60 deg/dtk + easing) + EdgeScrollDetector (85% x) + EvaporasiZone.
- Feature.Input: bedakan drag awan vs drag tanah vs edge, deadzone jelas.
- Feature.FX: RainPool, PuddlePool, UapPool via UnityEngine.Pool.
- UI.asmdef: MVP 4 presenter (MainMenu, HUD-Pause, Pause, GameOver). Full ikon, no teks/angka.
- Audio.asmdef: Event→SFX map (pop merge, hujan loop, tumbuh, mati, gameover thin) + BGM chill 2-bar loop FL Studio + adaptive layer hujan/mekar.

Pola wajib: SSOT, SOLID, Runtime State Separation (Persistent FTUE vs Run), Layered Architecture, Observer decoupled, Flyweight shared SO.

## 4. Balancing Anchors v0.1 (SSOT, tuning via Spreadsheet)

- cloudSmallLife 8, cloudNormalLife 40, mergeHold 0.4, snapRadius 0.6
- evaporateTick 1.0, rainToWet 2.0, soilWetDuration 25
- plantGrow 15, thirsty 10, death 12, bloomVisual 20 (hiasan)
- planetRotate 60 deg/dtk, edgeZone 15%
- start: 2 Normal, max 5 awan, emergency 2 dtk
- lose: AliveCount==0. No win target.

Resource flow endless: `Air tak terbatas (butuh muter) → Awan terbatas lifetime → Basah sementara → Hidup butuh rutin`. Lihat [[Map Resource Flows]] + [[Establish Math Anchors]].

## 5. Task 2 Minggu (Solo)

**Minggu 1 — Core Fun:**
1. Core + GameManager enum 5 state + EventChannel + BalanceConfigSO
2. PlanetRoot + 12 slot whitebox + drag rotate
3. Cloud drag + merge overlap + hujan placeholder
4. Soil Dry/Wet + Dirty Flag
5. Plant state + AliveTracker + GameOver placeholder
6. Playtest 2 menit: merge-hujan-tumbuh jalan?

**Minggu 2 — Flow + Juice:**
7. Factory + Pool ganti sprite awan lucu
8. Edge-scroll + panah + clamp/easing
9. UI MVP: MainMenu + HUD Pause + Pause + GameOver (tanpa skor)
10. FTUE 5 langkah + PlayerPrefs flag
11. Audio decoupled + BGM loop
12. Balancing survival + build APK + playtest buta 5 menit

## 6. Go / No-Go + Skill List

- **Lolos:** baru tanpa teks bisa merge + hujan + muter + bertahan >3 menit, mau retry.
- **Gagal:** edge salah >3x, merge random, mati <1 mnt / bosen >10 mnt → tuning SO dulu.

**Skill dipakai:** [[Deconstruct Mechanics]], [[Identify Core Loops]], [[Designing FTUE (First-Time User Experience)]], [[Tutorial Level Building Blocks]], [[Level Design Workflow (Whiteblocking & Modular)]], [[Single Source of Truth (SSOT)]], [[Centralized State Manager (GameManager Singleton & Event)]], [[Simple FSM Berbasis Enum (Game State Prototyping)]], [[State Pattern (Unity FSM)]], [[Observer Pattern Events]], [[Factory Pattern (Unity)]], [[Object Pool Pattern (Unity)]], [[SOLID Principles (Unity)]], [[Runtime State Separation]], [[Layered Architecture Stabilization]], [[MVP Pattern (Unity UI)]], [[Dirty Flag Pattern (Unity)]], [[Flyweight Pattern (Unity Shared Data)]], [[Apply Balance Foundations]], [[Map Resource Flows]], [[Establish Math Anchors]], [[Spreadsheet Setup]], [[Decoupled Audio System (Event Channel & Pooling)]], [[Adaptive Audio (Vertical Layering & Horizontal Resequencing)]], [[Teori Musik Dasar untuk Game BGM]], [[Prospect & Refuge Spatial Design]], [[Framework Kihon-Kata-Kumite (Learning Curve & Encounter Design)]].

---
Next: eksekusi Phase 0 — Core + Planet whitebox + AliveTracker.
