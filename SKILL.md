---
name: postgres-upstream-review
description: Review PostgreSQL upstream patches and revisions, assess their test evidence, and draft concise mailing-list replies grounded in the current discussion. Not a general PostgreSQL administration or CommitFest triage workflow.
license: PostgreSQL
---

# PostgreSQL upstream review

Use the project's instructions for its build, archive access, artifact
locations, and contribution conventions. This skill supplies review method,
not machine configuration or permission to change external state.

## Establish the review

- Identify the requested scope, exact patch version and base, current
  checkout state, and the discussion being answered. Preserve unrelated
  work. For a revision, consult earlier notes and resolutions first.
- Read the latest relevant messages before drafting. A local archive can
  be stale; verify its coverage when recent replies matter. Keep canonical
  message URLs as sources, rather than treating a search summary as the
  discussion. If a source is unavailable, state the limit.
- For substantive patch review, read
  [review principles](references/review-principles.md).
- When selecting, running, or reviewing tests, also read
  [test evidence](references/testing.md). Its examples are conditional
  prompts, not a requirement to add every kind of test to every patch.

## Produce useful feedback

Keep detailed evidence and the short reply separate. Notes should preserve
the reviewed version, reasoning, commands and actual outcomes, unresolved
questions, and why earlier concerns were set aside. Do not invent a test
run or describe an inherited result as something just verified.

Draft replies in an editable plain-text file, wrapped at about 72 columns,
using the user's chosen language, identity, and sign-off. Continue the
latest discussion: quote only the text being answered, attribute findings
already raised by others, and lead with new information. Explain the
mechanism and consequence, then ask a precise question or propose a
direction. Do not re-list uncontested patch properties or routine test
commands; include unusual test setups and relevant failures.

Before handoff, recheck that each finding still applies to the reviewed
version and that shortening the reply has not turned a hypothesis into a
fact. A useful scoped review does not require an overall approval.

Review and drafting requests do not themselves authorize fixes, pushes,
mail, PR comments, or CommitFest changes. Follow the user's actual scope;
do not expand it because a next action would be convenient.
