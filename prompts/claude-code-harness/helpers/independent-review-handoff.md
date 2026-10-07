# Independent Review Handoff

**When to use:** After the implementation phase (major prompt 08) and its completeness check, to prepare a neutral brief for the adversarial review (major prompt 09), which must run in a different, fresh session.
**Session:** SAME (the implementation session, which knows what changed). The review itself runs in a NEW session.
**Mutation:** ARTIFACTS ONLY (writes under `AUDIT_DIR`)

## Copy/Paste Prompt

```text
AUDIT_DIR: <path outside every repository>

Prepare an independent review brief so that a different session, with no memory of
this one, can review the implemented harness. Do not modify any repository or
configuration. Write only under AUDIT_DIR.

Write AUDIT_DIR/handoff/09-review-brief.md containing FACTS ONLY:
1. Scope: exactly which files and constructs were created, changed or moved, by
   path relative to placeholders, with the scope (user/workspace/repository) and
   whether each is shared or personal.
2. Intended behaviour, written as testable assertions (for example "a session
   started in <REPOSITORY> loads only X", "skill S triggers for requests like Y and
   not for Z", "hook H runs on event E in under N seconds").
3. Where the design and implementation report are: the paths of the approved design,
   the approvals record, and the implementation report.
4. Baseline and after measurements of always-loaded context, labelled measured or
   estimated, with the method.
5. How to run the validation probes again (commands or steps), and how to roll back.
6. Known deviations from the design, stated factually, and anything not implemented.

Do NOT include: your opinion of the quality of the work, arguments for why choices
are good, or requests that the reviewer go easy on anything. The reviewer must be
free to disagree with every decision.

Then verify the brief by re-reading it, and update AUDIT_DIR/STATUS.md (phase 08
Notes: "review brief written").

End with the exact text I should paste into a NEW session, after the review prompt:
"Read AUDIT_DIR/handoff/09-review-brief.md first. Treat every claim in it and in the
implementation report as unverified."
```
