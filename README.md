<!-- vim: set fenc=utf-8 ts=4 sw=4 sts=4 sr et si tw=0 fdm=marker fmr={{{,}}} ft=markdown: -->

<!-- {{{ Introduction -->
# useful-commands
This file is just filled with commands I've kept for years in my homedir in a mess of a shell script that I finally got around to publish somehow so that it's not just tied to my personal computer anymore.
<!-- }}} -->

<!-- {{{ All the useful arguments to add a user -->
## All the useful arguments to add a user
```bash
# Create my user
useradd \
        --uid 3153\
        --shell /bin/zsh\
        --comment "Andy3153"\
        --create-home\
        --home-dir /home/andy3153/\
        --groups docker,libvirt,gamemode,uucp,input,wheel\
        andy3153

# Create user with non-unique UID
useradd \
        --non-unique\
        --uid 3153\
        --shell /bin/zsh\
        --comment "Andy3153 2"\
        --create-home\
        --home-dir /home/andy3153_2/\
        --no-user-group\
        --groups docker,libvirt,gamemode,uucp,input,wheel,andy3153\
        andy3153_2
```
<!-- }}} -->

<!-- {{{ Setting hostname -->
## Setting hostname
```bash
hostnamectl set-hostname sparkle #midnight
```
<!-- }}} -->

<!-- {{{ Set clock -->
## Set clock
```bash
# On an OS with systemd using NTP
timedatectl set-ntp true

# Manually
hwclock --set --date='10/16/19 21:45:00'
hwclock --hctosys
```
<!-- }}} -->

<!-- {{{ Set timezone -->
## Set timezone
```bash
# On an OS with systemd
timedatectl set-timezone Europe/Bucharest

# Manually
ln -sf /usr/share/zoneinfo/Europe/Bucharest /etc/localtime
```
<!-- }}} -->

<!-- {{{ Setting swappiness -->
## Setting swappiness
```bash
# Change /etc/sysctl.conf for permanent changes
cat /proc/sys/vm/swappiness                                # see already-set swappiness
sysctl vm.swappiness=40                               # the higher the more 'swappy'
```
<!-- }}} -->

<!-- {{{ Test flash drive for real capacity -->
## Test flash drive for real capacity
```bash
f3probe --destructive --time-ops /dev/sdx
```
<!-- }}} -->

<!-- {{{ SSH key generation -->
## SSH key generation
```bash
# Generate a key
ssh-keygen -t rsa

# Copy ID to remote server
ssh-copy-id -f username@remote_ip
```
<!-- }}} -->

<!-- {{{ Mount partitions from image files -->
## Mount partitions from image files
```bash
# IMG
fdisk -l file.img                                            # see start of partition and sector size

# Mount the partition inside the img file
mount --mkdir -o loop,offset=$((partstart * sectorsize)) file.img mountpoint/

# ISO
mount --mkdir -o loop file.iso mountpoint/

# Bin to ISO
bchunk file.bin file.cue file.iso
```
<!-- }}} -->

<!-- {{{ Renew pacman mirrors -->
## Renew pacman mirrors
```bash
# Download and rank mirrors
curl -s "https://archlinux.org/mirrorlist/?country=all&protocol=https&use_mirror_status=on" | sed -e 's/^#Server/Server/' -e '/^#/d' | rankmirrors --parallel - > ~/.cache/mirrorlist-generated
doas pacman -U 'https://cdn-mirror.chaotic.cx/chaotic-aur/chaotic-keyring.pkg.tar.zst' 'https://cdn-mirror.chaotic.cx/chaotic-aur/chaotic-mirrorlist.pkg.tar.zst' ; cat /etc/pacman.d/chaotic-mirrorlist | sed -e 's/^#Server/Server/' -e '/^#/d' | rankmirrors --parallel - > ~/.cache/mirrorlist-generated-chaotic

# Apply new and ranked mirrors
doas mv ~/.cache/mirrorlist-generated /etc/pacman.d/mirrorlist && doas rm -rf /etc/pacman.d/mirrorlist.pacnew
doas mv ~/.cache/mirrorlist-generated-chaotic /etc/pacman.d/chaotic-mirrorlist && doas rm -rf /etc/pacman.d/chaotic-mirrorlist.pacnew
```
<!-- }}} -->

<!-- {{{ Two mice at the same time -->
## Two mice at the same time
```bash
xinput --create-master insertaname
xinput --list                                                # get the 'id=' of your mouse on the line corresponding to your mouse name
                                                             # and the 'id=' of the new master you created, on the line corresponding to 'insertaname pointer'
xinput --reattach insertmouseid insertmasterid

xinput --remove-master insertmasterid                        # to revert changes
xinput --reattach insertmouseid insertoldmasterid            #
```
<!-- }}} -->

<!-- {{{ Convert videos with FFMpeg using NVENC -->
## Convert videos with FFMpeg using NVENC
```bash
ffmpeg -i source.ext1 -c:v h264_nvenc -rc constqp -qp 29 output.ext2
```
<!-- }}} -->

<!-- {{{ Converting conditionals to short form -->
## Converting conditionals to short form
```bash
# Long form
if [ condition ]
then true
else false
fi

# Short form
[[ condition ]] || true && false
```
<!-- }}} -->

<!-- {{{ Testing nvim config one-liner -->
## Testing nvim config one-liner
```bash
sudo rm -rf /home/bot/.config /home/bot/.local/ ; sudo mkdir /home/bot/.config ; sudo cp -r ~/src/nvim/andy3153-init_vim/ /home/bot/.config/nvim ; sudo chown -R bot:bot /home/bot ; sudo -iu bot bash
```
<!-- }}} -->

<!-- {{{ Git -->
## Git
<!-- {{{ Account details -->
### Account details
```bash
git config --global user.name ${username}
git config --global user.email ${email}
```
<!-- }}} -->

<!-- {{{ Add remotes -->
### Add remotes
```bash
git remote add $remotename git@${gitserver}:${username}/${reponame}.git
# ex.: git remote add origin git@github.com:Andy3153/useful-commands.git
```
<!-- }}} -->
<!-- }}} -->

<!-- {{{ UFW -->
## UFW
<!-- {{{ Basic -->
### Basic
```bash
ufw status verbose            # detailed status
ufw {enable,disable}          # start/stop
```
<!-- }}} -->

<!-- {{{ Rules {allow,deny,limit,delete} -->
### Rules {allow,deny,limit,delete}
```bash
ufw default deny              # deny everything by default
ufw allow from 192.168.1.0/24 # allow everything on local networks
ufw allow 1000                # allow port
ufw allow 1000:2000/{tcp,udp} # allow port range
```
<!-- }}} -->

<!-- {{{ Applications-->
### Applications
<!-- {{{ Basic -->
#### Basic
```bash
ufw app list                # show all applications
ufw allow SSH               # allow SSH
```
<!-- }}} -->

<!-- {{{ Adding custom apps -->
#### Adding custom apps
```ini
# /etc/ufw/applications.d/custom
[Terraria]
title=Terraria
description=Terraria Server
ports=7777/tcp

[Minecraft]
title=Minecraft
description=Minecraft Server
ports=25565

[WiFi-Hotspot]
title=WiFi Hotspot
description=Ports needed for a WiFi Hotspot to run (dnsmasq)
ports=53,67/udp|68/udp
```
<!-- }}} -->
<!-- }}} -->
<!-- }}} -->
