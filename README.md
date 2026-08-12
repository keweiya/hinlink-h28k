# HINLINK H28K 固件

本仓库用于每周自动编译 ImmortalWrt HINLINK H28K 固件（RK3528）。

> 本项目仅供个人使用与配置留档，不面向通用环境，也不提供技术支持；请自行评估适配性。

## 版本支持

本仓库按 ImmortalWrt/OpenWrt 版本线维护不同补丁集。不要把 `v25.12.x` 的补丁直接套到 `v24.10.x`，两条版本线的 Rockchip/U-Boot/设备树上下文不同，旧版本会出现冲突或缺文件。

| 版本线 | 补丁目录 | 固件包配置 | 状态 |
| --- | --- | --- | --- |
| `v25.12.x` | `patches-25.12/` | `config/h28k-firmware.packages` | 支持 |
| `v24.10.5+` | `patches-24.10.5-plus/` | `config/immortalwrt-24.10.config` | 支持 |
| `v24.10.0` - `v24.10.4` | 无 | 无 | 不支持当前补丁集 |

`v24.10.0` 缺少 `target/linux/rockchip/armv8/base-files/etc/init.d/phy-leds`，`v24.10.1` - `v24.10.4` 在 `package/boot/uboot-rockchip/Makefile` 附近与当前补丁上下文不兼容。因此当前 `v24.10` 补丁集明确标记为 `v24.10.5+`。

## 补丁说明

| 补丁 | 说明 |
| --- | --- |
| `0010-rockchip-add-HINLINK-H28K-U-Boot-support.patch` | 添加 U-Boot 目标、H28K DTS、U-Boot DTSI 和 defconfig。 |
| `0020-rockchip-add-HINLINK-H28K-device-tree.patch` | 添加 Linux H28K 设备树和系统 LED 别名。 |
| `0030-rockchip-add-HINLINK-H28K-board-defaults.patch` | 添加 LED 默认值、LAN/WAN 分配、MAC 地址生成和 IRQ affinity。 |
| `0040-rockchip-add-HINLINK-H28K-image.patch` | 添加 `hinlink_h28k` 固件设备配置。 |
| `0050-rockchip-configure-HINLINK-H28K-RJ45-LEDs.patch` | 配置两个 RJ45 接口的链路灯和活动灯。 |

补丁文件名在不同版本线中保持一致，实际内容按目录区分。

## 自动编译

GitHub Actions 每周自动运行一次，也可以在 Actions 页面手动触发。每次构建会：

1. 使用手动选择的 ImmortalWrt 版本标签（例如 `v25.12.1`、`v24.10.6`）。
2. 根据版本自动选择对应补丁目录和配置文件。
3. 使用对应正式版的官方 `config.buildinfo`。
4. 应用 HINLINK H28K 补丁。
5. 加载固件参数和额外 Git 软件包。
6. 编译完整固件并上传到 Artifacts 和 Releases。

## 构建配置

所有可调整的构建配置放在 `config/`：

| 文件 | 用途 |
| --- | --- |
| `packages.conf` | 每行一条完整的 `git clone` 命令。 |
| `h28k-imagebuilder.config` | H28K 目标和 ImageBuilder 构建配置。 |
| `h28k-firmware.packages` | `v25.12.x` 固件 ImageBuilder 软件包配置。 |
| `immortalwrt-24.10.config` | `v24.10.5+` 固件 ImageBuilder 软件包配置。 |

## 本地验证补丁

示例：在本地 ImmortalWrt 源码仓库中验证某个版本线补丁是否可应用：

```bash
git checkout v24.10.6
git reset --hard
for patch in /path/to/hinlink-h28k/patches-24.10.5-plus/*.patch; do
  git apply --check --3way "$patch"
done
```

如果要验证 `v25.12.x`，请切到对应 `v25.12.*` 标签并使用 `patches-25.12/`。

## 默认包含

- Fluent LuCI 主题：`luci-theme-fluent`
- Nikki：`luci-app-nikki`
- MT7921U USB 无线网卡驱动：`kmod-mt7921u`
- OpenSSH SFTP 服务：`openssh-sftp-server`

## 设备信息

- 型号：HINLINK H28K
- SoC：Rockchip RK3528
- 架构：ARMv8 / AArch64
- LAN：`eth0`
- WAN：`eth1`
- 固件设备名：`hinlink_h28k`
