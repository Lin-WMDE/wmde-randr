# Maintainer: WMDE <https://wmde.fun>
# Contributor: System76 <jeremy@system76.com> (original cosmic-randr)
# Builds our fork Lin-WMDE/wmde-randr (branch wmde; master mirrors pop-os upstream).
# Ships /usr/bin/wmde-randr, the display-configuration CLI that wmde-osd spawns
# ("wmde-randr kdl") to APPLY a monitor arrangement. Only the CLI binary is renamed
# (cosmic-randr -> wmde-randr); the cosmic-randr / cosmic-randr-shell crate names are
# kept so the [patch] redirect in wmde-osd/wmde-comp/wmde-settings resolves.
pkgname=wmde-randr
pkgver=1.5.0
pkgrel=1
pkgdesc="WMDE display configuration CLI (fork of cosmic-randr; applies monitor layout for wmde-osd)"
arch=('x86_64')
url="https://wmde.fun"
license=('MPL-2.0')
# A small wayland client CLI (clap + wayland-client + kdl). No libcosmic.
depends=('glibc' 'gcc-libs' 'wayland')
makedepends=('rust' 'cargo' 'git')
source=("$pkgname::git+https://github.com/Lin-WMDE/wmde-randr.git#branch=wmde")
sha256sums=('SKIP')

pkgver() {
  cd "$srcdir/$pkgname"
  # WMDE version: <COSMIC major.minor>.<WMDE release>.<commits since the nearest tag>.g<short>.
  local desc
  desc=$(git describe --long --tags --abbrev=7 2>/dev/null || true)
  if [ -n "$desc" ]; then
    printf '1.10.2.%s.g%s' "$(printf '%s' "$desc" | sed -E 's/.*-([0-9]+)-g[0-9a-f]+$/\1/')" "$(git rev-parse --short=7 HEAD)"
  else
    printf '1.10.2.%s.g%s' "$(git rev-list --count HEAD)" "$(git rev-parse --short=7 HEAD)"
  fi
}

build() {
  cd "$srcdir/$pkgname"
  # x86-64-v3 (AVX2/BMI2) baseline for the WMDE repo; runs on Haswell+ (and the VM).
  export RUSTFLAGS="${RUSTFLAGS:+$RUSTFLAGS }-C target-cpu=x86-64-v3"
  cargo build --release --bin wmde-randr
}

package() {
  cd "$srcdir/$pkgname"
  install -Dm755 "${CARGO_TARGET_DIR:-target}/release/wmde-randr" "$pkgdir/usr/bin/wmde-randr"
  install -Dm644 LICENSE "$pkgdir/usr/share/licenses/$pkgname/LICENSE"
}
