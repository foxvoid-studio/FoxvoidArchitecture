# ADR 002: Scripting Boundary (C++ Core vs. Python Logic)

## Status
**Accepted**

## Context
Foxvoid Engine utilizes C++ as its core systems language and integrates Python 3 via `pybind11` for gameplay scripting. Without a strict architectural boundary, developers risk executing heavy algorithmic loops in Python or hardcoding high-level game logic in C++, leading to severe performance bottlenecks and "spaghetti code." Crossing the `pybind11` memory boundary carries a measurable CPU cost.

## Decision
A strict separation of concerns is enforced:
* **C++ Domain (The Engine):** Must handle all high-frequency, performance-critical loops. This includes Rendering (Raylib), Virtual File System (VFS) I/O, SAT Physics collision resolution, ECS memory management, and network packet serialization (MessagePack).
* **Python Domain (The Game):** Must handle low-frequency, highly iterable game logic. This includes Quest progression, dialogue trees, UI interaction events, state machine transitions, and high-level AI decision-making.

## Rationale
* **Performance Optimization:** Iterating over 10,000 physical entities every 16ms must remain entirely in native C++ memory space.
* **Iteration Speed:** By keeping game logic in Python, the developer can tweak game mechanics, AI behaviors, and UI flow without triggering lengthy C++ CMake recompilations across multiple target platforms.
