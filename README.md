# Lenovo IdeaPad Pro 5 14APH8 Display Fix for Ubuntu
If the monitor of the IdeaPad glitches on Ubuntu, chances are that the monitor EDID is wrong.

## Steps

### Check current checksum
The EDID checksum should end with values `f8 90`.

If you don't have `edid-decode` installed, install it with `sudo apt install edid-decode`.

Run `edid-decode < /sys/class/drm/card1-eDP-1/edid`

Check if the checksum ends with `f8 90` - if not, the EDID is wrong.

### Create file with correct checksum hex
Create a file with any name, such as `edid-b0rked.txt` with the following content:

```
00 ff ff ff ff ff ff 00 0e 6f 16 14 00 00 00 00
00 20 01 04 b5 1e 13 78 03 21 15 a8 53 49 9c 25
0f 50 54 00 00 00 01 01 01 01 01 01 01 01 01 01
01 01 01 01 01 01 ce 87 40 a0 b0 08 6a 70 30 20
36 00 2d bc 10 00 00 18 00 00 00 fd 00 28 78 e5
e5 46 01 0a 20 20 20 20 20 20 00 00 00 fe 00 43
53 4f 54 20 54 33 0a 20 20 20 20 20 00 00 00 fe
00 4d 4e 45 30 30 37 5a 41 31 2d 35 0a 20 01 af

70 13 79 00 00 03 01 14 9a 0f 01 05 3f 0b 9f 00
2f 00 1f 00 07 07 69 00 02 00 05 00 00 00 00 00
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
00 00 00 00 00 00 00 00 00 00 00 00 00 00 f8 90
```

### Create a binary EDID file
Run `xxd -r -p edid-b0rked.txt edid-fixed.bin`. Note: You can change the names to your liking.

### Move binary file
Make sure that folder `/usr/lib/firmware/edid` exist. If not, create it (`sudo mkdir /usr/lib/firmware/edid`)

Move the binary file to `/usr/lib/firmware/edid`:
`sudo mv edid-fixed.bin /usr/lib/firmware/edid`

### Add to kernel boot parameter
To add kernel boot parameters, you need to modify `GRUB_CMDLINE_LINUX_DEFAULT` in `/etc/default/grub`.

Add the following AFTER the current content (usually `"quiet splash"`):
`drm.edid_firmware=eDP-1:edid/edid-fixed.bin` Note - make sure the file name is correct.

It should now look something like this:
`GRUB_CMDLINE_LINUX_DEFAULT="quiet splash drm.edid_firmware=eDP-1:edid/edid-fixed.bin"`

Run `sudo update-grub`

## DONE!