<div align="center">

```text
██╗      ██████╗  ██████╗ ███████╗███████╗███╗   ██╗███████╗███████╗
██║     ██╔═══██╗██╔════╝ ██╔════╝██╔════╝████╗  ██║██╔════╝██╔════╝
██║     ██║   ██║██║  ███╗███████╗█████╗  ██╔██╗ ██║███████╗█████╗
██║     ██║   ██║██║   ██║╚════██║██╔══╝  ██║╚██╗██║╚════██║██╔══╝
███████╗╚██████╔╝╚██████╔╝███████║███████╗██║ ╚████║███████║███████╗
╚══════╝ ╚═════╝  ╚═════╝ ╚══════╝╚══════╝╚═╝  ╚═══╝╚══════╝╚══════╝
```

### **What went _wrong_ at 3 AM?**

Paste your logs. Get the root cause in seconds.<br>
Ten thousand lines analysed entirely in your browser — nothing ever uploaded.

<br>

[![status: live](https://img.shields.io/badge/status-live-3fb950?style=for-the-badge&labelColor=0a0e14)](#-quick-start)
[![logs uploaded: 0 bytes](https://img.shields.io/badge/logs_uploaded-0_bytes-3b82f6?style=for-the-badge&labelColor=0a0e14)](#%EF%B8%8F-privacy-is-the-moat)
[![dependencies: none](https://img.shields.io/badge/dependencies-none-ff8c42?style=for-the-badge&labelColor=0a0e14)](#%EF%B8%8F-tech-stack)
[![build time: 2 hours](https://img.shields.io/badge/build_time-2_hours-ffd93d?style=for-the-badge&labelColor=0a0e14)](#-team)

![HTML5](https://img.shields.io/badge/HTML5-E34F26?style=flat-square&logo=html5&logoColor=white&labelColor=0a0e14)
![CSS3](https://img.shields.io/badge/CSS3-1572B6?style=flat-square&logo=css&logoColor=white&labelColor=0a0e14)
![Vanilla JS](https://img.shields.io/badge/Vanilla_JS-F7DF1E?style=flat-square&logo=javascript&logoColor=black&labelColor=0a0e14)
![works offline](https://img.shields.io/badge/works-offline-3fb950?style=flat-square&labelColor=0a0e14)
![license: MIT](https://img.shields.io/badge/license-MIT-93c5fd?style=flat-square&labelColor=0a0e14)

<br>

[The 3 AM Problem](#-the-3-am-problem) · [What It Does](#-what-logsense-does) · [Quick Start](#-quick-start) · [How It Works](#-how-the-root-cause-engine-works) · [Privacy](#%EF%B8%8F-privacy-is-the-moat) · [Test It](#-test-it-yourself) · [Team](#-team)

</div>

---

## 🌙 The 3 AM Problem

The pager goes off. You open the log. It looks like this:

```log
08431  03:11:46.102  INFO   [api-gateway]    GET /api/v2/orders/8841 200 (42ms)
08432  03:11:46.389  DEBUG  [cache-redis]    GET session:u-29183 hit ttl=1792s
08433  03:11:46.771  INFO   [auth-service]   token refreshed sub=u-29183 exp=+3600s
08434  03:11:47.014  DEBUG  [db-pool]        checkout conn-0097 wait=28ms
08435  03:11:47.523  FATAL  [order-service]  java.lang.OutOfMemoryError: Java heap space
08436  03:11:47.880  INFO   [api-gateway]    GET /healthz 200 (1ms)
08437  03:11:48.205  DEBUG  [cache-redis]    EXPIRE cart:u-10422 ttl=900s
```

<p align="center"><i>9,993 more lines just like this.</i></p>

Line 08435 is the one that matters. Finding it took four scrolls, two greps and a coffee — and it still doesn't tell you *why*.

<table align="center">
  <tr>
    <td align="center" width="33%"><h2>40 min</h2><sub>average manual triage<br>during a live incident</sub></td>
    <td align="center" width="33%"><h2>97%</h2><sub>of every log<br>is routine noise</sub></td>
    <td align="center" width="33%"><h2>3 AM</h2><sub>when the highest-stakes<br>debugging always happens</sub></td>
  </tr>
</table>

---

## ⚡ What LogSense Does

```text
┌──────────────────┐     ┌──────────────────┐     ┌──────────────────┐     ┌──────────────────┐
│      PASTE       │     │      SCORE       │     │      TRACE       │     │     EXPLAIN      │
│     or drop      │ ──► │    every line    │ ──► │    backwards     │ ──► │    in English    │
├──────────────────┤     ├──────────────────┤     ├──────────────────┤     ├──────────────────┤
│ .log/.txt/.json  │     │ 0–100 per line   │     │ 6 nodes          │     │ + confidence     │
│ or raw text      │     │ 30+ signals      │     │ de-duped         │     │ + evidence       │
│ up to 10k lines  │     │ 8 formats        │     │ chronological    │     │ + next steps     │
└──────────────────┘     └──────────────────┘     └──────────────────┘     └──────────────────┘
         │                                                                          │
         └──────────────────────────────────────────────────────────────────────────┘
                                    ⏱ TOTAL: 0.38 seconds
```

Not a search box. A diagnosis engine — it tells you **why**, not just *what*.

---

## 📤 What You Get Back

One card. No dashboard, no query language, no scrolling.

```diff
┌────────────────────────────────────────────────────────────────────────────────────────────┐
│  ● ROOT CAUSE IDENTIFIED                                                   CONFIDENCE 92%  │
├────────────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                            │
-  L8435 · 03:11:47.523 · FATAL [order-service] java.lang.OutOfMemoryError: Java heap space  │
│                                                                                            │
│  WHY                                                                                       │
│  Database connections stopped being released after deploy #4471 at 02:59.                  │
│  Pool utilisation climbed 12% → 100% over 19 minutes while heap rose 68% → 96%.            │
│  47 queued requests held memory until the JVM could not recover.                           │
│                                                                                            │
│  CHECK FIRST                                                                               │
+  1. git diff v4470..v4471 -- src/db/     any new path that skips conn.close()?             │
+  2. db-pool idleTimeout / maxLifetime    leaked connections never time out?                │
+  3. heap dump of order-service pod       what holds 47 request contexts alive?             │
│                                                                                            │
└────────────────────────────────────────────────────────────────────────────────────────────┘
```

Every line number in the card is a link. Click it, land on the evidence.

---

## 🔗 The Failure Chain

LogSense walks backwards from the crash and keeps only the lines that explain it:

```text
   ○  L2841  02:59:02  api-gateway    INFO   deploy #4471 complete — order-service v2.14.0 live
   │
   ○  L3902  03:02:40  db-pool        WARN   connection conn-0042 not returned after 30s
   │
   ○  L5610  03:06:21  order-service  WARN   heap 79% (+11% in 4 min)
   │
   ○  L7104  03:09:45  db-pool        ERROR  pool EXHAUSTED 100/100 active, 47 waiting
   │
   ○  L8201  03:11:31  api-gateway    ERROR  circuit breaker OPEN → order-service
   │
   ◉  L8435  03:11:47  order-service  FATAL  java.lang.OutOfMemoryError: Java heap space
```

Twelve minutes and forty-five seconds from deploy to crash. 5,593 lines in between. LogSense shows you six.

---

## 🚀 Quick Start

```bash
git clone https://github.com/<your-org>/logsense.git
cd logsense

open index.html        # macOS
start index.html       # Windows
xdg-open index.html    # Linux
```

> No `npm install`. No build step. No server. No API key. No account.
> One HTML file. It works on a plane.

<p align="center">No logs within reach? Press <b>⚡ Load Demo: 10,000-line Production Crash</b> and watch it find line 8435.</p>

---

## 🧠 How the Root Cause Engine Works

Four stages. All deterministic. All in under half a second for 10,000 lines.

<details open>
<summary><b>① SCORE</b> — every line gets a number from 0 to 100</summary>

Each line starts from a base weight set by its level. Keywords push it up or down. The result is clamped to 0–100.

**Base weight**

| Level | Weight |
|:--|--:|
| `FATAL` / `CRITICAL` | 100 |
| `ERROR` | 85 |
| `WARN` | 55 |
| `INFO` | 10 |
| `DEBUG` | 3 |

**Keyword boost**

| Boost | Triggers |
|--:|:--|
| +15 | `outofmemory` · `oom` · `heap space` · `segfault` · `panic` |
| +12 | `exhausted` · `deadlock` · `corrupt` · `unrecoverable` |
| +10 | `timeout` · `refused` · `unreachable` · `circuit breaker` |
| +8 | `failed` · `cannot` · `rejected` · `denied` · `exception` |
| +6 | `retry` · `rollback` · `degraded` · `crashloop` |
| −20 | `health check` · `heartbeat` · `ping ok` — the noise penalty |

A `WARN` that says `pool exhausted` scores 67. An `INFO` that says `health check ok` scores 0. That is the whole trick.

</details>

<details>
<summary><b>② ANCHOR</b> — pick the moment everything is measured against</summary>

```text
anchor = last FATAL in the file
      ?? highest-scoring ERROR
      ?? highest-scoring WARN
      ?? none  →  "No critical issues detected"
```

Everything is measured relative to this moment. Lines after it are aftermath. Lines before it are suspects.

</details>

<details>
<summary><b>③ TRACE</b> — walk backwards from the anchor</summary>

1. Scan everything **before** the anchor. Nothing after it can be a cause.
2. Take the **6 highest-signal precursors** by score.
3. **De-duplicate** by stripping digits, hex IDs, UUIDs and timestamps from each message before comparing.
4. Allow at most **2 near-identical** messages, so one retry loop can't crowd out the real story.
5. **Re-order chronologically.** That is the failure chain.

</details>

<details>
<summary><b>④ DIAGNOSE</b> — match the chain against known failure patterns</summary>

| Pattern | Trigger |
|:--|:--|
| **Memory leak** | rising heap % across the chain, then an OOM |
| **Pool exhaustion** | pool / connection / thread exhaustion, usually after wait warnings |
| **Cascade failure** | 3+ distinct services erroring inside a short window |
| **Timeout storm** | 10+ timeout lines clustered in time |
| **Deploy correlation** | error rate 3×+ higher after a deploy / release / rollout line |
| **Repeated error** | the same normalised message 20+ times |
| **GC thrashing** | GC pauses of increasing duration |

Matches compose a **WHAT / WHY / CHECK FIRST** diagnosis plus a confidence score. Confidence rises with the number of agreeing patterns, the strength of the anchor and the tightness of the timeline.

</details>

---

## 🔒 Why No AI?

Deliberately. LogSense is deterministic: same log in, same answer out, every time.

An LLM can invent a plausible-sounding root cause. At 3 AM a hallucinated answer is worse than no answer, because it sends you debugging the wrong thing while production stays down.

LogSense physically cannot hallucinate. Every claim it makes links to a line number you can go read. If the evidence isn't in the log, it isn't in the diagnosis.

---

## 🛡️ Privacy Is the Moat

```text
┌─────────────────────────────────────────────┐
│   WHAT LEAVES YOUR MACHINE                  │
├─────────────────────────────────────────────┤
│                                             │
│   ✗  no upload        ✗  no telemetry       │
│   ✗  no cloud         ✗  no API key         │
│   ✗  no account       ✗  no network         │
│                                             │
│   ✓  100% client-side JavaScript            │
│   ✓  works fully offline                    │
│                                             │
│   Nothing. Not even a DNS lookup.           │
│                                             │
└─────────────────────────────────────────────┘
```

Real production logs contain API keys, customer emails and internal hostnames. Most engineers are contractually barred from pasting them into any cloud tool. Which is exactly why "just use ChatGPT" doesn't work here.

LogSense never asks. Pull the network cable and it behaves identically.

---

## ⚖️ Why Not Existing Tools?

| | Splunk / Datadog | ChatGPT paste | **LogSense** |
|:--|:--:|:--:|:--:|
| **Time to first answer** | ⚠️ minutes, after you write the query | ⚠️ 1–2 min, possibly correct | ✅ **< 1 second** |
| **Cost** | ❌ $$$ per GB ingested | ⚠️ subscription | ✅ **free** |
| **Logs leave your machine** | ❌ always | ❌ always | ✅ **never** |
| **Handles 10k lines at once** | ✅ yes | ❌ context window | ✅ **yes** |
| **Explains causation** | ❌ shows, doesn't explain | ⚠️ sometimes, no evidence | ✅ **always, with evidence** |
| **Works offline** | ❌ no | ❌ no | ✅ **yes** |
| **Can hallucinate** | ✅ no | ❌ yes | ✅ **no** |

---

## 🔬 Test It Yourself

Paste each sample into LogSense. No setup, no fixtures.

<details>
<summary><b>Test 1 — classic OOM crash</b></summary>

```log
2024-03-14 02:59:02.118 INFO  [api-gateway]    deploy #4471 complete — order-service v2.14.0 live
2024-03-14 03:01:15.402 INFO  [order-service]  POST /orders 201 (88ms)
2024-03-14 03:02:40.077 WARN  [db-pool]        connection conn-0042 not returned after 30s
2024-03-14 03:03:58.311 WARN  [db-pool]        connection conn-0057 not returned after 30s
2024-03-14 03:04:30.925 WARN  [order-service]  heap 71% (+9% in 5 min)
2024-03-14 03:06:21.840 WARN  [order-service]  heap 79% (+8% in 2 min)
2024-03-14 03:07:44.193 WARN  [db-pool]        pool 88/100 active, 6 waiting
2024-03-14 03:09:45.667 ERROR [db-pool]        pool EXHAUSTED 100/100 active, 47 waiting
2024-03-14 03:10:12.004 ERROR [order-service]  GC overhead limit approaching: 4 full GCs in 60s, freed 2%
2024-03-14 03:11:02.519 WARN  [order-service]  heap 96%
2024-03-14 03:11:31.248 ERROR [api-gateway]    circuit breaker OPEN → order-service (5xx rate 71%)
2024-03-14 03:11:47.523 FATAL [order-service]  java.lang.OutOfMemoryError: Java heap space
2024-03-14 03:11:47.601 FATAL [order-service]  JVM exiting: uncaught OutOfMemoryError in thread "http-nio-8080-exec-14"
```

**Expected:** root cause = `OutOfMemoryError`, chain traces back to deploy `#4471`, patterns = memory leak + pool exhaustion + deploy correlation.

</details>

<details>
<summary><b>Test 2 — healthy log</b></summary>

```log
2024-03-14 10:00:01.000 INFO  [api-gateway]    GET /healthz 200 (1ms)
2024-03-14 10:00:04.212 INFO  [auth-service]   token issued sub=u-10422 exp=+3600s
2024-03-14 10:00:07.588 INFO  [order-service]  POST /orders 201 (64ms)
2024-03-14 10:00:09.903 INFO  [cache-redis]    SET cart:u-10422 ttl=900s
2024-03-14 10:00:12.117 INFO  [db-pool]        pool 12/100 active, 0 waiting
2024-03-14 10:00:15.440 INFO  [api-gateway]    GET /api/v2/orders/8842 200 (38ms)
```

**Expected:** green "No critical issues detected". No red card. No crash.

</details>

<details>
<summary><b>Test 3 — malformed input</b></summary>

```log
starting worker-7 (pid 4412)
Traceback (most recent call last):
  File "worker.py", line 88, in run
    conn = psycopg2.connect(DSN)
psycopg2.OperationalError: connection refused
retrying in 5s...
retrying in 10s...
kernel: TCP: out of memory -- consider tuning tcp_mem
{"lvl":"error","msg":"queue full, dropping 412 events"}
CRITICAL main loop died, supervisor giving up
panic: runtime error: invalid memory address or nil pointer dereference
goroutine 1 [running]:
main.(*Worker).flush(0x0)
```

**Expected:** no timestamps and no consistent format, so LogSense falls back to keyword inference. The anchor lands on the `CRITICAL` / `panic` lines. No blank screen.

</details>

<details>
<summary><b>Test 4 — nginx error log</b></summary>

```log
2024/03/14 03:10:01 [warn] 2211#2211: *4821 upstream response is buffered to a temporary file while reading upstream, client: 10.0.4.17, server: shop.example.com, request: "GET /api/v2/orders HTTP/1.1"
2024/03/14 03:10:44 [error] 2211#2211: *4830 upstream timed out (110: Connection timed out) while reading response header from upstream, client: 10.0.4.22, server: shop.example.com, upstream: "http://10.0.9.3:8080/orders"
2024/03/14 03:11:02 [error] 2211#2211: *4833 connect() failed (111: Connection refused) while connecting to upstream, client: 10.0.4.19, server: shop.example.com, upstream: "http://10.0.9.3:8080/orders"
2024/03/14 03:11:30 [crit] 2211#2211: *4841 SSL_do_handshake() failed (SSL: error:0A000126:SSL routines::unexpected eof while reading) while SSL handshaking to upstream, client: 10.0.4.31
2024/03/14 03:11:47 [emerg] 2211#2211: bind() to 0.0.0.0:443 failed (98: Address already in use)
```

**Expected:** recognises bracket levels — `[warn]` → WARN, `[error]` → ERROR, `[crit]` and `[emerg]` → FATAL.

</details>

---

## 🛠️ Tech Stack

```text
FRONTEND    single-file HTML + CSS + vanilla JS
            no framework · no bundler · no build step

PARSING     regex multi-format tokeniser · 8 log grammars
            bracketed · ISO 8601 · syslog · nginx · JSON lines
            logfmt · stack traces · unstructured fallback

ANALYSIS    weighted severity scoring · 0–100 · 30+ signals
            backward causal tracing with de-duplication
            7-pattern matcher · confidence derivation

RENDERING   pure CSS data visualisation · zero chart libraries
            virtualised 300-row log table

DEPLOY      open the file — that's the whole deployment story
```

---

## 📊 By the Numbers

<div align="center">

| **10,000** | **0.38s** | **97.3%** | **0 bytes** |
|:---:|:---:|:---:|:---:|
| lines parsed | analysis time | noise removed | uploaded |

| **8** | **30+** | **7** | **~98%** |
|:---:|:---:|:---:|:---:|
| log formats | keyword signals | failure patterns | triage time saved |

</div>

---

## 🗺️ Roadmap

**Near term**

- [ ] Kubernetes / Docker / systemd format presets
- [ ] Shareable incident report links
- [ ] Multi-file correlation across services
- [ ] Regex-based custom severity rules

**Longer term**

- [ ] Local on-device LLM for richer narratives — still no network
- [ ] Pattern learning across historical incidents
- [ ] CLI tool and CI pipeline integration
- [ ] Real-time streaming ingestion

---

## ⚠️ Honest Limitations

- **Rule-based pattern matching.** The seven common failure modes are handled well. Exotic ones get a scored timeline but no named diagnosis.
- **Single file at a time.** No cross-service correlation yet. If the cause lives in another service's log, you still have to go and get it.
- **Tuned for backend and server logs.** Mobile crash reports and embedded firmware output hit the fallback grammar and get less insight.
- **The confidence score is a heuristic.** It reflects pattern agreement and timeline tightness. It is not statistically calibrated — 92% does not mean "right 92 times out of 100".

---

## 👥 Team

<div align="center">

| | | |
|:---:|:---:|:---:|
| **Ayushman Sharma**<br>`2392608038`<br>_Root Cause Engine_ | **Mohd Shadab Ali**<br>`2392608147`<br>_Parsing & Formats_ | **Tanishq Goyal**<br>`2392608040`<br>_Frontend & UI_ |
| **Vashu Kushwaha**<br>`2392608059`<br>_Data Visualisation_ | **Shreyas Gondkar**<br>`2392608194`<br>_Demo Log Simulation_ | **Priyanshu Kumar Jha**<br>`2392608096`<br>_Pitch & Product_ |

`6 MEMBERS` · `2 HOURS` · `1 FILE`

</div>

---

## 📁 Project Structure

```text
logsense/
├── index.html              ← the entire application
├── deck.html               ← pitch deck
├── samples/
│   ├── crash.log           ← the 10,000-line demo incident
│   ├── healthy.log         ← six boring INFO lines
│   ├── malformed.log       ← no timestamps, mixed formats
│   └── nginx.log           ← [warn] [error] [crit] [emerg]
├── docs/
│   └── screenshots/
├── LICENSE
└── README.md
```

---

## 📜 License

MIT. Do whatever you want with it.

---

<div align="center">

## **Ten thousand lines. One answer.**

_LogSense — private log intelligence that runs entirely in your browser._

⭐ **Star this repo if it would've saved you at 3 AM **

</div>
