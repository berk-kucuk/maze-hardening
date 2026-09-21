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
pkgrel=4
pkgdesc="Maze Linux kernel & network hardening defaults (sysctl profile, LAN-silence, Broadcom wl gating)"
arch=('any')
url="https://mazelinux.berkkucukk.com.tr"
license=('GPL3')
install="${pkgname}.install"
backup=(
  'etc/sysctl.d/99-maze-hardening.conf'
  'etc/systemd/resolved.conf.d/zz-maze-privacy.conf'
)
source=()

package() {
  cp -a "${startdir}/maze-hardening/etc" "${pkgdir}/etc"
  cp -a "${startdir}/maze-hardening/usr" "${pkgdir}/usr"
}
