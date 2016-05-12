# Maintainer: twilinx <twilinx@mesecons.net>

pkgname=gtk3-typeahead
pkgver=3.20.4
pkgrel=1
conflicts=(gtk3)
provides=("gtk3=$pkgver")
pkgdesc="GTK+ 3 with typeahead feature enabled for the file chooser widget"
arch=(i686 x86_64)
url="http://www.gtk.org/"
install=gtk3.install
depends=(atk cairo libcups libxcursor libxinerama libxrandr libxi libepoxy gdk-pixbuf2
         libxcomposite libxdamage pango shared-mime-info colord at-spi2-atk wayland libxkbcommon
         adwaita-icon-theme json-glib rest librsvg gtk-update-icon-cache wayland-protocols desktop-file-utils)
optdepends=('libcanberra: gtk3-widget-factory demo')
makedepends=(gobject-introspection libcanberra gtk-doc)
license=(LGPL)
source=(https://download.gnome.org/sources/gtk+/${pkgver:0:4}/gtk+-$pkgver.tar.xz
        settings.ini
        gtk-query-immodules-3.0.hook
        trap_possible_X_error.diff
        typeahead.patch)
sha256sums=('e7e3aaf54a54dd1c1ca0588939254abe31329e0bcd280a12290d5306b41ea03f'
            '01fc1d81dc82c4a052ac6e25bf9a04e7647267cc3017bc91f9ce3e63e5eb9202'
            'de46e5514ff39a7a65e01e485e874775ab1c0ad20b8e94ada43f4a6af1370845'
            '1204b67e45938304ce8500c4b9de52af5d2d90bcb4a2e28bc665f5b29803f28d'
            '5b3b9ba35378515359a25714e7c185783d15ec97c137e3564fcb1dbe658c941f')

prepare() {
    cd gtk+-$pkgver

    # Typeahead-specific changes
    patch gtk/gtkfilechooserwidget.c -i $srcdir/typeahead.patch

    NOCONFIGURE=1 ./autogen.sh

    # upstream fix for crashes with X servers not supporting XI2
    # https://bugzilla.gnome.org/show_bug.cgi?id=766233
    # https://github.com/GNOME/gtk/commit/7e7d7991cc8e7c7a2b50ce6530a8ebafd673516b
    patch -Np1 -i ${srcdir}/trap_possible_X_error.diff
}

build() {
    cd "gtk+-$pkgver"

    CXX=/bin/false ./configure --prefix=/usr \
        --sysconfdir=/etc \
        --localstatedir=/var \
        --disable-schemas-compile \
        --enable-x11-backend \
        --enable-broadway-backend \
        --enable-wayland-backend

    #https://bugzilla.gnome.org/show_bug.cgi?id=655517
    sed -i -e 's/ -shared / -Wl,-O1,--as-needed\0/g' libtool

    make -j4
}

package() {
    install=gtk3.install

    cd "gtk+-$pkgver"
    make DESTDIR="$pkgdir" install
    rm $pkgdir/usr/bin/gtk-update-icon-cache
    install -Dm644 ../settings.ini "$pkgdir/usr/share/gtk-3.0/settings.ini"
    install -Dm644 ../gtk-query-immodules-3.0.hook "$pkgdir/usr/share/libalpm/hooks/gtk-query-immodules-3.0.hook"
}
