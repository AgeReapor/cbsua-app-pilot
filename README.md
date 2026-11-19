# CBSUA Pilot

CBSUA-maintained fork of [Frappe Pilot](https://github.com/frappe/pilot), used for **local prototyping and development** of CBSUA Frappe applications. Pilot provides bench/site lifecycle management and an Admin UI; it is a developer environment manager, not the production deployment orchestrator for CBSUA Helpdesk.

## Intended use

Use this repository to create and manage local benches, initialize sites, fetch CBSUA app forks, build frontend assets, and inspect/manage the local environment through Pilot's Admin UI.

The production Helpdesk image and runtime are managed separately by [cbsua-app-helpdesk-deployment](https://github.com/AgeReapor/cbsua-app-helpdesk-deployment), using reusable container definitions from [cbsua-app-frappe-docker](https://github.com/AgeReapor/cbsua-app-frappe-docker). A Pilot-managed bench is not automatically identical to the production container image; record and compare exact app commits and runtime versions before promoting changes.

## Installation

This fork retains Pilot's installer and command-line workflow. For a source-based development checkout, consult the install script and requirements in this repository. Do not assume upstream Pilot's latest requirements are compatible with every historical CBSUA bench.

## CBSUA local workflow

The following sequence reflects the project's local prototyping workflow. Pilot may prompt for runtime/database initialization and site setup; complete those steps before installing apps.

```bash
pilot new cbsua-app-bench
pilot -b cbsua-app-bench start
```

Open the Admin UI at the local URL printed by Pilot. The project has used:

```text
http://[::1]:7000/sites
```

Fetch the CBSUA application repositories:

```bash
pilot get-app https://github.com/AgeReapor/cbsua-app-telephony
pilot get-app https://github.com/AgeReapor/cbsua-app-helpdesk
```

Install Telephony before Helpdesk because Helpdesk declares it as a dependency:

```bash
pilot --site cbsua-helpdesk.localhost install-app telephony
pilot --site cbsua-helpdesk.localhost install-app helpdesk
```

Build the Helpdesk frontend assets:

```bash
pilot build --app helpdesk
```

The commands assume that a compatible CBSUA Frappe bench and the target site already exist. The site name shown is an example used by the project; substitute the actual local site name when necessary. Use the deployment repository's source lock as the reference when aligning local and image builds.

## Common workflow

| Task | Pilot command |
|---|---|
| Create a bench | `pilot new <bench-name>` |
| Start a bench / Admin UI | `pilot -b <bench-name> start` |
| Fetch an app | `pilot get-app <repository-url>` |
| Install an app on a site | `pilot --site <site-name> install-app <app-name>` |
| Build app assets | `pilot build --app <app-name>` |

Consult `pilot --help` and the current command help for additional options. CLI flags and supported runtimes can change between fork revisions.

## Development notes

- Use a separate local bench/site for experiments; avoid mixing unrelated branches in one environment.
- Keep a record of framework, app, Python, Node, MariaDB, and Redis versions when reporting a reproducibility issue.
- Do not place production credentials or real institutional data in a prototype site.
- Before validating a release, compare local app commit SHAs with the intended deployment source lock.
- Pilot's Admin UI is a local development convenience; it is not a public-facing CBSUA Helpdesk administration portal.

## Relationship to other repositories

- [cbsua-app-frappe](https://github.com/AgeReapor/cbsua-app-frappe) — framework runtime.
- [cbsua-app-telephony](https://github.com/AgeReapor/cbsua-app-telephony) — Helpdesk dependency.
- [cbsua-app-helpdesk](https://github.com/AgeReapor/cbsua-app-helpdesk) — ticketing application.
- [cbsua-app-helpdesk-deployment](https://github.com/AgeReapor/cbsua-app-helpdesk-deployment) — authoritative image/release/deployment documentation.

## Upstream relationship and license

This is a fork of Pilot. Preserve upstream attribution and licensing. Keep CBSUA-specific changes documented and periodically compare with upstream Pilot.
