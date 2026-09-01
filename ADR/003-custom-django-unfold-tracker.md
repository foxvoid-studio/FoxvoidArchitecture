# ADR 003: Custom Project Management Infrastructure (The "Lab" Approach)

## Status
**Accepted**

## Context
Managing a highly complex, multi-language ecosystem (Foxvoid Engine in C++, Elyria Engine in C#, Cloud API in Python/Django) requires rigorous project tracking. Generic third-party tools (like Taiga or Jira) introduce unnecessary UI bloat, require constant context-switching, and do not align with the studio's specific cross-engine workflow.

## Decision
Foxvoid Studio will build and maintain a custom, internal project management tracker using **Python, Django, and Django Unfold**. It will be hosted directly within the existing Proxmox LXC container infrastructure alongside the Foxvoid SSO and API services.

## Rationale
* **Ecosystem Synergy:** Although it requires an initial time investment (temporarily pausing game development), building a custom tracker ensures a frictionless workflow tailored exactly to the developer's mental model.
* **Stack Mastery:** Utilizing Django Unfold reinforces the developer's mastery over the studio's primary backend technology stack.
* **Data Sovereignty:** Keeping project tracking within the same database ecosystem as the game API and SSO services opens the door for future automated integrations (e.g., the engine's "One-Click Deploy" automatically updating ticket statuses in the tracker).
