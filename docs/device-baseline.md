# Detected ER1 baseline

Read-only inventory collected on 2026-08-11 from JDCloud RE-CS-07.

## Bootloader

- U-Boot: `U-Boot 2016.01 (Dec 16 2025 - 15:27:02 +0800)`
- Board: `QCA, IPQ6018-CP03-C4`
- Upstream release: `chenxin527/uboot-ipq60xx-emmc-build`, tag
  `25.12.16-15.26.27`
- APPSBL and APPSBL_1 SHA-256:
  `7b44e87776fe391090f68907d5cf68c19bc7746dd26e152cc302412d47770cdb`
- Both APPSBL slots are byte-identical.

The detected release supports a 12 MiB kernel but predates the U-Boot web
interface support for sysupgrade archives added on 2026-01-02. Never write or
replace either APPSBL partition as part of this project.

## Relevant partitions

| Partition | Size | Current purpose |
| --- | ---: | --- |
| `0:HLOS` | 12 MiB | Active kernel target |
| `0:HLOS_1` | 6 MiB | Legacy/inactive slot |
| `rootfs` | 1 GiB | Active squashfs plus F2FS overlay |
| `rootfs_1` | 60 MiB | Legacy/inactive slot |
| `primary` | about 6 GiB | ext4 data partition |

The running root is `rootfs` (`PARTUUID=146620bd-ebe2-9654-5656-384bb2925247`).
Its squashfs occupies the beginning of the partition; loop-backed F2FS starts
at byte offset `38207488` and uses the remaining space. A normal compatible
sysupgrade recreates this layout without changing GPT.

## Current risk signal

The inspected KWRT 25.12.5 system uses PPPoE while NSS PPPoE acceleration, NSS
ECM, software flow offloading, and hardware flow offloading are all enabled.
This is the first configuration to remove from the stability build. The device
has 2 GiB RAM, so memory capacity is not the limiting factor for the selected
services.
