# CircuitSmith / HephAIstus Context

Last updated: 2026-10-14

## For Agents: Start Here

This is your entry point. Read this first, then follow the document map below based on your task.

## Project Summary

**CircuitSmith** is an AI copilot for KiCad schematic design and circuit simulation, powered by the **HephAIstus** deterministic backend engine. It:

- Parses `.kicad_sch` files into structured JSON state
- Answers questions about circuits, simulations, and ERC reports
- Proposes previewable, validated patch plans (`hephaistus/patch-plan/v1`)
- Applies changes only after user confirmation
- Persists project-scoped session state, history, and simulation archives in `<project>/.circuitsmith/`

**Core principle:** Schematic is the source of truth. LLM (CircuitSmith) proposes; deterministic backend (HephAIstus) disposes.
**Naming boundary:** User-facing surfaces and project session folders use **CircuitSmith** (`.circuitsmith/`); internal Python packages (`hephaistus_*`), CLI tools (`hephaistus-*`), and schema contracts (`hephaistus/patch-plan/v1`) retain **HephAIstus**.

## Architecture Overview

```
KiCad schematic (.kicad_sch)
    ↓ parser
JSON state (components, nets, pins)
    ↓ context service
LLM (receives context, produces patch-plan)
    ↓ validation
Patch backend (text-level apply)
    ↓ round-trip validation
KiCad schematic (modified)
```

## Key Files

| File | Purpose |
|------|---------|
| `backend/hephaistus_circuit/parser.py` | KiCad schematic → JSON state |
| `backend/hephaistus_circuit/engine.py` | Patch-plan validation & application |
| `backend/hephaistus_circuit/text_apply.py` | Text-level schematic mutations |
| `backend/hephaistus_context/context_service.py` | LLM context assembly |
| `backend/hephaistus_llm/orchestrator.py` | LLM orchestration |

## Document Map

**Read in order for your task:**

| Task | Documents |
|------|-----------|
| **Understanding the project** | `docs/vision.md` → `docs/spec.md` |
| **Modifying the circuit engine** | `docs/ARCHITECTURE.md` §1-3 → `backend/hephaistus_circuit/IMPLEMENTATION.md` |
| **Working with LLM context** | `docs/ARCHITECTURE.md` §4 → `docs/LLM_CONTEXT.md` |
| **Understanding patch operations** | `docs/patch-plan-v1.md` → `backend/hephaistus_circuit/engine.py` |
| **Adding new operations** | `docs/patch-plan-v1.md` → `backend/hephaistus_circuit/text_apply.py` |
| **UI/Companion work** | `docs/ARCHITECTURE.md` §3 |
| **Testing** | `docs/TEST-MANUAL.md` |

## Current Status

| Component | Status |
|-----------|--------|
| KiCad parsing | ✅ Complete |
| Patch operations (7 types) | ✅ Complete |
| Stub-based net restructuring | ✅ Complete |
| SPICE property inheritance | ✅ Complete |
| Simulation output parsing | ✅ Complete |
| Session persistence | ✅ Complete |
| LLM context assembly | ✅ Complete |
| Companion UI | ✅ Complete |
| LLM orchestration | ✅ Complete |

## Branch Information

- Active branch: `feature/companion-reset`
- Archive: `archive/vscode-prototype` (tag: `vscode-prototype-2026-08-19`)

## Running CircuitSmith Locally

```bash
# Terminal 1: Backend API (port 8000)
cd ~/.openclaw/workspace/hephaistus
source .venv/bin/activate
hephaistus-api   # or: python backend/api/server.py

# Terminal 2: Companion UI (port 3000)
cd ~/.openclaw/workspace/hephaistus/companion
npm run dev
```
- **UI:** http://localhost:3000
- **API Docs:** http://localhost:8000/docs

## Quick CLI Tests

```bash
# Run circuit engine tests
cd ~/.openclaw/workspace/hephaistus
source .venv/bin/activate
python -m pytest tests/test_circuit_engine.py -v

# Parse a schematic
hephaistus-circuit parse fixtures/schematics/rectifier.kicad_sch

# Apply a patch plan (dry-run)
hephaistus-circuit apply-plan fixtures/schematics/rectifier.kicad_sch examples/patches/insert_shunt.json --dry-run
```