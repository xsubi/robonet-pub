# robonet

Distributed mesh orchestration platform. Self-organizing node runtime with leader election, encrypted P2P channels, and one-command remote provisioning.

Built solo. 59K lines of code. 663 classes. 2,599 tests. 201 use cases.

## What It Is

robonet is a self-organizing mesh runtime where every node runs the same binary. One node becomes the leader through election. The others join as followers. If the leader dies, they elect a new one. New nodes can be provisioned into the mesh with a single command.

```
Hardware → OS → robonet → App
```

Compare to the conventional stack:

```
Hardware → KVM → VM → OS → Docker → Container → Orchestrator → App
```

Same coordination capabilities. Fewer layers. Runs on bare metal.

## Architecture

```
                         ┌─────────────────────┐
                         │     Leader Node      │
                         │  ┌───────────────┐   │
                         │  │      Hub       │   │
                         │  │  (pub/sub +    │   │
                         │  │   routing)     │   │
                         │  └───────┬───────┘   │
                         │          │           │
                         └──────────┼───────────┘
                    ┌───────────────┼───────────────┐
                    │               │               │
              ┌─────┴─────┐  ┌─────┴─────┐  ┌─────┴─────┐
              │ Follower 1 │  │ Follower 2 │  │ Follower 3 │
              │  (Drone)   │  │  (Drone)   │  │  (Drone)   │
              └───────────┘  └───────────┘  └───────────┘
```

Every node is a **Drone**. The leader runs a **Hub** that handles pub/sub message routing, channel subscriptions, and membership. Followers maintain a persistent TCP connection to the leader.

## Key Capabilities

### Mesh Networking
- **Leader election** — consensus-based, automatic failover
- **Pub/sub channels** — typed message routing across the mesh
- **Heartbeat monitoring** — distributed health detection
- **Self-healing** — reconnection with exponential backoff, leader re-election on failure

### Security (HCTH)
- **Hash Chain Trust Hierarchy** — trust is not granted at handshake. It accumulates over time through sustained cryptographic chain continuity. A chain break kills the connection instantly. No PKI. No certificates.

### Remote Provisioning (Colonize)
- **One-command mesh expansion** — SSH into a fresh machine, detect OS, install runtime, start daemon, join mesh. 12-step automated process.
- **Human-initiated, never autonomous** — grows on command, not on its own.

### Chevron Addressing
- **Hierarchical routing** — `chevron/protocol/host:port`
- Flat mode: `1/tcp/127.0.0.1:8340` (node 1, dashboard)
- Federation: `3.12.7.1/tcp/...` (zone.region.swarm.node)
- The chevron identifies the mesh node. TCP is the transport. Host:port is the endpoint.

### CLI Composition
- **Distributed command pipelines** — `->` (structured context), `|` (raw pipe), `[]` (fork)
- Commands route through the mesh: `rn1:status -> rn2:gauge list`
- Named macros propagate across nodes via pub/sub

### HTTP/WebSocket Bridge
- **Mesh-routed proxy** — any follower can serve traffic for any endpoint on any node
- All traffic funneled through authenticated mesh connections
- No external ports opened on the target side

## Packages

| Package | Description |
|---------|-------------|
| `primitives/` | 18 atomic building blocks — shell executor, http client, filesystem ops, parser, retry, SMTP, diff, hasher |
| `foundation/` | Macro store, command tree, CLI composition, fleet naming |
| `protocol/` | Wire protocol for mesh communication |
| `crypto/` | HCTH — hash chain trust hierarchy |
| `swarm/` | Channel, Hub, Drone, Heartbeat, Election |
| `infra/` | System inventory, service management, installer, uninstaller |
| `cloud/` | Colonize, DNS, firewall, VM management, probe, certificate management |
| `federation/` | Cross-swarm routing, zone registry, envelope protocol |
| `storage/` | Mesh-routed file transfer, local/remote source resolution |
| `hardware/` | CPU, memory, disk, GPU, network, USB device abstraction |
| `media/` | Camera, audio, screen capture, streaming |
| `presentation/` | Dashboard server, WebSocket broadcast, theme engine |
| `tui/` | Terminal UI — canvas, widgets, panels, viewport, gauge, file browser |
| `cli/` | Client/server CLI with tab completion |
| `commands/` | 14 command modules — swarm, forge, cloud, recon, shell, fs, monitor, etc. |
| `ai/` | LLM router, hive immunity sentinel |
| `vendor/` | Ansible, Docker, systemd, cloud provider adapters |
| `platform/` | Capability registry, workload scheduler, node type taxonomy |
| `service/` | DroneRuntime lifecycle orchestrator |
| `monitor/` | App registry, watchdog, deploy coordinator |

## Stats

| Metric | Value |
|--------|-------|
| Lines of code | 59,000 |
| Classes | 663 |
| Functions | 2,780 |
| Tests | 2,599 |
| Use cases | 201 |
| Packages | 20+ |
| Python modules | 184 |
| Domains | 22 |
| External dependencies (core) | 0 |

## Use Case Driven Development

Every module traces back to a formal use case specification with preconditions, flow steps, postconditions, and error handling. The UC library is the source of truth — the entire codebase can be regenerated from specifications alone.

22 domains organized in a tiered dependency hierarchy:

```
Tier 1: Atoms (primitives)
Tier 2: Composed (combinations of atoms)
Tier 3: Foundation, Protocol, Crypto, Swarm
Tier 4: Infra, AI, Storage, Forge
Tier 5: Federation
Tier 6: Vendor
Tier 7: Cloud, Hardware, Media, Presentation
Tier 8: Platform
Tier 9: Production
```

Lower tiers never import from higher tiers. Cross-cutting concerns (Auth, Constraints) are explicitly scoped.

## Live Mesh

4 nodes running on KVM infrastructure. Real-time WebSocket dashboard with 18 selectable themes. CLI accessible from any node.

## Related Projects

- **[robonet-forge](https://github.com/xsubi/robonet-forge-pub)** — AI super-agent that turns use case specs into tested code
- **robonet-dashboard** — React SPA consuming the mesh WebSocket API
- **robonet-foundry** — CI/CD pipeline: scan, lock, worktree, worker, push, PR
- **robonet-stamp** — Version registry

## Author

Joel Johnston — [github.com/xsubi](https://github.com/xsubi)

Solo build. Architecture, design, implementation, testing, deployment, and operations.

## License

All rights reserved. Published for prior art disclosure and architectural review.

&copy; 2026 Joel Johnston / xsubi
