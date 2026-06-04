# Terraform 🌎

Terraform is a tool used to *define and provision cloud infrastructure using code* — instead of clicking around in dashboards.

**You describe what you want (a storage bucket, a database, a network), and Terraform figures out how to create it, update it, or destroy it.**

---

## Index 👩🏽‍💻

1. [Core Concepts](#core-concepts)
2. [How State Works](#how-state-works)
3. [File Structure](#file-structure)
4. [The Workflow](#the-workflow)
5. [Why it Matters in Data Engineering](#why-it-matters-in-data-engineering)

---

## Core Concepts

- **Infrastructure as Code (IaC)** — managing infrastructure through code files instead of manual processes. Versioned, reviewable, repeatable.
- **Provider** — a plugin that lets Terraform talk to a cloud platform (GCP, AWS, Azure...). Each provider exposes resources you can manage.
- **Resource** — a piece of infrastructure you want to create (a GCS bucket, a BigQuery dataset, a VM...).
- **Variable** — makes configs reusable. Define in `variables.tf`, set values per environment.
- **Locals** — computed constants within a module. Not meant to be overridden from outside.
- **Output** — values exposed after `apply`, useful for connecting resources or passing info to other tools.

---

## How State Works

Terraform keeps a **state file** (`.tfstate`) that maps your config to what actually exists in the cloud.

- It's Terraform's memory — it knows what was created, what changed, what needs to be deleted.
- Without state, Terraform can't track drift between your config and reality.
- State can be stored **locally** (default) or **remotely** (e.g. GCS bucket) for team use.

> ⚠️ Never edit the state file manually.

---

## File Structure

| File | Purpose |
|---|---|
| `main.tf` | Where resources are declared |
| `variables.tf` | Variable definitions and defaults |
| `terraform.tfvars` | Actual variable values (often gitignored) |
| `outputs.tf` | Values to expose after apply |
| `.tfstate` | Current state (auto-generated) |

---

## The Workflow

```
init → plan → apply → destroy
```

1. **`terraform init`** — downloads providers, sets up the backend. Run once per project.
2. **`terraform plan`** — previews what will change. Nothing is applied yet.
3. **`terraform apply`** — creates or updates resources. Asks for confirmation (or use `-auto-approve`).
4. **`terraform destroy`** — tears everything down. Useful in dev to avoid cloud costs.

---

## Why it Matters in Data Engineering

Data pipelines need cloud infrastructure — storage buckets, data warehouses, compute. Terraform makes that:
- **Consistent** — same infra setup across environments, no manual drift
- **Versioned** — infra changes are tracked in Git like any other code
- **Collaborative** — the whole team works from the same config
- **Safe** — `plan` shows you exactly what will change before anything happens
