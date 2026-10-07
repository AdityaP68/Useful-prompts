# Repair Handoff

**When to use:** After a Fresh Session Recovery Test returned `RECOVERY: INSUFFICIENT`. It fixes the specific gaps in the persisted artifacts so the test can be repeated.
**Session:** Preferably the ORIGINAL session if it is still open (it still holds the missing knowledge). Otherwise NEW, in which case paste the failed recovery verdict and gap list.
**Mutation:** ARTIFACTS ONLY (writes under `AUDIT_DIR`; read-only inspection elsewhere)

## Copy/Paste Prompt

```text
AUDIT_DIR: <path outside every repository>
PHASE: <NN and name of the frozen phase>
RECOVERY_GAPS:
<paste the numbered gap list and UNKNOWN items from the failed recovery test>

Repair the persisted artifacts for this phase so a fresh session could recover from
them alone. Do not start the next phase. Do not modify any repository, configuration
or history file. Write only under AUDIT_DIR.

For each numbered gap:
1. Decide where the missing information comes from:
   - this conversation (if it is still available): write it down now;
   - the live environment: re-derive it with minimal read-only inspection and label
     it RE-DERIVED with the date and method;
   - nowhere: record it as an explicit open question; never invent it.
2. Put it in the right place: the report if it is a finding, the handoff if it is
   process state. Replace conversation-dependent wording with the actual content.
3. Do not add unrelated material. Fix only the listed gaps and anything the fix
   directly makes inconsistent.

Then:
- Output a short change log: gap -> where it was fixed -> source (CONVERSATION /
  RE-DERIVED / OPEN QUESTION).
- Re-read the changed files and confirm no secrets were added.
- Update the Notes column of AUDIT_DIR/STATUS.md for this phase ("repaired, retest
  pending").

End with: "REPAIRED - run the Fresh Session Recovery Test again in a NEW session"
and stop.
```
