# Maintainer: Dennis Maina <dennismyner@gmail.com>
pkgname=littlenavmap
pkgver=3.0.18
_atoolsver=4.0.18
_xpconnectver=1.2.2
_navconnectver=3.0.17
_marblever=1.1
_xpsdkver=301
pkgrel=1
epoch=
pkgdesc="A Free Open Source Flight Planner, Navigation Tool, Moving
Map, Airport Search, and Airport Information System for
Flight Simulator X, Prepar3D, Microsoft Flight Simulator
2020, and X-Plane"
arch=(x86_64)
url="https://albar965.github.io/littlenavmap.html"
license=("GPL3")
groups=()
depends=(qt5-base qt5-svg qt5-declarative qt5-imageformats curl glib2)
makedepends=(git cmake patchelf)
checkdepends=()
optdepends=()
provides=()
conflicts=(littlenavmap-bin)
replaces=()
backup=()
options=()
install=
changelog=
#it's either this or recompile qt5-base as static :(
#we only need it for the xplane plugin
_binary=LittleNavmap-linux-ubuntu-22.04-${pkgver}.tar.xz
source=(
        "atools-v$_atoolsver.zip::https://github.com/albar965/atools/archive/refs/tags/v$_atoolsver.zip"
        "http://developer.x-plane.com/wp-content/plugins/code-sample-generation/sdk_zip_files/XPSDK$_xpsdkver.zip"
        "littlexpconnect-v$_xpconnectver.zip::https://github.com/albar965/littlexpconnect/archive/refs/tags/v$_xpconnectver.zip"
        "littlenavconnect-v$_navconnectver.zip::https://github.com/albar965/littlenavconnect/archive/refs/tags/v$_navconnectver.zip"
        "littlenavmap-v$pkgver.zip::https://github.com/albar965/littlenavmap/archive/refs/tags/v$pkgver.zip"
        "qt.conf"
        "LittleNavmap.desktop"
        "https://github.com/albar965/littlenavmap/releases/download/v${pkgver}/${_binary}"
    )
noextract=()
sha256sums=('0ca7a85a0936e413a6f12ea39dcfae3aaa7014509dda0d6537c4feeae85c27a3'
            'bf0b38ee82283ce18418cf37af3ace83a2cf8e2e88b1951e337be415961c5cbc'
            '174c49adf296fb31c24276eea09ed978557e2aa7622f4dbb818a6a7f146d43f2'
            'c4ecfb71d33ae2e23666e75b34647788ccbb92fc1361e0d4bec89bd1ca634889'
            'a64ce55a95b004f5f3993e31b32eacbe68cc13dc60d7fb49d387b46d0be9d66f'
            '1a47df8eda4bb56c52180a521ac0b573e68b9be23344cecae1685dbe49d12be4'
            'cd8009076d1f6c4300f98089eeb8acb5ea09fdb3bbafe83ccce910f0a05da51d'
            '76e0637ea1af9d206e316e87ac25dbbe92b565d565fa745a0c9192568c6905ea')
validpgpkeys=()

prepare() {
    # THESE SHOULD BE GLOBAL VARIABLES BUT makepkg seems to not have $srcdir available
    # outside of the functions??
    _builddir="$srcdir/$pkgname-$pkgver-builddir"
    _deploydir="$srcdir/deploy"

    mkdir -p "$_builddir"
    mkdir -p "$_deploydir"
    sed -i "s/@VERSION@/$pkgver/" "$srcdir/LittleNavmap.desktop"

    git -C "$srcdir" clone --branch lnm/$_marblever --depth 1 https://github.com/albar965/marble.git
}

