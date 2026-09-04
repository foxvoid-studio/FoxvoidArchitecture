# ADR 005: Visual Scripting Strategy and Graph-Ready Architecture for Elyria Engine

## Status
**Deferred (Post-Scope Freeze)**

## Context
Elyria Engine currently utilizes a Unity-style Entity-Component (EC) architecture programmed entirely in C#. Thanks to the implemented DLL hot-reloading mechanism, iteration times are already near-instantaneous. This workflow is highly optimized for a solo software engineer. 
However, the long-term vision for Foxvoid Studio includes scaling the team to incorporate Game Designers and Level Designers who may lack deep C# expertise. A node-based Visual Scripting (VS) system will eventually be necessary to allow these profiles to prototype gameplay, wire UI button actions, and manage trigger events without touching the core codebase.

## Decision
* **Strict Deferral:** The development of a graphical Visual Scripting node editor is strictly deferred. Building a robust node UI (handling bezier connections, graph serialization, and execution parsing) violates the current "Scope Freeze" doctrine and is an over-engineering trap for a solo developer.
* **Graph-Ready Codebase:** The C# architecture will immediately adopt a "Graph-Ready" pattern. We will implement custom C# Reflection Attributes (e.g., `[FoxvoidAction]`, `[FoxvoidEvent]`, `[ExposeToGraph]`).
* **Data-Driven Events:** Component events and triggers must be heavily decoupled using C# `Action` delegates or `EventHandler`.

## Rationale
* **Solo Productivity:** The existing C# DLL hot-reloading provides sufficient agility for current solo development. Building a VS tool now would halt actual game production.
* **Future-Proofing via Reflection:** By tagging relevant methods and properties with custom attributes today, the engine's backend is silently prepared. When the node editor is eventually built, it will use .NET Reflection to automatically parse these tags and generate the visual node catalog with zero core engine refactoring.
* **Data Serialization:** Designing the EC system to deserialize state and event bindings from standard formats (JSON/MessagePack) ensures that the future visual editor only needs to output a configuration file, not compile dynamic code.

## Consequences & Technical Rules
* Engineers must maintain strict discipline: any gameplay method or UI action that might be useful to a future Game Designer must be explicitly tagged with the designated Reflection attributes.
* The EC system must strictly separate internal logic from public reactive events to allow the future VS graph to listen to state changes without causing execution deadlocks.
