---
runbook: true
repo: gps_traccar
status: paused
type: docs
updated: 2026-07-16
health: unknown
deploy: GitHub Pages via MkDocs workflow in .github/workflows/docs.yml
next: add API documentation
---

# gps_traccar — Runbook

## Purpose

`gps_traccar` is a documentation repository for operating and understanding the Traccar GPS tracking platform. The README describes it as a curated, production-focused set of notes covering architecture, installation, configuration, databases, scaling, troubleshooting, security, supported devices, protocols, and operational responses.

## Stack

- Documentation site: MkDocs with the Material theme.
- Published docs source: `docs/`.
- Site config: `mkdocs.yml`.
- CI/deploy: GitHub Actions workflow at `.github/workflows/docs.yml`.
- Documented Traccar stack: Java server using Netty, Jetty, Liquibase, JDBC drivers, a React + Material UI web app, SQL databases, and GPS device protocol ports.

## Where it runs

The MkDocs config sets `site_url` to `https://qvidal01.github.io/gps_traccar/` and `repo_url` to `https://github.com/qvidal01/gps_traccar`.

Runtime hosts for any Traccar server covered by these notes are unknown from this repo. The README says Traccar can be deployed on-prem or in the cloud, but does not identify a live server for this repository.

## Run / deploy

Install the docs build dependency used by CI:

```bash
pip install mkdocs-material
```

Build the documentation site:

```bash
mkdocs build
```

Run the docs locally:

```bash
mkdocs serve
```

The workflow deploys GitHub Pages on pushes to `main` or `master` when `docs/**`, `mkdocs.yml`, or `.github/workflows/docs.yml` changes. It can also be run with `workflow_dispatch`.

## Health & recovery

Health is unknown. The workflow builds with `mkdocs build` and uploads the generated `site/` artifact before deploying to GitHub Pages. If deployment fails, check `.github/workflows/docs.yml`, rerun `mkdocs build`, and review the GitHub Actions run for the Documentation workflow.

For Traccar operations, the README points to `OPERATIONS_RUNBOOK.md`, `optimization.md`, `servertroubleshooting.md`, and the legacy `severtroubleshooting.md` alias.

## Current status

The most recent commits are documentation-focused: adding a docs deployment workflow, API reference placeholder, getting started guide, documentation homepage, MkDocs configuration, and the initial GPS Traccar documentation commit. The latest commit shown by `git log --oneline -30` is `4af0f55 ci: Add docs deployment workflow`; its commit date is 2026-01-28, so by the requested recency rule this repository is paused as of 2026-07-16. `docs/api.md` still says to add API documentation.

## Links

- Docs site: `https://qvidal01.github.io/gps_traccar/`
- Repository: `https://github.com/qvidal01/gps_traccar`
- Docs index: `docs/index.md`
- Getting started: `docs/getting-started.md`
- API reference: `docs/api.md`
