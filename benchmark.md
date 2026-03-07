# AgentIC Benchmark — March 2026

> **The first open benchmark for evaluating LLM performance on autonomous chip design.**  
> 10 standardized hardware designs. Rigorous pass/fail criteria. Honest results.

---

## Overview

| Metric | Value |
|--------|-------|
| Benchmark Date | March 06, 2026 |
| PDK | Sky130 (Open Source, SkyWater 130nm) |
| LLM Backend | NVIDIA NIM — Llama 3.3 70B |
| Pipeline Mode | RTL + Verification (no physical hardening) |
| Total Designs | 10 |
| **First-Attempt Pass Rate** | **20% (2/10)** |
| RTL Generation Success | 10/10 (100%) |
| GDS Generated | 0/10 (hardening not run) |
| Average Build Time | 5.0 min per design |

---

## Results

| Design | Complexity | Result | Failed At | Time | RTL | GDS |
|--------|------------|--------|-----------|------|-----|-----|
| counter8 | Simple | ✅ PASS | — | 5.0 min | ✓ | — |
| uart_tx | Simple | ❌ FAIL | RTL Lint & Syntax Fix | 7.9 min | ✓ | — |
| pwm_gen | Simple | ❌ FAIL | Functional Simulation | 0.5 min | ✓ | — |
| spi_master | Simple | ❌ FAIL | Formal Verification | 1.3 min | ✓ | — |
| sync_fifo | Simple | ❌ FAIL | RTL Lint & Syntax Fix | 2.4 min | ✓ | — |
| alu8 | Medium | ❌ FAIL | Formal Verification | 5.2 min | ✓ | — |
| i2c_master | Medium | ❌ FAIL | — | 9.8 min | ✓ | — |
| apb_timer | Medium | ✅ PASS | — | 4.2 min | ✓ | — |
| vga_ctrl | Medium | ❌ FAIL | RTL Lint & Syntax Fix | 6.7 min | ✓ | — |
| wb_uart | Complex | ❌ FAIL | RTL Lint & Syntax Fix | 7.0 min | ✓ | — |

---

## Stage Failure Breakdown

| Stage | Failures | Critical for Tapeout? |
|-------|----------|-----------------------|
| RTL Lint & Syntax Fix | 4 | 🔴 Yes |
| Formal Verification | 2 | 🟡 Optional (non-safety-critical) |
| Functional Simulation | 1 | 🔴 Yes |

**Primary bottleneck: RTL Lint & Syntax Fix (4/8 failures)**

The LLM backend generates syntactically broken Verilog that fails strict Verilator checks and cannot self-repair reliably across multiple attempts. This is a model capability limitation — not a pipeline architecture issue.

---

## What This Benchmark Measures

AgentIC evaluates LLM performance across the complete chip design flow:

| Pipeline Stage | What It Tests |
|----------------|---------------|
| RTL Generation | Can the LLM produce valid Verilog from a plain English spec? |
| Lint & Syntax Fix | Can the LLM fix strict Verilator errors deterministically? |
| Functional Simulation | Does the design behave correctly under test stimulus? |
| Formal Verification | Do SVA properties hold for all possible input sequences? |
| Coverage Analysis | Does the testbench exercise the full design space? |
| Physical Hardening | Does OpenLane produce a clean GDS on Sky130? |

A design **passes** only when it clears all active stages without human intervention.

---

## Benchmark Design Suite

10 standardized chip descriptions spanning three complexity tiers:

**Simple:**
- `counter8` — 8-bit synchronous counter with reset and enable
- `uart_tx` — UART transmitter at 115200 baud
- `pwm_gen` — PWM signal generator with configurable duty cycle
- `spi_master` — SPI master controller
- `sync_fifo` — Synchronous FIFO with full/empty flags

**Medium:**
- `alu8` — 8-bit arithmetic logic unit
- `i2c_master` — I2C master controller
- `apb_timer` — APB bus timer peripheral
- `vga_ctrl` — VGA signal controller

**Complex:**
- `wb_uart` — Wishbone bus UART controller

Full design specifications available in [`benchmark/designs/`](./designs/).

---

## Methodology

- Each design is built from a plain English description — no Verilog provided as input
- Builds are fully autonomous — no human intervention during the pipeline run
- Pass/fail is determined by the pipeline's own gates — not manually reviewed
- Results are reported as-is — no cherry picking, no re-runs after tuning
- Physical hardening (OpenLane → GDS) excluded from this run — RTL + verification only

---

## Interpretation

**RTL generation at 100%** confirms the pipeline architecture is sound. Every design produces syntactically plausible Verilog on first attempt.

**End-to-end pass at 20%** reflects the capability ceiling of Llama 3.3 70B on hardware semantics — specifically on strict Verilog width rules, signal declarations, and multi-turn repair reasoning.

**Projected pass rate with capable model (Claude Sonnet / GPT-4o): 60%+**  
Google TPU Research Cloud access confirmed March 2026 for model capability validation.

---

## Roadmap

| Milestone | Target |
|-----------|--------|
| Full benchmark with Claude/GPT-4o backend | Q2 2026 |
| OpenLane hardening pass rate added | Q2 2026 |
| eFabless chipIgnite submission | May 2026 |
| 20-design benchmark suite | Q3 2026 |
| GDS pass rate published | Q3 2026 |

---

## Citation

If you use this benchmark in your research:

```bibtex
@misc{agentic2026benchmark,
  title     = {AgentIC Hardware Design Benchmark},
  author    = {Nishad, Vicky},
  year      = {2026},
  month     = {March},
  note      = {Open benchmark for LLM evaluation on autonomous chip design},
  url       = {https://github.com/vickynishad/AgentIC}
}
```

---

## About AgentIC

AgentIC is an autonomous Text-to-Silicon platform that converts plain English chip descriptions into verified, silicon-ready hardware using a multi-agent AI pipeline.

- 🌐 Live demo: [agentic.buildstack.live](https://agentic.buildstack.live)
- 🏆 Google TPU Research Cloud — Selected March 2026
- 🏛️ Udyam Registered — Government of India MSME
- 📍 Lucknow, India

---

*Benchmark results are published transparently. Last updated: March 06, 2026.*
