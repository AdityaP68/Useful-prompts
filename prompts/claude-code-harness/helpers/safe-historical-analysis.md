# Safe Historical Analysis

**When to use:** Alongside major prompt 02 (history mining): paste it first, or paste it when Claude starts opening transcript files directly, loading very large files, or printing raw conversation content.
**Session:** EITHER (the session running prompt 02)
**Mutation:** ARTIFACTS ONLY (writes under `AUDIT_DIR`; history files are read-only)

## Copy/Paste Prompt

```text
AUDIT_DIR: <path outside every repository>

Constraints for all historical-usage analysis in this session:

1. Never open a session transcript in full and never paste transcript contents into
   the conversation. History can be huge and can contain source code, credentials and
   personal data. Treat the history folder as read-only.
2. Size first: list history locations with file counts, total size and date range
   only. Do not read contents yet.
3. Schema by sampling: for at most 3 files of different sizes, read only the first
   few records and report the KEY NAMES and record types, not the values.
4. Analyze with a small throwaway script, saved under AUDIT_DIR, using only the
   standard library of the scripting language available on this machine (prefer
   Python). It must: stream files line by line, tolerate malformed records, open files
   as UTF-8, normalize path separators and drive letters, compare paths
   case-insensitively, and work on Windows, macOS and Linux.
5. Output aggregates only: counts, sizes, distributions, top-N lists, normalized tool
   targets, and normalized command names. Strip or hash user prompt text. Redact
   anything that looks like a credential (tokens, keys, authorization headers, long
   random-looking strings, URLs with embedded credentials, email addresses). Never
   print more than about 50 lines from any single output; write larger results to
   files under AUDIT_DIR.
6. Test the script on one small session and verify its numbers by hand, then run it
   over everything.
7. When a workflow cluster needs a closer look, read at most 2-3 representative
   sessions, selectively (opening request, tool-call outline, outcome), through the
   script's extraction rather than raw dumps, with redaction applied.
8. Every claim must carry its sample size. If a field is not recorded in this
   version's history, say so rather than estimating it.
9. Do not modify, move, truncate or delete history files, repositories or
   configuration.

Confirm these constraints, then continue with the history-mining prompt.
```
