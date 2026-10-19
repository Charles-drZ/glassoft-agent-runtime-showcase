# Glassoft Agent Runtime

**A developer-facing AI engineering runtime for bounded, reviewable software execution — currently powered by NVIDIA hosted inference and Nemotron models.**

Glassoft Agent Runtime (GAR) is an engineering project for running coding agents inside explicit project, security, lifecycle, validation, and review boundaries.

GAR is not built around the assumption that an LLM should become engineering authority. Model output is one input inside a controlled system: accepted work defines scope, GAR owns durable execution state, runtime policy constrains what can happen, evidence is collected independently, and human/native-runtime gates remain explicit.

> **Current state:** active pilot. The system has a real dedicated Linux worker, durable interactive control plane, managed OpenShell runtime boundary, and NVIDIA/Nemotron inference lane. Current stacked qualification work is extending the lifecycle through independent review, bounded correction, accepted-workspace promotion, draft PR creation, CI observation, and final evidence persistence. GAR remains provider- and model-neutral by design.

## Why I built it

AI coding tools are useful, but an unconstrained chat or terminal session is a weak foundation for repeatable engineering work.

GAR is designed to answer concrete engineering questions:

- What exactly is the agent allowed to change?
- Which project rules and skills apply?
- Which runtime is allowed to execute the work?
- Who owns job lifecycle when the terminal detaches?
- Which backend, provider, model, and runtime actually produced the evidence?
- What happens when isolation, capability, or source evidence is incomplete?
- What validation is required before a task can claim completion?
- Where does human approval remain mandatory?

The aim is to make those boundaries explicit, inspectable, and testable.

## Current architecture

```text
GitHub Issue / accepted engineering contract
                ↓
deterministic execution packet
                ↓
GAR daemon + durable job state
                ↓
authority / capability / runtime gates
                ↓
dedicated Linux worker
                ↓
qualified bounded runtime
                ↓
agent backend
                ↓
provider / model
                ↓
build / test / runtime evidence
                ↓
review + human gates
                ↓
PR / merge / deployment decision
```

The current execution worker is a dedicated Ubuntu Linux machine.

Current deployment lane:

- **agent backend:** OpenCode;
- **inference provider:** NVIDIA hosted inference;
- **model family:** NVIDIA Nemotron;
- **runtime/security boundary:** OpenShell;
- **operator/native-validation client:** macOS;
- **engineering source of truth:** GitHub.

This is an important part of GAR's current real-world shape: the system is not only an abstract orchestration layer, it is actively engineered around a working NVIDIA/Nemotron inference lane.

At the same time, GAR deliberately keeps **role, backend, provider, model, and runtime separate**. NVIDIA/Nemotron is the current deployment choice, not a hard-coded authority dependency.

## Developer front door

GAR is designed as a scrolling developer shell rather than a full-screen TUI.

The operator starts GAR inside a project and issues natural-language engineering requests while GAR keeps lifecycle and runtime state explicit.

The control surface is built around GAR-owned state rather than raw backend output.

Relevant concepts include:

- project / issue / job / run identity;
- lifecycle state and execution phase;
- backend / provider / model provenance;
- readiness and blocking reason;
- runtime and worker identity;
- execution progress and evidence;
- validation result;
- next action and human gates;
- explicit abort and recovery.

Backend logs can remain useful evidence, but they are not the lifecycle authority.

## NVIDIA / Nemotron inference lane

GAR's current AI execution path uses **NVIDIA hosted inference** with the **Nemotron model family** behind the managed OpenCode backend.

Provider and model identity are persisted as execution provenance rather than hidden inside a generic "AI" label. That means GAR can show which backend, provider, model, worker, and runtime produced a job's evidence while keeping those implementation choices separate from engineering authority.

This distinction matters in practice:

- NVIDIA is the current hosted inference provider lane;
- Nemotron is the current model family used by that lane;
- OpenCode is the managed agent backend;
- OpenShell is a runtime/security boundary, not the model backend;
- GAR owns durable job lifecycle and evidence;
- a change of model or provider does not silently change project authority.

Availability can affect routing or fallback choices, but it does not change GAR's scope, policy, validation, or human gates.

## Implemented foundations

Current private implementation work includes:

- canonical project profiles and skill contracts;
- deterministic GitHub Issue parsing and execution-packet compilation;
- fail-closed rejection of incomplete contracts before execution;
- dedicated worker preflight and worker abstraction;
- durable job registry and daemon/control API;
- managed OpenCode backend/session supervision;
- explicit **read-only / write-allowed** interactive authority modes;
- durable interactive session identity and reattach/conversation continuity;
- managed OpenShell runtime/security qualification and explicit runtime cleanup;
- provider-neutral backend contracts and capability declarations;
- persisted final reports and bounded provider retry;
- deterministic NVIDIA runtime-intelligence events with session/turn/agent/LLM/tool/validator scope identity;
- bounded reviewer-subagent role with deny-by-default permissions;
- durable independent-review disposition and bounded correction-loop state;
- evidence redaction and conservative runtime-failure attribution;
- Go validation including formatting, vetting, build, test, and race checks across qualified slices.

A core invariant is:

`role != backend != model != provider != runtime`

Those identities remain separate so provenance can be recorded without turning any one implementation choice into engineering authority.

