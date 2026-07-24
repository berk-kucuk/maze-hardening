# maze-hardening

Maze Linux's **kernel & network hardening defaults**, extracted from the ISO's
`airootfs` overlay into a pacman package.

The guiding rule: settings must raise the security posture **without breaking
normal desktop use** — including VPNs (WireGuard/Mullvad), Tor and containers.
Anything that would override the user's own network choices is deliberately left
out.

## What's inside

| Path | Contents |
| ---- | -------- |
| `etc/sysctl.d/99-maze-hardening.conf` | Kernel & network hardening sysctls |

Highlights of the sysctl profile:

- **Kernel:** hide kernel pointers (`kptr_restrict=2`), root-only `dmesg`,
  YAMA `ptrace_scope=1`, disable unprivileged eBPF + harden the JIT, disable
  runtime `kexec`, restrict SysRq, no TTY line-discipline autoload.
- **Filesystem:** no setuid core dumps, hardlink/symlink/FIFO/regular-file
  protections in world-writable dirs.
- **Network:** reverse-path filtering in **loose mode** (anti-spoofing that
  still tolerates VPN/container asymmetric routing), SYN cookies, ignore ICMP
  redirects, reject source-routed packets, smurf-attack mitigation, log martians.

The file lives in `/etc` and is marked `backup`, so pacman preserves your local
edits across upgrades (new versions land as `.pacnew`).

## Ready out of the box

A `.install` scriptlet runs `sysctl --system` on install so the hardening
profile is live immediately — no reboot required.

## Not shipped here (on purpose)

- **MAC randomisation** — owned exclusively by **maze-guard**, whose privileged
  helper randomises the MAC. A second NetworkManager-based randomiser here
  conflicted with it and made maze-guard's MAC feature fail.
- **DNS override** — an earlier version forced all DNS through Cloudflare via
  NetworkManager's `[global-dns-domain-*]`. That overrode VPN- and DHCP-provided
  resolvers (causing DNS leaks) and broke Tor and split-DNS, so it was removed.
  DNS is left to the active connection / VPN / DHCP.

## Layout & building

```
maze-hardening/
├── PKGBUILD  build.sh  README.md  maze-hardening.install
└── maze-hardening/   # payload — verbatim mirror of the target filesystem
    └── etc/...
```

```sh
./build.sh
./build.sh --repo ../MazeLinux/localrepo
```
