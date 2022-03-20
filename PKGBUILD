#!/bin/bash

# Disable various shellcheck rules that produce false positives in this file.
# Repository rules should be added to the .shellcheckrc file located in the
# repository root directory, see https://github.com/koalaman/shellcheck/wiki
# and https://archiv8.github.io for further information.
# shellcheck disable=SC2034,SC2154
# ToDo: Add files: User documentation
# ToDo: Add files: Tooling
# FixMe: Namcap warnings and errors

# Maintainer: Ross Clark <archiv8@artisteducator.com>
# Contributor: Ross Clark <archiv8@artisteducator.com>


_langname="nodejs"
_relname="acorn"
_repo="https://registry.npmjs.org"


# pkgbase=
pkgname="${_relname}"
pkgver=8.7.0
pkgrel=1
# epoch=
pkgdesc="A tiny, fast JavaScript parser written in JavaScript."
arch=("any")
url="https://eslint.org"
license=("MIT")
# groups
depends=("nodejs")
# optdepends=()
makedepends=("npm" "jq")
# checkdepends=()
# provides=()
# conflicts=()
# replaces=()
# backup=()
# options=()
# install=
# changelog="CHANGELOG.md"
source=(
"$_repo/$_relname/-/$_relname-$pkgver.tgz"
)
noextract=("$_relname-$pkgver.tgz")
# validpgpkeys=()
sha512sums=(
  "57f2c6af500fcbe3d723029e6c45ab9193f0a1ea05fb0d6388e0549aec6e89421a387b686fc41cf414eb628ed5b88e5f47cb6ab32f9bb80d96168a11e6896abd"
)

package() {
  # Ensure cache is set when install is run in order to avoid littering user's home directory
printf "\e[1;32m==>\e[0m \e[38;5;248mBuilding nodejs package... \e[0m\n"
printf "    This may take some time depending on the size of the package and number of dependencies to download... \e[0m\n"

npm install --global \
--cache "$srcdir/npm-cache" \
--prefix "$pkgdir"/usr "$srcdir"/$_relname-$pkgver.tgz

# Fix incorrect user / group as highlighted by namcap
printf "\e[1;32m==>\e[0m \e[38;5;248mCorrecting possible ownership issues\e[0m\n"
while IFS= read -r fsitem; do
  chown -h root:root "$fsitem"
done <   <(find "$pkgdir" -not -group root -not -user root)

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
local pkgjson="$pkgdir/usr/lib/node_modules/$_relname/package.json"
jq '.|=with_entries(select(.key|test("_.+")|not))' "$pkgjson" > "$tmppackage"
mv "$tmppackage" "$pkgjson"
chmod 644 "$pkgjson"

# Install license
# install -dm755 "${pkgdir}/usr/share/licenses/${_relname}"
# ln -s ../../../lib/node_modules/eslint/LICENSE "${pkgdir}/usr/share/licenses/${_relname}/LICENSE"

# Create Archiv8 documentation folder
# install -dvm 755 "$pkgdir/usr/share/doc/${_relname}/packaging/"

# Install Archiv8 Documentation
# install -Dm 644 "CHANGELOG.md" "$pkgdir/usr/share/doc/${_relname}/packaging/CHANGELOG.md"
# install -Dm 644 "README.md" "$pkgdir/usr/share/doc/${_relname}/packaging/README.md"
}
