# Poco F1 Windows install Guide
If you have any questions
My telegram @byoneet
> This guide contains the materials which work 100% stable
> without major errors `on Xiaomi Poco F1 beryllium EBBG Panel`.
I've created this guide for those who don't want to deal 
with all the nuances and subtleties. But if you want so,
you have the links below for a detailed review

_Resources:_

- [Renegade] Official web-site 
- [Telegram] Group
- [Discord] Server
- [Forum] Project
- [4PDA] Rusian Forum 

## Firstly 
- Which touchscreen?
> You should download `Device Info HW`
> String `touchscreen`
> My value is `fts_ts` which means `ebbg`
> If you have `nvt_ts` or other, you have `tianma`
> This will be needed, when downloading the UEFI
> and the drivers

- [R-Drive] ([timecode 3:40]) partitions
> It's not necessary, but at least you'll have 
a backup of the main partitions and if you do 
something wrong, like deleting the wrong partition,
you can still restore the device to its original
state without `"bricking"` the device.
You won't be able to do this operation at the 
very beginning because it is only available in 
mass storage mode, which is in turn
available in the UEFI menu.

- Check supportable [devices list]

- Warming up problem
> During gaming, it gets quite hot. I've seen others solve 
> this problem by attaching a laptop radiator to a case.

- Unlock Bootloader

- You will need an OTG mouse (not bluetooth). Keyboard is not necessary but recommended

## Downloads
- [Windows 11 ARM]
- [TWRP]
- [Drivers]
> `v2.0 rc2` drivers were compiled with an error,
so I downloaded both `v2.0 rc1` and `v2.0 rc2`
and moved GPU driver from `rc2` to `rc1` via file replacement.
Now the latest version is `2210.1-fix`. I don't know how it works,
so you may try to install it, but I don't know anything about its functionality.
- [WinPE]
- [DISM]
- [parted]
- [platform-tools ]
- [uefi ]
- [Activator ]
- [.reg fix file]
- [devcfg]
- [R-Drive ]
- [DFE] Disable Force Encryption
- Android ROM Poco F1 (beryllium) 
> Actually, you may use any ROM compatible with Poco F1,
but I will use `Pixel Experience`. You can find both
`Pixel Experience` and `MIUI12` on GitHub
- [Firmware] Poco F1 (beryllium) 
>  `All these files are also available on GitHub`

## Installation
- Move the `platform-tools` folder to any path and 
 you mustn't delete it, otherwise the cmd commands won't work.
