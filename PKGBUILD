# Based on the official Arch Linux flameshot 14.0.0-1 PKGBUILD.
# Arch package maintainers: Jiachen YANG, Caleb Maclennan, Robin Candau.

pkgname=flameshot
pkgver=14.0.0.native_sway1
_upstream_version=14.0.0
# This commit matches the source used by Arch's original 14.0.0-1 package.
# The upstream v14.0.0 tag later moved, so pin the tested source content.
_upstream_commit=da6121bdd6fe58060fd7c05fb8503520a150b715
_source_dir="flameshot-${_upstream_commit}"
pkgrel=1
pkgdesc="Flameshot with native per-output Sway capture"
url="https://github.com/flameshot-org/flameshot"
arch=('x86_64')
license=('GPL-3.0-or-later')
depends=('qt6-svg' 'hicolor-icon-theme' 'kguiaddons' 'grim')
makedepends=('qt6-tools' 'cmake' 'git')
optdepends=('gnome-shell-extension-appindicator: for system tray icon if you are using Gnome'
            'xdg-desktop-portal: for wayland support, you will need the implementation for your wayland desktop environment'
            'qt6-imageformats: for additional export image formats (e.g. tiff, webp, and more)')
source=("${pkgname}-${_upstream_version}.tar.gz::${url}/archive/${_upstream_commit}.tar.gz"
        'native-sway-capture.patch')
sha256sums=('ed1432cd7438ec9ef3c7c0194030399e3767a94b77acaadf7cf246ccf09c23b2'
            'a50bf3511888e236e62188e293f514184b17acaa7edb6b179c8af0537225cb53')

prepare() {
    cd "${srcdir}/${_source_dir}"
    patch -Np1 -i "${srcdir}/native-sway-capture.patch"
}

build() {
    cmake -B build -S "${_source_dir}" \
		-DCMAKE_BUILD_TYPE=None \
		-DCMAKE_INSTALL_PREFIX=/usr \
		-DUSE_WAYLAND_CLIPBOARD=ON \
		-DDISABLE_UPDATE_CHECKER=ON \
        -DUSE_KDSINGLEAPPLICATION=OFF \
        -Wno-dev
	cmake --build build
}

package() {
	DESTDIR="${pkgdir}" cmake --install build

	# Remove vendored static builds of shared libraries (used for single application, which we disable)
	# See https://github.com/flameshot-org/flameshot/blob/v13.0.0/packaging/rpm/fedora/flameshot.spec#L64-L70
	rm -rf "${pkgdir}/usr/include/QtColorWidgets/" \
		"${pkgdir}/usr/include/kdsingleapplication-qt6/" \
		"${pkgdir}/usr/lib/cmake/KDSingleApplication-qt6/" \
		"${pkgdir}/usr/lib/cmake/QtColorWidgets/" \
		"${pkgdir}/usr/lib/libQtColorWidgets.a" \
		"${pkgdir}/usr/lib/libkdsingleapplication-qt6.a" \
		"${pkgdir}/usr/lib/pkgconfig/QtColorWidgets.pc"
	rmdir "${pkgdir}/usr/include/" "${pkgdir}/usr/lib/"{cmake,pkgconfig,}
}
