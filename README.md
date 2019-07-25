## Building unattended bootable ISO installation media for CentOS with kickstart

### ISO Builder server: CentOS Linux 7 (Core)

The server on which the customized bootable CentOS ISO will be created requires three packages.

#### Required Yum packages
- mkisofs
- syslinux
- isomd5sum

#### Required ISO 
- CentOS-7-x86_64-Minimal-1810 or full DVD if needed

#### Steps to create the customized ISO

----
https://serverfault.com/questions/517908/how-to-create-a-custom-iso-image-in-centos#521672
----

#### Customizations 

- Reduce boot delay
-- https://askubuntu.com/questions/148095/how-do-i-set-the-grub-timeout-and-the-grub-default-boot-entry
- Use ETH0 instead of variable BIOS names
-- https://thornelabs.net/posts/kickstart-centos-7-with-eth0-instead-of-predictable-network-interface-names.html

#### Example ks.cfg using static IP and ETH0 as network interface name

ks.cfg:
```
#version=DEVEL
# System authorization information
auth --enableshadow --passalgo=sha512
# Use CDROM installation media
cdrom
# Use graphical install
graphical
# Run the Setup Agent on first boot
firstboot --enable
ignoredisk --only-use=sda
# Keyboard layouts
keyboard --vckeymap=us --xlayouts='us'
# System language
lang en_US.UTF-8

# Network information
#network  --bootproto=dhcp --device=eth0 --onboot=off --ipv6=auto --no-activate
network  --bootproto=static --device=eth0 --gateway=10.9.37.254 --ip=10.9.37.81 --mtu=9000 --nameserver=10.9.12.1,10.9.12.2 --netmask=255.255.255.0 --noipv6 --activate
network  --hostname=localhost.localdomain

# Root password
rootpw --iscrypted $6$Po6LRe3a4uVDtqq4$H/Rbe.NSsWF9EPUS.8QV2.WnLoSaAff0gRVjSYVhTb49EmsGr58KB45gscnSt8lyQJKjShrskba5KEvkybzNg/
# System services
services --disabled="chronyd"
# System timezone
timezone Europe/Amsterdam --isUtc --nontp
# System bootloader configuration
bootloader --location=mbr --boot-drive=sda
autopart --type=lvm
# Partition clearing information
clearpart --none --initlabel

%packages
@^minimal
@core
-biosdevname
%end

%addon com_redhat_kdump --disable --reserve-mb='auto'

%end

%anaconda
pwpolicy root --minlen=6 --minquality=1 --notstrict --nochanges --notempty
pwpolicy user --minlen=6 --minquality=1 --notstrict --nochanges --emptyok
pwpolicy luks --minlen=6 --minquality=1 --notstrict --nochanges --notempty
%end
```
