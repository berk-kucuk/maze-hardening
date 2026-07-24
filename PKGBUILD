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
pkgrel=3
pkgdesc="Maze Linux kernel & network hardening defaults (sysctl profile)"
arch=('any')
url="https://mazelinux.berkkucukk.com.tr"
license=('GPL3')
install="${pkgname}.install"
backup=(
  'etc/sysctl.d/99-maze-hardening.conf'
)
source=()

package() {
  cp -a "${startdir}/maze-hardening/etc" "${pkgdir}/etc"
}
