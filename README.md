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
