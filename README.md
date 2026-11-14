# Z5 Droidspaces Kernel

面向 **Galaxy Z Fold5（q5q）/ Z Flip5（b5q）** 的独立维护内核，基于 Kokuban SM8550 源码和 DroidSpaces 官方 GKI 适配。

- 内核：Linux 5.15.178 / Android 13 GKI
- 分支：`main`（LKM）、`bakasu`（BakaSU + SUSFS）
- 构建：[Z5 Kernel CI](https://github.com/Alhkxsj/Kokuban_Kernel_CI_Center)
- 下载：[Releases](https://github.com/Alhkxsj/android_kernel_samsung_sm8550_Z5/releases)

## DroidSpaces 支持

已开启 IPC/PID/User Namespace、devtmpfs、NAT/IPv6、MACVLAN、UFW/Fail2ban、完整 ipset、NTSync、UNIX_DIAG、PROC_CHILDREN 和虚拟 HCI。

以下选项因实测破坏 kABI 而保持关闭：

- `CFS_BANDWIDTH`、`CGROUP_PIDS`：DS 的 CPU/进程数限制不可用
- `CGROUP_DEVICE`
- `NF_TABLES`
- `BRIDGE_NETFILTER`

保留 `MODVERSIONS=y` 和官方 SYSVIPC kABI 修复。每次正式构建均检查最终配置，并要求 **0 CRC mismatch、0 missing**。

## 刷入

1. 备份 `boot`、`init_boot` 和个人数据。
2. 在 Recovery 中刷入对应 ZIP，ZIP 仅允许 `q5q`、`b5q`。
3. 重启后运行 DroidSpaces 的 **Settings → Requirements → Check Requirements**。
4. LKM 版若需 Root，另用 KernelSU Manager 修补并刷入 `init_boot`；BakaSU 版已内置 Root。

刷机包只替换 boot，不主动刷写 `vendor_boot`、`vendor_dlkm` 或 `system_dlkm`。

## 构建

在 CI 仓库运行 **Build Kernel**：

- Project：`z5_sm8550`
- Branch：`main` 或 `bakasu`
- 测试时关闭 Create Release，正式发布时开启

推送这两个分支也会自动构建。产物包含刷机 ZIP、`.config` 和 `vmlinux.symvers`。

## 上游同步

上游：[YuzakiKokuban/android_kernel_samsung_sm8550_Z5](https://github.com/YuzakiKokuban/android_kernel_samsung_sm8550_Z5)

[上游监视 Issue](https://github.com/Alhkxsj/Kokuban_Kernel_CI_Center/issues/1) 每 6 小时检查一次，仅在新提交出现时追加提醒，不依赖 fork 网络，也不自动合并。

首次先添加上游 remote（已有可跳过）：

```bash
git remote add upstream https://github.com/YuzakiKokuban/android_kernel_samsung_sm8550_Z5.git
git fetch upstream main bakasu
git log main..upstream/main
# 审核后 cherry-pick 需要的提交并重新构建验证
```

## 注意

构建和 kABI 检查不能代替实机验证。首次刷入前请确认固件匹配并保留恢复手段，启动后再检查厂商模块、显示、基带、休眠唤醒及 DroidSpaces 功能。
