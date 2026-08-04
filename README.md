# SauceDemo Login/Cart Assurance Demo (kane-cli)

A minimal, end-to-end walkthrough of kane-cli's **assurance lifecycle**: starting from a plain-language feature spec, deriving cited use-cases, generating acceptance-criteria-backed tests, and running them for real against [SauceDemo](https://www.saucedemo.com) — with every test permanently linked back to the requirement it verifies.

Unlike `kane-cli generate` (quick, one-off test cases from a prompt), this repo demonstrates the full assurance path: sources are ingested and versioned, use-cases are extracted with cited evidence, every proposal is human-reviewed before being trusted, and each test carries a traceable link to the acceptance criterion it proves.

## What's in here

| File / folder | Purpose |
|---|---|
| `sources/feature-spec.md` | The plain-language requirement doc: 4 requirements covering SauceDemo login (valid + locked-out) and cart behavior (add + remove). This is the single source of truth the whole lifecycle derives from. |
| `.context/` *(gitignored)* | kane-cli's local, content-addressed store — snapshotted sources, extracted use-cases, and review verdicts. Append-only; rebuilt locally via `kane-cli context ingest` rather than committed. |
| `.testmuai/` *(gitignored)* | kane-cli's internal working directory for authored/run tests. |
| `tests/*_test.md` | The two designed tests for UC-1 (login), each tagged with the acceptance criterion (`ac-1`) it verifies. |
| `.gitignore` | Excludes the local kane-cli state (`.context/`, `.testmuai/`) from version control. |

## The lifecycle, stage by stage

The loop

```
  requirement docs                          product changes
        │                                          │
        ▼                                          ▼
  context ingest ──► context extract ──► context review ──► design tests
  (snapshot the      (agent proposes       (promote to       (ACs, scenarios,
   sources)           use-cases, cites      trusted)          one test per
                      every claim)                            scenario — written
                                                              as *_test.md files)
                                                                     │
                                                                     ▼
  maintain ◄── cover ◄── evidence pack ◄── testrun run ◄── testmd run ◄── context review
  (reconcile   (proven     (sealed proof)   (batch replay)   (author each    (approve the
   a changed    vs owed)                                      test once)      design output)
   source)
```



| Stage | Command | What it does | Status in this repo |
|---|---|---|---|
| Capture | `kane-cli context ingest sources/feature-spec.md` | Snapshots the feature spec into the local, content-addressed `.context/` store | ✅ done |
| Extract | `kane-cli context extract` | KaneAI agent reads the spec and proposes use-cases, citing the exact lines it read | ✅ done — UC-1 (login), UC-2 (add to cart), UC-3 (remove from cart) |
| Review | `kane-cli context review` | Promote proposals from `derived` to `trusted` (or edit/reject) | ✅ done — all 3 use-cases promoted |
| Design | `kane-cli design tests --use-case UC-1` | Turns one use-case into acceptance criteria, scenarios, and runnable tests — each test tagged with the criteria it verifies | ✅ done for UC-1 → 2 tests. ⏳ not yet run for UC-2 / UC-3 |
| Review the design | `kane-cli context review` | Design output is derived too — approve, edit, or reject the generated ACs, scenarios, and tests | ✅ done for UC-1's output |
| Execute | `kane-cli testmd run <test>.md` | Authors and executes each test for real (launches Chrome against saucedemo.com) | ✅ done — both UC-1 tests run |
| Execute (replay) | `kane-cli testrun run` | Batch-replays authored tests; every run seals an evidence pack | ⏳ next |
| Measure | `kane-cli cover` | Reports two axes: what the evidence pack proved vs. what the design still owes | ⏳ pending |
| Maintain | `kane-cli maintain` | Reconciles the suite if `sources/feature-spec.md` changes | not yet exercised — no source changes made in this demo |

## Vocabulary, as it applies here

| Term | In this repo |
|---|---|
| **Source** | `sources/feature-spec.md` — content-addressed on ingest; editing and re-ingesting would create a new version |
| **Use-case** | UC-1 (login), UC-2 (add to cart), UC-3 (remove from cart) — each extracted with cited lines from the spec |
| **Acceptance criterion (AC)** | `ac-1`: submitting the valid `standard_user` / `secret_sauce` pair takes the user from the login page to the products page. `ac-2` / `ac-3`: submitting `locked_out_user` / `secret_sauce` displays a locked-account error message and does **not** reach the products page — two ACs verified by one test |
| **Scenario** | One path through a use-case — UC-1 has a happy-path scenario (standard user) and a negative scenario (locked-out user) |
| **Test** | Exactly one `*_test.md` file per scenario — `standard-user-reaches-the-products-page-after-login_test.md` (`t-2`, verifies `ac-1`) and `locked-out-user-stays-blocked-from-the-products-page_test.md` (`t-1`, verifies `ac-2`/`ac-3`) |
| **derived / trusted / archived** | All 3 use-cases and UC-1's design output started `derived` and were promoted to `trusted` via review — nothing here was silently trusted |
| **fresh / stale / orphaned** | Everything in this repo is `fresh` — `sources/feature-spec.md` hasn't changed since ingest |
| **Gap** | UC-2 and UC-3 are extracted and trusted but not yet designed into tests — that's a recorded gap, not silence |
| **Evidence pack** | The sealed `.evidence` file produced by `testrun run`, viewable via the LambdaTest evidence viewer link printed at the end of each run |

