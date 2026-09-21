# Hexforge Arena

> **Note:** Due to academic integrity policies and plagiarism regulations associated with this course, the full source code for this project cannot be publicly released.  
> This repository showcases system design, implementation details, and demonstrations of the project. Please feel free to reach out if you would like to discuss the technical implementation in more detail.

Real-time 2-player fighting game on the DE1-SoC implemented in bare-metal C, with direct memory-mapped VGA rendering and no operating system or graphics libraries.

---

**Authors:** [Apiwich Sumeksri](https://github.com/apiwichs) and [Shangzhe Tian](https://github.com/tsz-1) — ECE243, University of Toronto (Winter 2026). Co-designed as a team of two.

---

## Demo

<p align="center">
  <img src="gifs/zoom_demo.gif" alt="Gameplay demo" width="650"/>
</p>

<p align="center">
  ▶️ Full dynamic camera zoom demo (real-time): 
  <a href="https://youtu.be/ivtzvIkwsYQ">Watch here</a>
</p>

---

## Overview

This project implements a real-time 2-player fighting game entirely on an FPGA using bare-metal C. The system directly interfaces with memory-mapped I/O for VGA output and input handling, without relying on any OS or graphics framework.

The game integrates real-time input processing, animation state machines, collision detection, and rendering within a single deterministic loop under tight performance constraints.

---

## Controls

### Player 1
- Movement: `W` `A` `S` `D`
- Attack / Parry: `J`
- Defense: `K`
- Ability: `N/A`

### Player 2
- Movement: Arrow Keys
- Attack: `NUMPAD 1`
- Defense (Block / Parry): `NUMPAD 2`
- Ability: `NUMPAD 3`

---

## Core System Design

### Movement and State Management
Player behavior is implemented using explicit state machines (idle, walk, attack, defense, ability). State transitions are driven by input and timing constraints to ensure deterministic behavior.

Positions are maintained in world coordinates and transformed into screen coordinates through the camera system, decoupling game logic from rendering.

### Attack and Hit Detection
Attacks are frame-accurate and active only during specific animation windows. Hit detection is synchronized with animation frames, requiring precise coordination between rendering and gameplay logic.

### Defense
Defense is implemented as a hold-based state that remains active while the input is pressed. This required continuous input handling and correct synchronization with animation, movement constraints, and damage logic.

### Parry System
Parry is implemented as a separate timing-sensitive mechanic with a short active window. Successful parries negate and reflect incoming attacks, requiring precise coordination between state transitions, frame timing, collision detection, and attack resolution.

<img src="./gifs/parry_gif_demo.gif" alt="Parry Demo" width="500"/>

### Ability System
Abilities spawn additional entities (e.g., projectiles or spells) with independent lifecycles. These entities are updated and rendered in real-time using the same collision and rendering pipeline.

---

## Camera and Dynamic Zoom

A dynamic camera system tracks both players and controls rendering to the VGA display.

- Camera position is computed as the midpoint between players  
- Zoom level is adjusted based on inter-player distance  
- Rendering uses nearest-neighbor scaling implemented directly in software  

All scaling is performed during pixel writes to the VGA buffer without hardware acceleration, requiring careful optimization to maintain real-time performance.

---

## Example Mechanics (Visuals)

### Warrior
**Class Type:** Melee  
**Focus:** Close-range attacks, defense, and parry timing  

**Attack and Defense (Parry / Block)**

<img src="./gifs/warrior_mechanics_gif.gif" alt="Warrior mechanics demo" width="500"/>

---

### Sorceress
**Class Type:** Ranged  
**Focus:** Projectile attacks, defense, and special ability usage  

**Attack, Defense, and Ability**

<img src="./gifs/sorceress_mechanics_gif.gif" alt="Sorceress mechanics demo" width="500"/>

---

## Systems Engineering Highlights

- **Bare-metal graphics pipeline**  
  Direct pixel writes to a memory-mapped VGA buffer, reinforcing low-level hardware control.

- **Deterministic real-time loop**  
  Integrated input handling, state updates, and rendering within a tightly controlled execution loop.

- **State machine architecture**  
  Structured player behavior using explicit state machines for predictable and maintainable transitions.

- **Software-based rendering optimizations**  
  Implemented dynamic zoom and scaling without hardware acceleration, requiring efficient pixel operations.

- **Synchronization across subsystems**  
  Coordinated animation timing, collision detection, and gameplay logic to maintain responsiveness and correctness.

- **Modular embedded design**  
  Separated input handling, rendering, physics, and game logic into cohesive components for easier debugging and scalability.
