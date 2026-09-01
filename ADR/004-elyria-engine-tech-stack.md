# ADR 004: Core Technology Stack for Elyria Engine (C# / Veldrid / JoltPhysics)

## Status
**Accepted**

## Context
Elyria Engine, originally developed exclusively for the 3D low-poly RPG *Project Elyria*, is being abstracted into a general-purpose 3D engine for Foxvoid Studio. The engine must support Desktop (Windows/Linux), Mobile (Android), and WebAssembly (WASM). 
As a solo developer managing multiple projects, there is a critical need to balance raw execution performance, cross-platform graphics abstraction, and rapid gameplay iteration. A rewrite in Rust (`wgpu` + `winit`) was briefly considered to optimize the WebAssembly payload but required evaluation against the studio's "Scope Freeze" policy.

## Decision
We will retain and solidify the existing technology stack:
* **Core Language:** C# (.NET 8+) compiled via Native AOT.
* **Graphics Abstraction:** Veldrid (Vulkan/D3D11 for Desktop, OpenGL ES/WebGL 2 for Web/Mobile).
* **Physics Engine:** JoltPhysicsSharp (C# wrapper for the multithreaded Jolt Physics engine).

## Rationale
* **Zero-Boundary Architecture:** Unlike Foxvoid Engine (which pays a performance cost crossing from C++ to Python via `pybind11`), using C# for both the engine core and the gameplay scripting allows entities to live in the same memory space (the CLR). This eliminates P/Invoke overhead and maximizes CPU throughput.
* **Momentum & Scope Freeze:** The engine is already in an advanced state. Scrapping it for a Rust rewrite would cost months of R&D, violating the studio's primary irective to ship games rather than endlessly rebuild infrastructure.
* **WASM Pragmatism:** While the .NET runtime adds a significant initial payload size (10-30 MB) to web builds, this is acceptable for modern web standards. The custom Virtual File System (VFS) will mitigate perceived load times by displaying an instant HTML loading screen while the engine and assets stream asynchronously. Browser caching (IndexedDB) will make subsequent loads nearly instantaneous.

## Consequences & Technical Rules
* **Zero-Allocation Hot Path:** To prevent the .NET Garbage Collector (GC) from causing micro-stutters during gameplay, the core Entity Component System (ECS) and rendering loop must strictly avoid the `new` keyword. Developers must rely heavily on `struct`, `ref struct`, `Span<T>`, and object pooling.
* **GPU Instancing:** Because WebGL 2 suffers from high draw-call overhead, the engine's renderer must natively support GPU Instancing to draw thousands of low-poly objects efficiently.
* **WebGPU Deferral:** Veldrid currently lacks stable WebGPU support. For the current target scope (low-poly aesthetics), WebGL 2 is perfectly sufficient. Moving to a WebGPU architecture is deferred to a future engine iteration.
