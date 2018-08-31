# Maintainer:  Bartłomiej Piotrowski <bpiotrowski@mirantis.com>
# Based on: http://www.archlinux.org/packages/core/x86_64/libpcap

pkgname=libpcap-remote
provides=(libpcap)
conflicts=(libpcap)
pkgver=1.9.0
pkgrel=1
pkgdesc='A system-independent interface for user-level packet capture'
arch=('x86_64')
url='http://www.tcpdump.org/'
license=('BSD')
depends=('glibc' 'libnl' 'sh' 'libusbx' 'dbus')
makedepends=('flex' 'bluez-libs' 'cmake')
source=(http://www.tcpdump.org/release/libpcap-${pkgver}.tar.gz{,.sig}
    0001-CMake-correct-symlinks-creation.patch
    rpcapd.service rpcapd@.service
)
validpgpkeys=('1F166A5742ABB9E0249A8D30E089DEF1D9C15D0D') # The Tcpdump Group
sha256sums=('2edb88808e5913fdaa8e9c1fcaf272e19b2485338742b5074b9fe44d68f37019'
            'SKIP'
            '57465b510c784f4c9dfd7beae473c5ed7b99f674d02524811c8d5ae8ca846774'
            '838518fc07e8ce2dd8158bdcd0502874e204c8931bab9b09f00865ae95890088'
            '03a694cda5939f1d00fe54397287f9db6b48a329b9e6a5cef2d77377f8231e13')

prepare() {
  cd libpcap-${pkgver}
  patch -Np1 -i ../0001-CMake-correct-symlinks-creation.patch
  cd ..

  [ -d "${pkgname}-build" ] || mkdir "${pkgname}-build"
  cd "${pkgname}-build"

  cmake ../libpcap-${pkgver} \
    -DINET6=ON \
    -DBUILD_SHARED_LIBS=ON \
    -DENABLE_REMOTE=ON \
    -DPCAP_SUPPORT_PACKET_RING=ON \
    -DBUILD_WITH_LIBNL=ON \
    -DDISABLE_USB=OFF \
    -DDISABLE_BLUETOOTH=OFF \
    -DDISABLE_NETMAP=OFF \
    -DDISABLE_RDMA=OFF \
    -DDISABLE_DAG=ON \
    -DDISABLE_SEPTEL=ON \
    -DDISABLE_SNF=ON \
    -DDISABLE_TC=ON \
    -DBDEBUG=OFF \
    -DYYDEBUG=OFF \
    -DCMAKE_INSTALL_PREFIX=/usr \
    -DCMAKE_BUILD_TYPE=MINSIZEREL
}

build() {
  cd "${srcdir}/${pkgname}-build"
  make
  make rpcapd
}

package() {
  cd "${srcdir}/${pkgname}-build"
  make DESTDIR="${pkgdir}" install

  # install services
  cd ..
  install -Dm644 rpcapd.service "$pkgdir/usr/lib/systemd/system/rpcapd.service"
  install -Dm644 rpcapd@.service "$pkgdir/usr/lib/systemd/system/rpcapd@.service"
  cd libpcap-${pkgver}
  install -Dm644 rpcapd/rpcapd.socket "$pkgdir/usr/lib/systemd/system/rpcapd.socket"
}
