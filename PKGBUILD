# SPDX-License-Identifier: AGPL-3.0

#    ----------------------------------------------------------------------
#    Copyright © 2022, 2023, 2024, 2025, 2026  Pellegrino Prevete
#
#    All rights reserved
#    ----------------------------------------------------------------------
#
#    This program is free software: you can redistribute it and/or modify
#    it under the terms of the GNU Affero General Public License as published by
#    the Free Software Foundation, either version 3 of the License, or
#    (at your option) any later version.
#
#    This program is distributed in the hope that it will be useful,
#    but WITHOUT ANY WARRANTY; without even the implied warranty of
#    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
#    GNU Affero General Public License for more details.
#
#    You should have received a copy of the GNU Affero General Public License
#    along with this program.  If not, see <https://www.gnu.org/licenses/>.

# Maintainer:
#   Truocolo
#     <truocolo@aol.com>
#     <truocolo@0x6E5163fC4BFc1511Dbe06bB605cc14a3e462332b>
#   Pellegrino Prevete (dvorak)
#     <pellegrinoprevete@gmail.com>
#     <dvorak@0x87003Bd6C074C713783df04f36517451fF34CBEf>
#   Konstantin Gizdov
#     <arch at kge dot pw>
#   Caleb Maclennan
#     <caleb@alerque.com>
# Contributors:
#   Baptiste Jonglez
#     <archlinux at bitsofnetworks dot org>

if [[ ! -v "_os" ]]; then
  _os="$(
    uname \
      -o)"
fi
if [[ "${_os}" == "Android" ]]; then
  _libc="ndk-sysroot"
  _compiler="clang"
  _libcompiler="libllvm"
  _sh="dash"
elif [[ "${_os}" == "GNU/Linux" ]]; then
  _libc="glibc"
  _compiler="gcc"
  _libcompiler="libgcc"
  _sh="sh"
elif [[ "${_os}" == "Msys" ]]; then
  _libc="msys2-w32api-runtime"
  _libc_headers="msys2-w32api-headers"
  _compiler="gcc"
  _libcompiler="gcc-libs"
  _sh="sh"
else
  _msg=(
    "Unknown os '${_os}'."
  )
  msg \
    "${_msg[*]}"
  _libc="msys2-w32api-runtime"
  _libc_headers="msys2-w32api-headers"
  _compiler="gcc"
  _libcompiler="gcc-libs"
  _sh="sh"
fi
_evmfs_available="$(
  command \
    -v \
    "evmfs" || \
    true)"
if [[ ! -v "_evmfs" ]]; then
  if [[ "${_evmfs_available}" != "" ]]; then
    _evmfs="true"
  elif [[ "${_evmfs_available}" == "" ]]; then
    _evmfs="false"
  fi
fi
pkgbase=fig2dev
pkgname=(
  "fig2dev"
)
pkgver=3.2.9
pkgrel=2
pkgdesc="Format conversion utility that can be used with xfig"
arch=(
  'aarch64'
  'arm'
  'armv7l'
  'armv8l'
  'i686'
  'mips'
  'pentium4'
  'powerpc'
  'x86_64'
)
url="http://mcj.sourceforge.net/"
license=(
  "Xfig"
)
depends=(
  'bash'
  'bc'
  'ghostscript'
  "${_libc}"
  'libpng'
  'libxpm'
  'netpbm'
  'zlib'
)
makedepends=()
if [[ "${_os}" == "Msys" ]]; then
  makedepends+=(
  )
fi
conflicts=(
  'transfig'
)
replaces=(
  'transfig'
)
provides=(
  'transfig'
)
source=(
  "https://downloads.sourceforge.net/mcj/${pkgname}-${pkgver}.tar.xz"
  $pkgname-3.2.9-remove_broken_tests.patch
)
sha256sums=(
  '15e246c8d13cc72de25e08314038ad50ce7d2defa9cf1afc172fd7f5932090b1'
  '32e1fe1d99c76db7d49cb46245442cdf0fce693c3ebcde7b47913ebafb0c72fa'
)

prepare() {
  # remove broken tests:
  # https://sourceforge.net/p/mcj/tickets/171/
  patch \
    -Np1 \
    -d \
      "${pkgname}-${pkgver}" \
    -i \
    "../${pkgname}-3.2.9-remove_broken_tests.patch"
  # delete pre-generated test script
  rm \
    -v \
    "${pkgname}-${pkgver}/${pkgname}/tests/testsuite"
  # extract license file from sources
  sed \
    -n \
    '1,17p' \
    "${pkgname}-${pkgver}/${pkgname}/alloc.h" > \
    "Xfig.txt"
  cd \
    "${pkgname}-${pkgver}"
  autoreconf \
    -fiv
}

build() {
  cd \
    "${pkgname}-${pkgver}"
  "./configure" \
    --prefix="/usr" \
    --enable-transfig
  make
}

check() {
  cd \
    "${pkgname}-${pkgver}"
  make \
    check
}

package() {
  cd \
    "${pkgname}-${pkgver}"
  make \
    DESTDIR="${pkgdir}" \
    XFIGLIBDIR="/usr/share/xfig" \
    FIG2DEV_LIBDIR="/usr/share/fig2dev" \
    MANPATH="/usr/share/man" \
    install
  install \
    -vDm644 \
    "../Xfig.txt" \
    -t \
    "${pkgdir}/usr/share/licenses/${pkgname}/"
}
