# TempleOS — Keeper Work Log (Preservation Only)

**Date:** 2026-10-07 ~11:55–12:10 EDT
**Role:** Keeper preservation task — READ-ONLY condition report.
**Nothing in this archive was modified, added, deleted, renamed, or compiled.** All reads were
non-destructive (`ls`, `du`, `find`, `git status`, `git fsck`, `file`, `cat`). The TOSZ binary
was tested via a copy in `/tmp`, never executed in place. No writes were made to the archive.

---

## Archive state

- **Source:** https://github.com/cia-foundation/TempleOS (the canonical archive, per Wikipedia)
- **Git:** single shallow commit `c26482b` ("Fix corrupted Compiler.BIN, remove files that
  were not present in the final snapshot", 2020-02-24)
- **Total size:** 52M (matches the ~52MB assessment)
- **Files:** 686 total, across all expected top-level dirs
- **License:** Public domain (Terry Davis's declaration) — CLEAN for keeping/mirroring

### Top-level structure (verified present)

| Directory | Size | Contents |
|---|---|---|
| `0000Boot/` | 188K | bootloader sources |
| `Kernel/` | 964K | kernel (BlkDev, Mem, SerialDev subdirs) |
| `Compiler/` | 864K | HolyC JIT compiler, incl. `Compiler.BIN` |
| `Adam/` | 28M | core libraries (ABlkDev, AutoComplete, Ctrls, DolDoc, God, Gr, Opt, Snd) |
| `Apps/` | 648K | userland apps (Budget, GrModels, KeepAway, Logic, Psalmody, Span, Strut, TimeClock, Titanium, ToTheFront, Vocabulary, X-Caliber) |
| `Demo/` | 4.4M | demos incl. Games, Graphics, Lectures, MultiCore |
| `Doc/` | 544K | documentation |
| `Misc/` | 4.6M | incl. `Misc/Tour` |
| `Linux/` + `Downloads/Linux/` | 72K each | TOSZ file-expander tool (binary + CPP source) |
| Root | — | `HomeKeyPlugIns.HC`, `HomeLocalize.HC`, `HomeSys.HC`, `HomeWrappers.HC`, `MakeHome.HC`, `Once.HC`, `PersonalMenu.DD` (63K), `PersonalNotes.DD`, `ReadMe.TXT`, `StartOS.HC` |

## Integrity findings

- **Git working tree: CLEAN.** `git status --porcelain` returned zero changes; the checkout is
  byte-identical to the canonical commit.
- **`git fsck`: no errors reported** — the local object store is intact.
- **Zero-byte files: none.** Every file in the tree has content; no truncations found.
- **No checksum manifest ships with the repo.** Nothing to verify against beyond git itself
  (the commit hash IS the integrity anchor — `c26482b` matches the canonical archive).
- **ISO/images: none present.** The archive is the source tree only — there is no
  `TempleOS.ISO` or disk image. Per `ReadMe.TXT`, Davis distributed via ISO; the archive
  itself contains sources + the Compiler binary.
- **TOSZ tooling: functional.** The `Linux/TOSZ` ELF binary runs correctly (usage banner
  verified from a `/tmp` copy). Exec bit was lost in the clone (files are `-rw-rw----`);
  the binary itself executes fine when the bit is set — **this is noted, NOT changed**
  (restoring exec bits would be a modification of the artifact; the canonical repo may
  carry these bits via git's executable flag and should be confirmed upstream before
  any decision).

## Boot test: NOT ATTEMPTED

- `qemu-system-x86_64` is **not installed** on this machine.
- `apt-get install -y qemu-system-x86` was not viable: a separate `apt-get` process
  (PID 10550, unattended/system update) holds the apt lock, and a full QEMU install
  (~hundreds of MB of deps) exceeds the task's ~15-minute boot-attempt budget.
- Deeper blocker: **there is no bootable ISO or disk image in the archive** — only the
  source tree. Building one would mean running ISO-creation tooling against Terry's
  tree and generating new artifacts, which goes beyond a read-only preservation pass
  and risks "updating" the artifact rather than preserving it.
- **Verdict:** boot readiness cannot be established from this checkout. What CAN be said:
  the source tree is complete and uncorrupted, the canonical commit hash matches, and
  the Linux-side TOSZ extractor works. Boot validation must be done by a keeper-authorized
  process using a canonical ISO (see recommendations).

## Preservation recommendations (for the keeper)

1. **Keep the repo untouched as the read-only original.** Any generated ISO, fixed build,
   or documentation additions go in a SEPARATE keeper-managed tree, never here.
2. **Anchor integrity on the commit hash.** `c26482b` is the checksum-of-record. Re-clone
   from the canonical archive periodically (e.g., yearly) and diff — any upstream
   change is an event to record, not to merge blindly.
3. **Create a keeper-owned integrity manifest OUTSIDE the archive** (e.g.,
   `~/workspace/keeper/templeos-checksums.txt` or similar): SHA-256 of every file +
   the git tree hash. This is the keeper's seal; the archive itself stays pristine.
4. **Source canonical ISO(s) separately.** Davis's v5.03 ISO lives on archive.org and
   community mirrors. A keeper should fetch one, hash it, and preserve it as the
   *bootable reference* alongside (not inside) this source archive.
5. **Boot validation belongs to the keeper's build bench.** On a machine with QEMU,
   boot the canonical ISO, capture a screenshot/log, and file it as keeper evidence —
   not as part of this tree.
6. **Exec-bit observation (decision needed):** `Linux/TOSZ` and `Downloads/Linux/TOSZ`
   lost their executable bit in the clone. Check whether the canonical repo records
   them as executable in git (`git ls-tree` mode). If yes, the local clone dropped
   it and a fresh checkout restores it — no action in THIS tree either way.

## Decisions needed from Steve

1. **ISO custody:** approve fetching the canonical v5.03 ISO from archive.org (or a
   mirror of his choice) into keeper custody as the bootable reference? Which mirror?
2. **Integrity manifest:** approve creating keeper-owned SHA-256 manifest + git tree
   hash OUTSIDE the archive as the keeper's seal? (Does not touch the artifact.)
3. **Boot bench:** when a machine with QEMU is available, run the boot validation and
   file the screenshot/log as keeper evidence? Or keep this strictly source-tree
   preservation with no boot program at all?
4. **Exec bits:** leave the local checkout as-is (bits lost, archive pristine) or
   authorize a fresh clone so git restores canonical modes? The current tree stays
   untouched pending his word.
