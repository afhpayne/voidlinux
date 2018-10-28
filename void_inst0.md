* Creating LVM on encrypted Luks Partition

drive example = /dev/sdx

1. Create partition
gdisk
make drive gpt
create EFI partition ~600MiB
create Linux partition 100% remaining

2. Format efi as Fat32
mkfs.fat -F 32 /dev/sdx1

3. Encrypt linux partition
cryptsetup luksFormat --type luks2 /dev/sdx2
cryptsetup luksOpen /dev/sdx2 crypt_name

4. Make LVM partitions
pvcreate /dev/mapper/crypt_name

vgcreate vg_name /dev/mapper/crypt_name

lvcreate vg_name -L 8GiB vg_name -n swap
lvcreate vg_name -l 100%FREE vg_name -n sysroot

5. Create and mount LVM filesystems
mkswap /dev/vg_name/swap
swapon /dev/vg_name/swap
mkfs.ext4 /dev/vg_name/sysroot
mount /dev/vg_name/sysroot /mnt

6. Mount efi partition to /boot
mkdir /mnt/boot
mount /dev/sdx1 /mnt/boot
