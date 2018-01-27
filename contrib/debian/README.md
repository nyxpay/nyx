
Debian
====================
This directory contains files used to package nyxd/nyx-qt
for Debian-based Linux systems. If you compile nyxd/nyx-qt yourself, there are some useful files here.

## nyx: URI support ##


nyx-qt.desktop  (Gnome / Open Desktop)
To install:

	sudo desktop-file-install nyx-qt.desktop
	sudo update-desktop-database

If you build yourself, you will either need to modify the paths in
the .desktop file or copy or symlink your nyx-qt binary to `/usr/bin`
and the `../../share/pixmaps/nyx128.png` to `/usr/share/pixmaps`

nyx-qt.protocol (KDE)

