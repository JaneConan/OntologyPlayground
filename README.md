# Ontology Playground — HarmonyOS 应用（签名发行构建）

Ontology Playground 的 HarmonyOS 应用工程。它把打包好的单文件 Web 包内嵌到 `rawfile` 资源中，并构建出一个**已签名、可安装**的 HAP，供 HarmonyOS 设备与模拟器使用。

> 本应用已上架华为应用市场，支持手机、平板、PC 2-in-1、折叠屏，可直接下载安装：
> https://appgallery.huawei.com/app/detail?id=com.janeconan.ontologyplayground&channelId=SHARE&source=appshare

本仓库是原生的**发行构建**：其 ArkWeb 宿主结构与 `OntologyPlayground-App` 原生壳子模块一致，但配置了签名，因此产物可以直接安装与分发。

## 架构

- **框架**：HarmonyOS ArkUI（ArkTS），使用标准 HarmonyOS 工具链（hvigor）构建。
- **入口模块（`entry`）：**
  - `pages/Index.ets` — 全屏 `Web({ src: $rawfile('index.html'), controller })`，开启 JavaScript / DOM 存储 / 文件 / 数据库访问。
  - `entryability/EntryAbility.ets` — 加载 `pages/Index` 的 `UIAbility`。
  - `entrybackupability/EntryBackupAbility.ets` — 备份/恢复钩子。
- **`build-profile.json5`**：目标 HarmonyOS SDK **6.0.0（API 20）**，携带 **debug + release 签名配置**；构建产出**已签名**的 `entry-default-signed.hap`。
- **`rawfile/index.html`**：自包含的 Web 包（由 Web 应用的 `npm run build:harmony` 产出），是 Web 层在此处贡献的唯一产物。

## 构建与运行

1. 先产出 Web 构建（在 Web 应用仓库执行 `npm run build:harmony`），并把 `build/index.html` 拷贝到：
   `entry/src/main/resources/rawfile/index.html`
2. 在 DevEco Studio 中打开（需 HarmonyOS SDK 6.0.0）。
3. 构建 → **Build HAP**（或执行 `hvigorw assembleHap`）。已签名的 HAP 位于
   `entry/build/default/outputs/default/entry-default-signed.hap`。
4. 安装到设备 / 模拟器：
   ```bash
   hdc -t <device-ip:port> install entry/build/default/outputs/default/entry-default-signed.hap
   hdc -t <device-ip:port> shell aa start -b com.janeconan.ontologyplayground -a EntryAbility
   ```

## 签名说明

签名材料（`*.p12`、`*.p7b`、`*.cer`、`*.csr`）已被 **git 忽略**，绝不会提交入库。请通过 DevEco Studio 自动签名（或在本地配置自己的 `material/` 与 `build-profile.json5` 条目）来完成签名。

## 状态

用于设备 / 模拟器分发。每次签名构建前，请保持 `rawfile/index.html` 与最新的 Web 构建同步。
