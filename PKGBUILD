# vim:set ft=sh:
# Maintainer: BlackIkeEagle <ike DOT devolder AT gmail DOT com>
# Contributor: Vyacheslav Konovalov <echo dnlhY2hrb25vdmFsb3ZAZ21haWwuY29tCg== | base64 -d>

pkgname=redis-desktop-manager
pkgver=2019.5
pkgrel=1
pkgdesc='Open source cross-platform Redis Desktop Manager based on Qt 5'
arch=('x86_64')
url="https://redisdesktop.com/"
license=('GPL')
depends=('qt5-base' 'qt5-imageformats' 'qt5-tools' 'qt5-declarative'
         'qt5-quickcontrols2' 'qt5-graphicaleffects' 'qt5-svg' 'qt5-charts'
         'libssh2' 'python-bitstring' 'python-cbor' 'python-msgpack'
         'python-phpserialize' 'python-rdbtools')
makedepends=('git')
conflicts=('redis-desktop-manager-bin')
source=("rdm::git://github.com/uglide/RedisDesktopManager.git#tag=${pkgver}"
    "link-python.patch")
sha512sums=('SKIP'
            'daf861d447bcb9c66384b6861b0197ef205277b5ae6a60afa009f6340e1341740e23506eb320ecdf5087ca1013c49da53420247a59f2ad89f13c24ea204887d5')

prepare() {
    cd rdm/
    patch -p1 -i "$srcdir/link-python.patch"
    git submodule update --init --recursive
    python3 build/utils/set_version.py "${pkgver}" > \
        src/version.h
}

build() {
    cd $srcdir/rdm/src
    qmake CONFIG+=release CLEAN_RPATH=true
    make
}

package() {
    cd $srcdir/rdm/src
    make INSTALL_ROOT="$pkgdir" install

    chmod +x "$pkgdir/opt/redis-desktop-manager/rdm.sh"

    install -dm755 "$pkgdir/usr/bin"
    ln -sf "/opt/redis-desktop-manager/rdm.sh" "$pkgdir/usr/bin/rdm"

    rm "$pkgdir/opt/redis-desktop-manager/qt.conf"
}
