# Maintainer: sneekyfoxx <sneekyfoxx09@gmail.com>

pkgname=python3.13
pkgver=3.13.0
pkgrel=1
_pyver=3.13.0
_pybasever=3.13
_pymajver=3
pkgdesc="Python programming language interpreter"
arch=('i686' 'x86_64')
license=('PSF-2.0')
url="https://www.python.org/"
depends=('bzip2' 'expat' 'gdbm' 'libffi' 'libnsl' 'libxcrypt' 'openssl' 'zlib' 'llvm' 'llvm-libs' 'clang')
makedepends=('bluez-libs' 'mpdecimal' 'gdb')
optdepends=('sqlite' 'mpdecimal: for decimal' 'xz: for lzma' 'tk: for tkinter')
source=(https://www.python.org/ftp/python/${_pyver}/Python-${pkgver}.tar.xz)
md5sums=('726e5b829fcf352326874c1ae599abaa')

prepare() {
  cd "${srcdir}/Python-${pkgver}"

  # Ensure that we are using the system copy of various libraries (expat, zlib and libffi),
  # rather than copies shipped in the tarball
  rm -rf Modules/expat
  rm -rf Modules/zlib
  rm -rf Modules/_ctypes/{darwin,libffi}*
  rm -rf Modules/_decimal/libmpdec
}

build() {
  cd "${srcdir}/Python-${pkgver}"

  CFLAGS="${CFLAGS} -fno-semantic-interposition -fno-omit-frame-pointer -mno-omit-leaf-frame-pointer"
  ./configure ax_cv_c_float_words_bigendian=no \
              --prefix=/usr \
              --enable-shared \
              --with-computed-gotos \
              --with-lto \
              --enable-ipv6 \
              --with-system-expat \
              --with-dbmliborder=gdbm:ndbm \
              --with-mimalloc \
              --with-system-libmpdec \
              --enable-loadable-sqlite-extensions \
              --with-ensurepip \
              --enable-optimizations \
              --disable-gil \
              --enable-experimental-jit=yes \
              --with-tzpath=/usr/share/zoneinfo

  make EXTRA_CFLAGS="$CFLAGS"
}

package() {
  cd "${srcdir}/Python-${pkgver}"
  # altinstall: /usr/bin/pythonX.Y but not /usr/bin/python or /usr/bin/pythonX
  make DESTDIR="${pkgdir}" altinstall maninstall

  # Split tests
  rm -r "$pkgdir"/usr/lib/python*/{test,idlelib/idle_test}

  # Avoid conflicts with the main 'python' package.
  rm -f "${pkgdir}/usr/lib/libpython${_pymajver}.so"
  rm -f "${pkgdir}/usr/share/man/man1/python${_pymajver}.1"

  # Clean-up reference to build directory
  sed -i "s|$srcdir/Python-${pkgver}:||" "$pkgdir/usr/lib/python${_pybasever}/config-${_pybasever}-${CARCH}-linux-gnu/Makefile"

  # Add useful scripts FS#46146
  install -dm755 "${pkgdir}"/usr/lib/python${_pybasever}/Tools/{i18n,scripts}
  install -m755 Tools/i18n/{msgfmt,pygettext}.py "${pkgdir}"/usr/lib/python${_pybasever}/Tools/i18n/
  install -m755 Tools/scripts/{README,*py} "${pkgdir}"/usr/lib/python${_pybasever}/Tools/scripts/

  # License
  install -Dm644 LICENSE "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"
}
