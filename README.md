# Glassoft Agent Runtime

**A bounded execution and orchestration layer for AI-assisted software engineering.**

Glassoft Agent Runtime (GAR) is an engineering project for running coding agents inside explicit project, security, validation, and review boundaries.

The goal is not to make an LLM the engineering authority. GAR treats model output as one input inside a controlled delivery system: a GitHub Issue defines the intended outcome, deterministic contracts constrain execution, runtime evidence is collected, and human/native-runtime gates remain authoritative.

> **Current state:** pilot / not production-qualified. The public case study distinguishes implemented foundations from target architecture and unqualified runtime paths.

## Why I built it

AI coding tools are useful, but an unconstrained chat session is a weak foundation for repeatable engineering work.

For my non-iOS projects I wanted a system that could answer practical questions such as:

- What exactly is this agent allowed to change?
- Which project rules and skills apply?
- Which runtime is allowed to execute the work?
- What evidence is required before a task can claim completion?
- How are backend, model, provider, role, and runtime kept separate?
- What happens when isolation or source evidence is incomplete?
- Where does human approval remain mandatory?

GAR is my attempt to make those boundaries explicit and testable.

## Architecture direction

```text
GitHub Issue
      ↓
deterministic execution packet
      ↓
GAR orchestration / durable job state
      ↓
bounded execution backend
      ↓
coding agent + remote model inference
      ↓
build / test / runtime evidence
      ↓
independent review and human gates
      ↓
draft PR / merge decision
```

The target execution plane is Raspberry-Pi-resident for always-on orchestration, with macOS kept as an operator and native-validation surface where required.

## Implemented and validated foundations

Current private implementation evidence includes:

- canonical project profiles and skill contracts;
- deterministic GitHub Issue parsing and execution-packet compilation;
- fail-closed rejection of incomplete Issue contracts before worker contact;
- Mac → Raspberry Pi worker preflight;
- durable job model and registry foundation;
- GAR daemon/control API foundation;
- OpenCode supervisor foundation;
- a provider-neutral agent-backend contract;
- explicit backend capability declarations and deterministic error classes;
- focused Go tests plus `gofmt`, `go vet`, `go build`, `go test`, and race validation for the current backend-contract work.

The backend contract deliberately keeps **role ≠ backend ≠ model ≠ provider ≠ runtime**. Backend-specific provenance is preserved as evidence without making it engineering authority.

## Deliberately not claimed as complete

The following remain gated, blocked, or unqualified and are **not** presented as finished capabilities:

- production-qualified sandbox isolation;
- resource-aware scheduler/admission control;
- full autonomous phase-gated execution;
- model/agent routing;
- independent-review adapter;
- accepted-invariants ledger;
- production deployment authority.

The current Raspberry Pi worker has exposed real isolation constraints around Landlock and rootless cgroup memory handling. Those are treated as qualification failures to resolve, not as reasons to weaken the boundary silently.

## Engineering principles

### Fail closed

Missing source evidence, incomplete contracts, unavailable isolation, or unsupported backend capabilities must block or downgrade execution rather than producing a confident-looking success state.

### Deterministic before semantic

Scope, project configuration, capability declarations, source parsing, and completion evidence should be machine-checkable where possible before model judgment is involved.

### Evidence over self-report

An agent saying that something is complete is not completion evidence. Builds, tests, runtime checks, diffs, and independent review remain separate signals.

### Explicit authority

Model output can propose implementation. It does not own production authority, merge authority, security policy, or final runtime acceptance.

## Technology and concepts

Go · Linux · Raspberry Pi · GitHub Issues/PRs · OpenCode · OpenShell · NVIDIA Nemotron · Podman · systemd · durable job state · capability contracts · runtime evidence · human gates

## What this project demonstrates

GAR is primarily a systems-engineering project around AI-assisted development.

It demonstrates work on:

- orchestration and state-machine thinking;
- execution and trust boundaries;
- provider/backend abstraction;
- failure classification;
- bounded automation;
- reproducible engineering contracts;
- evidence-driven delivery;
- infrastructure qualification instead of assumption.

## Public boundary

The implementation repository remains private. This public case study does not publish credentials, worker access details, reusable sandbox policy, infrastructure secrets, private prompts, production authority, or deployable operational configuration.

The public material is intentionally descriptive: architecture, engineering decisions, verified outcomes, limitations, and lessons learned.

## Related work

- [Developer profile](https://github.com/Charles-drZ)
- [GlassPort](https://github.com/Charles-drZ/glassport-showcase)
- [NodeMedic](https://github.com/Charles-drZ/nodemedic-showcase)
- [Automation workflow](https://github.com/Charles-drZ/automation-workflow-showcase)
