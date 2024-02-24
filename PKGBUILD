# Maintainer: Philip Müller <philm[at]manjaro[dot]org>
# Maintainer: Bernhard Landauer <bernhard@manjaro.org>

# Arch credits:
# Based on the file created for Arch Linux by:
# Massimiliano Torromeo <massimiliano.torromeo@gmail.com>
# Bob Fanger <bfanger(at)gmail>
# Filip <fila pruda com>, Det <nimetonmaili(at)gmail>

_linuxprefix=linux-xanmod

_module=r8168
pkgname="${_linuxprefix}-${_module}"
pkgver=8.052.01
pkgrel=67610
pkgdesc="A kernel module for Realtek 8168 network cards"
arch=('x86_64')
url="http://www.realtek.com.tw"
license=("GPL")
groups=("${_linuxprefix}-extramodules")
depends=('glibc' "${_linuxprefix}")
makedepends=("${_linuxprefix}-headers")
source=("https://github.com/mtorromeo/r8168/archive/$pkgver/${_module}-$pkgver.tar.gz"
        "https://github.com/mtorromeo/r8168/releases/download/$pkgver/${_module}-$pkgver.tar.gz.asc")
sha256sums=('cd8ee58a260e9b654080d39e3a42e3a3fb821041ee79e631b4647d84120aa999'
            'SKIP')
validpgpkeys=('0CADAACF70F64C654E131B3111675C743429DDEF') # Massimiliano Torromeo <massimiliano.torromeo@gmail.com>

prepare() {
  cd "${_module}-$pkgver"
}

build() {
  _kernver="$(cat /usr/src/${_linuxprefix}/version)"
  _kernver="$(cat /usr/src/${_linuxprefix}/version)"

  cd "${_module}-$pkgver"

  # avoid using the Makefile directly -- it doesn't understand
  # any kernel but the current.

  make -C /usr/lib/modules/${_kernver}/build M="$srcdir/${_module}-$pkgver/src" \
    ENABLE_USE_FIRMWARE_FILE=y \
    CONFIG_R8168_NAPI=y \
    CONFIG_R8168_VLAN=y \
    CONFIG_ASPM=y \
    ENABLE_S5WOL=y \
    ENABLE_EEE=y \
    modules
}

package() {
  _kernver="$(cat /usr/src/${_linuxprefix}/version)"

  cd "${_module}-$pkgver"
  install -Dm644 src/*.ko -t "$pkgdir/usr/lib/modules/${_kernver}/extramodules/"
  find "$pkgdir" -name '*.ko' -exec strip --strip-debug {} +
  find "$pkgdir" -name '*.ko' -exec xz {} +

# We'll let mhwd-db handle blacklisting for now
#  echo "blacklist r8169" | \
#    install -Dm644 /dev/stdin "$pkgdir/usr/lib/modprobe.d/$pkgname.conf"
}
