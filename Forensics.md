All forensics tools and links

## Images
### Metadata Tools:
* exiftool
* exif
* identify
### Steganography Tools:
* zsteg
* strings 
### Using CyberChef for Steganography: Showcase "Red - from PicoCTF Forensics"
* Just type steg in search and you will get mutliple methods
* For example you can extract Least Significant Bit (LSB) from image:
*   Insert the image .... then ..... Put the colors ex: RGB .... then ... whatever you notice ex: you notice "==" so base64 ... then ... from base64

## Image Disk
When you got an image disk this means it may have all the data even the removed data so for this we will use library that is called **The Sleuth Kit** which has useful tools to recover them

To show partition layout inside a disk image, this will show you the partitions and from there you can take the offset and use it later for your recovering process
```
mmls disk.flag.img
```
example result:
```
      Slot      Start        End          Length       Description
000:  Meta      0000000000   0000000000   0000000001   Primary Table (#0)
001:  -------   0000000000   0000002047   0000002048   Unallocated
002:  000:000   0000002048   0000206847   0000204800   Linux (0x83)         ---------> Usually here where you have to look anytimt you see (Linux 0x83)
003:  000:001   0000206848   0000360447   0000153600   Linux Swap / Solaris x86 (0x82)
004:  000:002   0000360448   0000614399   0000253952   Linux (0x83)         ---------> Or here, note: offset here is 360448
```
To list files and folders even the deleted ones: (-r is to list recursively) (-o is the offset of the partition we want)
```
fls -r -o 360448 disk.flag.img
```
example result (Not all of it since its long but the intresting part of it):
```
+ d/d 3981:     my_folder
++ r/r * 2082(realloc): flag.txt -------> note "*" which tells file is deleted and "realloc" tells space may used and it is partially or fully corrupted
++ r/r 2371:    flag.uni.txt     -------> but we have an undeleted one so we propabily will use this which has inode = 2371
d/d 1996:       run
d/d 1997:       srv
d/d 1998:       sys
d/d 2358:       swap
V/V 31745:      $OrphanFiles
```

To extract a file by its inode number:
```
icat -o 360448 disk.flag.img 2371  --> note we have specified the partition we need and the inode 
```
Other useful tools from the sleuth kit:
```
stat	 ----> Show metadata info for a file (inode details)
fsstat ----> Show file system details (type, size, etc.)
```
