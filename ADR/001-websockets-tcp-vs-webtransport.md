# ADR 001: Hybrid Network Transport Architecture

## Status
**Accepted**

## Context
Foxvoid Engine requires a cross-platform multiplayer networking solution supporting Desktop, Android, and WebAssembly (WASM). Real-time action games demand UDP-like protocols to avoid latency spikes, pointing towards **WebTransport (QUIC)**. However, as of 2026, integrating C++ QUIC libraries for native Desktop/Android clients introduces massive infrastructure bloat and compilation friction for a solo developer. Conversely, **WebSockets (TCP)** are natively supported in browsers and easily integrated into C++ but suffer from Head-of-line blocking (packet loss freezes the data stream).

## Decision
We will implement an agnostic networking interface (`ITransportLayer`) to support a phased rollout:
*   **Phase 1 (Current): Layer V1 - WebSockets.** The engine will use the native Emscripten WebSocket API for WASM targets (`#ifdef __EMSCRIPTEN__`) and **IXWebSocket** for Desktop/Android targets.
*   **Phase 2 (Future): Layer V2 - WebTransport.** Development of a QUIC-based transport layer is deferred until lightweight, easily integrated C++ QUIC libraries become available for CMake ecosystems.

## Rationale
*   **Immediate Productivity:** WebSockets allow immediate development and shipping of asynchronous, turn-based, or management games (e.g., Clickers, Card games) where TCP head-of-line blocking is not a severe issue.
*   **Agnosticism:** By forcing gameplay code to rely strictly on `ITransportLayer->Send(payload)`, switching to WebTransport in the future will require zero changes to actual game logic.
