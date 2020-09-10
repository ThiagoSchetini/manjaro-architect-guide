# Manjaro Architect Guide 

A installation guide of Archlinux Manjaro distro using the Manjaro Architect Advanced tool!

This guide is focused on performance and security with some additional packages for developers, specialy for data engineers.

Requires some hardware background and basic shell commands knowledge.
	
### Prepare the instalation

Create your Architect pen driver installer: https://manjaro.org/downloads/official/architect/
	
Use UEFI only (on your BIOS), boot the pen drive and start:

#### [3] Partition Disk

Choose Automatic partitioning on your sda (it creates the boot + root parts) or use *parted* on manual mode if you have knowledge. Automatic is enough and perfect!

#### [7] Mount Partitions

Mount root partition: 

	sda2 (or nvme02)
	format ext4
	options relatime (or none)
	swap none (If you need swap, use swap file option)

Mount UEFI partition: 

	sda1 (or nvme01)
	mountpoint on /boot (for systemd-boot)

#### [8] Installer Mirrorlist 

Choose: Rank Mirrors by speed

#### [9] Refresh Pacman Keys
	
Run it (good idea).

#### [10] Pacman cache

Choose Yes (save some time on installation).

### Start

#### [1] Install Manjaro Desktop

Choose:

	linux58 (choose the last stable release!, it's an example)
	GNOME interface (this guide is for GNOME)  
	additional package to installation: NO 
	Full or Minimal?: minimal
		
Display Driver: 

*Warning: if your cpu does not have internal gpu, your graphics card/chip probably will loose the video signal!*

If it happens, wait some minutes, reboot, mount again, do not format and reinstall. The driver will be already ok and the installation will continue from the broken point.

	Intel: auto-install free drivers
	nvidia/AMD: auto-install proprietary drivers

Network Driver: 

	auto-install proprietary

Kernel (additional driver may appear, know your hardware): 

	KERNEL-headers (good idea)
	KERNEL-acpi_call (use calls: https://github.com/mkottman/acpi_call)
	KERNEL-r8168 (!only if you have Realtek PCIe Ethernet)
	KERNEL-tp_smapi (!only for thinkpad notebooks)
	... etc ...

#### [2] Install Bootloader

Choose systemd. It's newer than the old grub, follows a new specification that make it simple to config and will better support dual boot.

You can check more information about this incredible code on https://systemd.io
		
Choose:

	systemd-boot
	set bootloader as default: Yes

#### [3] Configure Base

Generate FSTAB (file): 

	Device UUID
		
Set Hostname: 

	"i7-3632QM" or "Xeon-E3-1270V3" or Workstation3 ... (put a good machine related name)
		
Set System Locale:

	language: en_US.UTF-8
	locale: en_US.UTF-8

Set Timezone and Clock: 

	America/Sao_Paulo, then choose UTC
		
Set Root Password.

Add New User: 

	username
	choose zsh
	add password
		
No tweaks (we will have performance improves later).

Reboot your system.

### Security 
	
#### Time

Hardware should be always on UTC (do not change your BIOS clock).

Check your timezone on Settings.

RTC in local TZ (timezone) must be *"no"*. On terminal type:

	timedatectl status
		
If you need to set RTC in local TZ to no:

	timedatectl set-local-rtc 0
	timedatectl status
	*should read: 'RTC in local TZ: no'
			
Calibrate system time:

	sudo ntpd -qg
		
For dual boot windows needs to be adjusted to UTC:

	https://wiki.archlinux.org/index.php/System_time#Set_hardware_clock_from_system_clock
		
#### KEYRING

Refresh the the web of trust of pacman keys:

	sudo pacman-key --init
	sudo pacman-key --refresh-keys
	sudo pacman-key --populate archlinux manjaro

#### Update using BMENU

On terminal, type:

	bmenu
	choose "1 Package manager UI", then "1 Update System"
		
#### SSH 

If you do not have a signature to use ssh, let's create one. Use a passphrase when it asks:

	ssh-keygen -o -a 300 -t ed25519 -f ~/.ssh/id_ed25519 -C "MY-MACHINE"

Read private key (do not show it to anyone):

	cat ~/.ssh/id_ed25519

Read public key (distribute to anyone):

	cat ~/.ssh/id_ed25519.pub

Read the directory with the keys, and make a beckup if you need:

	ls -la ~/.ssh

Check if your ssh agent service is running:

	eval "$(ssh-agent -s)"

Add the private key to the agent (put your passphrase and you can log on server without digit your pass anymore):

	ssh-add ~/.ssh/id_ed25519

#### Firewall

Go to menu and find *Firewall Configuration* app:
		
	profile=Office
	status=on
	incoming=deny
	outgoing=allow

### Remove Junk

	sudo pacman -Rs manjaro-hello manjaro-application-utility
	sudo pacman -Rs pamac-gtk pamac-gnome-integration
	sudo pacman -Rs yay 
	sudo pacman -Rs nano
	sudo pacman -Rs vi
	
### Packages

#### Probably pre-installed by architect, check it:

	sudo pacman -S curl
	sudo pacman -S grep
	sudo pacman -S make 	
	sudo pacman -S base-devel (extra packages to build, compile. Select all)		
	sudo pacman -S mhwd 		
	sudo pacman -S fuse-exfat 
	sudo pacman -S archlinux-keyring

#### Utils

	sudo pacman -S mesa-demos (GPU monitoring and test > www.mesa3d.org)
	sudo pacman -S tlpui (usefull visual interface for tlp)
	sudo pacman -S pacaur (AUR package manager, BE CAREFULL, read the instructions)
	sudo pacman -S bashtop (much metter than 'top' or 'htop')
	sudo pacman -S iotop
	sudo pacman -S hdparm (best as linux helper to hard and solid state drives)
	sudo pacman -S xclip
	sudo pacman -S the_silver_searcher (more performance than grep, use as 'ag')
	sudo pacman -S cmatrix
	sudo pacman -S links (terminal web navigator)
	sudo pacman -S lrzip (more powerfull, read the man)
	sudo pacman -S etcher (ISO's pen drive burner)
	sudo pacman -S transmission-gtk (torrents)

#### Office

	sudo pacman -S freeoffice (remember to add the key for free)
	sudo pacman -S rhythmbox (audio player)
	sudo pacman -S audacity (audio editor)
	sudo pacman -S kdenlive (video editor)
	sudo pacman -S krita (image editor)
	pacaur -S exif (image metadata editor)
	
#### Internet & Communication

All apps are native, not **Electron** crap:

	sudo pacman -S firefox (use epiphany as your primary web)
	pacaur -S discord-canary
	pacaur -S zoom
	pacaur -S slack-desktop
	pacaur -S teams
	pacaur -S telegram-desktop
	
#### Vim

	sudo pacman -S vim
	sudo vim /etc/environment (edit: "EDITOR=/usr/bin/vim")	
	vim .profile (edit: "export EDITOR=/usr/bin/vim")
	reboot
		
#### Git (+ssh config)

	sudo pacman -S git
		
Create directories to repositories of code, example:

	mkdir ~/Repositories
	mkdir ~/Repositories/github
	mkdir ~/Repositories/gitbucket
	... another names, like companyname, etc...

Config your global Git identity:

	git config --global user.email "you@example.com"
	git config --global user.name "Your Name"

Add your public ssh key to git server:

	xclip -sel clip < ~/.ssh/id_ed25519.pub

Test activity on your git, and check if everything is ok, example:

	cd ~/Repositories/github
	git clone git@github.com:ThiagoSchetini/drnglib.git

#### Docker

	sudo pacman -S docker
	sudo usermod -aG docker $USER
	sudo systemctl start docker 
	sudo systemctl enable docker 
	sudo docker run hello-world 

#### JVM

	sudo pacman -S jre8-openjdk
	sudo pacman -S jre11-openjdk
	sudo pacman -S jre14-openjdk
		
Use an intermediate version (better for Scala) as default:

	sudo archlinux-java set java-11-openjdk

Reload terminal:
		
	ls -la /usr/lib/jvm
	archlinux-java status
	java -version

	sudo pacman -S scala
	scala -version

	sudo pacman -S maven (choose default option)
	mvn --version

	sudo pacman -S sbt
	sbt --version (wait for downloads)
	* if terminal shows any [error] message downloading, execute 'sbt --version' again!

#### InteliJ

	sudo pacman -S intellij-idea-community-edition
		
#### Python3

Create the default venv python directory, and test it:

	python3 -m venv ~/.venv/python3
	source ~/.venv/python3/bin/activate
	pip list
	deactivate
		
Install the PyPy3, VM with Jit + performance:

	sudo pacman -S pypy3
	pypy3 -m venv ~/.venv/pypy3
	source ~/.venv/pypy3/bin/activate
	pip list
	deactivate
		
#### Pycharm

	sudo pacman -S pycharm-community-edition
		
Open it, on settings, new projects check if all interpreters are recognized to create virtual envs (python3 and pypy3)

Add the two envs that we just created on terminal as *Existing environments* and check *Make available to all projects*
		
#### Teamviewer (native for linux, manual install)

Check dependencies:

	sudo pacman -S qt5-webkit
	sudo pacman -S qt5-quickcontrols
	
Install:

	git clone https://aur.archlinux.org/teamviewer.git
	makepkg --install
	systemctl enable teamviewerd
	systemctl start teamviewerd

#### Spotify (native for linux)

Sign the key:

	curl -sS https://download.spotify.com/debian/pubkey.gpg | gpg --import -
		
Install:

	pacaur -S spotify
			
#### Soap UI

	pacaur -S soapui

#### Sublime Text 3 (optional)

Prefer using gedit, but:
	
	echo -e "\n[sublime-text]\nServer = https://download.sublimetext.com/arch/stable/x86_64" | sudo tee -a /etc/pacman.conf
	curl -O https://download.sublimetext.com/sublimehq-pub.gpg && sudo pacman-key --add sublimehq-pub.gpg && sudo pacman-key --lsign-key 8A8F901A && rm sublimehq-pub.gpg
	sudo pacman -S sublime-text
		
### Sanitize

Clean package manager cache:

	sudo pacman -Sc
	sudo pacaur -Sc
	
Use the incredible Bmenu script to maintain the OS:

	bmenu
	choose "1 Package manager UI", then "2 Maintain System"

### System Check

Read the system info from your Hardware:
	
	bmenu
	choose "2 System Information"

Check the CPU frequency current policy (governor):

	cpupower frequency-info

Check tlp service is running (if not ... it's a problem, try systemctl enable tlp --now):

	systemctl status tlp

Check the use of swap (should be ZERO if you did not use on installation):

	less /proc/meminfo | grep Swap
	vmstat
	free

GPU direct rendering check. It means compositing screen through the GPU and not wasting CPU cycles, see 'direct rendering: Yes':

	glxinfo | grep 'direct rendering'

### System monitor sensors
	
Update sensors (if something goes wrong, detect again, enter Y on all questions):

	sudo sensors-detect
	sensors
	sudo pacman -S psensor
		
On menu, open psensor:

	choose cpu temp and/or others to show on your topbar
	/Edit Preferences/Startup/ enable 
	"launch on session startup"
	"enable launch on session startup"

### Performance

#### TLPUI

Look for TLPUI app and open it. Read everything and check if there is something to change. There is an example of a notebook:

	pcie AC = performance / BAT = powersave
	turn off nvidia from pcie blacklist
	disks put the SATA_LINKPWR_ON_AC only on max_performance
	CPU_SCALING_GOVERNOR_ON_AC = performance
	CPU_SCALING_GOVERNOR_ON_BAT = powersave
	CPU_ENERGY_PERF_POLICY  ON_AC = performance ON_BAT=balance_power

#### Mitigations and Watchdog

Some reading about Mitigations and Watchdog (optional):

	About Mitigations
	Intel hyperthreading has exploit vulnerabilities, and linux kernel can do some juggling to avoid it.
	If you're not into currency trading or high finance or military contracting or anything of that 
	nature, consider turning off CPU exploit mitigations appending 'mitigations=off' on boot parameters.
	Improves performance (sometimes up to 50%).
	Maybe AMD processors hyperthreading has the same vulnerabilities... TODO

	Watchdog is an electronic timer that is used to detect and recover from computer malfunctions.
	It degrades the performance, pushing your reset button is more efficient on a desktop environment.
	To disable watchdog timers (both software and hardware), append 'nowatchdog' to your boot parameters.

			
Disable Mitigations, Watchdog and Hibernate with kernel parameters 'nohibernate nowatchdog mitigations=off' on boot parameters:

	sudo vim /boot/loader/entries/manjarolinux5.8.conf
	'options	root=UUID=fac688f2-9966-49df-9320-b533b82d6b89 rw nohibernate nowatchdog mitigations=off'

Find more Kernel parameters: https://www.kernel.org/doc/html/latest/admin-guide/kernel-parameters.html

Now, reboot before check the changes.
		
Check if the boot params are they there:

	cat /proc/cmdline

Check if Mitigations are off looking for disabled and vulnerable words (great!):

	grep -H '' /sys/devices/system/cpu/vulnerabilities/*

Check if Watchdog is off looking for the zero number signal:

	cat /proc/sys/kernel/watchdog
				
#### Dev(ices) schedulers, adding more parallelism on sata protocol

Check the scheduler used to all devices (rotating, ssd, nvme). nvme does not need because is connected directly on PCI lanes. The correct are:

	[none] to nvme
	[mq-deadline] to ssd
	[bfq] to rotational 
		
	grep "" /sys/block/*/queue/scheduler
		
Let's increase the *mq-deadline* paralellism (if you have one). If there is something wrong (like rotating using mq-deadline) you should change it. Open the .rules on vim to edit:

	sudo vim /etc/udev/rules.d/60-ioscheduler.rules
		
There is a sample that you can just copy and paste to the .rules file:

	# set scheduler for NVMe
	ACTION=="add|change", KERNEL=="nvme[0-9]*", ATTR{queue/scheduler}="none"

	# set scheduler for SSD
	ACTION=="add|change", KERNEL=="sd[a-z]", ATTR{queue/rotational}=="0", ATTR{queue/scheduler}="mq-deadline"

	# set scheduler for rotating disks
	ACTION=="add|change", KERNEL=="sd[a-z]", ATTR{queue/rotational}=="1", ATTR{queue/scheduler}="bfq"

	# improves throughput of devices at the cost of latency (default is 16). Only on SSD [mq-deadline]
	ACTION=="add|change", KERNEL=="sd[a-z]", ATTR{queue/rotational}=="0", ATTR{queue/iosched/fifo_batch}="32"

Reboot before check:
		
	grep "" /sys/block/*/queue/scheduler
	grep "" /sys/block/*/queue/iosched/fifo_batch
		
### SSD and NVME care tasks

#### Format and permanent mount a unit

If you need to format and permanent mount, there is a simple and modern way to to this.

Let's start making the label and the partition:
	
	sudo parted /dev/sdb (or sdc ... d, nvme1 ... 2)
	mklabel gpt
	mkpart "" ext4 2M 250G (change end size of your disk, like 500G, 1000G)
	quit
		
Format and get the UUID:

	sudo mkfs.ext4 /dev/sdb1
	'...'
	'Filesystem UUID: 3b5f8d12-bc30-4358-9739-b6dc8ae251ff'
	'...'
	
Create the path where you want to mount:

	mkdir /data
		
Then manual edit fstab:
		
	sudo vim /etc/fstab
		
Add generated UUID, path and the format (ext4 in this case):

	# /dev/sdb1
	UUID=3b5f8d12-bc30-4358-9739-b6dc8ae251ff	/data		ext4		rw,relatime	0 0
		
Reboot before check:

	sudo blkid (to check the uuid's)
	sudo fdisk --list
	ls -la /data

#### Speed/performance check

Check if speed is ok with specifications:

	sudo hdparm -t /dev/sda
	.................../sdb
			
#### Check free space on a device

	df
	df -H --output=size,used,avail	
		
#### Check the space used of any directory

	du -h --max-depth=1 .cache
	du -h --max-depth=1 .cache/epiphany
	sudo du -h --max-depth=1 /
		
#### Check trim support

Probably your solid state drive have it:

	sudo hdparm -I /dev/sda | grep TRIM
	sudo hdparm -I /dev/sdb | grep TRIM
		
DISC-GRAN (discard granularity) and DISC-MAX (discard max bytes) non-zero values indicate TRIM support:
		
	lsblk --discard
			
Check and enable trim auto service. Continuous trim is not recommended by UNIX community. Distros like Ubuntu come with trim scheduled to run once a week. Arch does not. Even so, let's check first:

	systemctl cat fstrim.service
	systemctl status fstrim.timer
			
Probably the result above of timer is 'Active: inactive (dead)', so:

	sudo systemctl enable fstrim.timer
		
**Reboot** before check. You should read *Active: active (waiting)*:

	systemctl status fstrim.timer
		
#### Perform a manual trim 

You can do it after some big change and not wait the weekly	trim runs. The command runs once on all supported mounted filesystems:

	sudo fstrim --verbose --all
		
#### About firmware updates on Archlinux 

Updating SAMSUNG SSD firmware on arch linux natively or AUR:

	https://wiki.archlinux.org/index.php/Solid_state_drive#Samsung
		
Firmware update of another brands on linux:

	https://wiki.archlinux.org/index.php/Solid_state_drive#Firmware
			
TODO NVM-Express user space tooling for Linux:
		
	pacman -S nvme-cli                                                                                                  
	
### Dark Gnome customization

#### First steps

First step on *bmenu*. On terminal do:

	bmenu				
	7 System & Setting > Appearance
	now select dark themes
		
Open *Tweaks* on Accessories/Tweaks/Appearance:

	Applications: Matcha-dark-sea
	Cursor: Xcursor-breeze
	Icons: Papirus-Dark-Maia
	Shell: Matcha-dark-sea

Open Accessories/Tweaks/Keyboard & Mouse:

	disable middle click paste
	disable mouse click emulation

Open Accessories/Kvantum Manager/Change/Delete Theme and choose *Matchama-Dark-Aliz*.

#### More darkness

On terminal:

	vim .config/gtk-2.0/gtkfilechooser.ini
	'ShowHidden=true'

	vim .config/gtk-3.0/settings.ini
	'gtk-application-prefer-dark-theme=1'

	vim .config/gtk-4.0/settings.ini
	'gtk-application-prefer-dark-theme=1'
		
#### Settings:

Wifi: config your wireless and them, if using desktop cable, disable it.

Sound: check your output and your microphone

Power: Blank Screen=Never / Automatic Suspend=Off / Power Button Action = Power Off

Displays/Night Light: manual set 17h00 to 8h00

Mouse & Touchpad/Mouse: enable natural scrolling
		
Now, reboot your system.
		
### Stress tests

Let's do a basic stress and stability test. Observe for some minutes the cpu and gpu temps:

	CPU should not be Higher than +- 80C
	GPU should not be Higher than +- 93C

Open psensor and use it to monitor:

	psensor
			
Start it and observe temperatures and stability during some minutes:

	glxgears_pixmap
			
*hint: open some another app like a internet tab during the test to feel the response of your's processors.*
			
More advanced stress test for linux (free) https://benchmark.unigine.com/superposition:

	pacaur -S unigine-superposition

### Kernel 
		
Optional, there is some readings about Kernel.
		
All boot params (official docs): https://www.kernel.org/doc/html/latest/admin-guide/kernel-parameters.html

#### Modules

You could have more than one kernel versions (modules) on:

	ls /usr/lib/modules
	
**Shell script** hint to look on the active one only '$(uname -r)':

	ls /usr/lib/modules/$(uname -r)/kernel
	
#### Change your Kernel

TODO (not tested yet). If you install another linux kernel and need to change it on boot, look at boot files to change it:

	cat /boot/loader/entries/manjarolinux5.8.conf
	cat /boot/loader/loader.conf 

#### Kernel location

Some Kernel files and locations ('.ko' files).

CPU (architectures):

	ls /usr/lib/modules/$(uname -r)/kernel/arch/x86/events/intel
		
Drivers:

	ls /usr/lib/modules/$(uname -r)/kernel/drivers/cpuidle
	ls /usr/lib/modules/$(uname -r)/kernel/drivers/cpufreq/
	ls /usr/lib/modules/$(uname -r)/kernel/drivers/thermal/intel
	ls /usr/lib/modules/$(uname -r)/kernel/drivers/bluetooth
	ls /usr/lib/modules/$(uname -r)/kernel/drivers/video/backlight
				
Filesystems:

	ls /usr/lib/modules/$(uname -r)/kernel/fs/ext4
		
Security:

	ls /usr/lib/modules/$(uname -r)/kernel/security/keys/trusted-keys
	ls /usr/lib/modules/$(uname -r)/kernel/security/keys/encrypted-keys
 
Extra modules (not native on kernel, like nvidia GPU):

	ls /usr/lib/modules/$(uname -r)/extramodules

### Credits

**That's all folks**
