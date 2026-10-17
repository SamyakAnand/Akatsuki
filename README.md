# Akatsuki AI

Akatsuki is a local mission-workforce prototype. The current implementation provides a durable mission desk, ten distinct member profiles, a deterministic Pain → Itachi → Sasori → Hidan workflow, typed persistent handoffs, separate member-chat history, a persisted collaboration timeline, registered artifacts, fixed verification, and one built-in deliverable: a Celsius/Fahrenheit and kilometers/miles converter.

Unsupported mission types produce a saved request scaffold and remain **Partial**. The app does not infer that a scaffold solved the request. Ten profiles have role/personality configuration, but only four participate as deterministic local mission routines. No language model or external research provider is connected. Direct member chat persists your messages and reports the missing provider; it never invents an agent response.

## Requirements

- Python 3.13
- Windows PowerShell commands below assume the Python Launcher (`py`) is installed.
- No third-party Python packages or frontend build step are required.

## Start Akatsuki

From the repository root:

```powershell
py -3.13 --version
py -3.13 run.py
```

Open <http://127.0.0.1:8766/>. The default port is 8766 because 8765 is reserved in the current development environment. To select another available local port, run `py -3.13 run.py --port 8767` and use the matching URL. Stop the server with `Ctrl+C`.

Mission state and artifacts are written under `data/`. Set `AKATSUKI_DATA_DIR` before startup to use another local directory:

```powershell
$env:AKATSUKI_DATA_DIR = "C:\AkatsukiData"
py -3.13 run.py
```

The HTTP server rejects non-loopback bind addresses. Do not expose it to untrusted users.

## Member and team conversations

Open **Agents** to select a member. **Your Chat** stores messages in a conversation history separate from mission messages, and safely renders basic Markdown emphasis and fenced code. It shows timestamps, supports new conversations and network retries, and reports provider unavailability. No model reply is produced until an actual provider adapter is configured. The prototype has no sign-in: all data uses one local owner scope, and other people with access to this machine can inspect the SQLite database.

**Team Conversations** and **Team activity** read persisted mission-agent messages. They filter by member, mission and category. Timeline records distinguish agent messages from system events and link to members, task context and registered artifacts. The activity view refreshes persisted data every five seconds while visible. It does not fabricate typing or presence. The SQLite message bus is durable storage, not a background queue or cross-process agent runtime.

API endpoints:

```text
GET  /api/agents
POST /api/agents/{agent_id}/conversations
GET  /api/agents/{agent_id}/conversations
GET  /api/agents/{agent_id}/conversations/{conversation_id}
POST /api/agents/{agent_id}/conversations/{conversation_id}/messages
GET  /api/team-conversations?mission_id={id}&member_id={agent_id}&kind={filter}
```

## Run tests

```powershell
py -3.13 -m unittest discover -s tests -v
py -3.13 -m compileall -q akatsuki run.py tests
git diff --check
```

The suite exercises process-restart persistence for missions and member chats, SQLite migration and legacy message recovery, server-enforced modes, task/mission transitions and dependencies, typed/idempotent agent messages, member routing, private/team conversation separation, provider absence, feed filters, bounded retries, independent verification, prompt-as-data handling, input limits and artifact containment. `node --check static/app.js` checks the browser script syntax; Node is not required to run Akatsuki.

## Try the built-in unit converter

Create a mission titled **Build a unit converter**, describe Celsius/Fahrenheit and kilometers/miles conversions with input validation, and select **Full Power**. The generated artifact directory contains `unit_converter.py` and `test_unit_converter.py`. To reproduce the deliverable checks, change to that mission’s directory under `data/artifacts/` and run:

```powershell
py -3.13 -m unittest -v test_unit_converter.py
py -3.13 unit_converter.py temperature 0 C F
py -3.13 unit_converter.py distance 1 km mi
```

Full Power runs Akatsuki’s fixed verifier against the generated module; it does not run an arbitrary command or trust the downloadable test file as proof. Safe creates artifacts without starting Python subprocesses. Balanced compiles the generated source but does not run the behavior contract. For a supported unit-converter mission, only a passing Full Power contract is marked Completed. Safe remains Draft, Balanced remains Partial, and unsupported missions remain Partial in every mode.

## Prototype boundary

This is a local prototype, **not a public or production-ready service**. It has no accounts, organization authorization, multi-user tenant isolation, provider gateway, live research, general coding agent, browser or shell tools, usage metering, user budgets, approvals, durable async queue, cancellation, mission resume, or production worker sandbox. Mission execution is synchronous. Six profiles are not connected to task routines. One repair attempt restores the deterministic artifact template; it is not AI diagnosis or general-purpose code repair. The timed Python subprocess is not an OS security sandbox.

See [implementation status](docs/roadmap/implementation-status.md), [security threat model](docs/security/threat-model.md), [source and license review](docs/research/source-and-license-review.md), and [deployment runbook](docs/operations/deployment-runbook.md) before planning a hosted service.
# Akatsuki
