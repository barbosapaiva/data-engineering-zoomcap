# Workflow Orchestration — Kestra 🔄

Workflow orchestration is the process of *coordinating a sequence of tasks in a data pipeline* — deciding what runs, in what order, when, and what to do if something fails.

**Without orchestration, you'd run scripts manually and hope nothing breaks. With it, everything is automated, scheduled, and observable.**

---

## Index 👩🏽‍💻

1. [What is Orchestration](#what-is-orchestration)
2. [What is Kestra](#what-is-kestra)
3. [Core Concepts](#core-concepts)
4. [Flow Anatomy](#flow-anatomy)
5. [Why it Matters in Data Engineering](#why-it-matters-in-data-engineering)

---

## What is Orchestration

A data pipeline rarely has just one step. A typical flow looks like:

```
extract data → validate → transform → load → notify
```

Each step depends on the previous one. Orchestration handles:
- Running tasks in the right order
- Passing outputs from one task to the next
- Retrying on failure
- Scheduling runs (daily, hourly, on-event...)
- Giving you visibility into what ran, when, and whether it succeeded

---

## What is Kestra

Kestra is a modern, open-source workflow orchestrator. Workflows are defined in **YAML**, making them easy to read, version-control, and share.

It can run on-premise or in the cloud, and integrates with most data tools out of the box.

---

## Core Concepts

- **Flow** — a workflow. The top-level unit in Kestra. Defined in YAML.
- **Task** — a single step inside a flow (run a script, call an API, query a database...).
- **Namespace** — an environment or folder to organise flows (e.g. `dev`, `prod`, `project_name`).
- **Input** — a parameter passed into the flow at runtime. Acts like a function argument.
- **Output** — a value produced by a task that can be reused by later tasks in the same flow.
- **Trigger** — what starts a flow. Can be a schedule, a webhook, another flow finishing, or manual.
- **Backfill** — running a flow for historical data that wasn't processed when it originally occurred. Useful to replay past periods after fixing a bug.

---

## Flow Anatomy

```yaml
id: my_flow              # unique name of the flow
namespace: dev           # environment / folder

inputs:
  - id: taxi_type        # input variable
    type: STRING
    defaults: yellow

tasks:
  - id: extract          # task 1
    type: io.kestra.plugin...
    
  - id: load             # task 2 — runs after task 1
    type: io.kestra.plugin...

triggers:
  - id: daily_schedule
    type: io.kestra.core.models.triggers.Schedule
    cron: "0 9 * * *"   # every day at 9am
```

---

## Why it Matters in Data Engineering

Real pipelines aren't single scripts — they're sequences of dependent steps across multiple systems. Orchestration makes them:
- **Reliable** — automatic retries and failure handling
- **Scheduled** — no manual triggering
- **Observable** — logs, run history, and alerts out of the box
- **Maintainable** — YAML flows are readable and version-controlled
