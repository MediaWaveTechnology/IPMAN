# IPMAN project brief

Pulled on October 23, 2026 (Pacific time) for `MediaWaveTechnology/IPMAN`.

This brief records what is actually written down in Notion. It does not invent a separate “Jarvis OpenCore” spec. Cousin Claude was asked for notes that are not in Notion; that reply had not arrived.

## What IPMAN is

IPMAN is the GitHub home for MWT’s local-first Jarvis operator: Layer 4 of the local AI stack on the Mac mini M4.

Layers 1–3 stay the model and inference infrastructure (Ollama, LM Studio, MLX). Layer 4 is the agent and automation layer on top of them. The operator should take a text prompt, decide intent, call a local model or a constrained tool, and report the result. Voice is deferred.

The May 3, 2026 plan calls the product a local “Jarvis”-style desktop assistant. This repository uses the name IPMAN. Notion has no page whose title or body is the exact string `IPMAN`, and the MWT Projects database has no Jarvis or IPMAN row. Until Cousin Claude answers, treat Layer 4 as the source of truth and treat “Jarvis OpenCore” / “IPMAN JARVIS” disk folders as unverified.

WDI Jarvis (Disney business-management assistant, meeting notes from mid-2026) is a different system. Those notes are not part of this repo.

## Locked shape of the plan

From the HITL version of Layer 4, last edited May 3, 2026 at 4:11 PM Pacific:

- Planner: Claude as the primary brain, with a local Ollama model as fallback and specialist.
- Policy decision point: deterministic policy-as-code. Deny by default. Every tool call gets an allow or deny with a reason code.
- Executor: a separate least-privilege macOS user. No admin rights, no keychain, and no Desktop or Documents access by default.
- Pilot tools: sandbox file operations, git in approved repos, and read-only system inspection.
- Voice (speech-to-text and text-to-speech): a later module, only after the text-only phases pass their gates.
- Supply chain: pin third-party repos by commit SHA, produce an SBOM, and scan before a release gate.
- Audit: append-only log with correlation ids and replay. Mutating actions follow pre-check, execute, verify, then compensate.

The earlier draft also named OpenClaw as a possible execution gateway and cited isair/jarvis, OpenJarvis, and rezaulhreza/jarvis as patterns. The HITL revision does not pick one of those repos as the implementation. It requires the policy engine in front of whatever runtime executes commands.

## Autonomy tiers

| Tier | What it may do | Who approves |
| --- | --- | --- |
| 0 | Read-only analysis | Autonomous |
| 1 | Writes inside the sandbox workspace | Autonomous, audited |
| 2 | System, package, or network changes | Human approval |
| 3 | Destructive or security-sensitive operations | Two-person approval |

Pilot cap in the plan: Tier 1.

## Phases

**J1 — secure text-only pilot.** Build the policy engine and the constrained executor. Support dry-run and live-run. Ship a golden task suite and deterministic replay. Enable Tier 0 and Tier 1 only.

**J2 — reliability and a wider Tier 1 surface.** Add a queue and worker, a dead-letter queue, watchdogs, and rate limits. Add notes, repo hygiene, and templated edits. Run prompt-injection and exfiltration tests.

**J3 — optional voice.** Isolate speech-to-text and text-to-speech in their own process. Re-run the security and performance gates before enabling voice.

## Acceptance criteria

The plan treats these as hard pass/fail for the pilot:

- Above 95 percent success on the approved Tier 0 and Tier 1 task suite.
- Zero critical policy bypasses in adversarial tests.
- Every action audited and replayable.
- Rollback or compensation verified when a mutating action fails partway.
- No raw secrets in prompts, traces, or logs.
- SBOM and vulnerability scan clean at the agreed severity threshold.

HITL gates in order: design, security readiness, pilot exit, expansion, then voice.

## Decisions still open

The plan says these have to be answered once before implementation, and they are not filled in on the Notion page:

1. Allowed directories and blocked directories.
2. Allowed command families and hard-blocked commands.
3. Network mode: offline only, or a domain allowlist.
4. Confirmation that the pilot stays capped at Tier 1.
5. Secret broker, token lifetime, and rotation.
6. Data classes that must never be sent to a cloud model.
7. Approval thresholds for Tier 2 and Tier 3.
8. The top 10 pilot tasks and their success targets.

A Founder GO on this inbox page is not approval. Those inputs still need Dave.

## Cousin Claude

