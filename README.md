# NEXUS-1 — Phase 0

> **Nuclear Plant Operator Console & Digital-Twin Platform** — an interactive, single-file demonstration of a complete nuclear power facility, driven by a live in-browser simulation.

![Status](https://img.shields.io/badge/status-Phase%200%20(demonstration)-0e7490)
![Built with](https://img.shields.io/badge/built%20with-HTML%20%C2%B7%20CSS%20%C2%B7%20JS-22d3ee)
![Backend](https://img.shields.io/badge/backend-none%20%C2%B7%20runs%20in%20browser-3ddc84)
![License](https://img.shields.io/badge/license-All%20Rights%20Reserved-9a6a00)

NEXUS-1 presents an entire plant — reactor physics, power generation, the secondary and electrical systems, safety and radiation monitoring, a multi-unit fleet, component ageing and decommissioning, staffing, a robotic fleet, zone access control, and analytical tools — through one navigable interface. Everything runs locally in a single HTML file, with no server, account, or build step.

This repository is **Phase 0**: the demonstration and design phase. The path to a production system is in the [Project Roadmap](#-documentation).

---

> ⚠️ **Scope — please read first.** NEXUS-1 is a **demonstration and educational project**. It is **not connected to any real reactor, sensor, or control system**, and its models are studied and representative rather than validated or certified. The strength of the project is in its software design, simulation, visualisation, and systems thinking — not in claiming nuclear-engineering validity. By design, it is a monitoring / analytics / advisory layer with **no control authority**: it never controls or bypasses certified safety systems.

---

## Table of contents

- [Demo](#-demo)
- [Quick start](#-quick-start)
- [Screenshots](#-screenshots)
- [Features](#-features)
- [Reactor physics](#-reactor-physics)
- [Real vs. simulated](#-real-vs-simulated)
- [Architecture & roadmap](#-architecture--roadmap)
- [Documentation](#-documentation)
- [Author & licence](#-author--licence)

---

## 🚀 Demo

The application is a single HTML file — open it in any modern browser and it just runs.

**Host it free on GitHub Pages** (recommended — it also gives the project a public, dated record):

1. Create a public repository and push these files.
2. Rename the application file to **`index.html`** (GitHub Pages serves that by default).
3. In the repo: **Settings → Pages → Build and deployment → Deploy from a branch → `main` / `(root)` → Save**.
4. Wait a minute, then open `https://<your-username>.github.io/<repo-name>/`.

> **Note:** one screen (the *3D Reactor View*) loads a 3D library from a public CDN, so it needs internet access to render. Everything else works fully offline.

## ⚡ Quick start

```bash
git clone https://github.com/<your-username>/<repo-name>.git
cd <repo-name>
# then simply open the .html file in your browser (e.g. double-click it)
```

**First tour (shows how the screens connect):** open **Reactor → Control Rods**, move the rod bank, then press **SCRAM**. Now check **Incident Analysis** and **Compliance** to see the event recorded, and **Rod Inspection** to see the rods flagged for post-trip inspection — one action rippling across the whole system.

**Lost on any screen?** Every page has a contextual **“?”** button, and the sidebar **Help & Guide** page explains what each screen does and how the parts connect.

## 🖼️ Screenshots

> Add a few PNGs to a `docs/` folder and reference them here, for example:

```markdown
![3D Reactor View](docs/20213844.png)
![Plant Overview](docs/213929.png)
![Rod Inspection](docs/214005.png)
```

<!-- Suggested shots: Plant Overview, Reactor Core, 3D Reactor View, System Dependencies graph, Reactor Kinetics, Optimization (RL). -->

## ✨ Features

- **Fleet & overview** — multi-unit fleet (PWR units + SMR modules) with on/off toggling, grid aggregation, and a unit selector that propagates across the unit-specific screens. Each unit type drives its own reactor core-map sizing, flux/temperature/rod rendering and live physics. A whole-plant **Plant 3D View** shows reactors, the steam header and the turbine-generators in one animated scene.
- **Reactor** — per-unit core map, interactive control rods + SCRAM, neutronics, a genuine **point-kinetics** simulator (6 delayed-neutron groups; Doppler / moderator / xenon-135 feedback; a two-node fuel/coolant thermal model; coupled iodine–xenon poisoning; stable operator-splitting integration), and a **3D digital-twin view** with two side-by-side cores — noisy telemetry vs. clean model — whose divergence is the twin early-warning signal. See [Reactor physics](#-reactor-physics).
- **Secondary & electrical** — steam/secondary (steam generators, turbine train, condenser), coolant loops / thermal-hydraulics, power & grid synchronisation.
- **Monitoring & safety** — radiation, alarms & events, incident analysis, a hash-sealed compliance/audit log, and OT-security posture.
- **Analysis & intelligence** — AI predictive diagnostics; an experimental **reinforcement-learning optimiser** (BETA, twin-only, advisory, interpretable); a **system-dependencies** graph / influence-matrix / causal-chain; digital twin; and trends.
- **Root cause graph** — a causal fault-propagation graph built on an engineered fault-tree backbone (auditable, regulator-aligned), overlaid with live alarm state and a low-confidence data-informed layer (ML-weighted edges, plus rejected candidate edges held out for engineer confirmation); collapses an alarm flood into a single origin and its downstream cascade, and ranks hypotheses by causal origin rather than alarm volume — surfacing the upstream cause over the loudest symptom. Advisory only, never feeding control or safety actuation.
- **Integrity & lifecycle** — rod inspection (NDT), a per-unit component registry with live health, component ageing & degradation, and decommissioning.
- **People, robots & access** — personnel + staffing stress test, a robotic/vehicle fleet + mission readiness, and zone access (live presence + permissions matrix).
- **Help & guidance** — a sidebar **Help & Guide** page organising every screen by theme, plus a per-page contextual **“?”** panel, both drawn from a single shared help model.
- **Connected behaviour** — a SCRAM ripples into incidents, compliance, inspection, and ageing; operation accrues component life; the unit selector drives the relevant screens.

## 🔬 Reactor physics

The console deliberately carries **two layers** of reactor physics, and they are documented honestly in the [Reactor Physics Reference](#-documentation):

- **Reactor Kinetics tab — the rigorous model.** A genuine point-kinetics solver integrating the standard reactor-dynamics equations with six delayed-neutron precursor groups (U-235 data), coupled to Doppler (fuel-temperature), moderator (coolant-temperature) and xenon-135 feedback, a lumped two-node thermal model, and an operator-splitting integration scheme (exact precursor update, implicit neutron update, explicit slow nodes) that stays stable despite the stiffness of the equations. The *structure* is the real physics and reproduces the correct qualitative phenomena — prompt jump, reactor period, power self-regulation through negative feedback, the delayed-neutron tail on SCRAM, and the post-shutdown xenon transient.
- **Neutronics tab — the illustrative model.** Its excore-detector readings (source / intermediate range, startup rate), reactivity-balance rows and axial-flux shape are driven by the lightweight per-unit fleet model plus monotonic display mappings — chosen to behave plausibly, and clearly labelled as presentation logic rather than first-principles neutronics.

In both layers the numerical **constants** are representative textbook-order values, not tuned to a specific reactor. Every equation and constant is set out in the Reactor Physics Reference PDF.

## 🔬 Real vs. simulated

| Real / genuine | Simulated / illustrative |
| --- | --- |
| The interface, navigation, visualisations and interactions are working code. | All data is generated in the browser; nothing connects to a real plant. |
| The **Reactor Kinetics** solver and the RL agent are real in structure and genuinely compute/learn. | Their constants are representative, not tuned to a specific reactor. |
| The **Neutronics** tab uses honest, clearly-labelled display mappings driven by the fleet model. | It does not run the point-kinetics solver; its detector readings and balance rows are illustrative. |
| Workflows reflect real industry concepts (NDT, staffing rules, dose limits, decommissioning stages). | Ageing figures, dependency weights, names, ratings and dates are plausible placeholders. |

## 🏗️ Architecture & roadmap

**Phase 0 (this repo):** a single self-contained HTML/CSS/JavaScript file — hand-written SVG for diagrams/charts, and a WebGL library for the 3D view. No framework, no build step.

**Production target (future phases):**

- **Backend:** .NET (C#) microservices — ASP.NET Core; REST + gRPC. *(No Orleans.)*
- **Front end:** Angular SPA.
- **Real-time:** SignalR for live updates to the client.
- **Relational data:** Microsoft SQL Server (config, master data, audit/compliance).
- **Messaging:** Azure Service Bus for events/commands; **Azure Event Hubs** (or Kafka) for the high-rate telemetry stream.
- **Ops:** Docker + Kubernetes; OpenTelemetry for observability.

The goal is a platform active in all of its modes — **Training, Shadow, Advisory, Supervisory and Hybrid Digital Twin** — built out from the Phase 0 Simulation baseline. Any future plant integration is **read-only** until — and unless — a formal assurance and certification path is completed. See the **Project Roadmap** for the phase-by-phase plan and indicative effort.

## 📚 Documentation

A full set of PDFs accompanies the application:

- **Reactor Physics Reference** *(new)* — the equations, constants and numerical methods behind the **Neutronics** and **Reactor Kinetics** tabs: the genuine point-kinetics model (6 delayed-neutron groups, Doppler / moderator / xenon-135 feedback, two-node thermal model, operator-splitting integration) and — clearly labelled as illustrative — the display model that drives the Neutronics tab. Written to be read alongside the source.
- **Functional Guide (Phase 0)** — an analytical walk-through of every screen and how the parts connect.
- **Glossary of Terms** — a plain-English guide to every term in the console, for non-engineers.
- **Root Cause Analysis Engine** — how the causal fault-propagation engine works: the fault-tree backbone, the live alarm overlay, and the held-out ML-weighted candidate edges, with hypothesis ranking by causal origin. Advisory only.
- **Root Cause Graph** — the graph view of fault propagation and alarm correlation.
- **Project Roadmap** — the phased plan, the intended stack, and an indicative effort estimate.
- **Project Brief** — a one-look overview.

## 👤 Author & licence

Created by **Grigorios Agathangelidis** (Electrical & Software Engineering) as a personal project — an effort to study the nuclear-energy domain and apply software-architecture, simulation, visualisation and systems-design skills to it. It does not claim nuclear-engineering expertise, and it is careful to mark where its models are illustrative.

## IMPORTANT NOTICE REGARDING NUCLEAR USE

This software is provided for **demonstration, educational, and research purposes only**.

NOT INTENDED FOR USE IN:
- Real-time control of any nuclear reactor, fuel handling, or safety system
- Operational decision-making at any nuclear facility
- Any application where failure could lead to radiological release, property damage, or personal injury

The author makes no claims regarding the accuracy, completeness, or fitness for any operational purpose. 
All models are representative, not validated or certified for any specific reactor design.

By using this software, you agree that the author and copyright holder bear no liability for any consequences 
arising from its use, whether authorized or not.

Build signature: `NX1-094C79E0A366D34F`

© 2026 Grigorios Agathangelidis. **MIT License** See [`LICENSE.txt`](LICENSE.txt). This is a demonstration and educational tool; it is not a licensed analysis tool and is not connected to any real reactor.
