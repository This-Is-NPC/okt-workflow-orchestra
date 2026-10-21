---
name: Test evidence
severity: error
---
**A behaviour change ships with evidence a reviewer can rerun.** Either a test added in the same diff that fails before the change and passes after it, or a `#tests-passing` comment with the command, the output lines and the duration. "Tested locally" is not evidence. A behaviour change inside a refactor is still a behaviour change: name it.

**Prove it on the real artifact.** A claim about the delivered system is checked by running that system under the condition it names — the server, the CLI, concurrent use — not only through test doubles.

**A state change is proven by an independent read.** Verify that a write took effect through a separate read path — a new request, connection or process — never only through the response of the write itself.

**Wait for evidence, not time.** A test waits for the state, output or signal that proves the work finished; a deadline only bounds a failure. Never sleep for a settle time.

**A test is proven by breaking what it checks.** Remove the guard or invert the condition and run the narrowest test: the test that names the behaviour must fail. Revert the mutation; never loosen an assertion to make it fail.
