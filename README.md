# ER1 stable firmware

Custom KWRT firmware for JDCloud RE-CS-07 (ER1), built with the official KWRT
ImageBuilder in GitHub Actions.

Firmware identity: **ER1 Stable Firmware 1.0.0**, built by **Cao-Junqi**.
The source repository and build run are recorded in every firmware artifact.

## Goals

- Prefer routing stability over maximum NSS benchmark throughput.
- Keep the existing U-Boot and GPT untouched.
- Provide lightweight DNS ad blocking, SSR Plus, the Aurora theme, and Docker
  management, together with a maintained FRPC interface.
- Produce both supported upgrade formats without compiling the full toolchain.

## Included behavior

- `adblock-fast` uses the 1Hosts Lite list through dnsmasq.
- SSR Plus is installed for single-node proxy use and remains disabled until a
  node is configured. The KWRT package pulls in Mihomo as a declared runtime
  dependency; OpenClash itself and its configuration are excluded.
- The official `luci-app-frpc` provides form-based client and proxy management.
  Its upstream sample SSH tunnel is removed, so no port is exposed until a
  proxy is explicitly added. FRPC initially targets localhost and keeps retrying
  until the actual server address and token are configured.
- iStoreX is included; its dependencies provide QuickStart and the LinkEase app
  store.
- Docker and Dockerman are installed, use `/mnt/mmcblk0p24/docker` for data, and
  remain disabled until explicitly started.
- The USB 3.0 host controller, USB mass-storage/UAS drivers, and ext4, exFAT,
  NTFS3, and VFAT filesystem support are available for external drives.
- Software and hardware flow offloading are disabled. NSS dataplane and SSDK,
  which drive the Ethernet ports, remain installed; NSS ECM and NSS PPPoE
  acceleration, together with the netlink package that depends on NSS PPPoE,
  are excluded.
- LAN address is `10.0.0.1/24`, hostname is `ER1`, timezone is Asia/Shanghai, and
  LuCI uses the Aurora theme.

## Build

The only build entry point is the manually triggered `Build ER1 firmware`
GitHub Actions workflow. It verifies the pinned ImageBuilder and package-feed
indexes before building, then uploads a short-lived artifact containing:

- `*-squashfs-sysupgrade.bin`: upgrade from a running compatible system.
- `*-squashfs-factory.bin`: recovery/installation through the current U-Boot
  web interface.
- `SHA256SUMS` and the generated package manifest.

No workflow in this repository builds or writes U-Boot, APPSBL, GPT, ART, CDT,
or BOOTCONFIG data. A firmware image must still be tested before it is used on
the primary router.

## Device compatibility

The detected bootloader and partition layout are documented in
[`docs/device-baseline.md`](docs/device-baseline.md). The current U-Boot cannot
flash a sysupgrade archive through its web UI. Use each image only through the
path named above.