build() {
    # THESE SHOULD BE GLOBAL VARIABLES BUT makepkg seems to not have $srcdir available
    # outside of the functions??
    _builddir="$srcdir/$pkgname-$pkgver-builddir"
    _deploydir="$srcdir/deploy"

    _atools_srcdir="$srcdir/atools-$_atoolsver"
    _atools_builddir="$_builddir/atools"

    _marble_srcdir="$srcdir/marble"
    _marble_builddir="$_builddir/marble"
    _marble_installdir="$_marble_builddir/marble-rel"
    
    _littlenavmap_srcdir="$srcdir/littlenavmap-$pkgver"
    _littlenavmap_builddir="$_builddir/littlenavmap"

    _littlenavconnect_srcdir="$srcdir/littlenavconnect-$_navconnectver"
    _littlenavconnect_builddir="$_builddir/littlenavconnect"

    _littlexpconnect_srcdir="$srcdir/littlexpconnect-$_xpconnectver"
    _littlexpconnect_builddir="$_builddir/littlexpconnect"

    # atools
    mkdir -p "$_atools_builddir"
    cd "$_atools_builddir"
    ATOOLS_NO_CRASHHANDLER=true qmake "$_atools_srcdir/atools.pro" -spec linux-g++ CONFIG+=release
    make -j$(nproc)

    # marble
    mkdir -p "$_marble_builddir"
    cd "$_marble_builddir"
    #TODO: remove old cmake
    CMAKE_POLICY_VERSION_MINIMUM=3.5 cmake -S "$_marble_srcdir" -B . -DCMAKE_BUILD_TYPE=Release -DSTATIC_BUILD=TRUE -DQTONLY=TRUE -DBUILD_MARBLE_EXAMPLES=NO -DBUILD_INHIBIT_SCREENSAVER_PLUGIN=NO -DBUILD_MARBLE_APPS=NO -DBUILD_MARBLE_EXAMPLES=NO -DBUILD_MARBLE_TESTS=NO -DBUILD_MARBLE_TOOLS=NO -DBUILD_TESTING=NO -DBUILD_WITH_DBUS=NO -DMARBLE_EMPTY_MAPTHEME=YES -DMOBILE=NO -DWITH_DESIGNER_PLUGIN=NO -DWITH_Phonon=NO -DWITH_Qt5Location=NO -DWITH_Qt5Positioning=NO -DWITH_Qt5SerialPort=NO -DWITH_ZLIB=NO -DWITH_libgps=NO -DWITH_libshp=NO -DWITH_libwlocate=NO -DCMAKE_INSTALL_PREFIX="$_marble_installdir" -DEXEC_INSTALL_PREFIX="$_marble_installdir"
    make -j$(nproc)
    make install

    # littlenavmap
    mkdir -p "$_littlenavmap_builddir"
    cd "$_littlenavmap_builddir"
    ATOOLS_NO_CRASHHANDLER=true ATOOLS_INC_PATH="$_atools_srcdir/src" ATOOLS_LIB_PATH="$_atools_builddir" MARBLE_INC_PATH="$_marble_installdir/include" DEPLOY_BASE="$_deploydir" MARBLE_LIB_PATH="$_marble_installdir/lib" qmake "$_littlenavmap_srcdir/littlenavmap.pro" -spec linux-g++ CONFIG+=release
    make copydata
    make -j$(nproc)
    make deploy

    #cleanup
    eval rm -fr "$_deploydir/Little\ Navmap/lib/{libQt*,libicu*,iconengines,imageformats,platform*,printsupport,sqldrivers} $_deploydir/Little\ Navmap/qt.conf"

    # littlanavconnect
    mkdir -p "$_littlenavconnect_builddir"
    cd "$_littlenavconnect_builddir"
    ATOOLS_NO_CRASHHANDLER=true ATOOLS_INC_PATH="$_atools_srcdir/src" ATOOLS_LIB_PATH="$_atools_builddir" DEPLOY_BASE="$_deploydir" qmake "$_littlenavconnect_srcdir/littlenavconnect.pro" -spec linux-g++ CONFIG+=release
    make -j$(nproc)
    make deploy
    eval rm -fr "$_deploydir/Little\ Navconnect/lib $_deploydir/Little\ Navconnect/qt.conf"
}

package() {
    # THESE SHOULD BE GLOBAL VARIABLES BUT makepkg seems to not have $srcdir available
    # outside of the functions??
    _deploydir="$srcdir/deploy"
    _approot="usr/lib/littlenavmap-$pkgver"
    _approot_pkg="$pkgdir/$_approot"

    mkdir -p "${pkgdir}/usr/bin"
    mkdir -p "${pkgdir}/usr/lib"
    mkdir -p "${pkgdir}/usr/share/applications"

    cp -r "$_deploydir" "$_approot_pkg"

    cp "$srcdir/qt.conf" "$_approot_pkg/Little Navmap"
    cp "$srcdir/qt.conf" "$_approot_pkg/Little Navconnect"
    cp "$srcdir/LittleNavmap.desktop" "${pkgdir}/usr/share/applications"

    cp -r "$srcdir/LittleNavmap-linux-ubuntu-22.04-$pkgver/Little Xpconnect" "$_approot_pkg"
    ln -sf "/$_approot/Little Navmap/littlenavmap" "${pkgdir}/usr/bin/littlenavmap"
}

