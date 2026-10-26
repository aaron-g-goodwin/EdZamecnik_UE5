---
custom-width: 85
tags:
  - EntryDate
  - EntryTime
---
# Changelog - [[Edward Zamecnik - UE5.7 - VR]]

## [Release Ready]

# Friday 10.04.2026

### 🟢 Added

- Victory-stone wind-up animations with completion callbacks controlling each launch.
- Smooth startup/restart stone lifts that follow the hands with world-down offsets.
- Separate white-Djinn test material with adjustable whitening, body glow, and Fresnel rim glow.
- Full-color variation retaining the new glow effects.

### 🟡 Changed

- Victory sequencing now waits for each wind-up before launching and hiding the corresponding held stone.
- Disabled on-screen debug messages while retaining Output Log messages.
- Restored TargetFireballCount from 5 to 111.
- Increased fireball visual size without changing collision or planning radius.
- Tuned heat distortion; its visibility remains subtle during fast flight.

### 🔵 Verified

- Stone presentation looks and functions correctly in playtesting.
- Startup/restart lifts return stones to their normal hand positions.
- Debug-message suppression, target-count update, and larger fireball visuals confirmed.

### Pending Verification

- DjinnGlowLight setup and final environmental-lighting balance.

# Friday 10.02.2026 - Saturday 10.03.2026

### 🟢 Added

- Shuffled 1–3 shot bursts with randomized shot gaps and between-burst pauses.
- Burst-dependent speed ranges: fast singles, medium-to-fast doubles, and slow-to-medium triples.
- Consecutive-arc variation checks based on the last successfully launched shot.
- Victory-stone handoff that hides and disables collision on each matching held stone.

### 🟡 Changed

- Flight duration now derives from spline length and selected speed.
- Tuned MinArcVariation to 5.0 and increased planning to 22 candidate attempts per shot.
- Failed launches end the current burst and schedule the next pause.

### 🔵 Verified

- Burst pacing and trajectory variation feel good in playtesting.
- Win and loss stop further launches, including during bursts.
- Held stones disappear left before right and return functional after level reload.

# Friday 09.25.2026 - Monday 09.28.2026
### 🟢 Added
- Flight-path validation against room bounds, djinn clearance, and environmental obstacles.
- Editor path previews and diagnostic rejection logs.
- Varied launch-locked targets inside the player’s head and torso damage capsules.
- Configurable minimum arc bend to reject overly straight trajectories.

### 🟡 Changed
- Migrated live fireballs to individually copied splines with swept movement and forward continuation after reaching the target.
- Restored smooth quadratic arcs and tuned target spread for improved readability and deflection.
- Preserved existing visual spin, audio, and hit handling.

### 🔵 Fixed
- Room-clearance calculations incorrectly mixing scaled and unscaled distances.
- Launch-clearance failures through exclusion-capsule placement adjustments.
- Invalid player-camera reference and incorrect planning-radius default.
- Capsule-axis sampling and minimum-bend calculation wiring errors.

# Friday 09.18.2026

### 🟢 Added

- Added randomized fireball launch and impact audio using Sound Cues.
- Added looping spatial travel audio through `FlightAudio` in `BP_FireballProjectile`.
- Added separate attenuation assets for launch, travel, impact, and plume audio.
- Added looping crackling audio to the Djinn’s overhead plume.
- Added per-launch victory-stone mesh selection matching the left and right held stones.
- Added an opaque, emissive milky window pane to conceal the exterior behind the player.
- Added a window spotlight aimed toward the main gameplay space.
- Created project-owned `M_Room_Lit` from the Engine’s `M_Unlit` material.

### 🟡 Changed

- Reparented victory-stone launch points to `BP_VRPawn > Camera` and positioned them over the player’s shoulders.
- Refined stone mesh sizes and normalized associated component scales to `1,1,1`.
- Configured launch and impact cues to select variations randomly without replacement.
- Connected launch and travel playback to the end of `InitializeCurvedFlight`.
- Extended the existing guarded impact function to stop travel audio and play an independent impact sound before spawning visual effects.
- Added explicit travel-audio cleanup to `Event Destroyed`.
- Reparented the room’s material instances directly to `M_Room_Lit`, preserving their individual texture assignments.
- Changed room shading from Unlit to Default Lit, routed surface color into Base Color, and added a rough surface response.
- Disabled shadow casting on the Directional Light and milky window pane while retaining dynamic shadow casting on the room actors.

### 🔵 Fixed

- Fixed victory projectiles using outdated stone visuals.
- Fixed room surfaces failing to respond to the new spotlight because their materials used unlit emissive shading.
- Resolved unwanted bar-shaped shadows cast by the room geometry under the Directional Light.
- Removed the unwanted shadow cast by the window-pane cube.

