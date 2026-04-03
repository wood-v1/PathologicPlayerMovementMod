# Pathologic Player Movement Mod (PPMM)

## Overview

The mod adds configurable sprint and movement tweaks to Pathologic Classic HD.

The mod is a DLL, injected into main game's process `Game.exe` and runs through a separate launcher [PPMMLauncher](https://github.com/wood-v1/PathologicPlayerMovementModLauncher).

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

- DLL uses [code caves to modify movement](hook.cpp) at runtime inside `Engine.dll` (for reference see [Engine_dll_asm.txt](Engine_dll_asm.txt))
- DLL uses [hook into the game console](execute_hook.cpp) to call a [script](ppmm_stats_effect.sc) that increases exhaustion stat.

All characters share the same movement system, so changes apply globally.
The file structure must be as follows:

## Installation

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
* Default value - 0  

---

### [Constants]

**MoveSpeedMultiplier**

* Description - Controls player movement speed. Lower values make the player run faster.  
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
