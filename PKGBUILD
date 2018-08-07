# vim:set ft=sh:
# Maintainer: BlackIkeEagle <ike DOT devolder AT gmail DOT com>
# Contributor: Vyacheslav Konovalov <echo dnlhY2hrb25vdmFsb3ZAZ21haWwuY29tCg== | base64 -d>

pkgname=redis-desktop-manager
pkgver=0.9.4
pkgrel=2
pkgdesc='Open source cross-platform Redis Desktop Manager based on Qt 5'
arch=('x86_64')
url="https://redisdesktop.com/"
license=('GPL')
depends=('qt5-base' 'qt5-imageformats' 'qt5-tools' 'qt5-declarative' 'qt5-quickcontrols'
         'qt5-graphicaleffects' 'qt5-svg' 'qt5-charts' 'libssh2')
makedepends=('git' 'python2' 'cmake')
conflicts=('redis-desktop-manager-bin')
source=("rdm::git://github.com/uglide/RedisDesktopManager.git#tag=${_tag}"
        "qsshclient.diff")
sha512sums=('SKIP'
            '28e5d00c54234612821eecfa53f251d27b02e949b5cd820b175d46211cd637ac994382d9130f11d5ee197af3d214b4bfa0f45afd6e9d8b9648ef720ae19a263b')

prepare() {
    cd rdm/
    git submodule update --init --recursive
    python2 build/utils/set_version.py "${pkgver}" > \
        src/version.h

    ## crashreporter is broken right now
    #python2 build/utils/set_version.py "${pkgver}" > \
        #3rdparty/crashreporter/src/version.h

    sed -e '/sudo make install/d' \
        -i 3rdparty/qredisclient/3rdparty/qsshclient/configure

    (
        cd 3rdparty/qredisclient/3rdparty/qsshclient
        patch -p1 -i "$srcdir/qsshclient.diff"
    )

    (
        cd 3rdparty/gbreakpad
        git clone --depth 1 \
            -v https://chromium.googlesource.com/linux-syscall-support \
            src/third_party/lss
    )
}

build() {
    ## crashreporter is broken right now
    #cd "$srcdir/rdm/3rdparty/crashreporter"
    #qmake CONFIG+=release \
        #DESTDIR="$srcdir/rdm/bin/linux/release" \
        #QMAKE_LFLAGS_RPATH=""
    #make

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

    ## crashreporter is broken right now
    #install -Dm755 "$srcdir/rdm/bin/linux/release/crashreporter" \
        #"$pkgdir/opt/redis-desktop-manager/crashreporter"

    install -dm755 "$pkgdir/usr/bin"
    ln -sf "/opt/redis-desktop-manager/rdm.sh" "$pkgdir/usr/bin/rdm"

    rm "$pkgdir/opt/redis-desktop-manager/qt.conf"
}
