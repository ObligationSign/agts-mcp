# AGTS MCP Server

**Sovereign MCP Gateway — Governed Tool Invocation for Autonomous Agents**

Model Context Protocol server that turns tool calls into cryptographically authorized, verifiable actions. 64 governed tools across 6 layers — hybrid Ed25519 + SLH-DSA signed and Merkle-anchored.

---

## Server URL

```
https://agts-mcp.obligationsign.com/mcp
```

| Transport | URL | Status |
|-----------|-----|--------|
| **Streamable HTTP (primary)** | `https://agts-mcp.obligationsign.com/mcp` | Active |
| SSE (deprecated) | `https://agts-mcp.obligationsign.com/mcp/sse` | Deprecated |

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
      "url": "https://agts-mcp.obligationsign.com/mcp",
      "headers": {
        "Authorization": "Bearer YOUR_TOKEN"
      }
    }
  }
}
```

### Programmatic (JSON-RPC over HTTP)

```bash
curl -X POST https://agts-mcp.obligationsign.com/mcp \
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

## Tool Catalog (64 Tools)

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
