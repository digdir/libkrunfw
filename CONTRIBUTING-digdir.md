# Contributing to the Digdir fork

How to change this fork. How it is synchronized with upstream and how its firmware is released is in
[MAINTAINING-digdir.md](MAINTAINING-digdir.md).

## Patch queue

`main-digdir` is an ordered patch queue on top of an upstream base.

- A change is a pull request against `main-digdir`, checked by CI and merged with a rebase merge. Commits are only
  folded and reordered when the queue is rebuilt on a synchronization.
- Each commit is one coherent change that could be offered upstream on its own, with its documentation in the same
  commit, so that dropping a commit drops everything that came with it.
- The queue is ordered: documentation and governance, CI, then kernel changes.
- Change upstream files only as much as a patch needs; every changed line can conflict on the next synchronization.
- A change to an upstream file not yet named in the README's Digdir modifications section adds it there, so that the
  source archive published with each runtime names every modified file.
- Write code, comments, commit messages and docs in US English.

## Checking kernel configuration changes

Kconfig silently drops an option whose dependencies are missing, so check the generated `.config`, not the
`config-libkrunfw_*` file. The `Makefile`'s source target downloads the kernel, applies the patches, installs the
configuration for the host architecture and runs `olddefconfig` without building:

```sh
make "$(awk '$1 == "KERNEL_VERSION" { print $3; exit }' Makefile)"
grep -E '^(# )?CONFIG_<OPTION>[= ]' linux-*/.config
```

Remove `linux-*/` before checking another architecture with `ARCH=arm64`. A full build is `make kernel.c`.

## Referencing upstream

When a commit, pull request, issue or comment here mentions an upstream issue or pull request, GitHub adds a
cross-reference to it that upstream's maintainers see. Avoid that noise:

- Cite upstream changes by short commit SHA. Never mention upstream issues, pull requests or security advisories,
  whether as `#123`, `owner/repo#123`, a link or a GHSA identifier.
- Refer to this fork's issues and pull requests as `digdir/libkrunfw#123`, which always resolves to this repository.

Before pushing, this prints every reference in the commit messages that is not to this fork:

```sh
git log --format=%B <base>..HEAD \
  | grep -o -E '([[:alnum:]_.-]+/[[:alnum:]_.-]+)?#[0-9]+|GHSA-[[:alnum:]-]+|github\.com/[^/]+/[^/]+/(issues|pull)/[0-9]+' \
  | grep -v -E '^(digdir/libkrunfw#|github\.com/digdir/libkrunfw/)'
```
