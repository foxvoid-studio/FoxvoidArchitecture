# Foxvoid Studio: Architecture and strategic roadmap

## Project overview and scope management
Foxvoid Studio is a highly complex solo-developed ecosystem. The architecture spans low-level systems programming (C++ Foxvoid Engine, C# Elyria Engine) to high-level cloud infrastructure (Django REST / SSO, Proxmox LXC).

**Immediate strategic focus**
* **Scope freeze:** Halt infrastructure expansion (e.g., custom launcher, Stripe integration) until a playable product is shipped.
* **Validation pipeline:** Finalize *Tappy Plane* to validate the WebAssembly (WASM) rendering pipeline.
* **First commercial product:** Develop a Clicker game. This leverages exponential mathematical loop (high retention, low asset requirements) to safely test the Django backend and SSO integration.

## Project management infrastructure
* **Tool:** Custom internal tracker (built with Python, Django, and Django Unfold).
* **Philosophy:** Ecosystem synergy. Rather than forcing the developer to adapt to generic, bloated, third-party tools (e.g., Taiga, Jira), the studio relies on a tailored, highly structured project management interface. While this requires a short-term development investment, it guarantees a frictionless workflow perfectly mapped to Foxvoid's specific multi-engine and cross-platform needs.
* **Deployment:** Integrated directly into the existing Proxmox LXC Django backend infrastructure, utilizing the same technology stack as the Foxvoid website and cloud API.

## Cross-platform monetization architecture
Instead of premature IAP (In-App Purchase), the initial monetization relies on **Rewarded Ads** and **Quests system**. Ad logic is abstracted using conditional compilation to respect platform constraints:

* **`PubManager` Abstraction:** A C++ Singleton utilizing `#ifdef` directives.
* **WebAssembly (`__EMSCRIPTEN__`):** Triggers a JavaScript HTML5 overlay via Emscripten bindings.
* **Android (`__ANDROID__`):** Triggers native AdMob UI via JNI calls.
* **Desktop (Windows/Linux):** Avoid native video ads. Opens the system browser to a Django-hosted ad page. The engine polls the Django API to validate the reward.

## Netcode and multiplayer architecture
The engine will support multiplayer using an Authoritative Headless Server (compiling the exact same C++ codebase without Raylib initialization to ensure strict math determinism).

**ECS Networking implementation**
* **`NetworkComponent`:** Stores the globally unique `NetID` (server-generated) and Authority flags.
* **`ReplicantComponent`:** Acts as a manifest, dictating which components attached to the entity must be synced.
* **Hash-Based dirty checking**: To avoid tracking individual variable changes, the engine generates the component's state, hashes it (e.e., CR32/MurmurHash), and compares it against the previous tick's hash. If different, the data is queued for transmission.
* **Payload serialization**: State is initially generated as JSON, then converted to **MessagePack** (using `nlohmann/json`'s `to_msgpack()`) before transmission to drastically reduce bandwidth footprint.

## Hybrid transport architecture: the agnostic layer
To support all game genres (from slow-paced management to fast-paced action), Foxvoid Engine utilizes an agnostic network transport layer (`ITransportLayer`). The game developer can select the underlying protocol at project initialization based on the game's latency requirements.

### Layer V1: Websockets (Reliable / TCP)
The foundation for early multiplayer, asynchronous games, and turn-based logic (e.g., Clickers, Card Games).
* **Web Target:** Uses the native Emscripten HTML5 WebSocket API (zero overhead).
* **Desktop/Android Targets:** Uses **IXWebSocket** (lightweight, easy CMake integration).
* **Constraint:** TCP Head-of-line blocking makes it unsuitable for twitch-action games.

### Layer V2: WebTransport (Unreliable / QUIC)
The future-proof layer designed for high-action PvP games requiring sub-50ms reaction times (e.g., Brawlers, Platformers).
* **Implementation strategy:** Will be developed when lightweight C++ QUIC libraries become accessible for native targets.
* **Agnostic integration:** Because the ECS network logic relies on `ITransportLayer->Send(payload)`, switching a project from Layer V1 to Layer V2 will only require changing a configuration flag (e.g., `EngineConfig.NetworkProtocol = Protocol::WebTransport`), without altering any gameplay code.

