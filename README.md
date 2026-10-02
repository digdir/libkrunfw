# libkrunfw (Digdir fork)

This is the Norwegian Digitalisation Agency's (Digdir) fork of [libkrunfw](https://github.com/libkrun/libkrunfw), a
library that bundles a Linux kernel so that [libkrun](https://github.com/containers/libkrun) can map it directly into
a guest. It tracks [superradcompany/libkrunfw](https://github.com/superradcompany/libkrunfw), the libkrunfw fork that
upstream Microsandbox builds from. See the upstream repository for the original documentation, including build
instructions.

## Branches

- `main-digdir` (default): the Digdir patch queue on the upstream commit that the Microsandbox release we build on
  pins. It is rebuilt on each synchronization with upstream, so its history is rewritten.
- `krunfw`: a mirror of upstream `krunfw`, updated by fast-forward only.

## Digdir modifications

The fork stays as close to upstream as possible and adds a change only where it is clearly needed, because every
change has to be carried forward on each synchronization. The changes are the commits on `main-digdir` after the
upstream base; list them with `git log $(git merge-base origin/krunfw origin/main-digdir)..origin/main-digdir`. Apart
from the Digdir documentation and CI, they change only the guest kernel configurations `config-libkrunfw_x86_64` and
`config-libkrunfw_aarch64`. We ship only the x86_64 and aarch64 kernels.

## Contributing and maintenance

[CONTRIBUTING-digdir.md](CONTRIBUTING-digdir.md) describes how to change the fork, and
[MAINTAINING-digdir.md](MAINTAINING-digdir.md) how it is synchronized with upstream and released. This repository
publishes no releases of its own; the Digdir Microsandbox runtime release builds and publishes the firmware.

Report problems with the Digdir changes or builds as issues in this repository. Problems that also exist upstream, and
changes that are useful beyond Digdir, belong upstream. Report security vulnerabilities as described in [Digdir's
security policy](https://github.com/digdir/.github/blob/main/SECURITY.md), not in public issues or pull requests.

## License

The upstream licensing applies to the Digdir modifications as well:

- **Linux kernel:** GPL-2.0-only ([LICENSE-GPL-2.0-only](LICENSE-GPL-2.0-only))
- **Files in the `patches` directory:** GPL-2.0-only
- **Library code, including automatically generated code:** LGPL-2.1-only
  ([LICENSE-LGPL-2.1-only](LICENSE-LGPL-2.1-only))

The library only stores the kernel and does not execute it, so programs linking against it are not required to be
licensed under GPL-2.0-only or LGPL-2.1-only. Binary distributions of the library must be accompanied by the source
code of the bundled kernel and of the library itself.
