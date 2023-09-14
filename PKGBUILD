# Maintainer: Bernhard Landauer <bernhard@manjaro.org>
# Maintainer: Philip Müller <philm[at]manjaro[dot]org>
# Archlinux credits:
# Massimiliano Torromeo <massimiliano.torromeo@gmail.com>
# Bob Fanger <bfanger(at)gmail>
# Filip <fila pruda com>, Det <nimetonmaili(at)gmail>

_linuxprefix=linux-xanmod
_extramodules=$(find /usr/lib/modules -type d -iname 6.5.3*xanmod* | rev | cut -d "/" -f1 | rev)
pkgname=$_linuxprefix-r8168
_pkgname=r8168
pkgver=8.051.02
pkgrel=65310
pkgdesc="A kernel module for Realtek 8168 network cards"
arch=('x86_64')
url="http://www.realtek.com.tw"
license=("GPL")
groups=("$_linuxprefix-extramodules")
depends=('glibc' "$_linuxprefix")
makedepends=("$_linuxprefix-headers")
provides=("$_pkgname=$pkgver")
source=("https://github.com/mtorromeo/r8168/archive/$pkgver/$_pkgname-$pkgver.tar.gz"
        "https://github.com/mtorromeo/r8168/releases/download/$pkgver/$_pkgname-$pkgver.tar.gz.asc"
        'linux61.patch'
        'linux65.patch')
sha256sums=('76f1c6f0b273d6a31bdb3e98c39a54f50a65766b99d485f9b4ddeda30dcd11d8'
            'SKIP'
            'b43a2ec8270124afe6fa23fafc1be156779e9a0d47db22e1583b60891bd286d5'
            'bc3ff8958d22ed85dc0f88fe48a5c4148c8d6b9dfbe481a7e791c7780dd542f0')
validpgpkeys=('0CADAACF70F64C654E131B3111675C743429DDEF') # Massimiliano Torromeo <massimiliano.torromeo@gmail.com>

prepare() {
  cd "$_pkgname-$pkgver"
  patch -p1 -i ../linux61.patch
  patch -p1 -i ../linux65.patch
}

build() {
  _kernver=$(find /usr/lib/modules -type d -iname 6.5.3*xanmod* | rev | cut -d "/" -f1 | rev)

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
  find "$pkgdir" -name '*.ko' -exec strip {} +
  find "$pkgdir" -name '*.ko' -exec xz {} +

  echo "blacklist r8169" | \
    install -Dm644 /dev/stdin "$pkgdir/usr/lib/modprobe.d/$pkgname.conf"

  # set the kernel we've built for inside the install script
  sed -i -e "s/EXTRAMODULES=.*/EXTRAMODULES=${_extramodules}/g" "${startdir}/${_pkgname}.install"
}
