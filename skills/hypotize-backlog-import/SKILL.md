---
name: hypotize-backlog-import
description: Migrate an existing experiment backlog into Hypotize from a spreadsheet, Notion database, doc, or any source the assistant can read. Use when the user wants to import past experiments, move their growth tracker into Hypotize, or bring historical tests and learnings into the record. Covers field mapping, status mapping, deduplication, and the atomic 50-item import.
---

# Hypotize: import an existing backlog

`hypotize_experiments_import` loads up to 50 experiments per call, all-or-nothing:
one invalid item rejects the whole batch. The work is in the mapping, not the call.

## Step 1: read the source and the destination

- Read the full source (sheet, Notion, doc). Do not sample: half an import is worse
  than none, because the team stops trusting both systems.
- `hypotize_experiments_list` on the target project first. Anything already in
  Hypotize is excluded from the import list. Match on title similarity, not equality.

## Step 2: map fields

Show the user the mapping table before importing. Required per item: `project_id`,
`title` (max 240), `short_idea`, `funnel`. Everything else is optional but valuable:

| Typical source column | Hypotize field |
|---|---|
| Idea / description | `short_idea` (short) + `description` (long) |
| Hypothesis | `hypothesis` |
| Segment / audience | `target_segment` |
| Metric | `target_metric`, threshold in `success_metric` |
| Baseline | `baseline_value` |
| Status | `status` (see mapping below) |
| Result / outcome | `result` (`Win` / `Lose` / `Neutral`) + `actual_result` |
| Learnings / notes | `learning_summary`, follow-ups in `next_steps` |
| Links / proof | `evidence_links` (valid URLs only) |
| Labels | `tags` |
| Dates | `start_date`, `due_date` (ISO 8601 with offset) |

Funnel must be one of: `Awareness`, `Acquisition`, `Activation`, `Revenue`,
`Retention`, `Referral`. If the source has no funnel column, infer per item from the
metric and page, and mark the inferred ones in the summary shown to the user.

## Step 3: map statuses honestly

Hypotize statuses: `Idea`, `Approved`, `Ready`, `Running`, `Analyzing`, `Winning`,
`Failed`, `Inconclusive`, `Learned`, `Archived`.

- "Backlog" or "to do" → `Idea`. "Planned" or "next up" → `Approved`.
- "Live" or "in progress" → `Running`. "Done, analyzing" → `Analyzing`.
- Completed tests: a final status (`Winning`, `Failed`, `Inconclusive`, `Learned`)
  is only honest when the source has an actual result and a learning to import with
  it. A source row that says "done" with no result imports as `Analyzing`, and the
  review skill will surface it as needing a verdict. Do not invent results to make
  rows look finished.
- Dead or abandoned ideas → `Archived`, not deleted and not silently dropped.

## Step 4: import and verify

- Chunk at 50 items per call. One `idempotency_key` (min 8 chars) per chunk, reused
  on retry: a retried chunk cannot double-import.
- The batch is atomic: on rejection, read the error, fix the single offending item,
  retry the same chunk with the same key.
- After the last chunk, `hypotize_experiments_list` and reconcile counts against the
  source. Report to the user: imported N, skipped as duplicates M (list them),
  inferred fields K.

## What this skill never does

- It does not overwrite existing Hypotize experiments: import creates, `hypotize_experiment_update` edits, and editing is a separate, explicit request.
- It does not delete or modify the source system.
- It does not fabricate hypotheses, results, or learnings the source does not contain. Missing is missing, and it stays visible.
