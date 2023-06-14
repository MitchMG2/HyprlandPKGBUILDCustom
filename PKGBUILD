#Based on the original work of Caleb Maclennan <caleb@alerque.com> and ThatOneCalculator <kainoa@t1c.dev>
#Revised by Mitch_MG2 with errors pointed out by eclairvoyant

_pkgname="hyprland"
pkgname="${_pkgname}-git"
pkgver=r2580.71ef1bde
pkgrel=3
pkgdesc="A dynamic tiling Wayland compositor based on wlroots that doesn't sacrifice on its looks."
arch=(x86_64 aarch64)
url="https://github.com/hyprwm/Hyprland"
license=('BSD')
depends=(
	libxcb
	xcb-proto
	xcb-util
	xcb-util-keysyms
	libxfixes
	libx11
	libxcomposite
	xorg-xinput
	libxrender
	pixman
	wayland-protocols
	cairo
	pango
	polkit
	glslang
	libinput
	libxcb
	libxkbcommon
	opengl-driver
	pixman
	wayland
	xcb-util-errors
	xcb-util-renderutil
	xcb-util-wm
	seatd
	vulkan-icd-loader
	vulkan-validation-layers
	xorg-xwayland
	libliftoff
	libdisplay-info
	pango)
makedepends=(
	git
	cmake
	ninja
	gcc
	gdb
	meson
	vulkan-headers
	wayland-protocols
	xorgproto)
source=("${_pkgname}::git+https://github.com/hyprwm/Hyprland.git")
conflicts=("${_pkgname}")
provides=(hyprland)
sha256sums=('SKIP')

pkgver() {    
    git -C $_pkgname describe --long --tags | sed 's/^v//;s/\([^-]*-\)g/r\1/;s/-/./g'    
}

prepare() {
	cd "${srcdir}/${_pkgname}"
  git submodule update --init
	make fixwlr
}

build() {
	cd "${srcdir}/${_pkgname}"
	pushd subprojects/wlroots
	meson build/ --prefix="$srcdir/tmpwlr" --buildtype=release
	ninja -C build/
	mkdir -p "$srcdir/tmpwlr"
	ninja -C build/ install
	popd
	pushd subprojects/udis86
	cmake --no-warn-unused-cli -DCMAKE_BUILD_TYPE:STRING=Release -H./ -B./build -G Ninja
	cmake --build ./build --config Release --target all
	popd
	make protocols
	make release
	pushd hyprctl
	make all
}

package() {
	cd "${srcdir}/${_pkgname}"
	mkdir -p "${pkgdir}/usr/share/wayland-sessions"
	mkdir -p "${pkgdir}/usr/share/hyprland"
	pushd src
	find . -name '*.hpp' -exec install -Dm0644 {} "$pkgdir/usr/include/hyprland/src/{}" \;
	popd
	pushd subprojects/wlroots/include
	find . -name '*.h' -exec install -Dm0644 {} "$pkgdir/usr/include/hyprland/wlroots/{}" \;
	popd
	pushd subprojects/wlroots/build/include
	find . -name '*.h' -exec install -Dm0644 {} "$pkgdir/usr/include/hyprland/wlroots/{}" \;
	popd
	mkdir -p "$pkgdir/usr/include/hyprland/protocols"
	cp protocols/*-protocol.h "$pkgdir/usr/include/hyprland/protocols"
	pushd build
	cmake -DCMAKE_INSTALL_PREFIX=/usr ..
	popd

	install -Dm0644 -t "$pkgdir/usr/share/pkgconfig" build/hyprland.pc
	install -Dm0755 -t "$pkgdir/usr/bin" build/Hyprland
	install -Dm0755 -t "$pkgdir/usr/bin" hyprctl/hyprctl
	install -Dm0644 -t "$pkgdir/usr/share/$_pkgname" assets/*.png
	install -Dm0644 -t "$pkgdir/usr/share/wayland-sessions" "example/$_pkgname.desktop"
	install -Dm0644 -t "$pkgdir/usr/share/$_pkgname" "example/$_pkgname.conf"
	install -Dm0644 -t "$pkgdir/usr/share/licenses/$_pkgname/" LICENSE
	install -Dm0755 -t "$pkgdir/usr/lib" "$srcdir/tmpwlr/lib/libwlroots.so.12032"
}

