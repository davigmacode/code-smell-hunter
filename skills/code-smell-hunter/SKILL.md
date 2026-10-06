---
name: code-smell-hunter
description: Systematic code smell detection, anti-pattern auditing, and precision refactoring for Google Antigravity. Hunts down Bloaters (Long Method, God Class), Object-Orientation Abusers, Change Preventers, Dispensables (Duplicated Code, Dead Code), and Couplers (Feature Envy). Applies Martin Fowler refactoring recipes with strict regression prevention and multi-agent review. Triggers on "/smell", "/refactor", "code smell", "code smell audit", "detect smells", "clean up technical debt", "refactor this cleanly".
---

# Code Smell Hunter & Refactor for Google Antigravity

Audit codebases for structural anti-patterns, quantify technical debt, and execute behavior-preserving refactoring without introducing regression bugs or speculative complexity.

---

## The Prime Directive

> **BEHAVIOR STAYS IDENTICAL. STRUCTURE GETS CLEAN.**
> Refactoring modifies internal architecture without altering observable external behavior. Never combine functional feature changes and refactoring in the same step. Existing tests MUST pass before, during, and after each refactoring transformation.

---

## 5 Taxonomy Categories of Code Smells

When scanning code, systematically inspect across these 5 Martin Fowler categories:

### 1. Bloaters (Excessive Size & Scope)
* **Long Method**: Functions > 30–50 lines doing multiple unrelated tasks.
  * *Refactor Recipe*: `Extract Method`, `Replace Temp with Query`, `Decompose Conditional`.
* **Large Class (God Object)**: Classes handling disparate responsibilities. Violates Single Responsibility (SRP).
  * *Refactor Recipe*: `Extract Class`, `Extract Subclass`.
* **Long Parameter List**: Methods requiring > 3–4 parameters.
  * *Refactor Recipe*: `Introduce Parameter Object`, `Preserve Whole Object`.
* **Primitive Obsession**: Using raw strings/integers instead of small value types (e.g. passing `"USD"` and `1000` rather than a `Money` value object).

### 2. Object-Orientation Abusers (Incorrect OOP Usage)
* **Switch Statements / Nested If-Else Chains**: Repeated type checks or polymorphic branch switching.
  * *Refactor Recipe*: `Replace Conditional with Polymorphism`, `Strategy Pattern`.
* **Refused Bequest**: Subclasses inheriting methods they throw `NotImplementedError` or ignore.
  * *Refactor Recipe*: `Replace Inheritance with Delegation`.
* **Temporary Field**: Instance variables only populated under specific conditions or methods.

### 3. Change Preventers (Friction to Modification)
* **Divergent Change**: A single module must be modified for multiple distinct feature reasons (DB changes, UI format changes, business rule changes).
* **Shotgun Surgery**: Making one conceptual change requires touching 10 different files simultaneously.
  * *Refactor Recipe*: `Move Method`, `Move Field`, `Inline Class`.

### 4. Dispensables (Senseless Clutter & Redundancy)
* **Duplicated Code (DRY Violation)**: Identical or copy-pasted logic across files.
  * *Refactor Recipe*: `Pull Up Method`, `Form Template Method`, `Extract Shared Utility`.
* **Dead Code**: Unreachable branches, unused exports, dead parameters.
* **Speculative Generality / Over-Engineering**: Preemptive abstractions, unused hooks, or theoretical interfaces built "just in case".
  * *Refactor Recipe*: `Collapse Hierarchy`, `Inline Class`, delete speculative dead code.
* **Zombie Comments**: Outdated comments or commented-out code blocks.

### 5. Couplers (Tangled Dependencies)
* **Feature Envy**: A function in Class A accesses getters and internal data of Class B more than its own.
  * *Refactor Recipe*: `Move Method` to the class owning the data.
* **Inappropriate Intimacy**: Classes that constantly peek into each other's private internals.
* **Message Chains**: Long navigation chains like `a.getB().getC().getD().getValue()`. Violates Law of Demeter.
  * *Refactor Recipe*: `Hide Delegate`.

---

## 4-Step Refactoring Protocol in Antigravity

### Step 1: Pre-Refactor Safety Baseline
1. Check test coverage: Run the existing test suite via `run_command` in the sandbox.
2. If tests are missing for the target method: **Write a baseline regression test first** before changing any production code!

### Step 2: Audit & Generate Smell Report Artifact
Produce a structured markdown audit report in the Antigravity conversation artifact (`code-smell-audit.md`):

| Location | Smell Detected | Severity | Root Cause | Proposed Transformation |
| :--- | :--- | :---: | :--- | :--- |
| `src/auth.ts:45` | Long Method (85 lines) | High | Mixing token decode, DB lookup, & rate-limit | `Extract Method` into 3 private helpers |
| `src/order.ts:12` | Feature Envy | Medium | Manipulates `Customer` state directly | `Move Method` to `Customer` class |
| `src/parser.ts:80` | Primitive Obsession | Low | Raw coordinate strings `"12.4,55.1"` | Introduce `GeoCoordinate` value type |

### Step 3: Atomic Transformations
* Apply **one refactoring recipe at a time**.
* After each atomic change, re-run tests immediately.
* Do not batch 5 major refactorings together. Keep git diffs clear and verifiable.

### Step 4: Multi-Agent Subagent Verification
* Use `invoke_subagent` to launch a **`Code-Quality-Judge`** (Model: `pro`).
* Prompt: *"Review this before-and-after diff. Did the refactoring introduce any hidden behavior changes, increase cyclomatic complexity, or add unnecessary abstractions? Validate against Martin Fowler clean code guidelines."*

---

## Trigger Commands

* `/smell [file or directory]`
* `/refactor [function or component name]`
* `audit code smells for: [file path]`
* `detect anti-patterns and clean up technical debt`
