---
name: hypotize-weekly-review
description: Run a weekly growth experiment review on Hypotize. Use when the user asks for a pipeline review, weekly growth update, experiment status summary, stand-up prep, or "where are we on our tests". Produces a stage-by-stage health check with stale tests, missing outcomes, and what to decide next, from the live record.
---

# Hypotize: weekly experiment review

A review reads the record and tells the team what needs a decision. It never moves
work or closes tests on its own: flag, propose, and let a person decide.

## Gather (read-only)

1. `hypotize_projects_list`, then `hypotize_project_get` for the project under review
   (confirm which project if the user has more than one).
2. `hypotize_experiments_list` with `statuses` filters, one call per group:
   - `["Idea"]`: intake waiting for scoring or approval
   - `["Approved", "Ready"]`: committed but not started
   - `["Running"]`: live now
   - `["Analyzing"]`: finished, waiting for a verdict
3. `hypotize_learnings_list`: what closed since the last review (compare `updated_after`
   with the review cadence, typically 7 days).

## Diagnose

Work through the groups in this order; the later stages are where value is stuck.

**Analyzing: the verdict queue.** Every experiment here is a finished test nobody has
closed. This is the most expensive queue in the pipeline: the traffic is spent and the
learning is not yet written. For each one, ask for the measured result and offer to
close it properly with `hypotize_experiment_record_outcome`.

**Running: staleness check.** Compare `start_date` and `due_date` with today. A test
running past its due date needs one of: extend (with a reason), or move to Analyzing.
A Running test with no due date gets one proposed.

**Approved and Ready: commitment check.** Committed but unstarted work older than two
weeks is a broken promise or a wrong priority. Ask: start it, or archive it honestly.

**Idea: intake pressure.** Count unscored ideas. If the backlog grows faster than tests
close, say so with numbers: proposing more ideas is not the constraint worth working on.

## Report format

Keep it decision-oriented, in this order:

1. **Closed this week**: result + one-line learning each, from the learnings list.
2. **Needs a verdict** (Analyzing): experiment, days waiting, what number is missing.
3. **At risk** (Running overdue, Ready gone stale): what to decide for each.
4. **Pipeline counts** per stage, with the same numbers from last review if known.
5. **Suggested next actions**: max 3, each mapped to a concrete tool call you can make
   on approval ("say go and I close X as Lose with the April dashboard as evidence").

## Rules

- Numbers come from the record, never from memory of a previous conversation.
- Do not transition or close anything during the review without explicit approval.
- If a test cannot be closed because there is no measured result, that is the finding
  to report, not a gap to paper over.
