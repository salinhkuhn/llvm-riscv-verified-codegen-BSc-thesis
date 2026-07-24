# RiscvDialect

>  Archived prototype: This repository is a historical snapshot of the early
> experimental work from my BSc thesis. The final code and proofs have been
> [upstreamed](#upstream-projects) and are maintained there — not here.

Verified code generation for RISC-V, developed in the [Lean 4](https://lean-lang.org/)
theorem prover and functional programming language.

The thesis explores how instruction selection from LLVM IR to RISC-V can be expressed
as a sequence of *peephole rewrites*, and how each rewrite can be proven correct
against a mechanized model of the RISC-V ISA in the Lean4 theorem prover.

---

## Write-Up

[thesis-in-paper-shape.pdf](thesis-in-paper-shape.pdf) — the write-up of my thesis in
paper format (non final version and wax to long for a paper, it is "shorter" version of my thesis). Only the presentation follows more the shape of a paper.

---

## Upstream Projects

The production-quality successors to this prototype live in the following repositories:

| Project | Description |
| --- | --- |
| [**opencompl/lean-mlir**](https://github.com/opencompl/lean-mlir) | Main integration of the final code generation and proof infrastructure |
| [**opencompl/riscv-lean**](https://github.com/opencompl/riscv-lean) | Standalone mechanization of the RISC-V ISA semantics in Lean 4 |
| [**opencompl/sail-riscv-lean**](https://github.com/opencompl/sail-riscv-lean) | Lean port of the official RISC-V Sail specification |

---

## What's in Here

The central idea is a **hybrid dialect** (`LLVMPlusRiscV`) whose operations are the
disjoint union of the LLVM and RISC-V dialects. A program starts as pure LLVM, and
instruction selection rewrites it — one peephole at a time — until only RISC-V
operations remain. Because both source and target live in the same dialect, every
intermediate program is well-typed and every rewrite step carries a refinement proof.

```
LLVM IR  ──▶  hybrid (LLVM + RISC-V)  ──▶  RISC-V
             each rewrite proven correct
```

### Layout

| Path | Contents |
| --- | --- |
| `RiscvDialect/RISCV64/` | Syntax, semantics and pretty-printing EDSL for the RV64 base ISA |
| `RiscvDialect/LLVMRiscv/` | The hybrid dialect, refinement relation, lowering pipeline and `opt2` driver |
| `RiscvDialect/Instructions/` | Per-instruction models (`add`, `sub`, …) for both sides |
| `RiscvDialect/Peephole_Optimizations/` | RISC-V peephole rewrites and their correctness proofs |
| `RiscvDialect/Tests/`, `test_pipeline/` | MLIR/LLVM inputs used to exercise the pipeline |
| `thesis_examples/` | Small self-contained examples used in the thesis text |
| `RiscvDialect/Archive/` | Superseded experiments, kept for reference |

---

## Building

Requires [`elan`](https://github.com/leanprover/elan); the toolchain pinned in
`lean-toolchain` (a Lean 4 nightly) is fetched automatically.

```bash
lake update   # resolve lean-mlir and sail-riscv-lean dependencies
lake build
```

Run the experimental optimizer driver on an MLIR file:

```bash
lake exe opt2 test_pipeline/bb0.mlir
```
