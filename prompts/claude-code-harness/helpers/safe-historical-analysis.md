# HHISTORY — Safe History Analysis

**When to use:** Alongside H02 (history mining): paste it first, or paste it when Claude starts opening transcript files directly, loading very large files, or printing raw conversation content.
**Session:** EITHER (the session running H02)
**Mutation:** ARTIFACTS ONLY (writes under the artifact root; history files are read-only)
**Placeholders to fill:** none

## Copy/Paste Prompt

```text
HARNESS WORKFLOW
Helper ID: HHISTORY
Operation: Safe History Analysis

This prompt was manually copied from an external prompt library. Do not assume that
library or its filenames are available in this environment. IDs such as H01 or HCHECK
are logical workflow identifiers, not file paths; do not try to open or look up a
prompt by ID or name.

Constraints for all historical-usage analysis in this session. Write only under the
harness artifact root (AUDIT_DIR, a folder outside every repository); if it has not
been stated in this session, determine it from the persistent harness artifacts or ask me.

1. Never open a session transcript in full and never paste transcript contents into
   the conversation. History can be huge and can contain source code, credentials and
   personal data. Treat the history folder as read-only.
2. Size first: list history locations with file counts, total size and date range only.
   Do not read contents yet.
3. Schema by sampling: for at most 3 files of different sizes, read only the first few
   records and report the KEY NAMES and record types, not the values.
4. Analyze with a small throwaway script, saved under the artifact root, using only the
   standard library of the scripting language available on this machine (prefer
   Python). It must: stream files line by line, tolerate malformed records, open files
   as UTF-8, normalize path separators and drive letters, compare paths
   case-insensitively, and work on Windows, macOS and Linux.
5. Output aggregates only: counts, sizes, distributions, top-N lists, normalized tool
   targets and normalized command names. Strip or hash user prompt text. Redact
   anything that looks like a credential (tokens, keys, authorization headers, long
   random-looking strings, URLs with embedded credentials, email addresses). Never
   print more than about 50 lines from any single output; write larger results to
   files under the artifact root.
6. Test the script on one small session and verify its numbers by hand, then run it
   over everything.
7. When a workflow cluster needs a closer look, read at most 2-3 representative
   sessions, selectively (opening request, tool-call outline, outcome), through the
   script's extraction rather than raw dumps, with redaction applied.
8. Every claim must carry its sample size. If a field is not recorded in this version's
   history, say so rather than estimating it.
9. Do not modify, move, truncate or delete history files, repositories or configuration.

Confirm these constraints, then continue with the history-mining phase (H02).
```
