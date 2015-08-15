# $Id$
# Maintainer: Dave Reisner <dreisner@archlinux.org>
# Contributor: Thomas Baechler <thomas@archlinux.org>

pkgname=cisco-vpnc
_pkgname=vpnc
_vpncver=0.5.3
_git=d2c5a77f3f0ea6ad80fc59158127d63ede81a6cb
pkgver=$_vpncver.svn527
pkgrel=1
pkgdesc="VPN client for cisco3000 VPN Concentrators - Pretend to be Cisco vpnclient"
url="http://www.unix-ag.uni-kl.de/~massar/vpnc/"
license=('GPL')
depends=('libgcrypt' 'openssl' 'iproute2')
optdepends=('openresolv: Let vpnc manage resolv.conf')
provides=(vpnc)
conflicts=(vpnc)
arch=('i686' 'x86_64')
source=("ftp://ftp.archlinux.org/other/$_pkgname/$_pkgname-$pkgver.tar.xz"
        "http://git.infradead.org/users/dwmw2/vpnc-scripts.git/blob_plain/$_git:/vpnc-script"
        'vpnc.conf')
backup=('etc/vpnc/default.conf')
md5sums=('db1908bc5cf2b70334c9f74a597a4623'
         '70198474faf36a84beaa11136f6c4d9c'
         'a3f4e0cc682f437e310a1c86ae198e45')

mktarball() {
  [[ -d tarball ]] || mkdir tarball

  pushd tarball

  svn co http://svn.unix-ag.uni-kl.de/vpnc/trunk
  _svnrev=$(cd trunk >/dev/null; svn info | awk '/^Revision:/ { print $2 }')

  find trunk/ -type d -name .svn -exec rm -rf {} +
  mv trunk "$pkgname-$_vpncver.svn$_svnrev"
  tar cJf "../$pkgname-$_vpncver.svn$_svnrev.tar.xz" "$pkgname-$_vpncver.svn$_svnrev/"

  popd
  rm -rf tarball
}

build() {
  cd "$srcdir/$_pkgname-$pkgver"
  sed -i 's/\tasprintf(&version, "Cisco Systems VPN Client %s:%s", VERSION, uts.sysname);/\tasprintf(\&version, "Cisco Systems VPN Client %s:%s", "4.8.00 (0490)", "Linux");/' config.c
  sed -i 's/PREFIX=\/usr\/local/PREFIX=\/usr/' Makefile

  # Build hybrid support
  sed -i 's|^#OPENSSL|OPENSSL|g' Makefile

  # fix resolvconf location for community/openresolv
  sed -i 's|/sbin/resolvconf|/usr&|g' "$srcdir/vpnc-script"

  make
}

package() {
  cd "$srcdir/$_pkgname-$pkgver"

  make DESTDIR="$pkgdir" PREFIX=/usr SBINDIR=/usr/bin install

  install -Dm644 "$srcdir"/vpnc.conf "$pkgdir"/etc/vpnc/default.conf
  install -Dm755 "$srcdir"/vpnc-script "$pkgdir"/etc/vpnc/vpnc-script
}
