# AGTS MCP Server

**Sovereign MCP Gateway — Governed Tool Invocation for Autonomous Agents**

Model Context Protocol server that turns tool calls into cryptographically authorized, verifiable actions. 115+ governed tools across 6 layers — hybrid Ed25519 + SLH-DSA signed and Merkle-anchored. Designed to support EU AI Act, GDPR, SOC 2, ISO 27001, DORA, NIS2, and FINRA compliance.

---

## Server URL

```
https://mcp.obligationsign.com/mcp
```

| Transport | URL | Status |
|-----------|-----|--------|
| **Streamable HTTP (primary)** | `https://mcp.obligationsign.com/mcp` | Active |
| SSE (deprecated) | `https://mcp.obligationsign.com/mcp/sse` | Deprecated |

## Authentication

The gateway requires a Bearer token issued through the ObligationSign platform. All requests must include an `Authorization` header.

To obtain a token, register at [obligationsign.com/start/](https://obligationsign.com/start/).

## Quick Start

### Claude Desktop / Cursor / Windsurf

Add to your MCP configuration:

```json
{
  "mcpServers": {
    "agts": {
      "url": "https://mcp.obligationsign.com/mcp",
      "headers": {
        "Authorization": "Bearer YOUR_TOKEN"
      }
    }
  }
}
```

### Programmatic (JSON-RPC over HTTP)

```bash
curl -X POST https://mcp.obligationsign.com/mcp \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -d '{
    "jsonrpc": "2.0",
    "id": 1,
    "method": "tools/list",
    "params": {}
  }'
```

---

## What is AGTS?

AGTS (Autonomous Governance Transparency Standard) is a transparency-backed governance protocol that converts validated machine decisions into append-only cryptographic records, independently verified by a network of witnesses and monitors.

```
decision → record → immutable history → verifiable truth
```

Every tool call passes through a **five-gate firewall** (Statistical, Causal, Regression, Evidence, Human-Compatible Explanation) before execution. Admitted actions are Merkle-anchored into an append-only transparency log and signed with hybrid post-quantum cryptography.

---

## Cryptography & Validator Quorum

Every governance envelope produced by this server is signed with a **hybrid post-quantum signature**: classical Ed25519 in parallel with SLH-DSA-SHAKE-128f. The signature is verifiable today with Ed25519-only tooling and remains forgery-resistant against future quantum adversaries via SLH-DSA. Sovereign Mail additionally uses **ML-KEM-512** for encrypted key exchange, and peer-trust ceremonies use **ML-DSA-44**.

Admit decisions for governed tool calls require an **independent 3-of-4 validator quorum** with constraint `regulator_count ≥ 1` and `auditor_count ≥ 1`. Validators are deployed as separate Cloudflare Workers (`agts-validator-{a,b,c,d}`) with distinct ECDSA P-256 keypairs; each validator independently re-evaluates the proof bundle and signs an `AGTS_VOTE_V1` certificate. The quorum certificate is then anchored alongside the governance envelope in the Merkle transparency log, so any external party can reconstruct the four signed votes and verify that quorum was honestly met.


## Tool Catalog (115+ Tools)

### Layer 1 — Infrastructure

#### VPN & Network
| Tool | Description |
|------|-------------|
| `create_tunnel` | Establish an attested VPN tunnel (WireGuard/IPSec/TLS) |
| `tunnel_status` | Monitor uptime, transfer bytes, and attestation status |
| `disconnect_tunnel` | Terminate session and log the event |
| `dns_query` | Secure DNS resolution through the Sovereign resolver |

#### Monitor
| Tool | Description |
|------|-------------|
| `system_health` | Probe all mesh workers for status and latency |
| `check_alerts` | Fetch the monitor alert timeline |
| `subscribe_alerts` | Webhook subscription for real-time alerts |
| `check_equivocations` | Fetch conflicting Signed Tree Head proofs |
| `gossip_status` | Current state of gossip protocol and monitor identity |

### Layer 2 — Identity & Communications

#### Sovereign Mail
| Tool | Description |
|------|-------------|
| `send_sovereign_mail` | Send encrypted (internal) or plaintext (external) messages |
| `list_inbox` | List message metadata from an inbox |
| `read_message` | Retrieve full message content by ID |
| `lookup_recipient_keys` | Retrieve public keys (ECDH P-384 / ML-KEM-512) |

#### Sovereign Drive
| Tool | Description |
|------|-------------|
| `upload_file` | Client-side encrypted upload with integrity anchoring |
| `list_files` | List file metadata, sizes, and sharing status |
| `download_file` | Retrieve ciphertext for client-side decryption |
| `share_file` | Share files using ECDH key wrapping |
| `delete_file` | Remove a file and create a death leaf in the log |

#### OpenClaw (Agent Identity)
| Tool | Description |
|------|-------------|
| `register_claw` | Create a cryptographic Claw Passport and identity keys |
| `scan_skill` | Scan agent skills through the five-gate firewall |
| `claw_status` | Return trust score, governance history, and quota |
| `export_passport` | Secure export of the identity envelope |
| `govern_skill_exec` | High-level gate for skill execution |
| `report_skill_execution` | Link skill results to authorization |
| `verify_skill_proof` | Cryptographic verification of skill scans |

#### Peer Trust (Post-Quantum)
| Tool | Description |
|------|-------------|
| `authenticate_peer` | Hybrid PQC (ML-DSA-44) + Classical (ECDSA) auth ceremony |
| `verify_agent_identity` | Fingerprint and revocation check via key registry |
| `exchange_credentials` | Secure transport using hybrid ECDH + ML-KEM-512 |
| `peer_trust_network` | List known peers and mutual trust status |

### Layer 3 — Governance

| Tool | Description |
|------|-------------|
| `submit_governance` | Pre-execution gate evaluation (ADMIT/QUARANTINE/REFUSE) |
| `verify_commitment` | Verify commitment in transparency log with Merkle proofs |
| `report_execution` | Log outcome of an authorized action |
| `update_policy` | Update firewall parameters |
| `list_policies` | List active governance thresholds and history |
| `governance_report` | Aggregated decision reporting |
| `governance_stats` | Per-gate pass/fail statistics and HCE distribution |
| `gate_analysis` | Detailed breakdown of a specific evaluation decision |

### Layer 4 — Memory

| Tool | Description |
|------|-------------|
| `memory_store` | Store hierarchical key-value entries |
| `memory_recall` | Retrieve specific entries by path |
| `memory_list` | Prefix-based listing of memories |
| `memory_search` | Semantic similarity search using vector embeddings |
| `cross_session_recall` | Semantic search across historical sessions |
| `memory_context` | Mode-based contextual summary |
| `consolidate_memory` | Compare and compact redundant entries |
| `memory_gc` | Garbage collection of stale entries |
| `set_memory_ttl` | Configure automatic expiration for entries |
| `memory_delete` | Remove specific memory paths |

### Layer 5 — Billing

| Tool | Description |
|------|-------------|
| `provision_resource` | Create a billing project for an agent |
| `manage_spending` | Freeze/unfreeze spending |
| `check_project_billing` | Detailed usage and charge breakdown |
| `verify_provision` | Check billing project linkage |
| `set_budget_alert` | Configure spending thresholds |
| `check_budget` | Real-time spend vs. cap tracking |

### Layer 6 — Orchestration

| Tool | Description |
|------|-------------|
| `spawn_sub_agent` | Launch an isolated asynchronous sub-agent |
| `sub_agent_status` | Poll sub-agent execution status |
| `sub_agent_result` | Retrieve sub-agent results |
| `chain_delegation` | Recursive delegation with budget decay and depth limits |
| `create_template` | Define a reusable multi-step workflow |
| `apply_template` | Instantiate a template into a running task |
| `list_sub_agents` | List active sub-agents |
| `list_templates` | List available workflow templates |
| `cancel_sub_agent` | Terminate a running sub-agent |
| `merge_sub_results` | Aggregate results from parallel sub-agents |
| `delegation_tree` | Reconstruct the full lineage of a delegated chain |

---

## REST API

In addition to MCP, the gateway exposes REST endpoints:

| Endpoint | Description |
|----------|-------------|
| `GET /api/v1/health` | Upstream service health checks |
| `GET /api/v1/audit` | Audit log queries |
| `GET /api/v1/assets` | Asset management |
| `GET /verify?commitment={hash}` | Commitment verification with Merkle proof |

---

## Architecture

The MCP server is one access surface to the AGTS protocol. The full clearinghouse specification is published separately.

```
Agent ─── MCP Gateway ─── Five-Gate Firewall ─── Transparency Log
               │                                        │
               ├── Mail Worker (E2EE)                   ├── Merkle Tree
               ├── Drive Worker (Encrypted Storage)     ├── Signed Tree Heads
               ├── OpenClaw (Identity)                  └── Gossip Protocol
               ├── Tunnel Worker (VPN)
               ├── Monitor Worker (Health)
               └── Protocol Worker (Governance)
```

---

## How This Server Differs

Most MCP servers in the ecosystem (~83%) are thin API wrappers — a single connector exposed as one or two tools, running locally via stdio, with no authentication or validation. The production-tier servers from vendors like GitHub, Slack, and AWS (~2%) are well-built but architecturally simple: they translate `tools/call` into API requests for their own service.

This server operates at a different level. It is not a wrapper around an external API — it implements a full governance protocol where:

- Every action must pass through **five evaluation gates** before execution is authorized
- Admitted actions are **cryptographically signed** (hybrid Ed25519 + SLH-DSA) and **Merkle-anchored** in an append-only transparency log
- Execution outcomes are **traced back** to their authorization, creating a closed governance loop
- Independent monitors verify log consistency through a **gossip protocol**, detecting equivocation or tampering
- Identity is handled through **post-quantum cryptography** (ML-DSA-44, ML-KEM-512) for forward security

The result is that every tool call produces independently verifiable cryptographic evidence of what was authorized, what was executed, and whether the outcome matched the authorization — the kind of audit trail required in regulated industries (financial services, healthcare, legal, critical infrastructure).

### Regulatory Readiness

The gateway's architecture is designed to support compliance across multiple regulatory frameworks. The table below maps specific requirements to the AGTS features that address them.

#### EU AI Act

| Requirement | AGTS Implementation |
|---|---|
| **Art. 9 — Risk management** | Five-gate firewall evaluates every action before execution; QUARANTINE decisions enable human intervention |
| **Art. 12 — Record-keeping** | Append-only Merkle transparency log with signed governance envelopes; automatic recording of every authorization and execution event |
| **Art. 13 — Transparency** | Every commitment is independently verifiable with cryptographic inclusion proofs; governance decisions are explainable via the HCE (Human-Compatible Explanation) gate |
| **Art. 14 — Human oversight** | Gate thresholds are configurable; QUARANTINE/REFUSE decisions halt execution; policy updates are themselves governed and logged |

#### SOC 2

| Trust Principle | AGTS Implementation |
|---|---|
| **Security** | Hybrid Ed25519 + SLH-DSA signing; post-quantum key exchange (ML-KEM-512); Cloudflare Access authentication with email binding; per-tool rate limiting |
| **Availability** | Multi-worker mesh architecture with independent health monitoring; gossip protocol detects node failures; `system_health` probes all upstream services |
| **Processing Integrity** | Five-gate firewall validates every action before execution; execution outcomes are traced back to authorization; variance detection on deviated outcomes |
| **Confidentiality** | End-to-end encrypted mail (ECDH P-384 key wrapping); client-side encrypted drive storage; attested VPN tunnels for network isolation |
| **Privacy** | No plaintext storage of message content; cryptographic identity via Claw Passport; key registry with revocation support |

#### ISO 27001

| Control Area | AGTS Implementation |
|---|---|
| **A.8 — Asset management** | Every file, message, and identity key is tracked with cryptographic hashes; death leaves record asset deletion |
| **A.9 — Access control** | Bearer token authentication; Cloudflare Access email binding; per-agent identity via Claw Passport with trust scoring |
| **A.10 — Cryptography** | Hybrid classical + post-quantum signing (Ed25519 + SLH-DSA); PQC key exchange (ML-KEM-512); ECDH P-384 for mail encryption |
| **A.12 — Operations security** | Immutable transparency log; independent monitor workers verify log consistency via gossip protocol; equivocation detection |
| **A.16 — Incident management** | Alert subscription system; equivocation checks detect conflicting Signed Tree Heads; governance variance records track deviations |
| **A.18 — Compliance** | All governance decisions, policy changes, and execution outcomes are cryptographically anchored; audit trail is independently verifiable |

#### DORA (Digital Operational Resilience Act)

| Requirement | AGTS Implementation |
|---|---|
| **Art. 6 — ICT risk management** | Five-gate firewall performs pre-execution risk evaluation; gate thresholds are configurable per policy; governance stats provide per-gate pass/fail analytics |
| **Art. 9 — Detection** | Monitor workers continuously probe service health; gossip protocol detects inconsistencies; alert subscriptions enable real-time incident notification |
| **Art. 10 — Response and recovery** | QUARANTINE decisions halt risky operations; `manage_spending` can freeze agent budgets; execution tracing links failures to their authorization |
| **Art. 11 — Testing** | Every governance decision is independently verifiable via Merkle inclusion proofs; `verify_commitment` enables third-party audit at any time |
| **Art. 15 — Third-party risk** | Sub-agent delegation enforces budget decay and depth limits; `chain_delegation` tracks recursive delegation lineage; `delegation_tree` reconstructs full provenance |

#### GDPR

| Requirement | AGTS Implementation |
|---|---|
| **Art. 5(1)(a) — Lawfulness, fairness, transparency** | Every agent action is pre-authorized through the five-gate firewall; governance decisions include HCE (Human-Compatible Explanation) for explainability |
| **Art. 5(1)(e) — Storage limitation** | `memory_gc` garbage collects stale entries; `set_memory_ttl` enforces automatic expiration; `delete_file` creates cryptographic death leaves proving deletion |
| **Art. 5(1)(f) — Integrity and confidentiality** | End-to-end encrypted mail and drive storage; hybrid post-quantum cryptography; append-only transparency log ensures integrity |
| **Art. 17 — Right to erasure** | `memory_delete` removes specific memory paths; `delete_file` with death leaf provides verifiable proof of deletion |
| **Art. 25 — Data protection by design** | Cryptographic identity via Claw Passport (no PII required); client-side encryption for stored files; key registry with revocation support |
| **Art. 30 — Records of processing activities** | Merkle transparency log records every authorization, execution, and policy change with timestamps and cryptographic signatures |

#### NIS2

| Requirement | AGTS Implementation |
|---|---|
| **Art. 21 — Cybersecurity risk management** | Governance firewall, cryptographic signing, transparency log, and post-quantum key exchange form a defense-in-depth architecture |
| **Art. 23 — Incident reporting** | Alert system with webhook subscriptions; equivocation detection; governance variance records provide forensic evidence chain |
| **Art. 21(2)(d) — Supply chain security** | Sub-agent orchestration with budget controls and depth limits; delegation tree provides full provenance tracking across agent boundaries |

#### FINRA

| Requirement | AGTS Implementation |
|---|---|
| **Rule 3110 — Supervision** | Five-gate firewall enforces pre-execution supervision of every agent action; QUARANTINE/REFUSE decisions prevent unauthorized transactions |
| **Rule 3120 — Supervisory control system** | Governance stats and gate analysis provide supervisory analytics; policy thresholds are configurable and themselves governed |
| **Rule 4511 — Books and records** | Append-only Merkle transparency log with Ed25519 + SLH-DSA signed governance envelopes; every authorization and execution is permanently recorded |
| **Rule 3310 — AML compliance** | `submit_governance` evaluates evidence including value, recipient, and intent before any financial action is authorized; variance detection flags deviations |
| **Rule 2210 — Communications** | `send_sovereign_mail` with governance pre-authorization ensures all agent communications are reviewed and logged before transmission |

The closest comparable in the market is not another MCP server — it is what a regulated enterprise would build internally to control what their AI agents can do in production.

---

## Specification

The normative AGTS Clearinghouse specification is available at:

**[github.com/obligationsign/agts-clearinghouse](https://github.com/obligationsign/agts-clearinghouse)**

---

## Links

- **Landing Page**: [obligationsign.com/mcp/](https://obligationsign.com/mcp/)
- **Platform**: [obligationsign.com](https://obligationsign.com)
- **Specification**: [github.com/obligationsign/agts-clearinghouse](https://github.com/obligationsign/agts-clearinghouse)
- **MCP Registry**: [registry.modelcontextprotocol.io](https://registry.modelcontextprotocol.io/?search=agts)

---

## License

This document is published for reference and integration purposes. The MCP server is operated by ObligationSign. See the [AGTS Clearinghouse Specification](https://github.com/obligationsign/agts-clearinghouse) for protocol terms.