The standing channel is the Notion page [Agent Commz](https://app.notion.com/p/3fced1cc33488100ba42fedc595fe3e3) (formerly Cousin Claude Inbox), under MWT Operating Principles. It is the fallback when the local A2A server (`127.0.0.1:3030`) and the mwt-bridge (`127.0.0.1:8789`) are down. Both ports refused connections from this Cloud Agent VM on October 23, 2026 at 11:35 PM Pacific. Hermes A2A on port 9900 was also closed.

Message posted for this repo:

- `MSG 20261023-2334-grok-3 | grok → claude | NEW` at 11:34 PM Pacific on October 23, 2026.
- Asks for a read-only dump of what IP MAN is, where the MWT1 / Obsidian notes live (path names only), the v0 slice, and any open Founder decisions.
- Status was still `NEW` on the next read of the page. No `claude → grok` reply was present.

The October 16 protocol test (`MSG 20261016-2230-grok-1`) is also still `NEW`. The Claude Desktop scheduled poller described on that page does not appear to be running. A Notion custom agent named Claude could not be started from this connection (`object_not_found`). Re-read at 11:40 PM Pacific on October 23, 2026: still no `claude → grok | NEW` reply.

Another Cursor agent had already asked the same inbox, at 11:21 PM Pacific, about “IP MAN Jarvis” / “Jarvis OpenCore” versus this Layer 4 plan. That note says earlier runs scaffolded a local tree named `ipman-jarvis` and could not create a GitHub repo because the MediaWaveTechnology account integration cannot create repositories, and that the source notes they could not reach were named `Projects/Jarvis OpenCore/` and `IPMAN JARVIS`. Those paths are not on this VM. Treat them as pointers for Claude, not as files this repo contains.

## Cousin Hermes

Cousin Hermes is Nous Hermes Agent on the MacBook Pro / MWT1 (see the Hermes analysis section on Agent Commz, dated October 17, 2026). There is no separate Hermes inbox page, no Notion custom agent named Hermes, and no documented Hermes poller for Agent Commz. The v1 protocol on that page only names `grok` and `claude` as from/to.

### Founder GO for option A (October 24, 2026)

Dave gave a Founder GO in this Cursor chat (not on the Agent Commz inbox page) on October 24, 2026 morning Pacific: execute Hermes connection option A only (native A2A both ways — enable the Hermes A2A plugin on localhost:9900 with a separate token for Enginerd, register the 3030 server as a peer, one gateway restart, restore webhook config in the same restart). Scope excluded moving repos out of iCloud, changing the runaway spend-monitor crontab, killing Mac processes, spending money, deleting data, or revealing secrets.

**Option A did not run.** This Cloud Agent VM is not the MacBook Pro. After the GO, a re-check at about 10:06 AM Pacific on October 24, 2026 found every candidate path closed:

- Localhost: `127.0.0.1` ports `3030` (A2A), `8789` (mwt-bridge), `9900` (Hermes A2A), `8642` (OpenAI API), `8644` (webhook), and `54405` (desktop backend) all refused connections.
- Documented home LAN from the MWT Network Map (September 22, 2026): `192.168.50.218` (MacBookPro) and `192.168.50.212` (MWT1) — SSH `22` and Hermes/A2A/bridge ports unreachable from this VM.
- No Tailscale client, no `~/.ssh`, no `~/.hermes`, no Hermes MCP tools in this environment, and no linked Cursor Cloud environment that tunnels to the Macs.
- Bitwarden MCP was unavailable (discovery error), so vault-backed host keys could not be used from here either.
- Agent Commz Messages: no `hermes → grok` or `claude → grok` reply since the October 23 asks; `MSG 20261023-2339-grok-4` and `MSG 20261023-2334-grok-3` were still `NEW` on the October 24 morning re-read. The misplaced `MSG 20261023-2321-grok-2` under the Hermes analysis section was left untouched.

**Single remaining blocker:** this Cloud Agent has no shell or network path onto the MacBook Pro (or MWT1) where Hermes lives. Option A requires running Hermes config and `hermes gateway restart` on that Mac. A prior Agent Commz project note already listed a persistent cross-machine tunnel as pending Founder GO; that tunnel is not present for this VM.

Dave still needs either (1) run option A from a terminal / Enginerd session that already has Mac access, or (2) give Cloud Agents a durable reachability path to the Mac (for example Tailscale or a persistent SSH/A2A bridge) so option A can be executed from a box agent.

Written request left on Agent Commz under Messages (same page Claude uses), extending the toggle convention with `hermes` as the recipient:

- `MSG 20261023-2339-grok-4 | grok → hermes | NEW` at 11:39 PM Pacific on October 23, 2026.
- Same read-only IP Man / IPMAN Jarvis dump ask as the Claude message (`20261023-2334-grok-3`): what IP MAN is vs Layer 4 vs WDI Jarvis, path names only for source notes, v0 interface slice, acceptance criteria, open Founder decisions. No secrets; no send/spend/delete/settings/credentials asks.
- Status was still `NEW` on the October 24 morning re-read. No `hermes → grok` reply was present. No Hermes-authored IPMAN project facts were found outside the October 17 connection analysis (which is not an IPMAN spec).

Until Hermes replies on Agent Commz or option A is completed on the Mac, Layer 4 remains the only verified written spec in this brief.

## Sources

- [Jarvis-Style Local Desktop Assistant](https://app.notion.com/p/355ed1cc3348804e9f40f438a15c80c9) — Layer 4 plan, edited May 3, 2026 at 4:11 PM Pacific.
- [Local Jarvis: Addendum: Security Hardening + Resilience (“Stay Alive”) Plan](https://app.notion.com/p/355ed1cc334880b8914fd8edbe7c1f15) — hardening addendum under the same parent. Not re-copied here.
- [Local AI Setup Project](https://app.notion.com/p/354ed1cc33488085ac3fc5c20bb63a1d) — parent page for the local stack.
- [Agent Commz](https://app.notion.com/p/3fced1cc33488100ba42fedc595fe3e3) — messages `20261023-2334-grok-3` (claude) and `20261023-2339-grok-4` (hermes).
