# 京东云太乙 ER1 稳定版 KWRT 固件

这是面向 **京东云太乙 RE-CS-07（ER1）** 的定制 KWRT 固件。设备采用高通
IPQ6018 平台，拥有 2 GB 内存和 8 GB eMMC，但市面上可长期稳定使用、软件兼容
较完整的现成固件并不多。本项目优先解决日常断网、代理插件卡死、功能堆叠过多和
升级不可靠等问题，而不是追求跑分或塞入尽可能多的软件。

- 当前固件标识：**ER1 Stable Firmware 1.0.1**
- 维护者：**Cao-Junqi**

## 适用设备

- 设备型号：京东云太乙 `JDC-RE-CS-07` / `RE-CS-07` / `ER1`
- SoC：Qualcomm IPQ6018，ARM64
- 已验证硬件：2 GB RAM、8 GB eMMC、USB 3.0
- 固件基础：KWRT 25.12.5，`qualcommax/ipq60xx`
- 默认管理地址：`10.0.0.1/24`

> [!IMPORTANT]
> 本项目当前面向已经完成兼容 U-Boot 和大分区布局的 ER1。已验证布局包含
> 12 MiB `0:HLOS` 和 1 GiB `rootfs`。项目不会更新 U-Boot，也不会修改 GPT、
> BOOTCONFIG、APPSBL、ART 或 CDT。原厂分区或分区情况不明的设备不要直接刷入。

## 固件特点

- 稳定优先：关闭软件/硬件流量分载以及 NSS ECM、NSS PPPoE 加速，保留网口必需的
  NSS dataplane 和 SSDK。
- SSR Plus：内置 Xray-core 与 Mihomo，支持 VLESS、VMess 和常用单节点协议；不预装
  OpenClash。
- 广告过滤：内置 `adblock-fast`，默认使用 1Hosts Lite，通过 dnsmasq 工作。
- 内网穿透：内置维护中的 FRPC 与 LuCI 管理界面，默认不开放任何穿透端口。
- Docker：内置 Docker、Dockerd 和 Dockerman，默认关闭；数据目录预设为
  `/mnt/mmcblk0p24/docker/`。
- 应用商店：只保留 LinkEase `luci-app-store`，不包含 iStoreX 和 QuickStart。
- 双主题：默认使用 Aurora，并提供完整的主题设计入口；同时内置 Argon 及其配置页面。
- USB 3.0：包含 USB 存储、UAS、ext4、exFAT、NTFS3 和 VFAT 支持。
- 中文界面：默认简体中文，时区为 `Asia/Shanghai`，首页不显示上游推广链接。

## 主题入口

- Aurora 设置：`系统 -> Aurora Theme Design -> Design Studio`
- Argon 设置：`系统 -> Argon Config`
- 切换主题：`系统 -> 系统 -> 语言和界面`

## 下载与刷机

固件通过 GitHub Actions 手动构建。构建成功后会同时上传到 Actions Artifact 和对应
版本的 GitHub Release。

产物包含两种固件，不能混用：

- `*-squashfs-sysupgrade.bin`：在已经运行的兼容系统中升级，建议使用
  `sysupgrade -n`，不要保留旧固件配置。
- `*-squashfs-factory.bin`：通过现有 U-Boot 网页恢复界面刷入。

当前已验证的 U-Boot 版本不支持在网页中解析 `sysupgrade.bin`，因此 U-Boot 中只能
选择 `factory.bin`。刷机过程中不要断电，也不要上传任何 U-Boot、APPSBL、GPT、ART、
BOOTCONFIG 或 CDT 文件。

## 默认行为

- 主机名：`ER1`
- LAN：`10.0.0.1/24`
- DHCP 地址池：`10.0.0.100` 至 `10.0.0.249`
- DHCP 租期：12 小时
- WAN 不使用 PPPoE 下发的 DNS，固定使用阿里云 `223.5.5.5` 和腾讯 DNSPod
  `119.29.29.29`；SSR Plus 的代理 DNS 使用 `8.8.8.8`
- SSR Plus、FRPC 需要用户添加自己的节点或服务端信息
- Docker 默认不启动，避免未配置存储时占用系统资源

## 构建方式

仓库只提供一个手动触发的 `Build ER1 firmware` 工作流，不在本地编译固件。工作流会：

1. 校验固定版本的 KWRT ImageBuilder、软件源索引和外部主题包。
2. 构建 ER1 的 factory 与 sysupgrade 镜像。
3. 检查必要软件包存在，并确认已排除不需要或存在稳定性风险的软件包。
4. 生成 `SHA256SUMS`、软件包 manifest 和 `BUILD-INFO.txt`。
5. 上传 Actions Artifact，并同步发布到对应版本的 GitHub Release。

设备基线、U-Boot 和分区探测记录见
[`docs/device-baseline.md`](docs/device-baseline.md)。

## 项目边界

本项目只负责系统固件及默认配置，不包含代理节点、FRP 密钥、Cloudflare Tunnel 凭据
或其他私人配置。固件不会自动写入或升级引导程序，也不会自动调整磁盘分区。
