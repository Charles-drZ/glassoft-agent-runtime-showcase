# Glassoft Agent Runtime

**A developer-facing AI engineering runtime for bounded, reviewable software execution.**

Glassoft Agent Runtime (GAR) is an engineering project for running coding agents inside explicit project, security, lifecycle, validation, and review boundaries.

GAR is not built around the assumption that an LLM should become engineering authority. Model output is one input inside a controlled system: accepted work defines scope, GAR owns durable execution state, runtime policy constrains what can happen, evidence is collected independently, and human/native-runtime gates remain explicit.

> **Current state:** active pilot. The system has a real dedicated Linux worker and developer-facing control surface, but it is not presented as production-autonomous infrastructure.

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

macOS remains an operator and native-validation surface where Apple-platform work requires Xcode or physical-device validation.

Machine choice, backend choice, model choice, and provider choice are deployment facts rather than authority.

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

## Implemented foundations

Current private implementation work includes:

- canonical project profiles and skill contracts;
- deterministic GitHub Issue parsing and execution-packet compilation;
- fail-closed rejection of incomplete contracts before execution;
- dedicated worker preflight and worker abstraction;
- durable job registry and daemon/control API foundations;
- managed OpenCode backend/session supervision;
- provider-neutral backend contracts;
- explicit backend capability declarations;
- persisted final reports;
- bounded provider retry;
- managed job/session wiring, recovery, and explicit abort;
- OpenShell runtime/security-boundary qualification work;
- evidence redaction and conservative runtime-failure attribution;
- Go validation including formatting, vetting, build, test, and race checks across qualified slices.

A core invariant is:

`role != backend != model != provider != runtime`

Those identities remain separate so provenance can be recorded without turning any one implementation choice into engineering authority.

## Current development focus

The current control-surface work is making execution easier to understand without weakening the underlying authority model.

That includes:

- smoother pre-execution loading/readiness feedback;
- richer running-state execution feedback;
- durable status after detaching/reconnecting;
- clearer phase/lifecycle separation;
- explicit provider/model/runtime provenance;
- validation and evidence surfaced as first-class job state.

The goal is not to recreate OpenCode, Claude Code, or Codex CLI visually. GAR remains a separate runtime layer with its own authority and evidence model.

## Deliberately not claimed as complete

The following are not presented as finished production capabilities:

- fully autonomous end-to-end engineering authority;
- unrestricted host-side agent execution;
- production deployment authority;
- mature resource-aware scheduling/admission control;
- generalized model/agent routing;
- complete independent-review automation;
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

Go · Linux · GitHub Issues/PRs · OpenCode · OpenShell · systemd · durable job state · agent orchestration · capability contracts · runtime policy · execution evidence · human gates

## What this project demonstrates

GAR is an AI-engineering and systems-engineering project rather than an LLM wrapper.

It demonstrates work on:

- agent orchestration;
- durable job and state-machine design;
- execution and trust boundaries;
- backend/provider/model abstraction;
- developer-facing CLI/runtime UX;
- failure classification;
- bounded automation;
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