>I suggest you to move it to `C:\` , but it's your choice
- Press `Win+R` and type `sysdm.cpl`
- Go to `Advanced (Дополнительно)` and press `Environment variables (Переменные среды)`
- In `User variables (Переменные среды пользователя)` find `PATH`
- Press `Edit (Изменить)` then `New (Создать)` and copy `platform-tools` path
> Example `C:\platform-tools`
- In `System variables (Системные переменные)` find `PATH`
- Press `Edit (Изменить)` then `New (Создать)` and copy `platform-tools` path
> Example `C:\platform-tools`
- Now you can close this menu
- Create a folder, put all the files into it and open a cmd there
```sh
cd C:\TestFolder
```
- Connect your device to your PC
- Reload to fastboot
- Boot TWRP
```sh
fastboot boot twrp-3.6.0_9-0-beryllium.img
```
- Push parted
```sh
adb push parted /sdcard
```
- Start adb shell
```sh
adb shell
```
- Move parted to sbin directory
```sh
cp /sdcard/parted /sbin/ && chmod 755 /sbin/parted
```
- Umount data and sdcard
```sh
umount /data && umount /sdcard
```
- Start parted
```sh
parted /dev/block/sda
```
- Make neccesary partitions (`128GB model`)
```sh
mkpart esp fat32 1611MB 2100MB
mkpart win ntfs 2100MB 60GB
mkpart pe fat32 60GB 66GB
mkpart userdata ext4 66GB 123GB
```
- Make neccesary partitions (128GB model 100% for Windows)
```sh
mkpart esp fat32 1611MB 2100MB
mkpart pe fat32 2100MB 4GB
mkpart win ntfs 4GB 123GB
```
- Make neccesary partitions (64GB model)
```sh
mkpart esp fat32 1611MB 2100MB
mkpart win ntfs 2100MB 30GB
mkpart pe fat32 30GB 36GB
mkpart userdata ext4 36GB 57GB
```
- Make neccesary partitions (64GB model 100% for Windows)
```sh
mkpart esp fat32 1611MB 2100MB
mkpart win ntfs 2100MB 51GB
mkpart pe fat32 51GB 57GB
```
- Set esp on
```sh
set 21 esp on
quit
```
- Reboot to bootloader
```sh
fastboot boot twrp-3.6.0_9-0-beryllium.img
```
- Format the partions
```sh
mkfs.fat -F32 -s1 /dev/block/by-name/pe
mkfs.fat -F32 -s1 /dev/block/by-name/esp
mkfs.ntfs -f /dev/block/by-name/win
```
- `ONLY IF` you created a partition for Android
```sh
mke2fs -t ext4 /dev/block/by-name/userdata
```
- Mount PE partion
```sh
mount /dev/block/by-name/pe /mnt
```
- Copy PE files to the /mnt or USB drive
```sh
adb push boot efi sources bootmgr.efi dism drivers activate.zip fix.reg win11.iso parted twrp.img commands.txt /mnt
```
- Reboot

## In WinPE
- Type in cmd or copy from `commands.txt` and don't close it
```sh
diskpart
select disk 0
list part
select part 21
assign letter=Y
exit
```
- Find DISM in Explorer and start it
- Go File>Apply Image
- In the first field, select the `iso` image of Windows
- In the second field, select the `C:\` 
- Tick `Add boot` and `Format`
- Wait for a while
- Press `Open session`
- Press `Drivers` then `Add`
- Select the folder with the drivers
- After adding the drivers, you may close the `DISM`
- Now open the cmd again and copy the following commands
```sh
bcdedit /store Y:\efi\microsoft\boot\bcd /set {Default} testsigning on
bcdedit /store Y:\efi\microsoft\boot\bcd /set {Default} nointegritychecks on
shutdown -s -t 0
```
- Go fastboot 
- Type:
```sh
fastboot flash devcfg_a devcfg-beryllium_FixTS.img
```
```sh
fastboot flash devcfg_b devcfg-beryllium_FixTS.img
```
```sh
fastboot flash devcfg_ab devcfg-beryllium_FixTS.img
```

## DualBoot

> I made dual boot via installing the Windows uefi instead of traditional recovery.
That means that you will have to press `volume+` and `lock button` when you will need to enter into the Windows

- Go to fastboot
- Boot to recovery
```sh
fastboot boot twrp.img
```
- Push the ROM, DisableForceEncryption and Firmware to `sdcard`
```sh
adb push PixelExp_Plus_beryllium-12.zip DisableForceEncrypt.zip fw_beryllium_9.0.zip /sdcard
```
- Go to phone
- Press `Install`
- Select your ROM
- Press `Add more zips`
- Select DisableForceEncryption
- Press `Add more zips`
- Select firmware
- Swipe
- Reboot to system 

> You may need to flash devcfg again after installing the Android for Windows to work properly.
Enter the Android and check if all the functions like sound, sim-card work well. If not try to install
other firmware (for ex. fw_beryllium_10.0). I left all the versions on GitHub
















[//]: # (These are reference links used in the body of this note and get stripped out when the markdown processor does its job. There is no need to format nicely because it shouldn't be seen. Thanks SO - http://stackoverflow.com/questions/4823468/store-comments-in-markdown-syntax)

   [Renegade]: <https://renegade-project.tech/en/home>
   [Telegram]: <https://t.me/joinchat/MNjTmBqHIokjweeN0SpoyA>
   [4PDA]: <https://4pda.to/forum/index.php?showtopic=884586>
   [Discord]: <https://discord.gg/XXBWfag>
   [Forum]: <https://forum.renegade-project.org/>
   [Devices list]: <https://renegade-project.tech/en/state>
   [Windows 11 ARM]: <https://drive.google.com/file/d/11UFTsnuyfgZyBsyYC31jl_FYLI__4q0p/view?usp=share_link>
   [TWRP]: <https://dl.twrp.me/beryllium/twrp-3.6.0_9-0-beryllium.img.html>
   [Drivers]: <https://github.com/edk2-porting/WOA-Drivers/releases>
   [WinPE]: <https://drive.google.com/file/d/15YjjFxhFZQBIzf5uSnC32qaouyplEo2j/view?usp=sharing>
   [DISM ]: <https://drive.google.com/file/d/1aHkMReuiH3ArBAEF7AAZxEEdUUDlBr6z/view?usp=sharing>
   [parted]: <https://drive.google.com/file/d/1n7FSW9zA7pDA9cc5TBvg7twt00hvSU-l/view?usp=sharing>
   [platform-tools]: <https://developer.android.com/studio/releases/platform-tools>
   [uefi]: <https://github.com/edk2-porting/edk2-sdm845/releases>
   [Activator]: <https://drive.google.com/file/d/1wPUVqtXqDKLw0OY0g_gTdfCNs2ol6k13/view?usp=sharing>
   [.reg fix file]: <https://drive.google.com/file/d/1c-SwgWu1uk8IPkqFYcamWQnY3au2nNNk/view?usp=sharing>
   [devcfg]: <https://drive.google.com/file/d/1pIxCpINozxOuUpSxAtyMlc2uZySXu5TD/view?usp=sharing>
   [R-Drive]: <https://disk.yandex.kz/d/RF_yS74F3UYjPU>
   [DFE]: <https://drive.google.com/file/d/1DYDGpIhQ2tzG7UpaQOddjHQj9p2PCPJo/view?usp=sharing>
   [Firmware]: <https://xiaomifirmwareupdater.com/firmware/beryllium/>
   [timecode 3:40]: <https://youtu.be/WBc5HmT-rSM?t=220>







