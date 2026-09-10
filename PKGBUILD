# Maintainer: Gurov <thuggethugger1@duck.com>

pkgname=xfce4-panel
pkgver=4.20.8
pkgrel=1.1
pkgdesc="Panel for the Xfce desktop environment with Windows-like tasklist hotkeys"
arch=('x86_64')
url="https://docs.xfce.org/xfce/xfce4-panel/start"
license=('LGPL-2.1-only')
groups=('xfce4')
depends=('exo' 'garcon' 'gtk-layer-shell' 'libxfce4ui' 'xfconf' 'libwnck3' 'libdbusmenu-gtk3'
         'libxfce4windowing' 'hicolor-icon-theme' 'desktop-file-utils')
makedepends=('glib2-devel' 'xfce4-dev-tools')
source=("https://archive.xfce.org/src/xfce/xfce4-panel/${pkgver%.*}/xfce4-panel-$pkgver.tar.bz2"
  '0001-window-buttons-windows-taskbar-hotkeys.patch')
sha256sums=('d69cb1f377953aeb1fb9bdbcef12c246bea66586e3f2868f3b758e0e8ce3d3fe'
            'f9dc1b6530c9f4c9924274a0e6a6e270d39e9fa9639c702485d59bdfa68b5d1e')

prepare() {
  cd "$pkgname-$pkgver"
  patch -Np1 -i "../0001-window-buttons-windows-taskbar-hotkeys.patch"
}

build() {
  cd "$pkgname-$pkgver"
  ./configure \
    --prefix=/usr \
    --sysconfdir=/etc \
    --localstatedir=/var \
    --enable-x11 \
    --enable-wayland \
    --disable-introspection \
    --disable-vala \
    --disable-gtk-doc \
    --disable-debug
  make
}

check() {
  cd "$pkgname-$pkgver"
  make check
}

package() {
  cd "$pkgname-$pkgver"
  make DESTDIR="$pkgdir" install
}

# vim:set ts=2 sw=2 et:
