# Maintainer: Berk Küçük <dev.berkkucukk@gmail.com>
#
# maze-hardening — Maze Linux's kernel & network hardening defaults: a single
# sysctl profile with conservative settings that raise the security posture
# without breaking normal desktop use, VPNs or Tor. Extracted from the ISO's
# airootfs overlay into a pacman package.
#
# The profile lands in /etc and is admin-tunable, so it is marked backup:
# pacman preserves local edits across upgrades (writes new versions as .pacnew).
#
# Also shipped (added in 1.0.0-4, after the eighth audit round found all three
# on a freshly installed machine — see MazeLinux/MD-Files/DENETIM-SEKIZINCI-TUR.md):
#   * usr/lib/modprobe.d/10-maze-broadcom-wl.conf + udev/rules.d/80-maze-broadcom-wl.rules
#     The proprietary Broadcom `wl` module carries a wildcard PCI alias and was
#     being auto-loaded on Intel-WiFi machines, tainting the kernel and
#     disabling the return-thunk mitigation ("Unpatched return thunk in use").
#     It is now blacklisted for alias loading and loaded explicitly by udev only
#     when a Broadcom (14e4) network controller exists.
#   * etc/systemd/resolved.conf.d/zz-maze-privacy.conf — MulticastDNS=no,
#     LLMNR=no. resolved's defaults answered mDNS/LLMNR on every LAN.
#   * The .install scriptlet masks passim.service (fwupd's LAN firmware-cache
#     sharing daemon) and avahi-daemon.{socket,service}, which passim pulls in;
#     together they advertised the machine over mDNS. Masked once, on install
#     and on the first upgrade to >= 1.0.0-4; an admin who unmasks them
#     afterwards is left alone.
#
# The .install scriptlet applies the sysctl profile immediately on a live
# system, so the hardening is effective without a reboot.
#
# Deliberately NOT shipped here:
#   * MAC randomisation — owned exclusively by maze-guard (a second
#     NetworkManager-based randomiser here conflicted with it).
#   * A global DNS override — forcing all DNS through Cloudflare via
#     [global-dns-domain-*] overrode VPN/DHCP resolvers (DNS leaks) and broke
#     Tor and split-DNS. DNS is left to the connection / VPN / DHCP.

pkgname=maze-hardening
pkgver=1.0.0
pkgrel=8
pkgdesc="Maze Linux kernel & network hardening defaults (sysctl profile, LAN-silence, Broadcom wl gating)"
arch=('any')
url="https://mazelinux.berkkucukk.com.tr"
license=('GPL3')
install="${pkgname}.install"
backup=(
  'etc/sysctl.d/99-maze-hardening.conf'
  'etc/systemd/resolved.conf.d/zz-maze-privacy.conf'
  # ── Adopted from the ISO's airootfs (2026-09) ──────────────────────────────
  # These used to exist only in the live image, so installed machines carried
  # them UNOWNED and no update ever reached them. They are in backup=() so the
  # takeover is silent: pacman does not treat an existing unowned file that the
  # package lists as a backup as a conflict — an identical copy is simply
  # adopted, a locally edited one is kept and the packaged one lands as .pacnew.
  # Without this, `pacman -Syu` on every installed Maze would stop with
  # "exists in filesystem" until the user ran --overwrite by hand.
  'etc/ssh/sshd_config.d/00-maze-hardening.conf'
  'etc/audit/rules.d/maze.rules'
  'etc/systemd/zram-generator.conf'
  'etc/systemd/oomd.conf.d/10-maze.conf'
  'etc/systemd/system/-.slice.d/10-oomd.conf'
  'etc/systemd/system/user@.service.d/10-oomd.conf'
  'etc/sudoers.d/00-maze-wheel'
)
source=()

package() {
  cp -a "${startdir}/maze-hardening/etc" "${pkgdir}/etc"
  cp -a "${startdir}/maze-hardening/usr" "${pkgdir}/usr"
  # sudo ignores a sudoers file that is group/world-writable or not owned by
  # root, and the directory itself belongs to the sudo package with 0750.
  chmod 0750 "${pkgdir}/etc/sudoers.d"
  chmod 0440 "${pkgdir}/etc/sudoers.d/00-maze-wheel"
}
