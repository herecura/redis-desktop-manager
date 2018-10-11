# vim:set ft=sh:
# Maintainer: BlackIkeEagle <ike DOT devolder AT gmail DOT com>
# Contributor: Vyacheslav Konovalov <echo dnlhY2hrb25vdmFsb3ZAZ21haWwuY29tCg== | base64 -d>

pkgname=redis-desktop-manager
pkgver=0.9.8
pkgrel=1
pkgdesc='Open source cross-platform Redis Desktop Manager based on Qt 5'
arch=('x86_64')
url="https://redisdesktop.com/"
license=('GPL')
depends=('qt5-base' 'qt5-imageformats' 'qt5-tools' 'qt5-declarative' 'qt5-quickcontrols'
         'qt5-graphicaleffects' 'qt5-svg' 'qt5-charts' 'libssh2')
makedepends=('git' 'python2' 'cmake')
conflicts=('redis-desktop-manager-bin')
source=("rdm::git://github.com/uglide/RedisDesktopManager.git#tag=${_tag}")
sha512sums=('SKIP')

prepare() {
    cd rdm/
    git submodule update --init --recursive
    python2 build/utils/set_version.py "${pkgver}" > \
        src/version.h

    sed -e '/sudo make install/d' \
        -i 3rdparty/qredisclient/3rdparty/qsshclient/configure

    (
        cd 3rdparty/gbreakpad
        git clone --depth 1 \
            -v https://chromium.googlesource.com/linux-syscall-support \
            src/third_party/lss
    )
}

build() {
    cd "$srcdir/rdm/3rdparty/gbreakpad"
    ./configure
    make

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
