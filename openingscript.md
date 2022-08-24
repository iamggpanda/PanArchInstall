####Checking the disk and creating partitions#####
lsblk
gdisk /dev/<diskname>
n - new partitons.  500 mb EFI,  xMB swap, chonky root , rest home
enter starting and ending sizes 
enter the type of file system necessary
w - write changes ; y - confirm changes
lsblk = will now show you the new partitions

####Formatting the partitions####
mkfs.vfat /dev/<efi>
mkswap /dev/<swap>
swapon /dev/<swap>
mkfs.ext4 /dev/<root>
mkfs.ext4 /dev/<home>
lsblk to show the partitions


####Mounting the partitions####
mount /dev/<root> /mnt
# we dont have boot/efi or home partitions so ---
mkdir -p /mnt/boot/efi
mkdir /mnt/home
mount /dev/<efi> /mnt/boot/efi
mount /dev/<home> /mnt/home
lsblk to check the mount points


####Ready to install - pacstrap####
pacstrap /mnt base linux linux-firmware git vim intel-ucode
## now create filesystem table 
genfstab -U /mnt >> /mnt/etc/fstab
arch-chroot /mnt
######## This is where you git clone your repo and use your own commands########
git clone <addr>
#edit whatever is necessary (optional)

####after the installation####
vim /etc/mkinitcpio.conf
# under MODULES =()  - add the display driver i915, amdgpu, nvidia ----if its VM just leave it blank
# if you made any changes to mkinitcpio.conf then you must run: mkinitcpio -p linux 
vim /etc/default/grub
# change video resolution if necessary GRUB_GFX_MODE=auto (change this to 1920x1080) and in GRUB_CMDLINE_LINUX_DEFAULT="loglevel=3 quiet" add video=1920x1080
grub-mkconfig -o /boot/grub/grub.cfg


#to be added/addressed in the future 
system timezone  : ln -sf /usr/share/zoneinfo/<region>/<country> /etc/localtime  -- so maybe remove this setting in the de/wm setup file and add it to the archmain.sh
change localization in the archmain.sh script. It just doesn't work that way. Set localization manually maybe ???