# PKGBUILD source: https://github.com/oijfa/openmw-tes3mp-arch-pkg

pkgname=openmw-tes3mp
pkgver=openmw.0.39.0.3312.gd7cbfc98e
pkgrel=1
pkgdesc="An open-source engine reimplementation for the role-playing game Morrowind with multiplayer support."
arch=('i686' 'x86_64')
url="http://www.openmw.org"
license=('GPL3' 'MIT' 'custom')

depends=('unzip' 'openal' 'openscenegraph' 'mygui>=3.2.1' 'bullet' 'qt5-base' 'ffmpeg' 'sdl2' 'unshield' 'libxkbcommon-x11' 'tes3mp-raknet-git' 'terra-git' 'libtinfo') #add openmw after conflicts resolved
makedepends=('git' 'cmake' 'boost')

source=('git://github.com/TES3MP/openmw-tes3mp.git')
#source=("${pkgname}::https://github.com/TES3MP/openmw-tes3mp.git#tag=tes3mp-0.5.1")
sha1sums=('SKIP')

prepare() {
  #Setup dependencies
  DEPENDENCIES="${srcdir}/${pkgname}"/dependencies
  CALLFF_LOCATION="${DEPENDENCIES}"/callff

  echo -e ">> Making Dependencies Folder..."
  mkdir -p "${DEPENDENCIES}"

  echo -e ">> Retrieving CallFF..."
  if [ -d "${CALLFF_LOCATION}"/.git ]
    then
      echo -e ">>>> git exists, pulling..."
      (cd "${CALLFF_LOCATION}" && git pull --depth 1)
    else
      echo -e ">>>> Cloning..."
      git clone https://github.com/Koncord/CallFF "${DEPENDENCIES}/"callff --depth 1
  fi

  #Apply patch
  cd "${srcdir}/${pkgname}"
  git apply < "${srcdir}"/../0001-CMakeList-openmw-tes3mp.patch
}

pkgver() {
  cd "${srcdir}/${pkgname}"
  git describe --always | sed 's|openmw-tes3mp-\([0-9]\+.[0-9]\+.[0-9]\+\)|\1|' | sed 's|-|.|g'
}

build() {
  CORES="$(cat /proc/cpuinfo | awk '/^processor/{print $3}' | wc -l)"

  echo -e ">> Building CallFF"
  mkdir -p "${CALLFF_LOCATION}"/build
  cd "${CALLFF_LOCATION}"/build
  cmake ..
  make -j$CORES

  echo -e ">> Building OpenMW"
  cd "${srcdir}/${pkgname}"
  cmake -Wno-dev -DCMAKE_INSTALL_PREFIX=/usr \
      -DCallFF_LIBRARY="${CALLFF_LOCATION}"/build/src/libcallff.a \
      -DCallFF_INCLUDES="${CALLFF_LOCATION}"/include \
      -DBUILD_OPENMW=ON \
      -DBUILD_BROWSER=ON \
      -DBUILD_MYGUI_PLUGIN=OFF \
      -DBUILD_OPENMW_MP=OFF \
      -DBUILD_OPENCS=OFF \
      -DBUILD_BSATOOL=OFF \
      -DBUILD_ESMTOOL=OFF \
      -DBUILD_ESSIMPORTER=OFF \
      -DBUILD_LAUNCHER=OFF \
      -DBUILD_MWINIIMPORTER=OFF \
      -DBUILD_WIZARD=OFF
  make -j$CORES
}

package() {
  echo "${srcdir}/${pkgname%-git}"
  cd "${srcdir}/${pkgname%-git}"
  make DESTDIR="${pkgdir}" install
}
# vim:set ts=2 sw=2 et:
