# Galaxy Game — Domain Context Guide
**Last Updated**: June 2, 2026
**Populated By**: GitHub Copilot (Patched)
**Source**: Internal project knowledge

> This file provides domain context for any agent working on Galaxy Game.
> `@file` this into your Continue session before starting any Galaxy Game task.
> Galaxy Game is a browser-based simulation and logistics game built on Ruby on Rails with Javascript and Docker.

---

## What Galaxy Game Is
Galaxy Game is a browser-based settlement, logistics, and expansion simulation game where players manage industrial sourcing loops, harvest materials, and coordinate deep-space supply lines across celestial bodies. Instead of attempting a full physical simulation of every minute orbital variable, the engine is grounded in reasonable, calculated industrial expectations like material loss rates during transit and refining. 

The game includes subtle easter eggs and narrative nods to famous sci-fi series (shows, books, and movies) as tributes to existing sci-fi universes. The primary gameplay revolves around industrial optimization, market pricing, and resource movement rather than traditional real-time combat or rigid turn-based matches, with gameplay systems actively being tuned.

---

## Core Simulation & Logistics Rules
The simulation uses a strict set of planetary distribution and extraction rules to govern expansion:

- **Multi-System Sourcing Loops**: Sourcing infrastructure operates across the Solar System. Material loops are highly localized to prevent heavy reliance on imports. Earth and Luna are not the only operational centers; Mars and Ceres are built out for robust asteroid mining operations.
- **The Super-Mars Setup Archetype**: 
  - If a generated Super-Mars planet has **no moons**, the system moves Phobos/Deimos-sized asteroids into orbit and converts them directly into orbital stations and supply depots.
  - If Super-Mars possesses a **large moon like Luna**, the simulation mandates settling the moon first, harvesting localized materials, and constructing an Earth L1-style station depot before attempting a planet-side presence.
  - If the planetary surface is **accessible**, players still use the Luna pattern: harvest resources on the moon, move them to build the L1 depot, and then branch down.
  - If the surface is **not accessible**, only then will the system allow importing station components from external locations to assemble them in-orbit, still exhausting every local harvesting option first to eliminate import overhead.
- **Atmospheric Harvesters & Gas Solutions**: Early-game atmospheric harvesting at high-difficulty gas destinations like Titan and Venus utilizes specialized early harvesters designed to circumvent early resource choking.
- **Cycler Networks & Skimmer Shuttles**: Interplanetary cargo transport relies on massive, cycling transit ships. Mid-space transport is handled by specialized skimmer craft, which utilize an identical docking setup and matching velocity profile to cleanly link up with the cyclers.
- **Player & Market Dynamics**: The game operates on an open economy where players can act as independent agents, market manipulators, or direct traitors. If a player or faction calculates that it is cheaper to harvest and haul Venusian nitrogen across the system to Mars rather than sourcing nitrogen locally, they will actively execute that shipping run to break the local market.

---

## Tech Stack
Galaxy Game is a modern web application designed for low-overhead local hosting and containerized scaling:

- **Core Framework**: Ruby on Rails backend coordinated with a Javascript frontend.
- **Persistence Layer**: PostgreSQL relational database handles player assets, market orders, and celestial telemetry.
- **Containerization**: Entire environment is encapsulated via Docker and orchestrated locally using Docker Compose.
- **Frontend Interactivity**: Javascript handles client-side state, tracking active industrial loops without taxing the database.

---

## Workspace & Testing Protocols (Hard Rules)
To prevent local hardware degradation and maintain database integrity, all agent actions must adhere to these environmental rules:

- **The RSpec Isolation Rule**: All backend testing is executed via RSpec inside the active web container. Every single spec invocation **MUST** prepend `unset DATABASE_URL` to prevent the test environment from accidentally bleeding into or corrupting the operational database.
- **No Parallel Testing**: Only run one RSpec runner at a time inside Docker. Parallel spec execution is strictly banned to prevent resource collision.
- **Targeted Testing Only**: Agents must never trigger a blanket run of the entire test suite. Only run specific, targeted file paths relative to your active task.
- **Host-Only Supervised Git Commits**: Because Git is not accessible inside the Docker containers, all version control operations must be executed directly on the host node (Intel Mac). Agents may prepare and execute staging and commits under direct human supervision on the host, but they are strictly prohibited from attempting Git commands inside Docker.
- **No JSON Commits**: Do not stage or commit raw JSON data files at this time. Version control is strictly reserved for application code, tests, and markdown documentation files.

---

## What Not To Do
- **Never assume or fabricate test results**: Do not tell the user a spec passed or failed without actively executing the command through the terminal and verifying the output string.
- **Never omit the `unset DATABASE_URL` directive**: Running a spec without unsetting this variable will trigger an immediate environment failure.
- **Never assume gameplay is turn-based**: The gameplay loop is centered around continuous material tracking, industrial balancing, and market logistics—avoid implementing turn-clocks, action-points, or standard phase mechanics.
- **Never import raw materials if local harvesting is possible**: When designing settlement logic or station bootstrapping algorithms, always exhaust local moon/asteroid extraction profiles before writing transport logic to import materials.
