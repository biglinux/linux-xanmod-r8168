# Maintainer: Bernhard Landauer <bernhard@manjaro.org>
# Maintainer: Philip Müller <philm[at]manjaro[dot]org>
# Archlinux credits:
# Massimiliano Torromeo <massimiliano.torromeo@gmail.com>
# Bob Fanger <bfanger(at)gmail>
# Filip <fila pruda com>, Det <nimetonmaili(at)gmail>

_linuxprefix=linux-xanmod
_extramodules=$(find /usr/lib/modules -type d -iname 6.5.9*xanmod* | rev | cut -d "/" -f1 | rev)
pkgname=$_linuxprefix-r8168
_pkgname=r8168
pkgver=8.052.01
pkgrel=65910
pkgdesc="A kernel module for Realtek 8168 network cards"
arch=('x86_64')
url="http://www.realtek.com.tw"
license=("GPL")
groups=("$_linuxprefix-extramodules")
depends=('glibc' "$_linuxprefix")
makedepends=("$_linuxprefix-headers")
provides=("$_pkgname=$pkgver")
source=("https://github.com/mtorromeo/r8168/archive/$pkgver/$_pkgname-$pkgver.tar.gz"
        "https://github.com/mtorromeo/r8168/releases/download/$pkgver/$_pkgname-$pkgver.tar.gz.asc")
sha256sums=('cd8ee58a260e9b654080d39e3a42e3a3fb821041ee79e631b4647d84120aa999'
            'SKIP')
validpgpkeys=('0CADAACF70F64C654E131B3111675C743429DDEF') # Massimiliano Torromeo <massimiliano.torromeo@gmail.com>

prepare() {
  cd "$_pkgname-$pkgver"
}

build() {
  _kernver=$(find /usr/lib/modules -type d -iname 6.5.9*xanmod* | rev | cut -d "/" -f1 | rev)

  cd "$_pkgname-$pkgver"

  # avoid using the Makefile directly -- it doesn't understand
  # any kernel but the current.

  make -C /usr/lib/modules/$_kernver/build M="$srcdir/$_pkgname-$pkgver/src" \
    ENABLE_USE_FIRMWARE_FILE=y \
    CONFIG_R8168_NAPI=y \
    CONFIG_R8168_VLAN=y \
    CONFIG_ASPM=y \
    ENABLE_S5WOL=y \
    ENABLE_EEE=y \
    modules
}

package() {
  cd "$_pkgname-$pkgver"
  install -Dm644 src/*.ko -t "$pkgdir/usr/lib/modules/$_extramodules/"
  find "$pkgdir" -name '*.ko' -exec strip --strip-debug {} +
  find "$pkgdir" -name '*.ko' -exec xz {} +

# We'll let mhwd-db handle blacklisting for now
#  echo "blacklist r8169" | \
#    install -Dm644 /dev/stdin "$pkgdir/usr/lib/modprobe.d/$pkgname.conf"

  # set the kernel we've built for inside the install script
  sed -i -e "s/EXTRAMODULES=.*/EXTRAMODULES=${_extramodules}/g" "${startdir}/${_pkgname}.install"
}
