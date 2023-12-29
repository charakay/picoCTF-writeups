# Operation Orchid

## Description
Download this disk image and find the flag. Note: if you are using the webshell, download and extract the disk image. <br>
Category : forensics

## Hints
No hints are available for this CTF.

## Approach
1. Download the compressed disk image  `wget https://artifacts.picoctf.net/c/214/disk.flag.img.gz`
2. The downloaded file is zipped. Unzip it with `gzip -d disk.flag.img.gz`
3. After the file is unzipped, now  `mmls disk.flag.img` to check details about the disc image:
```text
DOS Partition Table
Offset Sector: 0
Units are in 512-byte sectors

      Slot      Start        End          Length       Description
000:  Meta      0000000000   0000000000   0000000001   Primary Table (#0)
001:  -------   0000000000   0000002047   0000002048   Unallocated
002:  000:000   0000002048   0000206847   0000204800   Linux (0x83)
003:  000:001   0000206848   0000411647   0000204800   Linux Swap / Solaris x86 (0x82)
004:  000:002   0000411648   0000819199   0000407552   Linux (0x83)
```
<br>
We can observe few partitions. <br>

4. I will investigate on the first interesting partition which has a sector start with value `0000002048`<br>
`fls -o 0000411648 disk.flag.img `
```text d/d 460:        home
d/d 11: lost+found
d/d 12: boot
d/d 13: etc
d/d 81: proc
d/d 82: dev
d/d 83: tmp
d/d 84: lib
d/d 87: var
d/d 96: usr
d/d 106:        bin
d/d 120:        sbin
d/d 466:        media
d/d 470:        mnt
d/d 471:        opt
d/d 472:        root
d/d 473:        run
d/d 475:        srv
d/d 476:        sys
d/d 2041:       swap
V/V 51001:      $OrphanFiles```