## Current development frontier

The current stacked qualification line is moving GAR from "bounded execution runtime" toward a complete reviewable engineering lifecycle without giving the model merge or production authority.

The active frontier includes:

- accepted workspace and validation eligibility checks;
- independent reviewer disposition with **ACCEPT / CORRECTION / HUMAN_DECISION** outcomes;
- at most one bounded correction attempt with materially different correction context;
- runtime cleanup and explicit abort semantics;
- isolated source materialization;
- deterministic Git commit and normal non-force push;
- draft PR creation and exact-head readback;
- CI observation as a distinct lifecycle phase;
- canonical final report persistence/readback and reconciliation;
- **verified final-response delivery/recovery** after managed OpenCode execution.

### GAR-036 — final-response integrity and recovery

The newest qualification slice addresses a subtle runtime problem: a managed OpenCode execution can complete while the operator-facing GAR layer still lacks a safely verified final answer.

Current branch work includes:

- JSONL-first final-response extraction;
- fail-closed separation of extraction failure from storage failure;
- same-OpenShell-sandbox recovery only when a unique run-bound OpenCode session identity is available;
- validation of session identity, final assistant `finish=stop`, configured provider/model and raw text parts;
- reasoning/tool output exclusion;
- byte bounds, safety filters and SHA-256 integrity before durable response staging;
- no model retry when delivery/recovery is uncertain;
- a startup watchdog that converts silent/unstructured startup into **unknown / reconciliation required** rather than pretending success or replaying inference.

The implementation has deterministic offline tests and green self-hosted CI, but **live end-to-end qualification is still incomplete**. A bounded live smoke exposed an important failure mode: local abort occurred while managed OpenCode state was still running and sandbox processes remained present. GAR now records that state as remote-outcome unknown/reconciliation-required instead of claiming that the remote execution was successfully aborted.

That distinction is intentional: unknown is a first-class state.

These newer slices remain active qualification work and are not presented as merged production autonomy.

The goal is not to recreate OpenCode, Claude Code, or Codex CLI visually. GAR remains a separate runtime layer with its own authority, state, provenance, reviewer, response-integrity, and evidence model.

## Deliberately not claimed as complete

The following are not presented as finished production capabilities:

- fully autonomous end-to-end engineering authority;
- unrestricted host-side agent execution;
- production deployment authority;
- mature resource-aware scheduling/admission control;
- generalized model/agent routing;
- generalized independent-review automation beyond the current bounded reviewer/correction contract;
- removal of human merge or product acceptance gates.

When a required guarantee is unavailable, GAR is intended to block, downgrade, or expose that state rather than silently continue with weaker authority.

## Engineering principles

### Fail closed

Missing source evidence, incomplete contracts, unavailable runtime guarantees, or unsupported backend capabilities must block or downgrade execution rather than producing a confident-looking success state.

### Deterministic before semantic

Scope, configuration, capability declarations, source parsing, lifecycle state, and completion evidence should be machine-checkable where possible before model judgment is involved.

### Durable lifecycle ownership

A terminal session is not job authority.

Detaching, reconnecting, or losing a frontend should not invent a second lifecycle or implicitly cancel execution.

### Evidence over self-report

An agent saying that something is complete is not completion evidence.

Builds, tests, runtime checks, diffs, persisted reports, and independent review remain separate signals.

### Explicit authority

Model output can propose and implement work inside granted boundaries. It does not automatically own production authority, merge authority, security policy, or final runtime/product acceptance.

## Technology and concepts

Go · Linux · NVIDIA hosted inference · NVIDIA Nemotron · GitHub Issues/PRs · OpenCode · OpenShell · systemd · durable job state · agent orchestration · capability contracts · runtime policy · execution evidence · human gates

## What this project demonstrates

GAR is an AI-engineering and systems-engineering project rather than an LLM wrapper.

It demonstrates work on:

- agent orchestration and durable interactive sessions;
- durable job, lifecycle, and state-machine design;
- read-only/write authority modes and fail-closed execution boundaries;
- NVIDIA/Nemotron-backed inference integrated behind a provider-neutral execution contract;
- deterministic runtime-intelligence provenance;
- bounded reviewer subagents and correction loops;
- managed runtime cleanup and abort behavior;
- backend/provider/model/runtime abstraction;
- developer-facing CLI/runtime UX;
- draft-PR/CI/evidence promotion design;
- failure, unknown-outcome and reconciliation classification;
- final-response integrity/recovery without inference replay;
- reproducible engineering contracts;
- evidence-driven delivery;
- runtime qualification instead of assumption.

## Public boundary

The implementation repository remains private.

This public case study does not publish credentials, worker addresses, reusable access configuration, infrastructure secrets, private prompts, production authority, or deployable operational policy.

The public material is intentionally descriptive: architecture, engineering decisions, verified outcomes, limitations, and lessons learned.

## Related work

- [Developer profile](https://github.com/Charles-drZ)
- [GlassBox](https://github.com/Charles-drZ/glassbox-showcase)
- [GlassPort](https://github.com/Charles-drZ/glassport-showcase)
- [NodeMedic](https://github.com/Charles-drZ/nodemedic-showcase)
- [Automation workflow](https://github.com/Charles-drZ/automation-workflow-showcase)
