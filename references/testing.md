# Test evidence

## Choose tests by the mistake they would detect

- Before adding or requesting a test, identify the plausible future bug it
  would catch. The best tests represent a realistic user-visible failure and
  guard against a likely recurrence. Merely replaying the current reproducer
  is not enough if the probable future mistake lies outside its coverage.
  Weigh engineering, maintenance, and runtime cost. A useful diagnostic
  reproducer need not become a permanent upstream test.
- Prefer the smallest, clearest reproducer; a test a committer cannot quickly
  understand has little review value. If regress coverage proves the point,
  a TAP test may be unnecessary. Check execution assumptions, including
  installcheck and nondefault configurations.
- For a regression test, verify that it fails on the buggy code and passes
  with the fix, for the intended reason. If either side was not run, report
  that limit rather than claiming fail-before/pass-after evidence. Also
  check the intended path and oracle: a fallback can produce the expected
  output without exercising the feature. Where useful, temporarily substitute
  a plausible wrong implementation to see whether the test distinguishes it.
- Do not hand-craft regression expected output. Run the test, inspect the
  result, and use the actual output to update expectations only once its
  correctness is established.
- For race bugs, prefer eliminating the race window in the implementation
  over merely making a test pass more often. Use observable-state waits or
  explicit dependencies, and check that the waits succeeded. Shorter sleeps
  or faster worker intervals do not establish an ordering. Add injection
  points only when there is no simpler reliable way to exercise the window.

## Conditional lessons from reviews

These are reusable patterns, not claims that a particular upstream version
still contains a defect, and not a mandatory test matrix.

- **Positive protocol tests:** absence of one expected error is not evidence
  of success; a disconnect or a different error can satisfy that assertion.
  Check the successful outcome the test claims. For example, a CopyBoth
  response proves entry into streaming, not WAL delivery or replay. Use a
  real client and an end-to-end observation when testing those claims.
- **State lifetime:** reuse the connection, worker, or object whose cached
  state must change. Reconnecting may silently reset the state under test.
  Conversely, changing a role/database default does not prove that an already
  running worker adopted it; stop, observe, reset, and restart in the order
  the scenario requires. For allocated state, follow the owning memory
  context, including error cleanup and explicit free, not only normal use.
- **Indexes:** initialization and steady-state updates can establish summaries
  differently. A first non-NULL NaN value or singleton range can bypass a
  merge path exercised by larger datasets. Check the relevant query strategy
  and fresh-index behavior as well as stale contents. Compare exact results
  against an independent oracle; counts can hide a duplicate and an omission.
  If scan identity matters, compare physical TIDs and their multiplicities.
- **WAL and recovery:** select crash, replay, or consistency-check scenarios
  from the changed persistence invariant. A green SQL suite or a primary-side
  lock does not prove replay correctness. Do not request every recovery test
  mechanically; explain which failure each proposed scenario would catch.
- **Test discovery and CI:** check the test registration required on the target
  branch, including explicit Meson lists for new TAP files. Read the actual
  CI test results for the reviewed commit: an earlier suite failure can leave
  the relevant test unrun, and a later unrelated failure does not undo its
  successful run. Keep CI status separate from evidence about the patch.
- **Parallel regression tests:** catalog-visible objects can affect other
  tests before cleanup, including plan choices. Inspect concurrent schedules
  when a regression only appears in a suite. Transactional setup and rollback
  can avoid leakage when compatible with the scenario.

Run tests using the project's build and scratch-cluster instructions. Record
the exact version, configuration, outcome, and relevant limits in local
notes. A test blocked by its environment is neither a pass nor proof of a
patch defect.
