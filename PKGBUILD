# Maintainer: BlackIkeEagle <ike DOT devolder AT gmail DOT com>
# Contributor: Vyacheslav Konovalov <echo dnlhY2hrb25vdmFsb3ZAZ21haWwuY29tCg== | base64 -d>

pkgname=redis-desktop-manager
_tag=0.9.2
pkgver=0.9.2
pkgrel=1
pkgdesc='Open source cross-platform Redis Desktop Manager based on Qt 5'
arch=('x86_64')
url="https://redisdesktop.com/"
license=('GPL')
depends=('qt5-base' 'qt5-charts' 'qt5-quickcontrols' 'libssh2')
makedepends=('git' 'python2')
conflicts=('redis-desktop-manager-bin')
source=("rdm::git://github.com/uglide/RedisDesktopManager.git#tag=${_tag}")
sha512sums=('SKIP')

prepare() {
    cd rdm/
    git submodule update --init --recursive
    git submodule add https://chromium.googlesource.com/linux-syscall-support 3rdparty/linux-syscall-support

    python2 build/utils/set_version.py "${_tag}" > src/version.h
    python2 build/utils/set_version.py "${_tag}" > 3rdparty/crashreporter/src/version.h

    _lssdir='3rdparty/gbreakpad/src/third_party/lss/'
    mkdir -p ${_lssdir}
    cp 3rdparty/linux-syscall-support/linux_syscall_support.h ${_lssdir}

    # fix rdm.sh
    sed -e '/^export/d' -i src/resources/rdm.sh

    # fix rdm.desktop
    sed -e 's/^Exec.*/Exec=rdm/' \
        -e 's/^Icon.*/Icon=rdm/' \
        -i src/resources/rdm.desktop
}

build() {
    cd $srcdir/rdm/3rdparty/crashreporter
    qmake CONFIG+=release DESTDIR="$srcdir/rdm/bin/linux/release" QMAKE_LFLAGS_RPATH=""
    make

    cd $srcdir/rdm/3rdparty/gbreakpad
    ./configure
    make

    cd $srcdir/rdm/src
    qmake CONFIG+=release CLEAN_RPATH=true
    make
}

package() {
    _instdir="$srcdir/rdm/bin/linux/release"
    _bindir="$pkgdir/usr/lib/redis-desktop-manager/bin"

    install -Dm755 "$_instdir/rdm" "$_bindir/rdm"
    install -Dm755 "$_instdir/crashreporter" "$_bindir/crashreporter"
    install -Dm755 "$srcdir/rdm/src/resources/rdm.sh" "$_bindir/rdm.sh"

    install -Dm644 "$srcdir/rdm/LICENSE" "$pkgdir/usr/share/licenses/$pkgname/LICENSE"
    install -Dm644 "$srcdir/rdm/src/resources/rdm.png" "$pkgdir/usr/share/pixmaps/rdm.png"
    install -Dm644 "$srcdir/rdm/src/resources/rdm.desktop" "$pkgdir/usr/share/applications/rdm.desktop"

    mkdir "$pkgdir/usr/bin"
    ln -sf "/usr/lib/redis-desktop-manager/bin/rdm.sh" "$pkgdir/usr/bin/rdm"
}
