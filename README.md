# Games Cryptid Veggie Town Massacre

![Language](https://img.shields.io/badge/language-C%23-blue)
![Engine](https://img.shields.io/badge/engine-Unity%203D-black)
![AI](https://img.shields.io/badge/AI-NavMesh-orange)
![Build](https://img.shields.io/badge/platform-Windows%20x64-lightgrey)

A 3D wave-based first-person shooter built in **Unity** with **C#**. Fight through escalating waves of sentient produce across 4 playable levels, wielding a multi-weapon loadout against 13 distinct enemy types — each with its own NavMesh AI, attack pattern, and behavioral quirks. Survive the Daikon King boss encounter to win.

> Part of the Squatchworks portfolio. Developed by [Jacob Blackburn](https://github.com/Squatchworks) and the Algorithm Architects team.

---

## Gameplay

- First-person shooter with sprint, jump, and multi-jump
- 4 playable levels with escalating enemy counts and difficulty
- Kill all enemies to advance — enemy count tracked against a game goal via `gameManager`
- Flashlight toggle for dark environments
- Bounce pads for traversal and verticality
- Day/night cycle with dynamic skybox and rain controller
- Pause menu, win screen, and lose screen with randomized voice quip audio

---

## Enemy Roster

13 individually scripted enemy types, each implementing the `IDamage` interface and driven by Unity's NavMesh pathfinding:

| Enemy | Behavior |
|---|---|
| **Apple** | Burst-fire shooter — fires in configurable bursts with delay between shots |
| **Banana** | Burst shooter with split-bullet projectile on death |
| **Beet** | Animated melee/ranged hybrid with `Animator` integration |
| **Cabbage** | Animated ranged attacker with standard NavMesh pursuit |
| **Carrot** | Standard ranged chaser |
| **Cucumber** | Limited-ammo shooter — stops engaging when ammo depleted |
| **Daikon** | Standard ranged AI, base variant of the boss |
| **Daikon King** | Boss enemy — scripted boss fight with elevated HP pool and attack patterns |
| **Eggplant** | Gas-based enemy — deploys a poison gas cloud that attaches to the player, applies DoT and post-process depth-of-field blur |
| **Orange** | Weak-spot mechanic — armored until opened; `OrangeWeakSpot` component gates damage intake |
| **Pepper** | Animated ranged attacker |
| **Pumpkin** | Rapid-fire gatling variant — fires up to 200 rounds per burst then enters a cooldown |
| **Radish** | Suicide bomber — pursues player and detonates on proximity with particle explosion and AoE damage |
| **Strawberry** | Erratic shooter — moves randomly within a range while burst-firing |
| **Tomato** | Animated NavMesh pursuer with movement-driven animation state |
| **Turnip** | Animated ranged chaser |

All enemies share: configurable HP, respawn limit, render-distance culling, per-enemy health bar slider, hit-flash color feedback, and `gameManager` goal tracking on spawn.

---

## Player Systems

- `CharacterController`-based movement with configurable speed, sprint multiplier, jump height, jump count, and gravity
- **Status effects:**
  - Fire DoT — damage over time at a configurable rate and duration
  - Bleed DoT — separate damage-over-time track
  - Butter slow — reduces movement speed for a configurable duration, disables sprinting
  - Toxic gas — triggered by Eggplant proximity; applies poison DoT and post-process blur
- Multi-weapon inventory via `List<gunStats>` — weapon switching, per-weapon mag size, fire rate, and damage
- Ammo pickup and powerup pickup systems
- Muzzle flash and hit particle effects
- Full sound manager integration (`PlayerSoundManager`)

---

## Architecture
Assets/Scripts/
├── PlayerController.cs # Movement, status effects, weapon inventory, IDamage implementation
├── gameManager.cs # Singleton — enemy goal tracking, UI refs, player reference
├── MainManager.cs # Cross-scene persistent manager (DontDestroyOnLoad pattern)
├── EnemyAI.cs # Base enemy logic shared across types
├── EnemyFactory.cs # Enemy spawning and wave management
├── gun.cs # Shooting logic — raycast, muzzle flash, hit effects
├── gunStats.cs # ScriptableObject-style stats struct per weapon
├── IDamage.cs # Interface — takeDamage(int) contract across all damageable objects
├── damage.cs # Projectile damage applicator
├── DaikonKing.cs # Boss fight logic
├── DaikonAI.cs # Standard Daikon enemy
├── CarrotAI.cs # Carrot enemy
├── PowerupPickup.cs # Powerup trigger and application
├── CameraController.cs # Player camera logic
├── DayNightCycle.cs # Time-of-day system driving skybox rotation
├── SkyboxController.cs # Skybox blend and lighting updates
├── RainController.cs # Environmental rain particle system
├── BouncePad.cs # Trigger-based launch pad
├── ToxicEnemy.cs # Toxic gas enemy base
├── ToxicGasTrigger.cs # Gas zone trigger for player effect application
├── ParticleEffect.cs # Generic particle spawn utility
├── ButtonFns.cs # UI button callbacks (menu, pause, restart, quit)
├── LoadingScreen.cs # Async scene loading with progress bar
├── LogoManager.cs # Splash/logo sequence manager
└── MenuMusicManager.cs # Persistent menu audio controller

Assets/Prefabs/Enemies Final/
├── Apple, Banana, Beet, Cabbage, Cucumber, Eggplant
├── Orange/ (OrangeAI + OrangeWeakSpot)
├── Pepper, Pumpkin, Radish, Strawberry
├── Tomato/ (TomatoAI + Animator)
└── Turnip/


---

## Tech Stack

| Layer | Tech |
|---|---|
| Engine | Unity (3D) |
| Language | C# |
| AI / Pathfinding | Unity NavMesh + NavMeshAgent |
| Physics | Unity CharacterController + Rigidbody projectiles |
| Audio | Unity AudioSource + randomized AudioClip arrays |
| Post-Processing | Unity Post Process (DepthOfField on toxic gas) |
| UI | Unity UI (Slider HP bars, TextMesh Pro) |
| VFX | Unity Particle System (explosions, muzzle flash, gas, hit effects) |
| Version Control | Git |

---

## Lessons Learned

- **`IDamage` interface** decouples the damage pipeline — any object implementing `takeDamage(int)` can receive hits from projectiles, explosions, or DoT without the damage source needing to know what it hit.
- **Per-enemy scripting vs. base class inheritance** — each enemy has its own AI script rather than a deep inheritance chain. This made it easy to give each enemy a unique twist (burst fire, gas cloud, weak spot, suicide charge) without fighting a shared base class, at the cost of some duplicated NavMesh boilerplate.
- **Respawn limits on enemies** — `maxRespawns` and `currentRespawnCount` on each AI allow designer-controlled respawn behavior per enemy type without a centralized spawn manager needing to track individual enemy state.
- **Randomized audio quip pools** — storing multiple audio clips per UI action (pause, quit, lose, next level) and selecting randomly gives the game personality without any additional logic cost.

---

## Roadmap

### Current build
- 4 playable levels across wave-based FPS encounters
- 13 enemy types with individual AI scripts and behaviors
- Full player status effect system (fire, bleed, butter slow, toxic gas)
- Boss encounter (Daikon King)
- Day/night cycle, rain, dynamic skybox

### Planned — future iteration
- Additional levels and enemy types consistent with the Cryptid Forge Studios aesthetic
- Refined boss fight with multiple phases
- Weapon upgrade and crafting system
- Persistent progression between runs

---

## Team

Built by the **Algorithm Architects** team.
- **Jacob Blackburn** — Gameplay systems, enemy AI, UI systems, level logic
- GitHub: [@Squatchworks](https://github.com/Squatchworks)
- LinkedIn: [linkedin.com/in/squatchworks](https://linkedin.com/in/squatchworks)
