# Engineering Journey — Conversational ERP Integration

## Scope and Evidence

This case study is based on a de-identified engineering handoff. The portfolio preparation did not rerun ERP tests or independently inspect the original system. The handoff documents 18 read-only Tool functions and a final fixed test suite reported as 17/17 passing; it also states that the test output was not saved as a separate artifact.

The useful engineering boundary is between model intent selection and deterministic integration. The LLM chooses a tool and business parameters. Python validates parameters, calls official SOAP Web Services, and combines results. ERP remains the source of operational data; use of its official API does not itself prove complete per-user authorization or parity with UI behavior.

封存紀錄可支持工程歷程與固定測試結果，但不等於本次重新驗證。公開文件省略具體查詢結構、內部欄位、身分對應規則與測試資料。

## 1. Bound Retrieval Before Expanding Context

**Problem:** A broad date-based query retrieved many unrelated orders, leaving the model to filter them and exhausting context.

**Decision:** Move customer or supplier filtering into the retrieval path. Validate date ranges and ambiguous matches in the Tool layer, and enforce result limits without relying on instructions to the model.

**Recorded checks:** The fixed tests cover a filtered annual query, an unfiltered bounded query reaching the limit, an excessive date range, and an ambiguous keyword requiring clarification.

**Outcome:** The Tool returns a clarification or a limit response rather than presenting a partial list and total as complete. Larger responses use reduced detail and deterministic summaries.

**Limit:** Limits are fixed POC controls; role-specific policies and scale validation remain open. No token-savings benchmark was retained.

## 2. Preserve Composite Keys Before Diagnosing Data Corruption

**Problem:** Detail lookups produced non-unique-row errors. The initial symptom could have been mistaken for duplicate source data.

**Investigation and change:** Check the selected key fields first. Include complete composite keys and use a configuration-driven lookup registry for application-layer merging.

**Outcome:** The handoff reports three occurrences resolved by restoring missing key fields, with related tests passing. The model receives an assembled result instead of orchestrating per-record lookups.

**Limit:** Some reference mappings remain incomplete. No internal key names or registry definitions are published.

## 3. Make Personal Pending-Approval Queries Testable

**Problem:** The required personal pending-approval view was not available as a ready-made integration query.

**Decision and checks:** Build a deterministic read-only view from authorized service data, validate cross-consistency between submitted and pending records, and minimize personal information in the returned fields. Unsupported document types remain explicitly unknown.

**Outcome:** The archived fixed tests include cross-consistency and privacy-field assertions. This is a query capability, not approval execution.

**Limit:** Platform identity handling and Tool-level filtering are not proof of complete ERP-enforced per-user access control. Formal deployment requires an authentication and authorization review. Implementation details that could expose internal structures or permission gaps are omitted.

## 4. Stop Write Experiments When API Semantics Are Unclear

**Problem:** Exploratory import work accepted a test draft, but the retrieved draft did not contain expected derived values. Approval-interface work also lacked sufficient vendor documentation.

**Decision:** Pause write-tool work rather than assume that an official import endpoint triggers every UI calculation. Stop approval exploration until supported interface definitions are available.

**Outcome:** No completed write workflow, preview-confirm-execute flow, or approval action is claimed. The observed import behavior applies to the tested path; it is not a universal conclusion about the ERP API.

**Limit:** Calculation triggers and approval semantics remain unresolved. No transaction payloads, probing inputs, or interface weaknesses are included here.

## Validation and Maturity

| Evidence | What it supports | What it does not establish |
|---|---|---|
| Archived fixed suite: 17/17 | Recorded read-only checks for validation, aggregation, keys, tracking, bounded results, and privacy fields | Public reproducibility, general accuracy, or independent coverage of all 18 functions |
| Tool deployment read-back comparison | A recorded check that deployed Tool content matched the intended update | Complete conversation-level acceptance or Production security |
| Owner-reported UI checks | Practical feedback used during iteration | Retained end-to-end test artifacts |
| Warehouse availability integration | Interface reached in the test environment | Data correctness where test data was unavailable |

Exact ERP versions and a complete independently reproducible environment are not supplied by the handoff. Credential lifecycle management, per-user authorization, write confirmation, approval execution, and formal deployment acceptance remain unverified or incomplete.

## Roles and Public Scope

The Project Owner defined needs, architecture direction, solution choices, and acceptance decisions. Claude Code wrote the recorded Tool implementation and tests and performed integration debugging and deployment work. Codex reorganized the de-identified evidence into this portfolio documentation.

This is a documentation case study. Original code, data, credentials, private evidence reports, and internal integration definitions are excluded. No third-party publication rights or open-source license are asserted. See the [project overview](../README.md).
