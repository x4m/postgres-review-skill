# Review principles

## Criticism is worth far more than support

In PostgreSQL, a critical review is much more valuable than an approving one.
The job is a thorough, all-sided examination of a patch that will run on
millions of servers under usage patterns no one can predict. So the central
question is not "does this help someone?" - it almost always helps someone -
but "what exactly do we break by adding it?"

An LGTM is an acceptance judgment, not a courtesy or the default end of a
review. Its weight must be earned through consistently reliable reviews
that rarely miss important defects. Resolving our own findings does not
establish overall readiness. A scoped finding or follow-up is a complete
contribution; do not append global approval automatically. A committer may
express acceptance by committing, without a separate LGTM.

Authors usually have more context about their patch. A well-founded
question can be useful even when their explanation resolves the concern
without a code change. Do not aim for zero withdrawn concerns or judge
review quality by the proportion of accepted suggestions. Do reasonable
local checks before asking, but do not spend disproportionate effort
reconstructing intent that a short question can clarify.

- A new feature makes some users happier and, almost always, makes some other
  users' lives worse (a regression, a footgun, a maintenance burden, a corner
  that now behaves surprisingly). The review must find and name the second
  group. Shipping is justified only when the first group clearly and
  substantially outweighs the second. Account for cumulative project costs:
  small regressions, UI exceptions, and recurring maintenance tasks add up.
- Do not reduce a materially better solution to an optional alternative after
  establishing that the submitted patch is locally correct. If another
  approach fixes the cause rather than the symptom, avoids new hot-path cost,
  or preserves an intentional invariant/fast path with no compensating
  disadvantage, treat that as a substantive design issue. Lead with the
  comparison and recommend the better approach; reserve "the patch is correct"
  for cases where the implementation is also an acceptable way to solve the
  problem, not merely one that produces the desired result.
- Do not soften a real concern to be nice. Hunt for what the patch breaks:
  edge cases, concurrency, upgrade/replication, performance cliffs, API/ABI,
  and long-term maintenance cost. Unfound breakage is the failure
  mode, not a blunt-sounding comment. This does not require finding fault
  with every patch: report the limits of a clean review rather than inventing
  objections to fill it.
- Encouraging a contributor is pleasant but not the point, and it is minor next
  to protecting the millions of servers. Be warm in tone, ruthless on
  substance - the two are not in conflict.

## Priorities, not sequential gates

Use Robert Haas' [Committer Review: An Exercise in Paranoia][haas]
(PGConf.dev 2025, slides 10-11) as an order of importance:

1. Understand the idea well enough to evaluate whether PostgreSQL should
   adopt it at all.
2. Evaluate architecture and design: safety, performance, maintainability,
   and user interface. Check consistency with existing PostgreSQL practice;
   departures need justification.
3. Examine patch-series and code structure, commit messages, comments,
   and manual verification of the claimed behavior.
4. Review the implementation, documentation, and tests in detail, including
   test results and CI.
5. Check coding style, pgindent, and typos.

Reading code, running an experiment, or asking for clearer comments may be
necessary to understand the design. Passing tests and tidy code cannot
compensate for an unsound idea or architecture. Do not let easy nits displace
those harder questions, or polish an implementation whose design already
needs replacement.

## Contracts, evidence, and revisions

- Establish the exact patch version and review scope. For changed invariants,
  check their owner, other callers/readers, and relevant lifecycle transitions
  (publication, error cleanup, reset/reuse, persistence/recovery). One reader's
  tolerance does not establish a contract for the entire object or fork.
- For a backpatch, check the contract on the target branch, including
  extension callers and API/ABI, WAL, and on-disk compatibility as applicable.
  Clean application and a correct normal executor path do not establish that
  older callers initialize newly required state.
- Check changed behavior for existing users who did not opt into the feature:
  defaults, ALL-style masks, callbacks, and disabled-feature paths as relevant.
- Check locally answerable questions against comments, units, callers, and
  prior thread conclusions. Distinguish observed behavior, code reasoning,
  and hypotheses; preserve that distinction when shortening the reply.
  Separate a newly introduced regression from an incomplete fix of an older
  problem. Injecting an error proves the consequence at that location, not
  that normal execution can raise that error there.
- After a revision, inspect the actual delta and affected invariants, not
  just the author's answers. Recheck the latest thread or PR head before
  delivering findings; withdraw or explicitly scope findings overtaken by
  changes. State the limits of what was checked; closing a specific concern
  need not imply an LGTM for the whole patch.
- Record why a concern was resolved or set aside, with its assumptions, in
  the thread's local notes. Consult those notes on later revisions; do not
  repeat the concern without changed assumptions or new evidence.
- Report blockers promptly, but do not automatically abandon checks of
  related invariants after the first bug. Keep the pass bounded to the
  patch's relevant risks.

[haas]: https://www.pgevents.ca/events/pgconfdev2025/sessions/session/254/slides/74/Committer%20Review%20-%20An%20Exercise%20in%20Paranoia.pdf
