# Maintainer: Charles Bos <charlesbos1 AT gmail>
# Contributor: Xiao-Long Chen <chenxiaolong@cxl.epac.to>
# Contributor: Jan de Groot <jgc@archlinux.org>
# Contributor: thn81 <root@scrat>

pkgname=libnautilus-extension-ubuntu
_ubuntu_rel=0ubuntu7
_translations=20130418
pkgver=3.10.1
pkgrel=3
pkgdesc="Library for extending the GNOME file manager with Ubuntu patches"
arch=('i686' 'x86_64')
license=('GPL')
depends=('libexif' 'gnome-desktop' 'exempi' 'gvfs' 'desktop-file-utils' 'gnome-icon-theme' 'dconf' 'libtracker-sparql' 'libnotify' 'nautilus-sendto' 'libunity' 'gtk3' 'libzeitgeist')
makedepends=('intltool' 'gobject-introspection' 'python')
url="http://www.gnome.org"
options=('!libtool' '!emptydirs')
provides=libnautilus-extension=${pkgver}
source=("http://ftp.gnome.org/pub/gnome/sources/nautilus/${pkgver%.*}/nautilus-${pkgver}.tar.xz"
        "https://launchpad.net/ubuntu/+archive/primary/+files/nautilus_${pkgver}-${_ubuntu_rel}.debian.tar.gz"
        "https://dl.dropboxusercontent.com/u/486665/Translations/translations-${_translations}-nautilus.tar.gz")
sha512sums=('a201d661104025b4b5ef4d0aa8ba7d083bb60e164c948e9932a78967f4046cc6e3afe7dcd5d20cfb99f500c4561555022cce4c39f2acc72370a515141798e90c'
            'adf8b2446b3e11f866bc28ec656ef3c778a8fe372c41f2b401bfc4ec6c66d483d75fdce396c64097988b4972b0e6713619a4027f458142e09e5a4f6eac3c4d19'
            '71c5d7992c4a2b52711dfc9afe37c8029800ecc77c2bb2e89286f10c00f50d4531beec87e62302e9799db243fe437fba9d180554d4ab6e8ce4bb48ec5969d3de')

prepare() {
  cd "${srcdir}/nautilus-${pkgver}"

  # Apply Ubuntu's patches

  # Disable patches
    # Don't use Ubuntu help
      sed -i '/15_use-ubuntu-help.patch/d' "${srcdir}/debian/patches/series"

  for i in $(grep -v '#' "${srcdir}/debian/patches/series"); do
    msg "Applying ${i} ..."
    patch -p1 -i "${srcdir}/debian/patches/${i}"
  done

  # For updating translations in desktop files
  ln -s "${srcdir}/debian" .

  #msg "Merging translations from ${_translations}"
  #rm -f po/LINGUAS po/*.pot
  #mv "${srcdir}"/po/*.pot po/
  #for i in "${srcdir}"/po/*.po; do
  #  FILE=$(sed -n "s|.*/${pkgname%-*}-||p" <<< ${i})
  #  mv ${i} po/${FILE}
  #  echo ${FILE%.*} >> po/LINGUAS
  #done

  sed -i '/gnome_bg_set_draw_background/d' libnautilus-private/nautilus-desktop-background.c
}

build() {
  cd "${srcdir}/nautilus-${pkgver}"

  autoreconf -vfi

  ./configure \
    --prefix=/usr \
    --sysconfdir=/etc \
    --localstatedir=/var \
    --disable-static \
    --libexecdir=/usr/lib/nautilus \
    --disable-nst-extension \
    --disable-update-mimedb \
    --disable-packagekit \
    --disable-schemas-compile \
    --disable-appindicator \
    --enable-unity

  make
}

package () {
  cd "${srcdir}/nautilus-${pkgver}"
  make DESTDIR="${pkgdir}/" install

  # Split libnautilus-extension
  cd ..
  mkdir -p n-e/usr/{lib,share}
  mv "${pkgdir}"/usr/include n-e/usr
  mv "${pkgdir}"/usr/lib/{girepository-1.0,pkgconfig} n-e/usr/lib
  mv "${pkgdir}"/usr/lib/libnautilus-extension.so* n-e/usr/lib
  mv "${pkgdir}"/usr/share/{gir-1.0,gtk-doc} n-e/usr/share

  # Delete Nautilus
  rm -rf "${pkgdir}"/{debian,etc,usr}

  # Move libnautilus-extension back to pkgdir
  mv n-e/* "${pkgdir}/"
}
