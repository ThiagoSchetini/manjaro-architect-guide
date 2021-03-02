# Manjaro Architect install guide 

Under the hood, the *Manjaro Architect* is an *Arch Linux* and the most clean *Manjaro* option.

Here you're gonna find Nvidia fan control, system sensors, docker, advanced system configs and packages for development and gaming.
	
## Prepare USB key to install

Create your *Architect* pen drive installer: *https://manjaro.org/downloads/official/architect/*
	
Set UEFI only on your BIOS (Use UEFI only please!).

Boot the pen drive and start the installation.

## Prepare the installation after the boot

### Partition Disk (installation menu option)

Choose Automatic partitioning on your sda/nvme (it creates the boot + root partitions). 

Automatic is enough and perfect! It does the correct format and size for each one.

### Mount Partitions (installation menu option)

Mount root partition (for system files): 

	sda2 (or nvme02)
	format ext4
	options relatime (or none)
	swap none (If you need swap, use swap file option)

Mount UEFI partition (for boot files): 

	sda1 (or nvme01)
	mountpoint on /boot (for systemd-boot)

## Start the Installation!

### Install Manjaro Desktop (installation menu option)

Choose:

	kernel version
	GNOME interface (this guide is for GNOME)  
	additional package to installation: NO 
	Full or Minimal?: minimal
		
Display Driver: 

	*Warning! Warning! Warning!: if your cpu does not have internal gpu, your graphics card/chip probably will loose the video signal!*

	If it happens, wait some minutes, reboot, mount again, do not format and reinstall. The driver will be already ok and the installation will continue from the broken point.

	Intel: auto-install free drivers
	nvidia/AMD: auto-install proprietary drivers

Network Driver: 

	auto-install proprietary

