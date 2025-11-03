# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Unity 3D project simulating medical radiation therapy equipment. The simulation includes a patient positioning system with animated gantry, couch assemblies, and collision detection for safety visualization.

**Unity Version:** 2020.3.33f1

## Project Structure

```
Biohackathon2022/
├── Assets/
│   ├── Scenes/
│   │   └── Main.unity          # Primary scene
│   ├── Scripts/
│   │   ├── Controller.cs       # Animation instruction system
│   │   └── collider.cs         # Collision detection & effects
│   ├── Models/                 # Medical equipment OBJ models
│   │   ├── gantry.obj          # Radiation gantry
│   │   ├── upper_arm.obj
│   │   ├── lower_arm.obj
│   │   └── Track.obj
│   ├── Materials/              # Unity materials
│   ├── pbr_textures/           # PBR texture assets
│   └── Kevin Iglesias/         # 3D character dummy asset
```

## Core Architecture

### Controller System (Controller.cs)

The instruction-based animation system that drives equipment movement:

- **Target System**: Enums define animatable objects (BaseAssembly, MidAssembly, CouchAssembly, Patient)
- **Action System**: Translation and rotation operations with duration-based interpolation
- **Instruction Groups**: Sequential execution of instruction batches using FixedUpdate
- Instructions are processed as FIFO queue, removed upon completion

Key concepts:
- Each `Instruction` contains target, action type, final transform state, and duration
- Initial transform state captured on first execution (t0)
- Linear interpolation (Lerp/Quaternion.Lerp) for smooth transitions
- Uses local space transforms (localPosition, localRotation)

### Collision System (collider.cs)

OnTriggerEnter-based collision detection with explosion effects:
- Excludes specific objects from collision ("Dummy", "Shaft")
- Instantiates explosion GameObject at collision point
- Used for safety visualization when equipment intersects patient/objects

## Development Commands

### Opening the Project
Open the `Biohackathon2022` folder in Unity Hub using Unity 2020.3.33f1

### Key Unity Packages
- Universal Render Pipeline (URP) 10.8.1
- ProBuilder 4.5.2
- TextMeshPro 3.0.6

## Adding New Animations

To add equipment motion sequences:

1. Create instruction groups in `Controller.Start()`
2. Define target object (BaseAssembly, MidAssembly, CouchAssembly, Patient)
3. Specify action (Translate or Rotate) with final position/rotation
4. Set duration in seconds
5. Instruction groups execute sequentially; instructions within a group execute in parallel

Example:
```csharp
var ig = new List<Instruction>() {
    new Instruction(Target.BaseAssembly, Action.Translate, 0, -0.35f, 0, 5f),
    new Instruction(Target.MidAssembly, Action.Rotate, 0, 180, 0, 5f),
};
instructionGroups.Add(ig);
```

## Scene Configuration

Main scene (Assets/Scenes/Main.unity) requires:
- Controller component attached to GameObject with references to:
  - BaseAssembly GameObject
  - MidAssembly GameObject
  - CouchAssembly GameObject
  - Patient GameObject
- Collider components on objects requiring collision detection
- Explosion prefab reference for collision effects

## Important Notes

- All transforms use local space (relative to parent)
- Rotation specified in Euler angles (degrees)
- Time-based interpolation in FixedUpdate for physics consistency
- Collision exclusion list prevents false positives on equipment components