## Requirements

- kane-cli **0.6.1+** (`kane-cli --version` — 0.6.0 fails on these commands after a fresh install)
- A KaneAI login (the `context extract`, `design tests`, and `maintain` steps call the KaneAI agent and consume credits; everything else — `list`, `view`, `review`, `explain`, `cover`, `fsck` — is local and free)
- Chrome available locally for `testmd run` / `testrun run`


## Step-by-step: running the assurance lifecycle

Run these in order from the repo root. Each step names the exact command, what it does, and where its output goes.

### 1. Capture the requirements into the local store

```bash
kane-cli context ingest sources/feature-spec.md
```

Snapshots `sources/feature-spec.md` into the local, content-addressed `.context/` store. This is the only step that touches the source file directly — every later step reads from the snapshot, not the file, so re-running this after editing the spec creates a new version.

### 2. Extract use-cases from the spec

```bash
kane-cli context extract
```

Calls the KaneAI agent, which reads the ingested spec and proposes use-cases, citing the exact lines it read from `sources/feature-spec.md`. Output lands in `.context/` with trust state `derived` (unreviewed).

### 3. Review and promote the use-cases

```bash
kane-cli context review
```

Opens an interactive review of everything currently `derived`. For each proposed use-case, promote it to `trusted`, edit it, or reject it (which moves it to `archived`). Nothing kane-cli extracts is trusted automatically — this step is what makes it trusted.

### 4. Design acceptance criteria, scenarios, and tests

```bash
kane-cli design tests
```

Turns each `trusted` use-case into acceptance criteria (ACs), scenarios (happy path, negative, etc.), and exactly one runnable test per scenario. Writes the `*_test.md` files into `tests/` (this is how the four files already in `tests/` were produced) and tags each one with the AC(s) it verifies.

### 5. Review the design output

```bash
kane-cli context review
```

Same command as step 3, but now reviewing the `derived` ACs, scenarios, and generated tests instead of use-cases. Approve, edit, or reject before trusting any of it.

### 6. Author and run each test individually

```bash
kane-cli testmd run tests/standard-user-reaches-the-products-page-after-login_test.md
kane-cli testmd run tests/locked-out-user-stays-blocked-from-the-products-page_test.md
kane-cli testmd run tests/add-one-product-from-the-products-page-and-see-the-cart_test.md
kane-cli testmd run tests/remove-one-product-from-a-two-item-cart-and-see-the-cart_test.md
```

For each `*_test.md` file, this authors the test for real and launches Chrome against saucedemo.com to execute it. Run this once per test the first time you author it.

### 7. Reconcile any out-of-band edits

```bash
kane-cli maintain
```

If a `*_test.md` file (or the source spec) was hand-edited outside this lifecycle, `testrun run` will flag it as out of sync. This command calls the KaneAI agent to reconcile the design graph with the edited file. Run it before `testrun run` if you've hand-edited anything under `tests/`.

### 8. Batch-replay everything and seal an evidence pack

```bash
kane-cli testrun run
```

Replays every authored test in one batch run and seals the results into a `.evidence` file — the sealed proof that later steps measure coverage from. Prints a LambdaTest evidence-viewer link at the end.

### 9. Measure coverage

```bash
kane-cli cover
```

Reads the sealed evidence pack and reports two things: what it proved (which ACs have a passing test backing them) and what the design still owes (ACs or scenarios without a confirmed passing run).

## Scenarios covered

- Standard user (`standard_user` / `secret_sauce`) reaches the products page after logging in.
- Locked-out user (`locked_out_user`) is blocked from the products page and shown the lockout message.
- *(designed, not yet authored into tests)* Adding an item to the cart updates the cart badge.
- *(designed, not yet authored into tests)* Removing an item from the cart updates the cart badge.

## Known issue

The **login-page confirmation** step in `standard-user-reaches-the-products-page-after-login_test.md` originally duplicated the same "on the login page" check across two steps with slightly different wording, which caused an intermittent, self-flagged false failure (`status_disagrees` advisory in the evidence pack — the tool's own triage correctly identified the check as overly strict, not the app as broken). The redundant clause was removed from Step 1 of the standard-user test so Step 2 is the sole assertion of that state. The locked-out-user test (`t-1`) currently has the same "confirm the browser is on the SauceDemo login page before any credentials are submitted" phrasing in its Step 1 — it hasn't triggered the same false failure yet, but the same fix may be needed if it does. This is left documented here rather than papered over, in the same spirit as kane-cli surfacing gaps as first-class output rather than silence.
