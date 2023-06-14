# HyprlandPKGBUILDCustom
Edited -git Hyprland PKGBUILD with multiple improvements over the current one in AUR

Changes are as follows:
Removed the options flags that almost certainly caused the LDFLAGS errors (Section 4.4 in the ArchWiki: https://wiki.archlinux.org/title/makepkg ).
Outright copy pasted @eclairevoyant's pkgver line.
Removed -j flag
Build and Package functions almost entirely copied off official repo package. There was a few discrepancies with file permissions
Inserted header files several plugins rely on (such as hy3) that do not exist in the -git PKGBUILD. Also off the official PKGBUILD.
Added a quick "prepare" function. Could use some revision but seems to work for now.
Changed arch to x86_64 and aaarch64.

This PKGBUILD is purely experimental, use at your own risk.
