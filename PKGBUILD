# Maintainer: Bernhard Landauer <bernhard@manjaro.org>
# Maintainer: Philip Müller <philm[at]manjaro[dot]org>
# Archlinux credits:
# Massimiliano Torromeo <massimiliano.torromeo@gmail.com>
# Bob Fanger <bfanger(at)gmail>
# Filip <fila pruda com>, Det <nimetonmaili(at)gmail>

_linuxprefix=linux-xanmod
_extramodules=$(find /usr/lib/modules -type d -iname 6.4.10*xanmod* | rev | cut -d "/" -f1 | rev)
pkgname=$_linuxprefix-r8168
_pkgname=r8168
pkgver=8.050.03
pkgrel=64101
pkgdesc="A kernel module for Realtek 8168 network cards"
url="http://www.realtek.com.tw"
license=("GPL")
arch=('x86_64')
depends=('glibc' "$_linuxprefix")
makedepends=("$_linuxprefix-headers")
provides=("$_pkgname=$pkgver")
groups=("$_linuxprefix-extramodules")
source=("https://github.com/mtorromeo/r8168/archive/refs/tags/$pkgver.tar.gz"
        'linux518.patch' 'linux519.patch' 'linux61.patch')
sha256sums=('76f9e7c26a8ade7b01dd34060f5b17d74387f15e9b6baa6dbba8c43634a31ce6'
            'd8d542770e504775600f686d03412a37cc32489872be7aeb388b5b08d9806096'
            'f19c663f278096a93b2fc80222e208a54ab8677f6d7eeb9c15150c7c55ec2eff'
            'b43a2ec8270124afe6fa23fafc1be156779e9a0d47db22e1583b60891bd286d5')

install=$_pkgname.install

prepare() {
    cd "$_pkgname-$pkgver"
    patch -p1 -i ../linux518.patch
    patch -p1 -i ../linux519.patch
    patch -p1 -i ../linux61.patch
}

build() {
    _kernver=$(find /usr/lib/modules -type d -iname 6.4.10*xanmod* | rev | cut -d "/" -f1 | rev)

    cd "$_pkgname-$pkgver"

    # avoid using the Makefile directly -- it doesn't understand
    # any kernel but the current.

    make -C /usr/lib/modules/$_kernver/build \
      M="$srcdir/$_pkgname-$pkgver/src" \
      EXTRA_CFLAGS="-DCONFIG_R8168_NAPI -DCONFIG_R8168_VLAN -DCONFIG_ASPM -DENABLE_S5WOL -DENABLE_EEE" \
      modules
}

package() {
    cd "$_pkgname-$pkgver/src"
    install -D -m644 $_pkgname.ko "$pkgdir/usr/lib/modules/$_extramodules/$_pkgname.ko"

    # set the kernel we've built for inside the install script
    sed -i -e "s/EXTRAMODULES=.*/EXTRAMODULES=${_extramodules}/g" "${startdir}/${_pkgname}.install"

    find "$pkgdir" -name '*.ko' -exec gzip -9 {} \;
}
