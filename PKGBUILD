# Contributor: Lukas Jirkovsky <l.jirkovsky@gmail.com>
# Maintainer: Martin Peres <martin.peres@ensi-bourges.fr>

pkgname=gcc42
pkgver=4.2.4
pkgrel=10
epoch=0
pkgdesc="The GNU Compiler Collection"
arch=('i686' 'x86_64')
url="http://gcc.gnu.org"
license=('GPL')
depends=('glibc' 'mpfr')
makedepends=('binutils' 'mpfr')
provides=('gcc42')
conflicts=('gcc42')
options=('!libtool' '!emptydirs')
source=("ftp://gcc.gnu.org/pub/gcc/releases/gcc-${pkgver}/gcc-${pkgver}.tar.bz2" \
        "gcc_pure64.patch" "gcc-hash-style-both.patch" "linux-unwind.h.patch")
md5sums=('d79f553e7916ea21c556329eacfeaa16'                                                                                                                                                                                                                                    
         '4030ee1c08dd1e843c0225b772360e76'                                                                                                                                                                                                                                    
         'f3774714fddb0af7b4d85ea5bb5620ca'                                                                                                                                                                                                                                    
         'edce21b2ce295478d14d5dd988eb3287')

build() {
  cd "$srcdir/gcc-$pkgver"

  # May be necessary but it works for me without this locale
  #if ! locale -a | grep ^de_DE; then
  #  echo "You need the de_DE locale to build gcc."
  #  return 1
  #fi

  if [ "${CARCH}" = "x86_64" ]; then
    patch -Np1 < "$srcdir/gcc_pure64.patch" || true
  fi
  patch -Np0 < "$srcdir/gcc-hash-style-both.patch" || true
  patch -Np1 < "$srcdir/linux-unwind.h.patch" || true

  # Don't install libiberty
  sed -i 's|install_to_$(INSTALL_DEST) ||' libiberty/Makefile.in

  # Don't run fixincludes
  sed -i -e 's|\./fixinc\.sh|-c true|' gcc/Makefile.in

  if [ ! -d build ]; then
    mkdir build
  fi
  cd build

  unset CFLAGS
  unset CXXFLAGS
  ../configure --prefix=/usr --libdir=/usr/lib \
      --mandir=/usr/share/man --infodir=/usr/share/info \
      --enable-shared --disable-nls \
      --enable-languages=c,c++,fortran --enable-threads=posix \
      --enable-__cxa_atexit  --disable-multilib \
      --libexecdir=/usr/lib --enable-clocale=gnu --disable-libstdcxx-pch \
      --with-tune=generic \
      -program-suffix="-4.2" --enable-version-specific-runtime-libs
  make
}

package() {
  cd "$srcdir/gcc-$pkgver/build"
  make -j1 DESTDIR="$pkgdir/" install

  # remove conflicting files
  rm -rf "$pkgdir/usr/share"

  # remove libraries provided by the gcc-libs
  # fixes problems with linking some programs
  if [ "$CARCH" = "x86_64" ]; then
    rm $pkgdir/usr/lib/gcc/x86_64-unknown-linux-gnu/$pkgver/{libgomp,libmudflap,libssp,libstdc++}*
  else
    rm $pkgdir/usr/lib/gcc/i686-pc-linux-gnu/$pkgver/{libgomp,libmudflap,libssp,libstdc++}*
  fi

}

# vim:set ts=2 sw=2 et:
