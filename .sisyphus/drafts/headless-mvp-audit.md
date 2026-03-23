# Draft: Headless MVP Architecture Audit

## Requirements (confirmed)
- User request: assess what is missing for MVP in `packages/headless`.
- User request: identify architectural problems.
- User request: check whether components correctly implement concepts from `packages/headless/specs/ADR-001-headless-architecture.md`.
- MVP definition confirmed: **minimum for public package release** (not just monorepo-internal baseline).

## Technical Decisions
- Audit scope starts from ADR-001 as the source of truth.
- Compare stated architecture concepts to current implementation of package components.
- Output should include MVP gaps, architecture risks, and ADR compliance status.

## Research Findings
- Local evidence collected:
  - ADR-001 baseline confirms layered architecture and API shape expectation (`createX`, `state`, `selectors`, `actions`, `contracts/get*Props`).
  - Current package exports 40 component/layer entrypoints from `src/index.ts`, including MVP-next scaffolds (`popover`, `select`, `context-menu`, `command-palette`, `toast`, `progress`).
  - Tests exist broadly (45 test files), including core/interactions/component coverage and adapter integration tests.
  - Boundary script exists and forbids monorepo/internal imports and boundary escapes.
  - Potential drift found: codebase does not expose explicit `selectors` object in public models (computed values live inside `state`), which may diverge from ADR API terminology guidance.
  - Potential packaging risk: `package.json` exports point to TypeScript source (`./src/index.ts`) rather than built dist artifacts, which can weaken runtime portability across consumer toolchains.
- Potential independence risk in tooling: lint/format scripts depend on monorepo-level config paths (`../../.oxlintrc.json`, `../../.prettierrc.cjs`).
- Documentation-state drift detected:
  - `specs/IMPLEMENTATION-ROADMAP.md` still frames `select/popover/context-menu/command-palette/toast/progress` as Wave 6 MVP-next productization work.
  - Actual source and tests indicate these components are already implemented and exported.
  - This drift can confuse MVP scope decisions and release readiness signaling.
- Release docs exist and are relatively complete (`RELEASE-CANDIDATE.md`, migration/changelog docs), reducing process risk.

- Explore agent synthesis (ADR mapping):
  - Strong alignment with layered model (`core`, `interactions`, `a11y-contracts`, `adapters`) and `createX + state + actions + contracts` shape.
  - Flagged potential terminology mismatch: ADR-001 describes explicit `selectors`, while implementation exposes computed values inside `state`.
  - Flagged governance concern: ADR-003 is still `Draft`, while release-governance automation already enforces ADR-003 references.

- Explore agent synthesis (MVP readiness):
  - No obvious `TODO/FIXME` stubs found in source.
  - Core MVP-next components (`select`, `popover`, `context-menu`, `command-palette`, `toast`, `progress`) appear implemented with tests.
  - High-priority packaging gap suggested: exports currently point to `src/index.ts` (TypeScript source), not dist artifacts.
  - Potential docs drift: README/roadmap wording can be read as those components not finalized despite implementation.
  - Agent reported additional quality issues (failing meter test / formatting warnings), but these are currently unverified in this planning session and should be treated as execution-time validation checks.

## Candidate Gap Priorities (working)
- **P0 candidate**: clarify canonical MVP boundary and stable public surface (which exports are truly in-scope for first release).
- **P0 candidate**: resolve packaging model for independent consumption (source exports vs compiled dist exports).
- **P1 candidate**: align ADR/API terminology (`selectors` explicitness) or formally document acceptable equivalent.
- **P1 candidate**: synchronize docs/status (README + roadmap + ADR statuses) to current implementation reality.
- **P2 candidate**: deepen adapter-level integration guidance/examples for consumers.

## External Benchmark Notes
- MVP architecture benchmark (from librarian): prioritize API stability, migration safety, explicit dependency direction, side-effect isolation, compatibility tests, and ADR traceability.
- Suggested scoring model: 0/1/2 per criterion with no-zero rule for API stability and migration safety.

## Open Questions
- What MVP definition should be used for prioritization (strict minimal release vs production-ready baseline)?
- Which components are considered critical for first release acceptance?
- Should ADR-conformance assessment treat missing explicit `selectors` field as a hard violation or acceptable equivalent via computed state signals?
- Should MVP export only stable components from root API, or keep MVP-next scaffolds exported behind pre-v1 flexibility?

## Scope Boundaries
- INCLUDE: architecture-level review, component-concept alignment, MVP readiness gaps.
- EXCLUDE: direct implementation changes (planning/review only).

## Latest User Direction
- User explicitly asked to run a deeper requirement interview to maximize plan quality.

## Confirmed Release Decisions
- Release scope: all 30 components are considered stable for first public release.
- Build/distribution: `dist` with ESM + `.d.ts`.
- Version target: `0.1.0` public.
- Compatibility SLA: Node `20+`, TypeScript `5+`.
- ADR selectors mismatch handling: update ADR wording (accept computed-in-state as selectors-equivalent model).
- Mandatory publish gates: `lint + test + boundaries + npm pack smoke`.
- Test strategy for release work: tests-after + smoke verification.
- Docs minimum for MVP: `README + Quickstart + Migration`.

## Remaining Ambiguity
- Deliverable mode to user: final audit report only vs remediation work plan generation in `.sisyphus/plans/`.
