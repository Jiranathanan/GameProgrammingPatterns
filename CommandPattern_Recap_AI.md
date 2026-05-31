# Recap: Command Pattern & AI Prompting Guide for Game Developers

This repository contains the core architectural concepts of the **Command Pattern** adapted for modern game engines like Godot 4 (GDScript/C#), along with an engineering guide for leveraging AI assistants in the era of Vibe Coding and Agentic AI.

---

## 📌 Part 1: Recap — Core Mechanics of the Command Pattern

At its absolute core, the Command Pattern turns **"behavior (method calls) into standalone data objects placed between the producer and the consumer."**


```

[Traditional Tight Coupling]
Raw Input / AI Code ─────────────────────────────> player.jump() (Hard-coded)

[Command Pattern Decoupling]
Raw Input / AI Code ──> [Emits Command Object] ──> actor.execute(command)

```

### 1. The Core Problem: Tight Coupling
Traditional implementations hard-wire raw input events directly to player actions (e.g., `if Input.is_action_just_pressed("btn_x"): jump()`). 
* **The Consequences:** This locks your code down. It becomes extremely difficult to implement **Key Rebinding**, re-use the exact same combat/movement logic for **AI Enemies**, or build an **Undo/Redo system** because there is no intermediary data structure representing the action.

### 2. The Solution: Encapsulation & Decoupling
We encapsulate the action inside a middleman object (e.g., a `JumpCommand` class).
* **The Producer (Input Handler / AI Brain):** Detects states or makes decisions, then simply packages and emits a `Command` object.
* **The Consumer (Character/Actor):** Receives the command object and executes it by passing itself into the execution context (e.g., `command.execute(actor)`).

### 3. Ultimate Architectural Benefits
* **Key Rebinding:** Seamlessly hot-swap actions by changing the command object assigned to a specific button variable.
* **Undo / Redo:** By extending commands into an `UndoableCommand`, each object snapshots and encapsulates its historical state (e.g., `old_position`). Storing these objects chronologically in an array (History Stack) allows you to pop the latest action and call `.undo()` safely.
* **Networking & Replays:** Command objects can be serialized and streamed across networks for multiplayer replication or recorded step-by-step to implement deterministic replay systems without consuming excessive memory.

---

## 🤖 Part 2: Hands-on AI Prompting Guide for Vibe Coding & Agentic AI

In the era of **Vibe Coding** and **Agentic AI** (e.g., Cursor, Claude, GPT), AI assistants tend to generate quick, short-sighted, and tightly coupled code just to make a feature work instantly. 

As a **Head Engineer / PM**, you must enforce architectural boundaries (Guardrails) right from the first prompt. Writing prompts in English drastically optimizes token economy (saving up to 3-4x compared to Thai) and triggers precise software engineering context within Large Language Models (LLMs).

### 1. Eliminating Hard-coded Actions (Anti-Monolithic Prompt)
Use this prompt to force the AI to isolate raw inputs from action implementations, ensuring modularity right from the start.

> **Prompt:**
> "Implement a character combat and skill system in Godot 4 using **[GDScript / C#]**. 
> 
> Strict Architectural Rule: **Do NOT hard-code skill logic inside the Character/Actor script.** 
> 
> You must implement the **Command Pattern** to ensure the system is decoupled:
> 1. Create a base `BaseSkillCommand` class (extends RefCounted) with a virtual method `execute(actor: CharacterBody2D)`.
> 2. Create concrete subclasses in separate files, such as `SlashSkillCommand` and `HealSkillCommand`, overriding the execute method.
> 3. Design the Character script to receive and process these command objects via a request or delegate system, rather than checking input states directly inside the character class."

### 2. Building a Memory-Efficient Undo/Redo System
AI agents often default to heavy-handed approaches like snapshotting the entire scene state for undo systems. Use this prompt to force a lightweight, history-stack architecture.

> **Prompt:**
> "I need to build a furniture placement/room customization system with multi-level **Undo/Redo** support in Godot 4 using **[GDScript / C#]**.
> 
> Implement this using the **Command Pattern (similar to Redux State Management)**:
> - Every time a piece of furniture is moved, instantiated, or deleted, generate a unique `MoveFurnitureCommand` object encapsulating state variables like `furniture_id`, `old_position`, and `new_position`.
> - Create a centralized `HistoryManager` node that maintains an array/stack of these undoable command objects.
> - **Do NOT snapshot the entire world/room state.** Undo and Redo operations must be handled purely by calling `.undo()` and `.execute()` on the command objects in the history stack to optimize memory usage."

### 3. Advanced Vibe Coding: Global Project Rules (`.cursorrules`)
If you use Cursor or other AI agent IDEs, embed these English-centric rules directly into a configuration file. This guarantees that whenever you casually code or ask for new skills, the AI will automatically adhere to the pattern.

📄 **File Name:** `.cursorrules`
```markdown
# Project Architecture Rules: Game Dev (Godot 4)

## Core Coding Principles
1. **Decoupling over Speed**: Never embed input-handling or deep action logic directly inside the Player/Actor scripts.
2. **Command Pattern Enforcement**: 
   - All character actions, interactive elements, and skills must be reified into dedicated Command objects.
   - Input Handlers or AI components must only act as "Producers" that emit Command objects. The Actor must act as a "Consumer" that executes them.
3. **Undo/Redo Compliance**: Any state-altering commands in editor tools or turn-based mechanics must extend an undoable base class, encapsulating old state variables and implementing an `undo()` method.
4. Keep scripts modular, descriptive, and strictly adhere to separate file structures per command type (e.g., `move_command.gd`, `inspect_command.gd`).

```

---

## 🧠 Part 3: Architecture Summary — Unidirectional Data Flow

If you have experience with web state management, you will notice that this entire architecture mirrors the design of **React / Redux** perfectly.

```
[User Input / AI Brain] ──(Dispatches)──> [Command / Action Object] 
                                                    │
                                                    ▼
[Game State / Actor Engine] <──(Updates)── [History Manager / Store]

```

### The Architectural Blueprint:

1. **The Event:** Player inputs or AI decisions generate a **Command/Action** (the encapsulated data package).
2. **The Dispatch:** This command is pushed into a centralized manager (`HistoryManager` / Redux Store) where it executes its logic and logs itself onto the history stack.
3. **The Time Travel:** Features like **Undo/Redo, Replays, and Time Travel Debugging** become trivial because you are manipulating a clean stream of historical data objects, while your underlying game entities remain stateless regarding history management.

By enforcing the Command Pattern, your code base stays incredibly clean, testable, and highly collaborative—giving you maximum leverage as a Technical Project Lead.

```

