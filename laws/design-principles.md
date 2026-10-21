---
name: Design principles
severity: warning
---
Apply each principle where its condition holds; say which one changed a decision.

- **Model the domain in structure.** When logic is stateful or branches on the same shape in several places, encode it once — a state machine, a typed model, a table — instead of scattered conditionals.
- **Parse, don't validate.** Every input that crosses a trust boundary is turned into a domain type at that boundary; what cannot become one is rejected there, and the rest of the system only handles parsed values. The boundary starts at the outermost layer — transport and encoding before domain fields — and includes the error path: rejecting an input must never itself fail. In a tightly coupled codebase, follow its existing boundary instead of inventing layers.
- **Idempotent and atomic operations.** Where a command or step can be retried or interrupted, rerunning it converges to the same end state, and a partial run leaves nothing half-written (one transaction, write-then-rename). Skip it where the project has no retry or crash path.
- **Subtract before you add.** Remove the dead code and redundant checks in the area first, then build on the simpler base.
