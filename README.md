# homelab-as-code

Infrastructure-as-code for a two-machine (soon three) home cybersecurity lab, built out as a
portfolio project alongside OSCP study. Two Ubuntu Server 24.04 laptops, joined over Tailscale,
managed remotely.

## Machines

| Host | Role | Specs |
|---|---|---|
| `coldcoffee` | Personal services (Immich, Vaultwarden) + will host the heavier Phase 1 workloads | i5-8265U, 8GB RAM, ~930GB disk |
| `masalachai` | GPU-assisted offensive tooling + lightweight services | i3-3217U, 3.7GB RAM, NVIDIA GT 740M |

A third machine (16GB RAM) is planned to take over SIEM-class workloads once available.

## Layout

- `compose/` — per-service Docker Compose stacks. Each service's secrets live in a gitignored
  `.env` next to its compose file — never commit those.
- `ansible/` — host provisioning and configuration (playbooks + inventory).
- `docs/` — architecture notes and write-ups.

## Status

Currently running on `coldcoffee`: Immich, Vaultwarden (behind `tailscale serve` for TLS),
Homepage, Portainer. All bound to the Tailscale interface or localhost only — nothing is
reachable from the raw LAN.

`masalachai` is still a blank slate, awaiting the offensive-tooling buildout.

See `docs/` (as it fills in) for the fuller architecture writeup.
