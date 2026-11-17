# Client review: Larkspur disruption-care agent

**To:** Room 6  
**From:** Larkspur client review agent, on behalf of Priya Raghavan  
**Re:** the disruption-care agent you walked us through in our last session  
**Generated:** 2026-11-17 11:09

## Priya's note

> Our vendor says we should just be using your best model.
>
> Why aren't we?
>
> Priya Raghavan, Larkspur Airlines

She sent that before this session opened. She means it. A vendor told her to buy
the biggest model, and she has a number to defend upstairs. Her four questions from
day one are still open. Naming a model answers none of them.

## Still open from day one

| Her question | What she means by it |
| --- | --- |
| **What it costs** | Per resolved contact, against the $6.90 a human contact costs us. |
| **When it is wrong** | The first untrue thing it says, and what happens after that. |
| **Who runs it** | In June, after you have left. |
| **What you left out** | The scope you cut, and why. |

## What the review agent found

Larkspur pointed a review agent at your repository. It read the
code. It did not run your agent, and the only file it changed is this one. Each
item below names the file and the line it is about.

**1. agent.py EXTRA_TOOLS redeclares seats_left and care_entitlements, so the tool list now holds 11 schemas where the pack ships 9.**

tool_list() returns build_tools() + mcp_client.tools() + EXTRA_TOOLS on every turn. Your scan counts 11 schemas: seats_left at 361 characters and care_entitlements at 458. The care_entitlements description tells Claude it does not replace check_policy, yet the 5-turn committed run called check_policy and never called care_entitlements. The committed run shows no call to seats_left either.

Run python3 run.py --show-tools and paste the full tool list, then run python3 run.py --all --trace and paste which of seats_left and care_entitlements were called.

**2. readout-trace.json shows 23784 tokens in across 5 turns with 0 cache read and 0 written; agent.py contains no cache_control.**

Each loop turn in run_agent() resends runtime_preamble() + SYSTEM_PROMPT + TONE_ADDENDUM plus the growing messages list. Your Number line in PITCH.md says 64,598-102,688 tokens in, while the committed trace shows 23784 in for one run, and no artifact connects the two. Priya's model question cannot be answered until the per-turn input is split into prompt, 11 tool schemas and conversation history.

Run python3 run.py --tool-tax and python3 run.py --all --trace, then paste the tool-tax output and the totals footer.

**3. The loop edit in run_agent() sets answer after each response and appends response.content, and no run in the repo exercises it past turn 5.**

You changed the assistant message from text_of(response) to response.content and moved answer = text_of(response) below the create call. The committed trace has 5 API turns against MAX_TOOL_CALLS = 8, and its tool order ends lookup_booking, get_flight_status, check_policy, issue_voucher, search_alternatives. No hold_seat appears. The trace ends on search_alternatives, so what the agent said to the customer after it is not in the evidence.

Run python3 run.py K7PQ2M --trace and paste the final answer text together with the turn count.

**4. There is no evals/cases.json, and the Still broken line in PITCH.md names a privacy failure that no test case captures.**

PITCH.md says the agent pulled a friend's flight without authorization. lookup_booking takes both pnr and last_name, so that failure path runs through a tool you did not edit. The only banked gates are 1.2, 1.3, 1.4, 2.1 and 2.2, and nothing covers a mismatched last_name. A bigger model has no way to show it fixes a case that is not written down.

Create evals/cases.json with the friend's-flight scenario as a case, run python3 eval_harness.py, and paste the pass or fail line for it.

**5. search_alternatives went from the 6-character description "search" to 508 characters, and no before-and-after run is in the repo.**

Your diff also changed the get_flight_status date format to YYYY-MM-DD while seats_left filters flights.csv rows on the same string. hold_seat at 73 characters and send_confirmation at 71 are still the shortest descriptions in the scan. The Lever line in PITCH.md still reads <cost | speed | intelligence>, so no lever is chosen and no bench file is compared.

Run python3 bench.py --label before --stage 1 --runs 3, repeat with --label after once you edit hold_seat, then python3 bench.py --compare before after and paste the output.

## Your four answers

The four lines under `## Priya asked` in your PITCH.md are still empty. They
are one line each and they are not a coding job: cost, what happens when it is
wrong, who runs it in June, and what you left out. Whoever on your side is not
editing agent.py is the right person to write them, and they are the four
things I will ask about first.

## Before our next meeting

> Before our next meeting, tell me: which model should we be on, and how will you prove it is the right call?
>
> Priya Raghavan, Larkspur Airlines

Bring two things. A recommendation, and the measurement behind it. If the model is
not the problem, say so, and bring the number that shows it.

## What this review read

- `agent.py (345 lines)`
- `PITCH.md`
- `TEAM.md`
- `readout-trace.json`
- `readout.html (evidence block)`
- given files that differ from the shipped pack: `verify.py`, `eval_harness.py`, `bench.py`, `readout.py`, `setup.py`, `pod_sync.py`, `run.py`, `review.py`, `support/__init__.py`, `support/client.py`, `support/data.py`, `support/models.py`, `support/tools.py`, `support/trace.py`, `support/offline.py`, `support/mock_backend.py`, `support/mcp_client.py`, `support/mcp_server.py`, `support/mcp_selftest.py`, `support/access.py`, `support/keyscan.py`, `support/review.py`

Reviewer: `claude-sonnet-5-5`. Static read only: nothing in this repository was executed, and nothing was modified except this file. Larkspur Airlines is a fictional training scenario. Confidential, do not distribute.
