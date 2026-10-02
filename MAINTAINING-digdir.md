# Maintaining the Digdir fork

This repository publishes no releases of its own. The Digdir Microsandbox runtime release builds the kernel bundle
from the libkrunfw commit its `vendor/libkrunfw` submodule pins; the full procedure is in [Microsandbox's
MAINTAINING-digdir.md](https://github.com/digdir/microsandbox/blob/main-digdir/MAINTAINING-digdir.md). This document
covers what is specific to libkrunfw. How to change the fork day to day is in
[CONTRIBUTING-digdir.md](CONTRIBUTING-digdir.md).

## Digdir files

Files whose name contains `digdir` are Digdir's own, such as `CONTRIBUTING-digdir.md`, `MAINTAINING-digdir.md` and the
`check-digdir.yml` workflow, so they never collide with upstream files. A few files only work at a fixed path and
replace upstream's:

- `README.md` replaces upstream's. On a synchronization, keep ours and check whether upstream's change affects what
  ours says.
- `AGENTS.md` is ours. If upstream adds one, keep upstream's text and append our navigation as a section, as the
  Microsandbox fork does.
- `CODEOWNERS` replaces upstream's. Keep ours.

## Upstream workflows

Upstream's workflow files stay unchanged and are disabled with `gh workflow disable <file>`. When a synchronization
brings new upstream workflow files, disable them after moving `main-digdir`, unless they are useful for our CI.

## Synchronizing with upstream

Do this before the Microsandbox synchronization, because its submodule pins the rewritten commit.

1. Fast-forward `krunfw` from `upstream/krunfw`, and stop if that fails.
2. Take the new base from the libkrunfw commit that the selected Microsandbox release pins. If that commit is not on
   `krunfw`, such as a pull request head that was squash-merged, use the `krunfw` commit with the identical tree. If
   the base is unchanged, stop here.
3. Rebuild the patch queue on a `sync/libkrunfw-X.Y.Z` branch, where `X.Y.Z` is the Microsandbox release version, and
   compare it with the old queue using `git range-diff`.
4. Resolve kernel configuration conflicts option by option, and after a kernel update check that the Digdir options
   survive `olddefconfig` (see [Checking kernel configuration
   changes](CONTRIBUTING-digdir.md#checking-kernel-configuration-changes)).
5. Disable workflow files that upstream added (see [Upstream workflows](#upstream-workflows)).
6. After approval, make sure every commit a published runtime still uses is tagged, then move the branch with
   `git push --force-with-lease=main-digdir:<old-tip> origin sync/libkrunfw-X.Y.Z:main-digdir`.

## Tags and corresponding source

Every libkrunfw commit that a Microsandbox runtime release is built from is tagged `v<FULL_VERSION>-digdir.<n>`, with
`FULL_VERSION` from the `Makefile` and `n` increasing for each tagged commit with the same version. Runtime releases
built from the same commit share its tag. Tags are immutable and keep the commit reachable after `main-digdir` is
rewritten.

Each runtime release attaches the firmware's GPL-2.0 corresponding source: an archive of the tagged libkrunfw tree and
the Linux kernel tarball named by `KERNEL_VERSION` in the `Makefile`.
