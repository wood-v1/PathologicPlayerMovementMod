# Pathologic Player Movement Mod (PPMM)

## Overview

The mod adds configurable sprint and movement tweaks to Pathologic Classic HD

The mod is a DLL, and can be launched in several ways:
1. Through a separate launcher [PPMMLauncher](https://github.com/wood-v1/PathologicPlayerMovementModLauncher)
2. Through an [Ultimate ASI Loader](https://github.com/ThirteenAG/Ultimate-ASI-Loader) (installation instructions below)

## Core Features

- Configurable sprint (Shift / Caps Lock toggle)
- Movement speed multiplier
- Jump height multiplier
- Landing gravity adjustment
- Optional debug console

### Tiredness Effect

- Optional tiredness effect that applies exhaustion during sprint
- The Exhaustion stat slowly grows during sprint
- The Exhaustion stat changes only while active movement

## How it works

It works by patching the game's assembly at runtime.

The mod uses the *code caves* technique to inject hooks into the `Game.exe` process, targeting movement-related code inside [`Engine.dll`](Engine_dll_asm.txt). It identifies instruction blocks responsible for movement multipliers and patches them, redirecting execution to injector code (see [`hook.cpp`](hook.cpp)).

This custom code operates on pointers to multiplier variables, which are dynamically updated based on `Shift` / `Caps Lock` input.

For the Tiredness Effect, the mod hooks into the in-game console (see [`execute_hook.cpp`](execute_hook.cpp)) to execute a script ([`ppmm_stats_effect.sc`](ppmm_stats_effect.sc)). The script handles game logic (that is too complex to implement purely in the injector) - detecting player movement and updating the exhaustion stat.

All characters share the same movement system, so changes apply globally.

## Installation 

### Installation via PPMLauncher

The file structure must be as follows:

    Pathologic Classic HD/
    │
    ├─ bin/
    │   └─ Final/
    │       ├─ Game.exe
    │       ├─ ...
    │       ├─ PPMM.dll - `Core mod library`
    │       ├─ PPMM.ini - `Configuration file`
    │       └─ PPMMLauncher.exe - `Injector used to launch the game and inject PPMM.dll at startup`
    │
    ├─ data/
    │   └─ Scripts/
    │   │   └─ ppmm_stats_effect.bin - `used to implement Tiredness Effect`
    │   └─ init.cfg - `used to reassign the crouch key from LSHIFT to Left CTRL`

### Installation via Ultimate-ASI-Loader

- Download `dinput8.dll` from [Ultimate-ASI-Loader repository](https://github.com/ThirteenAG/Ultimate-ASI-Loader)
- Rename PPMM.dll to PPMM.asi and place it with `dinput8.dll` in the bin/Final/ folder


The file structure must be as follows:

    Pathologic Classic HD/
    │
    ├─ bin/
    │   └─ Final/
    │       ├─ Game.exe - `With this configuration, simply run it to start the modded game`
    │       ├─ ...
    │       ├─ PPMM.asi - `Core mod library`
    │       ├─ PPMM.ini - `Configuration file`
    │       └─ dinput8.dll - `ASI Loader used to load the PPMM.asi at startup`
    │
    ├─ data/
    │   └─ Scripts/
    │   │   └─ ppmm_stats_effect.bin - `used to implement Tiredness Effect`
    │   └─ init.cfg - `used to reassign the crouch key from LSHIFT to Left CTRL`

## Controls

- **Shift** - Hold to activate sprint
- **Caps Lock**  - Toggle sprint on/off

## Configuration

Settings are stored in `PPMM.ini`.

Example:

    [General]
    Debug=1
    TirednessEffect=1

    [Constants]
    MoveSpeedMultiplier=0.43
    JumpHeight=1.078
    JumpLandingGravity=-600
    TirednessDelta=30

## PPMM.ini Settings Guide

### [General]

**Debug**

* Description - 1 to enable debug information (only for testing purposes), 0 to disable
* Type - boolean
* Default value - 0

**TirednessEffect**

* Description - Enables exhaustion gain while sprinting. When enabled, sprinting will gradually increase player exhaustion.
* Type - boolean
* Default value - 1

---

### [Constants]

**MoveSpeedMultiplier**

* Description - Controls player movement speed. Lower values make the player run faster. In more detail, it's essentially a multiplier of the friction factor, so the smaller this force, the faster the character moves.
* Type - float
* Default value - 1.0

**JumpHeight**

* Description - Controls the height of jumps. Higher values make the player jump higher.  
* Type - float
* Default value - 1.0

**JumpLandingGravity**

* Description - Controls how fast the player falls after a jump. Higher values make the player "lighter" and fall slower. Constant also affects fall damage and collisions with obstacles. Setting it to 0 will prevent the player from falling after a jump.
* Type - integer
* Default value - 2500

**TirednessDelta**

* Description - Controls how quickly exhaustion increases during sprint. Higher values result in faster exhaustion buildup.
* Type - integer
* Default value - 30
