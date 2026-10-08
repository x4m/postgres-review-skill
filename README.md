# PostgreSQL upstream review skill

A reusable method for AI-assisted PostgreSQL patch review and concise
pgsql-hackers replies, developed by Andrey Borodin from ongoing review work
with coding assistants. The emphasis is on understanding contracts, testing
claims, and contributing new information to an existing discussion.

This is a working method, not PostgreSQL project policy or a correctness
guarantee. The human reviewer remains responsible for understanding and
checking every claim they send.

## What it teaches

- Prioritize the idea and architecture before mechanical polish, following
  Robert Haas' review hierarchy.
- Treat useful criticism as more valuable than a routine LGTM. Resolving one
  finding is not evidence that the whole patch is ready.
- Distinguish reproduction, source reasoning, and open questions. Keep that
  distinction when compressing a review into a short email.
- Follow changed invariants across owners, callers, and lifecycle transitions.
- Recheck revisions and retain the reasoning behind resolved concerns.
- Select tests by plausible failure modes and maintenance cost, not by count.
- Keep detailed evidence separate from a short, editable reply.

The cases behind the testing guidance include false-positive success checks,
fresh-index boundary cases, worker and cache lifetime, and test runs that
never reached the code under review. They have been generalized; this package
does not contain conversation transcripts, local archive credentials,
machine-specific commands, or a personal email identity.

## Install or use directly

Clone this repository into a separate tools directory, outside your
PostgreSQL checkout:

```sh
git clone https://github.com/x4m/postgres-review-skill.git
```

In Codex, link that checkout into the user skill directory. Replace
`/absolute/path/to/postgres-review-skill` with its actual location:

```sh
mkdir -p "$HOME/.agents/skills"
ln -s /absolute/path/to/postgres-review-skill \
  "$HOME/.agents/skills/postgres-upstream-review"
```

Do not overwrite an existing installation. If the skill is not discovered,
restart Codex. See the [official skill documentation][skills] for discovery
and other installation options.

Then ask, for example:

```text
Use $postgres-upstream-review to review v3 of this patch and draft a reply.
Check our previous concerns against the new code. Do not post anything.
```

Other assistants can read [SKILL.md](SKILL.md) directly, or install the
folder using their own support for the Agent Skills format. The instructions
have no dependency on Codex tools, Hackorum, a particular checkout path, or
one build system. Project-specific instructions still govern local work.

## Contents and contributions

- [SKILL.md](SKILL.md): entry point and reply workflow.
- [Review principles](references/review-principles.md): priorities,
  evidence, changed contracts, and revisions.
- [Test evidence](references/testing.md): test selection and conditional
  lessons from previous reviews.

Improvements should explain a recurring mistake or a concrete review where
the guidance helped or failed. Prefer a small correction over another
unconditional checklist item. Do not include private correspondence or
incident details in examples without permission.

Licensed under the [PostgreSQL License](LICENSE).

[skills]: https://learn.chatgpt.com/docs/build-skills
