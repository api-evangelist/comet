---
name: Log and score LLM traces in Opik
description: Create a project, ingest LLM traces and spans in batch, then attach feedback scores for evaluation.
api: openapi/comet-opik-openapi-original.yml
operations:
- createProject
- createTraces
- createSpans
- addTraceFeedbackScore
- getTracesByProject
---

# Log and score LLM traces in Opik

Use this skill to instrument an LLM application with Opik observability and record evaluation scores.

## Auth
- Opik Cloud: send `authorization: <API_KEY>` (no `Bearer ` prefix) and `Comet-Workspace: <workspace>`.
- Self-hosted: no auth headers; base URL `http://localhost:5173/api/v1`.
- Cloud base URL: `https://www.comet.com/opik/api/v1`.

## Steps
1. **Create (or reuse) a project** — `POST /v1/private/projects` (`createProject`). Projects group traces.
2. **Ingest traces in batch** — `POST /v1/private/traces/batch` (`createTraces`). Supply client-generated UUIDv7 ids so re-sends upsert rather than duplicate. Reference the project by `project_name`.
3. **Ingest spans in batch** — `POST /v1/private/spans/batch` (`createSpans`). Each span carries `trace_id` and an optional `parent_span_id` to build the call tree.
4. **Attach feedback scores** — `PUT /v1/private/traces/{id}/feedback-scores` (`addTraceFeedbackScore`) to record LLM-as-a-judge or heuristic scores on a trace.
5. **Read back** — `GET /v1/private/projects/{projectId}` list/query traces (`getTracesByProject`) with `page`/`size` pagination and the `filters` JSON predicate string.

## Conventions & errors
- Pagination: `page` + `size`; responses carry `content`, `page`, `size`, `total`.
- Errors: custom `ErrorMessage` envelope `{code, message, details}`; write endpoints return `{errors:[...]}`. Handle 401 (bad key/workspace), 409 (conflict), 422 (validation), 429 (throttle → back off).
- See `conventions/comet-conventions.yml` and `errors/comet-error-codes.yml`.
