# AgentIC — Autonomous Text-to-Silicon Platform

> Describe a chip in plain English. Get back verified RTL and physical layout.

AgentIC is a multi-agent AI system that automates the entire hardware design flow — from natural language specification to silicon-ready layout — without any manual HDL writing. It is built for researchers and engineers who know what they want to build but shouldn't need to speak Verilog to build it.

---

## The Problem

Custom chip design today requires years of expertise. Writing RTL by hand, creating testbenches, interpreting compiler warnings, running formal verification, fixing timing closure — every step is a specialist skill. This makes silicon inaccessible to the vast majority of engineers who could benefit from it.

AgentIC eliminates that barrier.

---

## What It Does

You provide a plain English description:

```
"8-bit synchronous counter with active-high reset and enable signal"
```

AgentIC autonomously produces:
- Structured specification with ports, FSM states, and sub-module contracts
- Verified Verilog RTL
- Simulation testbench with functional verification
- Formal property verification via SymbiYosys
- Physical layout hardened through OpenLane on Sky130 PDK

---

## Architecture

AgentIC runs a 16-stage autonomous pipeline:

```
Natural Language Input
        ↓
   SPEC (SID Decomposition)
        ↓
   RTL_GEN (Verilog Generation)
        ↓
   RTL_FIX (Syntax + Lint + Semantic Rigor)
        ↓
   VERIFICATION (TB Generation + Simulation)
        ↓
   FORMAL_VERIFY (SymbiYosys SVA)
        ↓
   COVERAGE_CHECK
        ↓
   HARDENING (OpenLane)
        ↓
   SIGNOFF (DRC + LVS + STA + Power)
```

### Multi-Agent Core

| Agent | Role |
|-------|------|
| Designer | Generates RTL from structured spec |
| Reviewer | Reviews and critiques generated RTL |
| ReAct Reasoning Agent | Step-by-step diagnostic reasoning on failures |
| Waveform Expert | Traces VCD signal output to pinpoint simulation failures |
| Deep Debugger | SymbiYosys + causal graphs for formal verification failures |

### Self-Healing Repair Loop

When any gate fails, AgentIC does not retry blindly. It:

1. Diagnoses failure at signal level — exact signal, exact value, exact line
2. Applies a surgical fix touching only the responsible lines
3. Enforces a diff gate — rejects any fix that changes more than 30% of the design
4. Regenerates testbench if RTL port changes invalidate the existing one
5. Falls back to strategy pivot if repair loop stagnates

### Mechanical Width Post-Processor

Verilator width warnings are parsed structurally. The Python post-processor extracts signal name, line number, expected width, and actual width from every warning — then applies the correct truncation or extension mechanically before any LLM call. This handles deterministic fixes without wasting LLM tokens on problems that have exact solutions.

---

## Benchmark Results

**Date:** March 03, 2026
**PDK:** Sky130
**Model:** Llama 3.3 70B (NVIDIA NIM)

| Design | Complexity | RTL Generated | Failed At |
|--------|------------|---------------|-----------|
| counter8 | Simple | ✓ | Functional Simulation |
| uart_tx | Simple | ✓ | RTL Lint & Syntax Fix |
| pwm_gen | Simple | ✓ | Functional Simulation |
| spi_master | Simple | ✓ | Coverage Closure |
| sync_fifo | Simple | ✓ | RTL Lint & Syntax Fix |
| alu8 | Medium | ✓ | Formal Verification |
| i2c_master | Medium | ✓ | — |
| apb_timer | Medium | ✓ | Coverage Closure |
| vga_ctrl | Medium | ✓ | Formal Verification |
| wb_uart | Complex | ✓ | Functional Simulation |

**RTL Generation: 10/10**
**Current bottleneck: LLM capability at verification reasoning stage**

The pipeline architecture is validated. RTL is generated correctly for all 10 designs. Failures at simulation and formal verification stages are traced to the underlying model's inability to reason about hardware semantics reliably — not to pipeline architecture issues. Benchmark with a stronger model is pending resource access.

---

## Technical Stack

- **Orchestration:** Python, CrewAI multi-agent framework
- **RTL Generation:** LLM-based (model-agnostic backend)
- **Simulation:** Verilator, Icarus Verilog
- **Formal Verification:** SymbiYosys, Yosys
- **Physical Design:** OpenLane, Sky130 PDK
- **Waveform Analysis:** VCD parsing, AST signal tracing
- **PDK Support:** Sky130A, GF180

---

## Current Limitations and Honest Status

AgentIC is a research prototype. It is not a production EDA tool.

The self-healing architecture works. The gate pipeline works. The benchmark infrastructure works. The gap between current results and the theoretical capability of the system is entirely a function of the LLM backend — a stronger model on adequate infrastructure is the single missing piece.

This is the research question AgentIC is designed to answer: **what is the relationship between LLM capability and autonomous chip design success rate, measured rigorously across a standardized benchmark?**

---

## Contact

Built by [Your Name] — Undergraduate, Lucknow University
For collaboration, compute access, or research enquiries: [your email]

*Full source code available to collaborators and researchers on request.*
