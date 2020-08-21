# Maintainer: Eli Schwartz <eschwartz@archlinux.org>
# Contributor: Iacopo Isimbaldi <isiachi@rhye.it>

# All my PKGBUILDs are managed at https://github.com/eli-schwartz/pkgbuilds

pkgname=zfs-dkms
pkgver=0.8.4
pkgrel=2
pkgdesc="Kernel modules for the Zettabyte File System."
arch=('any')
url="https://zfsonlinux.org/"
license=('CDDL')
conflicts=('spl-dkms')
provides=("ZFS-MODULE=${pkgver}" "SPL-MODULE=${pkgver}" 'spl-dkms')
# ambiguous, provided for backwards compat, pls don't use
provides+=('zfs')
replaces=('spl-dkms')
source=("https://github.com/zfsonlinux/zfs/releases/download/zfs-${pkgver}/zfs-${pkgver}.tar.gz"{,.asc}
        "https://github.com/openzfs/zfs/commit/d90015c5439167456d02585c25147742453d9aa6.patch"
        "0001-only-build-the-module-in-dkms.conf.patch")
sha256sums=('2b988f5777976f09d08083f6bebf6e67219c4c4c183c1f33033fb7e5e5eacafb'
            'SKIP'
            'e801bb4e820924b69289785976b280084b5b7d3bb2e9f9cc1a08710d6e5d898b'
            '780e590383fb00389c5e02ac15709b7a476d9e07d3c4935ed9eb67c951a88409')
b2sums=('776bcd6dfab8825c07d315085e288b29bf543d6957325d5d566b7b78c04505dde9bd25eb6684cb4a1b6a657de8a4e1290d04d2b9079d26d6b834a70f1ec3b569'
        'SKIP'
        '5375c5344ea88f6d2b1a4f9cf9c1efeae67a53ef487fe3962790f32d9dcff2622dd22f8f8b0f83b648fe1f490bd2e12e91f5d5fbfca274e1ec72aaa8f6c5e335'
        '1fdae935043d979b9241f07f8baa25a9a0367c24c31c84a59dfe8d6b468a523d8f49b68da3c7fd3194db6638f9d7bef046fc5e2669ce25d73c65009c16bf6c50')
validpgpkeys=('4F3BA9AB6D1F8D683DC2DFB56AD860EED4598027'  # Tony Hutter (GPG key for signing ZFS releases) <hutter2@llnl.gov>
              'C33DF142657ED1F7C328A2960AB9E991C6AF658B') # Brian Behlendorf <behlendorf1@llnl.gov>

prepare() {
    cd "${srcdir}"/${pkgname%-dkms}-${pkgver}

    # kernel 5.8 backport from https://github.com/openzfs/zfs/pull/10728
    patch -p1 -i ../d90015c5439167456d02585c25147742453d9aa6.patch

    patch -p1 -i ../0001-only-build-the-module-in-dkms.conf.patch

    # remove unneeded sections from module build
    sed -ri "/AC_CONFIG_FILES/,/]\)/{
/AC_CONFIG_FILES/n
/]\)/n
/^\s*(module\/.*|${pkgname%-dkms}.release|Makefile)/!d
}" configure.ac

    autoreconf -fi
}

build() {
    cd "${srcdir}"/${pkgname%-dkms}-${pkgver}

    ./scripts/dkms.mkconf -n ${pkgname%-dkms} -v ${pkgver} -f dkms.conf
}

package() {
    depends=("zfs-utils=${pkgver}" 'dkms')

    cd "${srcdir}"/${pkgname%-dkms}-${pkgver}

    dkmsdir="${pkgdir}/usr/src/${pkgname%-dkms}-${pkgver}"
    install -d "${dkmsdir}"/{config,scripts}
    cp -a configure dkms.conf Makefile.in META ${pkgname%-dkms}_config.h.in ${pkgname%-dkms}.release.in include/ module/ "${dkmsdir}"/
    cp config/config.* config/missing config/*sh "${dkmsdir}"/config/
    cp scripts/enum-extract.pl scripts/dkms.postbuild "${dkmsdir}"/scripts/
}
