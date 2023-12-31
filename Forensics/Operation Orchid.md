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

4. I will investigate on the first interesting partition which has a sector start with value `0000411648`<br>
`fls -o 0000411648 disk.flag.img `
```text
d/d 460:        home
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
V/V 51001:      $OrphanFiles
```
5. Locate the flag file by `fls -o 0000411648 disk.flag.img -r | grep flag` this looks up the disk contents recursively (including subdirectories), and then filters the output to display only the lines containing the word "flag"
```text
$ fls -o 0000411648 disk.flag.img -r | grep flag
+ r/r * 1876(realloc):  flag.txt
+ r/r 1782:     flag.txt.enc
```
6. i tried cat flag.txt but nothing interesting there, but flag.txt.enc has something `$ icat -o 0000411648 disk.flag.img 1782`
```
Salted__S�+%���+�O��k�ђ(A����c��
                                @]ԣ
L�ޢȤ7� ���؎$�'%  `
```
seems as if it has been encrypted, we can observe this through the file extension as well -> file.txt.enc

7. in an earlier CTF in forensics section, we were instructed to check root, so we will check root `fls -o 0000411648 disk.flag.img 472 `
```
r/r 1875:       .ash_history
r/r * 1876(realloc):    flag.txt
r/r 1782:       flag.txt.enc
```
looks like there is a bash history file.
<br>
`icat -o 0000411648 disk.flag.img 1875`
This gives us how the .txt.enc has been encrypted.
```
openssl aes256 -salt -in flag.txt -out flag.txt.enc -k unbreakablepassword1234567
```
8. so we need to decrypt flag.txt.enc. for that, we need to first save the content of the flag.txt.enc. I have save it here as the same name using `icat -o 0000411648 disk.flag.img 1782 > flag.txt.enc`
```
┌──(kali㉿kali)-[~/tmp]
└─$ ls
disk.flag.img  flag.txt.enc
```
9. Now we will be using the same openssl aes256 and reverse it to decrypt the content saved in flag.txt.enc<br>
```
openssl aes256 -salt -out flagDecrypted.txt -in flag.txt.enc -k unbreakablepassword1234567 -d
```
<br>
It will show as bad decrypt, but still we find the flag by <br>

```
cat flagDecryped.txt
```
<details>
  <summary><i>Click to reveal flag</i></summary>
  <b>picoCTF{h4un71ng_p457_1d02081e}</b>
</details>
