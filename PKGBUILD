#!/bin/bash

# Based on the original package by Felix Yan <felixonmars@archlinux.org>

# Disable various shellcheck rules that produce false positives in this file.
# Repository rules should be added to the .shellcheckrc file located in the
# repository root directory, see https://github.com/koalaman/shellcheck/wiki
# and https://archiv8.github.io for further information.
# shellcheck disable=SC2034,SC2154
# [ToDo]: Add files: User documentation
# [ToDo]: Add files: Tooling
# [ToDo]: Clean out unnecessary variables
# [FixMe]: Namcap warnings and errors

# Maintainer: Ross Clark <https://github.com/Archiv8/acorn/discussions>
# Contributor: Ross Clark <https://github.com/Archiv8/acorn/discussions>

# pkgbase=
pkgname="acorn"
pkgver=8.7.1
pkgrel=1
# epoch=
pkgdesc="A tiny, fast JavaScript parser written in JavaScript."
arch=(
  "any"
)
url="https://github.com/acornjs/acorn"
license=(
  "MIT"
  )
# groups
depends=(
  "nodejs"
)
# optdepends=()
makedepends=(
  "npm"
  "jq"
)
# checkdepends=()
# provides=()
# conflicts=()
# replaces=()
# backup=()
# options=()
# install=
# changelog="CHANGELOG.md"
source=(
  "https://registry.npmjs.org/${pkgname}/-/${pkgname}-$pkgver.tgz"
)
noextract=("${pkgname}-$pkgver.tgz")
# validpgpkeys=()
sha512sums=(
  "5f1e78b8b250675f652b28055ce5acb1c2946ec0595b408fca43e155076121e230adb3e6273a9e0120c89dcf272819e9fc94fa8a04ecf36a8f5f3d3af47f08fc"
)

package() {

  # Ensure cache is set when install is run in order to avoid littering user's home directory
  printf "\e[1;32m==>\e[0m \e[38;5;248mBuilding nodejs package... \e[0m\n"
  printf "    This may take some time depending on the size of the package and number of dependencies to download... \e[0m\n"

  npm install --global \
    --cache "$srcdir/npm-cache" \
    --prefix "$pkgdir"/usr "$srcdir"/${pkgname}-$pkgver.tgz

  # Fix incorrect user / group as highlighted by namcap
  printf "\e[1;32m==>\e[0m \e[38;5;248mCorrecting possible ownership issues\e[0m\n"
  while IFS= read -r fsitem; do
    chown -h root:root "$fsitem"
  done < <(find "$pkgdir" -not -group root -not -user root)

  # Non-deterministic race in npm gives 777 permissions to random directories.
  # See https://github.com/npm/npm/issues/9359 for details.
  printf "\e[1;32m==>\e[0m \e[38;5;248mCorrecting possible permission issues on directories\e[0m\n"
  find "$pkgdir/usr" -type d -exec chmod 755 '{}' +

  # Remove references to $pkgdir
  printf "\e[1;32m==>\e[0m \e[38;5;248mRemoving references to \$pkgdir\e[0m\n"
  find "$pkgdir" -type f -name package.json -print0 | xargs -0 sed -i "/_where/d"

  # Remove references to $srcdir
  printf "\e[1;32m==>\e[0m \e[38;5;248mRemoving references to \$srcdir \e[0m\n"
  local tmppackage="$(mktemp)"
  local pkgjson="$pkgdir/usr/lib/node_modules/${pkgname}/package.json"
  jq '.|=with_entries(select(.key|test("_.+")|not))' "$pkgjson" > "$tmppackage"
  mv "$tmppackage" "$pkgjson"
  chmod 644 "$pkgjson"

  # Install license
  install -dm755 "${pkgdir}/usr/share/licenses/${pkgname}"
  install -m644 "${pkgdir}/usr/lib/node_modules/${pkgname}/LICENSE" "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"
}
