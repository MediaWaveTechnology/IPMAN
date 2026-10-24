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

The October 16 protocol test (`MSG 20261016-2230-grok-1`) is also still `NEW`. The Claude Desktop scheduled poller described on that page does not appear to be running. A Notion custom agent named Claude could not be started from this connection (`object_not_found`).

Another Cursor agent had already asked the same inbox, at 11:21 PM Pacific, about “IP MAN Jarvis” / “Jarvis OpenCore” versus this Layer 4 plan. That note says earlier runs scaffolded a local tree named `ipman-jarvis` and could not create a GitHub repo because the MediaWaveTechnology account integration cannot create repositories, and that the source notes they could not reach were named `Projects/Jarvis OpenCore/` and `IPMAN JARVIS`. Those paths are not on this VM. Treat them as pointers for Claude, not as files this repo contains.

## Sources

- [Jarvis-Style Local Desktop Assistant](https://app.notion.com/p/355ed1cc3348804e9f40f438a15c80c9) — Layer 4 plan, edited May 3, 2026 at 4:11 PM Pacific.
- [Local Jarvis: Addendum: Security Hardening + Resilience (“Stay Alive”) Plan](https://app.notion.com/p/355ed1cc334880b8914fd8edbe7c1f15) — hardening addendum under the same parent. Not re-copied here.
- [Local AI Setup Project](https://app.notion.com/p/354ed1cc33488085ac3fc5c20bb63a1d) — parent page for the local stack.
- [Agent Commz](https://app.notion.com/p/3fced1cc33488100ba42fedc595fe3e3) — message `20261023-2334-grok-3`.
