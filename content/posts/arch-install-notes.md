---
title: "i use Arch, btw"
date: 2020-03-15T11:59:00-04:00
draft: false 
---

Yesterday my Pop OS install got borked and instead of
fixing it, since I'm bored at home and self-quarantined anyway, decided to try
installing Arch Linux. First time, and how hard could it be anyway? 

Got it done finally, and learned a lot along the way. Here are some notes to
speed it up next time.

1. Boot into my Windows partition, download the Arch ISO, use Rufus to make a
   bootable drive, and boot into Arch.

2. I decided to reuse the Linux partitions, so no new partitioning necessary.
   `fdisk -l` lists the drives. 
   - `mkfs.fat -F32 /dev/sda5` to ready the EFI partition
   - `mkswap /dev/sda6` and `swapon /dev/sda6` to setup swap
   - `mkfs.ext4 /dev/sda7` to create filesystem

3. `wifi-menu` is a huge improvement over past methods for connecting to WPA2.
   After connection, use `dhcpcd wlan0` if IP isn't assigned automatically. Ping
   to check connection.

4. Get fastest mirrors.
   - `pacman -Syy` to refresh the Pacman
   - `pacman -S reflector` to install Reflector
   - `reflector -c "US" -f 12 -l 10 -n 12 --save /etc/pacman.d/mirrorlist` for
   best mirror list

5. Mount drive and install kernel
   - `mount /dev/sda7 /mnt` to mount
   - `pacstrap /mnt base linux linux-firmware vim` to install base packages and
   Vim

6. Generate an fstab file: `genfstab -U /mnt >> /mnt/etc/fstab`

7. Access system as root: `arch-chroot /mnt`

8. Setup hostname `echo a1kn > /etc/hostname` and then `vim /etc/hosts` to edit
   hosts file. Add:
   ```
   127.0.0.1  localhost
   ::1        localhost
   127.0.1.1  a1kn
   ```

9. Use `passwd` to setup root password.

10. Install bootloader. I'm still using Grub because it's rock-solid and simple.
    - `pacman -S grub efibootmgr` to install
    - `mkdir /boot/efi` and `mount /dev/sda5 /boot/efi` to mount EFI partition
    - `grub-install --efi-directory=/boot/efi`
    - `grub-mkconfig -o /boot/grub/grub.cfg`

11. Install Gnome `pacman -S gnome`
    - `systemctl enable gdm.service`
    - `systemctl enable NetworkManager.service`

12. `reboot`

Fun part is done! Now for the boring system config stuff. Some stuff
to do (in a somewhat particular order):

- Add another user and login with that user instead of root
- Install `sudo` and add user to sudo
- Increase speed on Thinkpad nipple by adjusting mouse speed in settings
- Install `zsh` and switch default shell to it
- Clone [dotfiles](https://github.com/a1kn/dotfiles) to home directory
- Install `node npm yarn ruby` to get some development stuff ready to go
- Install `ohmyzsh`, `tmux`, and `alacritty`. Open `vim` and type `:PlugInstall`
  to install plugins
- Install fonts

And...we're out. Things are feeling pretty good and snappy and we'll have to
keep installing packages over the next few days, but right now we should have a
pretty usable environment.
