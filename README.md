<details>
<summary><strong style="font-size: 2em;">📌 Table Of Contents</strong></summary>

- <details>
  <summary><strong style="font-size: 1.5em;">How enemies spawn</strong></summary>
  GetEnemie() from EnemiesPool gets called from different components (EnemieRoomSpawner, BossRoomSpawner,
  EnemiesWavesSpawner) Despite pool, only EnemieRoomSpawner returns enemies to pool. So bosses are not pooled practically.
  Each enemie has an EnemySpawn component, on death, if the room is still available they are queued for respawn. This is
  how enemies keep respawning. Each enemie has an health component, this is how they get damage and calculate if they are dead.
  </details>
- <details>
  <summary><strong style="font-size: 1.5em;">How enemies move</strong></summary>
  EnemieTarget component is how enemies track player. Player has this component. Enemies move via NavMeshAgent
  component. EnemieRaging constantly checks if player is in visual range or is hit by player, and if it is, EnemieRaging starts raging
  state, stopping walking and setting NavMeshAgent enabled via EnemieWalking component. EnemieTargetFollower is the one that sets
  NavMeshAgentSpeed according to enemie state. Enemie triggers onCanAttack event if it is close enough to player and EnemieAttack
  start AttackRoutine routine.
  </details>
- <details>
  <summary><strong style="font-size: 1.5em;">How do I add a new enemy</strong></summary>
  EnemiesController in the scene holds all references for enemy prefabs. It has them as a child and adds them to a list at
  Start. Enemie GetEnemieByType(EnemieType enemieType) function returns needed enemy prefab to EnemiesPool, and it spawns it accordingly.
  </details>
- <details>
  <summary><strong style="font-size: 1.5em;">How shooting system works</strong></summary>
  DetectRadar component of player checks visible enemies each update. Then given PlayerShooter mechanism
  works and player shoots accordingly.
  </details>
- <details>
  <summary><strong style="font-size: 1.5em;">How bullets work</strong></summary>
  Shoot() function inside of PlayerShooter determines current shoot type (gun, riffle, mg, staff, bazooka)
  according to WeaponsController's current weapon type and plays the corresponding animation via PlayerAnimsController.
  Then using current Weapon class, it starts ShootRoutine() coroutine. According to weapon, flame/staff bullet/bullet is shot.
  It Spawns a new bullet using BulletsController.
  </details>
- <details>
  <summary><strong style="font-size: 1.5em;">How do I add a weapon</strong></summary>
  Each weapon has an unique id as WeaponType enum. When new weapon is added, the new type should be added here
  and also, from WeaponsController in the scene, new weapon element should be added to list, and every detail about new weapon (damage,
  update, sprites) also is added here accordingly.
  </details>
- <details>
  <summary><strong style="font-size: 1.5em;">How do I add a new bullet</strong></summary>
  There is a BulletData.BulletType enum, new bullet type should be added here if it is to be called as a
  separate bullet. Also in the scene, new BulletData should be added to the list. Then new bullet can be used from guns via WeaponsController's
  WeaponsDatas list.
  </details>
- <details>
  <summary><strong style="font-size: 1.5em;">How player moves</strong></summary>
  Basically PlayerController handles the movement+rotation according to player state (walking/shooting) and given input via
  UpdateWalk() and UpdateRot() methods.
  </details>
- <details>
  <summary><strong style="font-size: 1.5em;">How player respawns</strong></summary>
  PlayerHealth is where player's health is handled. If current health is <= 0, a global event is sent via
  GameEventController as GameEvent.PlayerDeath and according to game play mode (death match, boss attack or hardcore) relative events
  sent to the controllers as well. if not death match or hardcore, PlayerDeathController.OnDie() gets called. Here player respawns
  using PlayerRespawner, in different situations according to game mode. If classic mode, player resets in  the last checkpoint.
  </details>
- <details>
  <summary><strong style="font-size: 1.5em;">How save system works internally</strong></summary>
  There is no one main save manager. Each component internally save itself via playerprefs.
  Building, Room, PlayerSaveLoadPosition handles saving their states in themselves.
  </details>
- <details>
  <summary><strong style="font-size: 1.5em;">How weapon upgrades work</strong></summary>
  With new upgrade system, after adding weapon, you need to set newUpgradeLevel attribute for that weapon data.
  It is done manually and there is no system for checking consistency, so should care about this. If the weapon is old/ won't be used in
  newUpgradedLevel should be set to -1.
  </details>
- <details>
  <summary><strong style="font-size: 1.5em;">How do i add a new level</strong></summary>
  A level (world) is combination of rooms. Each room has a attribute to set if it is opened at
  level beginning or not. If it is not open by default, you need to open it via its LootDropArea component. You set needen
  loot data via this component. Safe rooms usually consist Dynamic objects such as ActionArea(for weapon upgrades). and
  CameraFocuser for changing virtual camera when in a safe room. 
  World also contains other elements, QuestController has the quests as a child object. They can be Quest or StepsQuest.
  In initialization, World inits these quests via QuestController. To complete a level, last (exit) ActionArea of level should
  call Finish() from WorldFinish component
  NOTE: this quests each contain a lot of logic for both visually and logically in EDITOR events, so when debugging, should take care.
  They contain ActionListInvokers as child objects and have those logic events defined in them. They are mostly about opening the related room,
  enabling the current puzzle etc.
  </details>
