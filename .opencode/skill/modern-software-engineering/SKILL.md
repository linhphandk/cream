# Skill: modern-software-engineering

# Modern Software Engineering (Farley)

Core thesis: software engineering = applying an iterative, experimental approach to learning what works. Engineers must be experts at two things: **learning fast** and **managing complexity**. Every rule below serves one of those two.

## The working loop (default for any non-trivial change)

1. **Slice**: cut the task to the smallest end-to-end piece that could work.
2. **Predict**: state what behavior the next test will verify, and how it should fail.
3. **Test first**: write the failing test (a small experiment). If the test is hard to write, the design is wrong — fix the design, not the test.
4. **Make it pass** with the minimum code.
5. **Verify by running** — never assume; run the tests/build/system and read the actual output.
6. **Refactor** while green: small, behavior-preserving, guided by the practices below.
7. Repeat. Keep the system releasable at every step.

## Optimize for learning

- **Iterative over planned**: build to discover, not to a big design up front. A detailed plan is a guess; working software is evidence. Change approach when results disagree with intent.
- **Fast, early feedback**: always take the earliest signal available — unit test before integration test, compile before run, run before deploy. Prefer feedback in seconds/minutes, not hours.
- **Incremental**: small steps, each leaving the system working. Limit the blast radius of every change: one module, one behavior at a time. Big bangs are unreviewable and unverifiable.
- **Empirical**: assume your ideas are wrong until the system proves otherwise. "I know that bug!" is self-deception — reproduce it, watch it, fix what you observe. Never claim behavior you haven't run.
- **Experimental**: treat each change as a hypothesis. Predict the failure mode, observe the actual result, and let the difference teach you. Automate the experiment — that's what a test is.

## Optimize for managing complexity

- **Modularity**: divide the system so each part can be reasoned about, built, and tested independently. Modularity is measured by testability — hard to test in isolation = bad boundaries.
- **Cohesion**: things that change together, and serve the same purpose, live together. Unrelated responsibilities in one module make every change risky.
- **Separation of concerns**: isolate domain logic from technical details (persistence, transport, UI). Ports & adapters: domain at the center, infrastructure at the edge. Model the problem domain's language (DDD).
- **Information hiding**: hide implementation behind abstractions. Prefer deep modules — small interface, lots of hidden behavior. Isolate third-party code behind your own boundary. Leaky abstractions are design debt.
- **Manage coupling**: coupling, not duplication, is the main cost. DRY is too simplistic — don't couple unrelated concepts just to share code. Prefer loose coupling in time (async) and space (events/messages) when coordination cost is real, and accept a little duplication to keep modules independent.

## Testability — the primary tool

- If a test is hard to write, the design is poor. Improve the design first.
- Testability is fractal: it applies to a few lines of code and to whole systems.
- Default to TDD for new logic: predict → failing test → pass → refactor. Tests written after the code are coupled to the implementation; tests written first drive better design.
- Watch the **measurement points**: can you establish state easily and observe results easily? If not, redesign.

## Concrete example: "hard to test" means "redesign"

Adding tax to an order, test-first. The first attempt pulls total from the DB, applies tax, and sends an email — that test needs a database and an inbox, so the design is wrong. Redesign: keep the domain pure and push infrastructure to the edges.

```python
# The test (a small experiment) stays trivial — no DB, no email:
assert apply_tax(total=100, tax_rate=0.2) == 120

# Domain logic isolated; persistence and notification are passed in from outside:
def apply_tax(total: Decimal, tax_rate: Decimal) -> Decimal:
    return total * (1 + tax_rate)
```

Easy test → good boundary. Same move at every scale: class, module, service.

## Review checklist (when reviewing code or a design)

1. Can each piece be tested independently? If not, where are the bad boundaries?
2. Is domain logic mixed with infrastructure concerns?
3. What does each module hide? (Nothing hidden = big ball of mud forming.)
4. What is coupled to what? Would a change here ripple there?
5. Is every claim in the review backed by something that was actually run?
6. Could this ship now, safely, with unfinished features hidden?

**Outcomes over mechanisms**: these principles are durable; any specific practice (tool, framework, ceremony) is only a means. Choose practices that speed learning or tame complexity; drop ones that don't.