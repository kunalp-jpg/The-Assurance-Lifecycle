# Kane CLI: The Assurance Lifecycle

An end-to-end demonstration of the **Kane CLI assurance lifecycle**: transforming a plain-language product specification into reviewed, traceable, and executable tests.

Starting from a feature specification, this repository demonstrates how Kane CLI can:

* ingest and version requirements
* extract use-cases with evidence linked back to source material
* generate acceptance criteria and test scenarios
* maintain traceability between requirements and tests
* execute tests and measure coverage

The demo uses [SauceDemo](https://www.saucedemo.com) as the target application and covers login and cart workflows.

Unlike `kane-cli generate`, which creates quick test cases from a natural-language description, the assurance lifecycle is designed for situations where teams need confidence that their tests are grounded in actual requirements. Every generated artifact can be reviewed, traced, and validated against the original source specification.

---

## What's in here

| File / folder               | Purpose                                                                                                                          |
| --------------------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| `sources/feature-spec.md`   | The source requirement document. All use-cases, acceptance criteria, and tests in this demo are derived from this specification. |
| `.context/` *(gitignored)*  | Kane CLI's local, content-addressed store containing source snapshots, extracted artifacts, and review history.                  |
| `.testmuai/` *(gitignored)* | Local workspace used for authored and executed tests.                                                                            |
| `tests/*_test.md`           | Generated test definitions linked to the acceptance criteria they verify.                                                        |
| `.gitignore`                | Prevents local Kane CLI state from being committed.                                                                              |

---

# The Assurance Lifecycle

The assurance lifecycle loop:

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

Each stage is independently reviewable. Nothing downstream is generated until upstream artifacts have been reviewed and trusted.

---

## Lifecycle Stages

| Stage | Command | Purpose |
|---|---|---|
| Capture | `kane-cli context ingest sources/feature-spec.md` | Creates a versioned snapshot of the feature specification in `.context/` |
| Extract | `kane-cli context extract` | Generates use-cases from the source document with references to supporting evidence |
| Review | `kane-cli context review` | Reviews extracted artifacts and promotes, edits, or rejects them |
| Design | `kane-cli design tests` | Generates acceptance criteria, scenarios, and runnable tests |
| Review Design | `kane-cli context review` | Reviews generated acceptance criteria and test designs |
| Execute | `kane-cli testmd run <test>.md` | Authors and executes individual tests |
| Replay | `kane-cli testrun run` | Runs the complete test suite and generates an evidence pack |
| Measure | `kane-cli cover` | Reports proven coverage versus remaining gaps |
| Maintain | `kane-cli maintain` | Updates the lifecycle when requirements change |

---

# Quick Start

Run the following commands from the repository root.

## 1. Capture requirements

```bash
kane-cli context ingest sources/feature-spec.md
```

Creates a versioned snapshot of the requirement document. Later lifecycle stages operate on this snapshot rather than directly reading the source file.

---

## 2. Extract and review use-cases

```bash
kane-cli context extract
```

The KaneAI agent analyzes the requirement document and proposes use-cases with references back to the original source.

The generated artifacts begin as `derived` and require review:

```bash
kane-cli context review
```

Review actions:

* promote artifacts to `trusted`
* edit proposals
* reject artifacts into `archived`

Nothing generated by the agent becomes trusted automatically.

---

## 3. Generate tests

```bash
kane-cli design tests
```

Creates:

* acceptance criteria
* test scenarios
* runnable `*_test.md` files

Each test maintains a link to the acceptance criteria it verifies.

Review the generated design:

```bash
kane-cli context review
```

---

## 4. Execute tests

Run individual tests:

```bash
kane-cli testmd run tests/<test-file>.md
```

The test is authored and executed against SauceDemo using Chrome.

---

## 5. Replay the suite and measure coverage

Run the complete suite:

```bash
kane-cli testrun run
```

This produces an evidence pack containing the results of the execution.

Measure coverage:

```bash
kane-cli cover
```

Coverage is reported across two dimensions:

* **Proven:** requirements supported by executed tests
* **Owed:** requirements or scenarios without sufficient verification

---

# Vocabulary

| Term                             | Meaning in this repository                                                                         |
| -------------------------------- | -------------------------------------------------------------------------------------------------- |
| **Source**                       | `sources/feature-spec.md`, the requirement document used as the foundation of the lifecycle.       |
| **Use-case**                     | A user goal extracted from the source document with supporting evidence.                           |
| **Acceptance Criterion (AC)**    | A specific, verifiable requirement that a test must prove.                                         |
| **Scenario**                     | A single path through a use-case, such as a happy path or negative case.                           |
| **Test**                         | A runnable `*_test.md` file linked to the acceptance criteria it validates.                        |
| **derived / trusted / archived** | Artifact states. Generated content starts as derived and must be reviewed before becoming trusted. |
| **fresh / stale / orphaned**     | Source relationship states used to track whether artifacts remain valid after requirement changes. |
| **Gap**                          | Missing coverage, such as an acceptance criterion without a corresponding verified test.           |
| **Evidence pack**                | The sealed output generated from test execution and used for coverage measurement.                 |

---

# Assurance vs Generate

Kane CLI provides two approaches for creating tests:

### `kane-cli generate`

Best for quickly creating test ideas from a plain-language description.

Example:

> "Create tests for a shopping cart checkout flow."

### Assurance Lifecycle

Best when requirements need to remain connected to implementation and testing.

The assurance lifecycle provides:

* requirement traceability
* human review checkpoints
* acceptance criteria mapping
* measurable coverage

For projects with formal requirements or compliance needs, assurance provides a stronger connection between what a product should do and what has been verified.

---

# Local Context Store

The assurance lifecycle uses a local `.context/` store.

Properties:

* **Append-only:** Previous versions are preserved instead of overwritten.
* **Local:** Requirements, extracted artifacts, and reviews remain in the project directory.
* **Git ignored:** The store should not be merged between branches.

Useful commands:

```bash
kane-cli context fsck
```

Verify store integrity.

```bash
kane-cli context rebuild
```

Rebuild local caches from verified records.

---

# Scenarios Covered

This demo covers the complete SauceDemo login and cart workflow, with each scenario linked back to its acceptance criteria.

## Authentication

✅ **Standard user login**  
`standard_user / secret_sauce`

- User successfully authenticates.
- User is redirected to the products page.

✅ **Locked-out user login**  
`locked_out_user / secret_sauce`

- User receives the locked-account error message.
- User remains blocked from accessing the products page.

---

## Cart Management

✅ **Add product to cart**

- User adds a product from the products page.
- Cart badge count increases by exactly 1.

✅ **Remove product from cart**

- User removes a product from the cart.
- Cart badge count decreases by exactly 1.

---
---

# Requirements

* Kane CLI **0.6.1+**

  * Check with:

    ```bash
    kane-cli --version
    ```
* KaneAI account authentication

  * Required for:

    * `context extract`
    * `design tests`
    * `maintain`
  * Other commands operate locally.
* Google Chrome installed for test execution.

---

# Known Issue

During development, the login test initially contained duplicate confirmation steps checking the same login-page state. This resulted in an intermittent false failure caused by overly strict validation rather than an application issue.

The redundant assertion was removed from the standard-user login test. The locked-out-user test contains a similar pattern that has not yet produced the same failure but may require the same cleanup.

This issue is documented because surfacing gaps and improving traceability is part of the assurance workflow itself.