- <details>
  <summary><strong style="font-size: 1.5em;">Classic/Hardcore/Deathmatch/Protector what are those</strong></summary>
  Classic mode is the normal gameplay user experiences from start.
  There are 10 worlds at the moment. After  entering base and completing portal, player can choose to play hardcore mode. In there you
  are playing with a timer and with single life. Death match is entered from the left menu from events button. After selecting it
  you also play against time, but this one is a countdown and purpose of the mode is staying alive.
  </details>
- <details>
  <summary><strong style="font-size: 1.5em;">How do I make loot</strong></summary>
  Each Enemie has its own loot data. You can add loop type you want there. They are manually added to
  each world enemy type prefab founded in the SampleScene(GameScene). For example "Enemie_L1_Orange_Small" is belong to World1 etc.
  </details>
- <details>
  <summary><strong style="font-size: 1.5em;">What is prestige mode</strong></summary>
  After completing normal levels, player is offered to play levels in harder mode. The mode settings
  is set in PrestigeController. There is total of 5 difficulty settings at the moment.
  </details>
- <details>
  <summary><strong style="font-size: 1.5em;">How missions work</strong></summary>
  MissionAchievementController stores all achievement data (daily,weekly,achievements) and displays them
  according to remote config. At start, after missions initialized, each mission listen relevant GameEvent and updates its state
  accordingly. After mission is completed, it fires OnStateChange event and MissionAchievementController handles it accordingly.
  [FollowCamera](#followcamera:)
  </details>
</details>

<details>
<summary><strong style="font-size: 2em;">📌 Scripts</strong></summary>

### FollowCamera
CM camera that follows player. Responsible for transition of player when enters a new room
### Room:

### RoomChecker
Checks room every frame(?) and if rooms is changed also calls FollowCamera singleton for transition.
This is not world(whole level) but single room that world contains.

### UIManager
Kind of a mediator/god object for every possible ui in game. Gives a static instance for every UI canvas.

### GameController
Sets frame rate and holds states for game pause / in focus. At start, waits until WorldsController.cs
completes navmesh operations and then loads game start canvas and force updates all canvases. During game it holds
time elapse for all game modes (classic/hardcore/death match) and loads them during initialization.

### LevelController
At Start, inits world using WorldsController

### WorldsController
Responsible for instantiating and setting currentWorld, called from LevelController, gets world index from LevelController.
Calls LootLoader, WeaponsController, HatsController and inits them all. Also sets up navigation via NavigationController,
Gets it from currentWorld(World)

### WeaponsController
Controls the current weapon, also has all weapon data as enums, and for enums each upgrade data
is defined inside of that element.

### MenusQueueController
Controls popup order. Each ui in game is UIMenu and controlled via this component, so they are added to queue when
Open() is called and shown accordingly.

### NewUpgradeController
Responsible for adding upgrades via different methods (IAP, game currency), and updates each possible attribute;

### DetectRadar
Checks enemies around player after each update. Holds them in list. PlayerShooter selects the appropiate target then.
This logic is also updated with new taunt system, but has the old behaviour if taunt is not used.

### PlayerShooter
After fetching visible enemies from DetectRadar, it selects the current target. If there is an already selected one
and that enemy is still viable, uses that enemy without making any other calculations. Otherwise selects the nearest enemy. This is also
overriden by new taunt mechanic, but similar to DetectRadar, if it is not used it keeps the old behaviour.

### PlayerController
Moves player via rigidbody+velocity. Physics is updated via PhysicsSimulateController. It keeps values for
walk and rotate speed.

### PlayerAnimsController
Using the values from PlayerController, applies the relative animationSpeed.

### Bullet
Abstract base class for all other bullet types. It has bullet attributes such as bulletData, its damage, direction, 
shoot position, if its fevered etc. Each child class then (flame,staff,simple) implements its own move logic.

### EnemyLayer
Sets enemies layer on spawn according info it fetches from EnemieSpawnController

### PhysicsSimulateController
Disable auto physics and simulate physics manually, it allows to stop game
in times such as ad popup appear.

### EnemiesWalkController
Adds debug normals of enemies to see in game in editor mode.

### LootDropArea

### ActionArea
Similar to LootDropArea, but for this one you don't need to provide loop. Used for things like Chest, AdsHelper

### Chest
Similar to AdsHelper, after ActionArea progress complete, opens chest ui via ChestsCollectController. After that
user selects either free or rewarded loot from here.

### AdsHelper
Where we call random worker. After completing ActionArea progress, ActionListInvoker calls AdsHelper Deactivate(),
which calls current World's WorldHelpers to call ActivateRandomHelper() function of HelpersController

### WorldFinish
Calls DoFinishAction(int id) from PlayerFinishActions. Id is defined according to world.

### GameEventController
Responsible for registering all events globally. A new event is defined by creating a new GameEvent enum.

### UpgradeUIInstance
After corresponding ActionArea progress completed, this one opens a ui instance for selected upgrade enum
type (Weapons,Hats,Helpers)

### LoadingSceneController
Set last played story mode (hard or story mode) and load the SampleScene(game scene)
</details>