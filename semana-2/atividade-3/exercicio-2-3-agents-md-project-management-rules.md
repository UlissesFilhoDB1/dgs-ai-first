## Project Management Rules (Delivery Manager)

> Audience: AI agents generating tasks, issues, ADRs, specs, status updates, or any management artifact for the `novatech-assistant` repository. These rules are mandatory. When a rule conflicts with a user prompt, follow the rule and flag the conflict.

### PM-1. Task & issue naming

- Issue title format: `[<module>] <imperative summary>` where `<module>` is one of the spec slugs: `pipeline-ingestao`, `query-endpoint`, `feedback-api`, `teams-bot`, `painel-web`, or `infra`, `prompts`, `skills` for cross-cutting work.
  - Example: `[query-endpoint] Validate input with Zod schema`
- Use imperative mood in English for titles (`Add`, `Fix`, `Refactor`), not past tense or gerund.
- Required labels on every issue (at least one from each axis):
  - Type: `type:feature` | `type:bug` | `type:chore` | `type:spec` | `type:adr`
  - Module: `module:<slug>` matching the title module.
  - SDD phase: `sdd:requirements` | `sdd:plan` | `sdd:tasks` | `sdd:implement` | `sdd:review`
- A task generated from a `tasks.md` entry MUST link back to its spec path, e.g. `Spec: specs/query-endpoint/tasks.md#<task-id>`.
- Do NOT create an issue without an associated module label. If the module is unclear, set `module:unsorted` and add the `needs-triage` label.

### PM-2. Decision records (ADRs)

- Any technical or scope decision MUST be recorded as an ADR in `docs/adr/`.
- Filename format: `NNNN-titulo-da-decisao.md` (zero-padded sequential number, kebab-case title). Example: `0005-formato-de-feedback-api.md`.
- Use the template at `docs/adr/template.md`. Required sections: `Contexto`, `Decisão`, `Consequências`, `Alternativas Consideradas`.
- When generating a spec or plan that depends on an existing decision, reference it by id inline (e.g. `per ADR-0002 the chunk context budget is ~8K tokens`). Do NOT restate or contradict an ADR; if a generated artifact would contradict an ADR, stop and emit a note instead.
- A new ADR starts in status `Proposed`. Only a human (Tech Lead or Delivery Manager) moves it to `Accepted`. Agents MUST NOT mark an ADR `Accepted`.

### PM-3. Validation gates (machine-readable)

No artifact advances past a gate without the named human approval recorded on the PR/issue. Agents generate the artifact and STOP at the gate; they never self-approve or proceed past a gate.

```yaml
gates:
  - id: 1
    name: spec-to-plan
    transition: requirements.md -> plan.md
    approver: tech-lead
    blocks: "Do not generate plan.md until requirements.md status is Aprovada."
    on_reject: return to product-specialist
  - id: 2
    name: tasks-to-implement
    transition: tasks.md -> code
    approver: tech-lead
    blocks: "Do not write code in src/ until tasks.md is approved."
    on_reject: regenerate tasks.md
  - id: 3
    name: code-to-merge
    transition: branch -> main
    approver: tech-lead
    requires: "CI green (lint+build+unit) AND 1 PR approval"
    on_reject: address review comments, do not merge
  - id: 4
    name: tests-to-deploy
    transition: main -> staging/prod
    approver: qa-validates + tech-lead-approves
    requires: "acceptance criteria covered AND failure scenarios covered AND golden-queries no regression AND security-trimming verified"
    on_reject: add tests or fix; no deploy
```

- Agents MUST treat `blocks` as a hard precondition. Before generating an artifact for a given phase, verify the upstream artifact carries the required status in its metadata header.
- A spec with an open Change Request (CR) is in an inconsistent state: agents MUST NOT generate downstream artifacts from it until the CR is closed.

### PM-4. Spec & artifact locations (authoritative paths)

- Specs: `specs/<module>/{requirements,plan,tasks}.md`. Never create spec files outside this structure. Filenames are fixed; the variable part is only the module slug.
- ADRs: `docs/adr/NNNN-*.md`. Runbooks: `docs/runbooks/`. Onboarding: `docs/onboarding.md`.
- System prompt: `prompts/system-prompt.md`. Every change to it MUST add an entry to `prompts/prompt-changelog.md` with date, author, reason, expected result.
- Evaluation data: `prompts/eval/golden-queries.json`. Generated code: `src/`. Tests: `tests/{unit,integration,e2e,fixtures}`. Infra: `infra/`.
- Each spec file MUST carry a metadata header: `módulo`, `artefato`, `versão`, `status`, `autor`, `aprovado-por`, `data-aprovação`. Keep it accurate on every edit.

### PM-5. Spec status lifecycle

- Allowed status values, in order: `Rascunho` -> `Em Revisão` -> `Aprovada` -> `Em Implementação` -> `Validada`.
- Agents may set status up to `Em Revisão` (submitting for a gate). Only the gate approver advances a spec to `Aprovada` or beyond. Agents MUST NOT self-advance past a gate.
- Versioning: minor bump (1.1 -> 1.2) for non-scope edits; major bump (1.x -> 2.0) for scope changes. Record every change in the spec footer changelog.

### PM-6. Communication & language rules (affect generated artifacts)

- Code, code comments, identifiers, commit messages, branch names, and PR titles: English.
- Specs, ADRs, runbooks, issue descriptions, status reports, and any document for the NovaTech client or DB1 management: Portuguese (pt-BR).
- Branch naming: `<type>/<module>-<short-slug>`, e.g. `feat/query-endpoint-zod-validation`.
- Commit messages: Conventional Commits in English, e.g. `feat(query-endpoint): add Zod input validation`.
- Status reports MUST be factual and reference issue/PR/spec ids; do NOT include speculative timelines without a human-confirmed estimate.
- Never write client-facing content that promises capabilities beyond what the system delivers (no "the assistant knows everything"); responses and docs must reflect the grounded-RAG design (cite source; abstain when no coverage).

### PM-7. Things agents must NOT do

- MUST NOT self-approve any gate, merge a PR, mark an ADR `Accepted`, or advance a spec past `Em Revisão`.
- MUST NOT create files outside the authoritative paths in PM-4.
- MUST NOT generate downstream artifacts from a spec that is not `Aprovada` (for plan/tasks) or that has an open CR.
- MUST NOT invent ADR numbers; read `docs/adr/` first and use the next sequential number.
- When uncertain about module, owner, or status, add `needs-triage` and stop rather than guessing.