### ⚪ Removed

- Removed the redundant player-hit `SpawnImpactVFX` call with an unconnected impact location.

### Production Notes

- Confirmed that only deflections count toward victory; avoided or missed fireballs do not count. Existing gameplay behavior was retained.
- Non-spatial win/loss audio cues are excluded from the current scope at the client’s request.
- Dark hallway treatment, final lighting/audio balance, and held-stone victory/restart animations remain pending.

# Friday 09.05.2026

### 🟢 Added
- Imported and configured the final Djinn model and its five three-tile UDIM texture sets.
- Created a virtual-texture Djinn material using Base Color, Normal, Ambient Occlusion, Roughness, and Opacity maps.
- Converted the Djinn into a Skeletal Mesh and created a custom skeleton, skin binding, Control Rig, and idle animation.
- Added `HeadFXRoot` for head-attached effects and projectile spawning.
- Added centralized win/loss transition handling to `BP_FireballGameManager`.
- Added slow-motion presentation for both victory and defeat.
- Added fade-to-black, audio fade, time-dilation restoration, and current-level reload behavior.
- Added separate victory impact markers to `BP_DjinnPlaceholder`.
- Added `GetVictoryImpactLocation`, selecting the left or right impact marker by stone index.
- Added left and right victory-stone launch markers to `BP_VRPawn`.
- Added `GetVictoryStoneStartLocation`, selecting the corresponding hand location by stone index.
- Added `BP_VictoryStoneProjectile` with configurable start location, target location, travel duration, and arc height.
- Added Timeline-driven parabolic flight and multi-axis tumbling for victory stones.
- Added the `OnStoneArrived` dispatcher for notifying the GameManager when a stone completes its flight.
- Added `StartVictoryStoneSequence`, `LaunchCurrentVictoryStone`, and `HandleVictoryStoneArrived` logic to launch two victory stones sequentially.
- Imported and prepared two individual stone meshes for left- and right-hand use.

### 🟡 Changed
- Replaced the Djinn’s static presentation with the animated Skeletal Mesh and looping idle animation.
- Attached the overhead fire plume and fireball spawn point to the Djinn’s head hierarchy.
- Enabled local-space behavior where required so the plume remains attached during Djinn animation.
- Updated game-over handling to use the existing `bGameOver` state as the single transition guard.
- Updated win and loss paths to pass through the shared `BeginEndTransition` presentation flow.
- Updated the Djinn shutdown flow so no additional fireballs spawn after game over.
- Preserved existing fireballs after game over so they can complete or expire naturally.
- Updated the player’s left and right deflectors to support different stone visuals.
- Changed victory-stone collision to `NoCollision`, allowing the cinematic Timeline to control the complete flight.
- Updated victory-stone sequencing to use the GameManager’s existing `Player` and `DjinnRef` references.
- Made victory-stone timing configurable for tuning under global slow motion.

### 🔵 Fixed
- Fixed the Djinn plume repeatedly orbiting or respawning away from the character.
- Fixed incomplete left/right return handling in `GetVictoryImpactLocation`.
- Fixed victory stones being destroyed during every Timeline Update.
- Corrected the projectile execution order so arrival and destruction occur only from the Timeline’s `Finished` output.
- Fixed the standalone victory-stone test disappearing before a visible flight by assigning a valid travel duration.
- Verified that the victory stone now flies, arcs, tumbles, reaches its destination, and completes normally.

### ⚪ Removed
- Removed the temporary `BeginPlay` victory-projectile test chain.
- Removed the temporary placed victory-projectile instance from the level.
- Removed the temporary `Destroy Actor` debugging breakpoint.
- Abandoned the experimental complex cloth-weight editing pass in favor of the stable existing idle animation.
# Saturday 08.08.2026

### 🟢 Added

- Added four randomized fireball arc profiles: High Left, High Right, Low Left, and Low Right.
    
- Added previous-profile tracking to prevent identical arc profiles from firing consecutively.
    
### 🟡 Changed / Optimized

- Expanded fireball midpoint variation for more distinct curved trajectories.
    
- Refined the Niagara fireball core and tail proportions.
    
- Adjusted the tail to follow the projectile’s curved flight path more naturally.
    
### 🔵 Fixed

- Fixed incorrect fireball flame rotation by disabling conflicting vortex forces.
    
- Added validity checks to prevent runtime errors from invalid references.
    
### ⚪ Removed / Deprecated

- Disabled vortex-based flame rotation that conflicted with projectile orientation.

# Monday 08.03.2026