Kernel extras:

	Choose some specifc drivers for your hardware if you need as the examples below.

	KERNEL-headers (good idea for development environments)
	KERNEL-acpi_call (if you need to use acpi calls: https://github.com/mkottman/acpi_call)
	KERNEL-r8168 (when you have a Realtek PCIe Ethernet)
	KERNEL-tp_smapi (when you are using a thinkpad notebook)

### Install Bootloader (installation menu option)

Choose *systemd*. It's newer than the old *grub*, follows a new specification that make it simple to config and will better support dual boot. Check more info on *https://systemd.io*

	systemd-boot
		
### Configure Base (installation menu option)

Generate FSTAB (file): 

	Device UUID
		
Set Hostname. You can find a professional or machine related name. Examples below: 

	"i7-3632QM", "Xeon-E3-1270V3", "Workstation3", "GamerMachine2"
		
Set System Locale:

	language: en_US.UTF-8
	locale: en_US.UTF-8

Set Timezone and Clock: 

	choose region (example America/Sao_Paulo), then choose UTC
		
Set Root Password.

Add New User: 

	username
	choose zsh
	add password
		
No tweaks (we will have performance improves later).

Finally, reboot your system.

## After install (reboot) 
	
### Time

First check if your hardware/BIOS is on UTC. (probably not the same time of your system).

Then on *Settings*, check your timezone.

Check the RTC in local TZ (timezone) must be *no*:

	timedatectl status

If you need to set RTC in local TZ to *no*:

	timedatectl set-local-rtc 0
			
Calibrate system time:

	sudo ntpd -qg
		
For dual boot your Windows OS needs to be adjusted to UTC:

	https://wiki.archlinux.org/index.php/System_time#Set_hardware_clock_from_system_clock
		
### SSH 

 If you have private and public keys, bypass this *SSH* creation. If you do not have, let's create one to use ssh:

	ssh-keygen -o -a 300 -t ed25519 -f ~/.ssh/id_ed25519 -C "MY-MACHINE"
	*then use a passphrase when it asks*

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

### Firewall

Go to menu and find *Firewall Configuration* app:
		
	profile=Office
	status=on
	incoming=deny
	outgoing=allow
	
### Using Vim as the default terminal editor

	sudo pacman -S vim
	sudo vim /etc/environment (edit: "EDITOR=/usr/bin/vim")	
	vim .profile (edit: "export EDITOR=/usr/bin/vim")
	reboot
	
### Git (+ssh config)

	sudo pacman -S git
		
Create directories to repositories of code, example:

	mkdir ~/Repositories
	mkdir ~/Repositories/github
	mkdir ~/Repositories/gitbucket

Config your global Git identity:

	git config --global user.email "you@example.com"
	git config --global user.name "Your Name"

Add your public ssh key to git server. You can read the value with the command below, then add to the git website or git server:

	xclip -sel clip < ~/.ssh/id_ed25519.pub

Test activity on your git, and check if everything is ok, example:

	cd ~/Repositories/github
	git clone git@github.com:ThiagoSchetini/drnglib.git

### Remove Manjaro Junk

	sudo pacman -Rs manjaro-hello manjaro-application-utility
	sudo pacman -Rs yay 
	sudo pacman -Rs nano
	sudo pacman -Rs vi (ensure you installed the vim part, because pacaur will need it)
	sudo pacman -Rs epiphany
	sudo pacman -Rs pamac-gtk pamac-gnome-integration gnome-layout-switcher
	
### Check/Install Utilities packages that we are gonna use (probably some are pre-installed):

	sudo pacman -S curl
	sudo pacman -S grep
	sudo pacman -S make 	
	sudo pacman -S base-devel (extra packages to build, compile. Select all)		
	sudo pacman -S mhwd 		
	sudo pacman -S fuse-exfat 
	sudo pacman -S archlinux-keyring
	sudo pacman -S mesa-demos (GPU monitoring and test > www.mesa3d.org)
	sudo pacman -S tlpui (usefull visual interface for tlp)
	sudo pacman -S pacaur (AUR package manager)
	sudo pacman -S bashtop (much metter than 'top' or 'htop')
	sudo pacman -S iotop
	sudo pacman -S hdparm (best as linux helper to hard and solid state drives)
	sudo pacman -S xclip
	sudo pacman -S the_silver_searcher (more performance than grep, use as 'ag')
	sudo pacman -S links (terminal web navigator)
	sudo pacman -S lrzip (more powerfull, read the man)
	sudo pacman -S etcher (ISO's pen drive burner)
	sudo pacman -S transmission-gtk (torrents)
	sudo pacman -S qopenvpn (VPN)
	
## System Improvement

### Nvidia fan control for Manjaro Arch Linux distro

First of all:

	sudo nvidia-settings
	goto GPU0: Thermal Settings/Enable GPU Fan Settings
	goto: X Server Display Configuration/Save to X Configuration File
	save on: /etc/X11/mhwd.d/nvidia.conf 
	
	sudo mhwd-gpu --setmod nvidia --setxorg /etc/X11/mhwd.d/nvidia.conf 
	reboot
	
Try to install: 

	pacaur -S nfancurve -d
	
If it breaks because "nvidia-settings" follow the next steps. Open PKGBUILD:

	vim /home/thiagosc/.cache/pacaur/nfancurve/PKGBUILD 
	
Remove the line:
	
	depends=("bash" "nvidia-utils" "nvidia-settings" "procps")
	
Try again:

	pacaur -S nfancurve					
	
Config the fan profile and PWM signal. Quiet and non-linear increase suggestion:

	sudo vim /etc/nfancurve.conf
	
	min_t="25" (if your GPU supports zero speed fans)
	sleep_time="3" (more responsive fans)

	fcurve="30 30 70 85 85 99" # fan speeds
	tcurve="30 55 60 65 80 85" # temperatures

If your GPU has two PWM fan, set it or use the same profile on **which_curve** param:

	which_curve="1 1 1 2"

More configs? Read the instructions on the nfancurve.conf. Required test:

	nfancurve -l -c /etc/nfancurve.conf 

Finally, enable as a service:

	systemctl --user daemon-reload
	systemctl --user start nfancurve.service
	systemctl --user enable nfancurve.service
	
	reboot
	
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
	
### Disable mitigations and hibernate

Disable the linux kernel juggling to avoid hyperthreading exploit vulnerabilities, the mitigations.

Find your current *kernel* configuration file:

	ls -la /boot/loader/entries

Open it and add the flags **nohibernate** and  **mitigations=off** on boot options as the example below:

	sudo vim /boot/loader/entries/manjarolinux5.9.conf
	'options	root=UUID=fac688f2-9966-49df-9320-b533b82d6b89 rw nohibernate mitigations=off'

Reboot
		
Check if the boot params are they there:

	cat /proc/cmdline

Check if Mitigations are off looking for disabled and vulnerable words (great!):

	grep -H '' /sys/devices/system/cpu/vulnerabilities/*
	
### Install nvme user space tooling for Linux

	pacman -S nvme-cli
	
### More parallelism on sata protocol to SSD's

Check the scheduler used to all devices (rotating, ssd, nvme). **nvme** does not need because is connected directly on PCIe lanes:

	[none] to nvme
	[mq-deadline] to ssd
	[bfq] to rotational 
		
	grep "" /sys/block/*/queue/scheduler
		
Let's increase the *mq-deadline* paralellism (SSD only). Open the *.rules* on vim to edit:

	sudo vim /etc/udev/rules.d/60-ioscheduler.rules
		
Use this sample. Copy and paste to the .rules file above. Note the "32" value on the last line:

	# set scheduler for NVMe
	ACTION=="add|change", KERNEL=="nvme[0-9]*", ATTR{queue/scheduler}="none"

	# set scheduler for SSD
	ACTION=="add|change", KERNEL=="sd[a-z]", ATTR{queue/rotational}=="0", ATTR{queue/scheduler}="mq-deadline"

	# set scheduler for rotating disks
	ACTION=="add|change", KERNEL=="sd[a-z]", ATTR{queue/rotational}=="1", ATTR{queue/scheduler}="bfq"

	# improves throughput of devices at the cost of latency (default is 16). Only on SSD [mq-deadline]
	ACTION=="add|change", KERNEL=="sd[a-z]", ATTR{queue/rotational}=="0", ATTR{queue/iosched/fifo_batch}="32"

Reboot then check:
		
	grep "" /sys/block/*/queue/scheduler
	grep "" /sys/block/*/queue/iosched/fifo_batch
	
### Enable trim auto service (to SSD's and nvme's)
	
*Continuous trim is not recommended by UNIX community*. Distros like Ubuntu come with trim scheduled to run once a week. Arch does not. Even so, let's check first:

	sudo systemctl enable fstrim.timer
	reboot
	systemctl status fstrim.timer
	
	Hint: you should read *Active: active (waiting)*

### TLPUI power configuration

Look for TLPUI app and open it. Read everything and check if there is something to customize.

### Format and permanent mount a unit

Make the label:
	
	sudo parted /dev/sdb (sdc|sdd|nvme1|nvme2...)
	mklabel gpt
	
Partition:

	mkpart "" ext4 2M 250G (500G|1000G|2000G...)
	quit
		
Format and note/save the UUID generated:

	sudo mkfs.ext4 /dev/sdb1
	'...'
	'Filesystem UUID: 3b5f8d12-bc30-4358-9739-b6dc8ae251ff'
	'...'
	
Create the path where you want to mount:

	mkdir /data
		
Open fstab to edit:

	sudo vim /etc/fstab
		
Add generated UUID to fstab, path and the format (ext4 in this case):

	# /dev/sdb1
	UUID=3b5f8d12-bc30-4358-9739-b6dc8ae251ff	/data		ext4		rw,relatime	0 0
		
Reboot then check:

	sudo blkid (to check the uuid's)
	sudo fdisk --list
	ls -la /data
	
### Keyring

Refresh the the web of trust of pacman keys:

	sudo pacman-key --init
	sudo pacman-key --refresh-keys
	sudo pacman-key --populate archlinux manjaro
	
### Remember to customize on Settings

Wifi: config your wireless and them, if using desktop cable, disable it.

Sound: check your output and your microphone

Power: Blank Screen=Never | Automatic Suspend=Off | Power Button Action = Power Off

Displays/Night Light: manual set 17h00 to 8h00

Mouse & Touchpad/Mouse: enable natural scrolling
		
### Darkness

On terminal:

	bmenu				
	7 System & Setting > Appearance
	select dark themes
		
Open *Tweaks* on Accessories/Tweaks/Appearance:

	Applications: Matcha-dark-sea
	Cursor: Xcursor-breeze
	Icons: Papirus-Dark-Maia
	Shell: Matcha-dark-sea

Open Accessories/Tweaks/Keyboard & Mouse:

	disable middle click paste
	disable mouse click emulation

Open Accessories/Kvantum Manager/Change/Delete Theme:

	choose *Matchama-Dark-Aliz*

On terminal:

	vim .config/gtk-2.0/gtkfilechooser.ini
	'ShowHidden=true'

	vim .config/gtk-3.0/settings.ini
	'gtk-application-prefer-dark-theme=1'

	vim .config/gtk-4.0/settings.ini
	'gtk-application-prefer-dark-theme=1'
	
Reboot
	
## Development Packages

### Docker

	sudo pacman -S docker
	sudo usermod -aG docker $USER
	sudo systemctl start docker 
	sudo systemctl enable docker 
	sudo docker run hello-world 

If you have nvidia GPU:

	pacaur -S nvidia-container-runtime
	reboot system
	docker run -it --rm --gpus all ubuntu nvidia-smi
	
### JVM

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

InteliJ, IDE for Java and Scala:

	sudo pacman -S intellij-idea-community-edition
		
### .NET 5

Community arch supported versions, search:

	sudo pacman -Ss dotnet
	
If the version is not there, use snap:

	sudo pacman -S snapd 
	systemctl enable --now apparmor.service
 	systemctl enable --now snapd.apparmor.service
	sudo snap install dotnet-sdk --classic --channel=5.0
	
Add autocomplete on .zshrc:

	# zsh parameter completion for the dotnet CLI
	_dotnet_zsh_complete()
	{
	  local completions=("$(dotnet complete "$words")")

	  reply=( "${(ps:\n:)completions}" )
	}
	compctl -K _dotnet_zsh_complete dotnet
	
After all, check it:

	dotnet --list-sdks
	dotnet --version
	dotnet a+TAB (*autocomplete)
	dotnet +TAB (*autocomplete)
	
Visual Studio code, ide for C# (native version):

	pacaur -S visual-studio-code-bin 
	open it
	CTRL+P
	ext install ms-dotnettools.csharp
	
### Python3

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
		
Pycharm, IDE for Python:

	sudo pacman -S pycharm-community-edition
		
Open Pycharm, on settings, new projects check if all interpreters are recognized to create virtual envs (python3 and pypy3)

Add the two envs that we just created on terminal as *Existing environments* and check *Make available to all projects*

### Soap UI

	pacaur -S soapui

### Sublime Text 3 (native)

Install the GPG key:

	curl -O https://download.sublimetext.com/sublimehq-pub.gpg && sudo pacman-key --add sublimehq-pub.gpg && sudo pacman-key --lsign-key 8A8F901A && rm sublimehq-pub.gpg
	
Select stable channel:

	echo -e "\n[sublime-text]\nServer = https://download.sublimetext.com/arch/stable/x86_64" | sudo tee -a /etc/pacman.conf
	
Install:

	sudo pacman -S sublime-text
	
If does not work do with pacman update: 

	sudo pacman -Syu sublime-text
	
## User Packages

All apps are native, not **Electron** crap. Select the ones that make sense for you.

### Office

	sudo pacman -S freeoffice (remember to add the key for free)
	sudo pacman -S rhythmbox (audio player)
	sudo pacman -S audacity (audio editor)
	sudo pacman -S kdenlive (video editor)
	sudo pacman -S krita (image editor)
	pacaur -S exif (image metadata editor)
	pacaur -S ideamaker (3D files editor)
	
### Internet & Communication

	sudo pacman -S firefox
	pacaur -S discord-canary
	pacaur -S teams
	pacaur -S slack-desktop
	pacaur -S telegram-desktop
	pacaur -S zoom
	pacaur -S teamviewer14

### Spotify (native for linux)

Sign the key and install:

	curl -sS https://download.spotify.com/debian/pubkey.gpg | gpg --import -
	pacaur -S spotify

### Gaming

	pacaur -S unigine-superposition
	pacaur -S unigine-heaven

## Final check list

	[mantain arch] bmenu (1 Package manager UI>2 Maintain System)
	[clean cache] sudo pacman -Sc
	[clean cache] sudo pacaur -Sc
	[manual trim] sudo fstrim --verbose --all
	[nvidia power] watch -t nvidia-smi
	[motherboard] watch -t sensors
	[CPU power] sudo powertop
	[ram memory] free
	[swap check] less /proc/meminfo | grep Swap
	[directory space] du -h --max-depth=1 .cache/epiphany 
	[directory space] sudo du -h --max-depth=1 /
	[device speed] sudo hdparm -t /dev/sda 
	[device space] df -H 
	[gpu rendering] glxinfo | grep 'direct rendering'

## Change Kernel 

All kernel boot params (official docs): https://www.kernel.org/doc/html/latest/admin-guide/kernel-parameters.html

	[list kernels] ls /usr/lib/modules
	[active kernel] echo $(uname -r)
	
Change default kernel example:

	sudo vim /boot/loader/loader.conf 
	"default manjarolinux5.9"
	
Kernel params read and edit example:

	cat /boot/loader/entries/manjarolinux5.9.conf
	sudo vim /boot/loader/entries/manjarolinux5.9.conf
	
## VPN native Arch client

You can add the file.ovpn on Settings/Network or using the terminal:

	sudo nmcli connection import type openvpn file /path/to/your.ovpn
	nmcli connection up <connection-name>
	nmcli connection show
	
Use the default gateway if your internet does not work anymore on the VPN host:	

	sudo ip route add default via <default-route>
	
Get <default-route> on Settings/Network/Wired-or-WiFi/settings/Details/Defaul Route

	ex: sudo ip route add default via 192.168.15.1
