## CHROOT and SYSTEMD-NSPAWN

####chroot:
- Debian/Ubuntu

`mkdir /mnt/chroot-ubuntu-trusty`

`debootstrap trusty /mnt/chroot-ubuntu-trusty http://archive.ubuntu.com/ubuntu/`

`chroot /mnt/chroot-ubuntu-trusty /bin/bash`

####systemd-nspawn:
- Debian/Ubuntu

``apt install systemd-container``

``debootstrap --arch=amd64 stable ~/DebianJessie``

``systemd-nspawn -bD ~/DebianJessie``

To verify and mange container run next command in the new terminal:

``machinectl``

To remove:

``machinectl terminate DebianJessie``