### 🟢 Added
- Added a continuous Niagara fire plume above `BP_DjinnPlaceholder`.
- Added a Niagara-based visual body for `BP_FireballProjectile`.
- Added `VisualRoot` to separate projectile visuals from collision and movement behavior.
- Added one-shot Niagara impact VFX for fireball collisions with the environment, player deflectors, and player.
- Added duplicate-impact protection so a fireball spawns only one impact effect per collision.

### 🟡 Changed / Optimized
- Replaced the visible stylized fireball mesh with a more realistic Niagara fire effect while retaining the mesh as a hidden fallback.
- Configured the fireball-body emitters to use Local Space so the effect remains coherent while the projectile moves.
- Reoriented the fireball Niagara component to follow the projectile's changing curved trajectory and extend behind its direction of travel.
- Configured impact VFX to spawn independently at the collision point, complete after one activation, and clean itself up after the projectile is destroyed.
- Separated the Djinn plume, traveling fireball, and impact effect into project-owned Niagara copies for independent tuning.

### 🔵 Fixed
- Fixed traveling fireball particles appearing as separated, low-framerate flame snapshots along the flight path.
- Fixed the fireball effect remaining vertically oriented instead of following its trajectory.
- Fixed impact VFX being destroyed with the projectile by spawning it as an independent Niagara system.

### ⚪ Removed / Deprecated
- Deprecated the stylized static-mesh fireball as the primary projectile visual; retained it as a hidden fallback.


# Sunday 07.19.2026
### 🟢 Added
- Randomized fireball arcing
- Restored player tracking

# Saturday 07.18.2026
### 🟡 Changed / Optimized
- Continued fireball projectile arcing
- Stone Deflectors:
	- Disabled grab/release functionality on stone deflectors
	- Reassigned stone deflector blueprints to player R/L hands
	- Scaled deflector visuals
	- Updated collision to block WorldDynamic (so player hands do not go through surfaces of room)

### ⚪ Removed / Deprecated
- Removed most FAB assets/unused template content
# Friday 07.17.2026
### 🟢 Added
- Blueprint functionality for fireball projectile arcing

### 🟡 Changed / Optimized
- Settled on room:actors scale
- Refactored template content to reduce project size

# Friday, 7.03.2026

### 🟢 Added
- FAB Assets:
	- Eroding Ring - Fireball origin over Djinn head
	- Arrow Trail - Fireball trail
	- MsvFx Niagara Explosion Pack 01 - Fire FX/Fireball
	- Vefects (Free_Fire) - Realistic Fire FX/Fireball
- 3 different scaled copies of room to test player/pawn size
### 🟡 Changed / Optimized
- Content Folder: Moved all 3rd party assets to FAB content folder


--- 

# #EntryDate | 06.19.2026
1. `BP_FireballProjectile`
	- Event Graph to check both actor and component tags for `PlayerDeflector`.
2. `BP_StoneDeflector`
	- Moved blueprint to Blueprints/Actors.
	- Wired Event Graph `GrabComponent` collision override events  `OnGrabbed` and `OnDropped` collision settings to ensure `BP_StoneDeflector` triggers collision events with `BP_FireballProjectile`.
	- Tested in VR to confirm stone deflectors trigger collision events.

# #EntryDate | 06.15.2026
- Created `BP_StoneCollider` to replace colliders directly attached to `BP_VRPawn` hands

# #EntryDate | 06.13.2026

## Log Details - 1825
- Fixed an issue with BP_DjinnPlaceholder where fireball count events were not being stored and recalled in log during testing.
	- Blueprint event initialization was not setting game manager class due to a missing Get[] node.

## Log Details - 1400
- Fireball->Deflector Collision Events:
	- Edited collision settings on `BP_FireballProjectile` & `BP_VRPawn` components
	- Was overlap-based, now block-based
	- Relocated `DestroyActor` nodes in `BP_FireballProjectile` to collision checks from `RegisterOutcome` pathway
		- Fireballs are now properly destroyed upon contact with player deflectors
- Fireball->Environment Collision Events:
	- Edited collision settings on environment assets
	- Fireballs now destruct on collision with environment assets (floors, walls, etc)
- Captured VR Preview footage of fireballs destructing on collision with player deflectors & resulting log statements reflecting fireball event status

# #EntryDate | 06.07.2026
## Log Details - 1144
- Enabled SM6 Windows D3D12 Targeted Shader Formats for project
- Made `DirectionalLight` `Moveable = true` to fixed unbuilt light interactions warning
- Reconfigured `BP_FireballProjectile`:
	- Added print statements to reflect `BP_FireballProjectile` collision events
	- Rewired `RegisterOutcome` path to connect collision event print statements
	- Verified collision events & print statements function
- Player/Deflector colliders:
	- Edited player colliders/settings
	- Edited deflector colliders/settings
	- Verified player collision functions
	- Verified deflector collision functions