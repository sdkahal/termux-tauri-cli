# termux-tauri-cli

在 GitHub Actions 上交叉编译 **可在 Termux (Android / arm64) 直接运行** 的
[Tauri CLI](https://github.com/tauri-apps/tauri) 二进制。

## 为什么需要它

`@tauri-apps/cli` 的 npm 包只发布这些宿主平台的原生绑定：

```
darwin-x64 / darwin-arm64 / linux-x64-gnu / linux-x64-musl / linux-arm64-gnu /
linux-arm64-musl / linux-arm-gnueabihf / linux-riscv64-gnu /
win32-x64-msvc / win32-ia32-msvc / win32-arm64-msvc
```

**没有 `@tauri-apps/cli-android-arm64`**（npm 上不存在这个包）。所以在 Termux 上：

```
Error: Cannot find native binding.
[cause]: Cannot find module '@tauri-apps/cli-android-arm64'
```

替代方案就是在 CI 上从 Rust 源码编译 `tauri-cli`，目标三元组
`aarch64-linux-android`。NDK 产物链接的是 bionic，与 Termux 相同，可以直接运行。

## 用法

1. 打开 **Actions → Build cargo-tauri for Termux → Run workflow**
   （`version` 填和项目里 `@tauri-apps/cli` 相同的版本，例如 `2.11.4`）。
2. 等构建完成（首次约 20–30 分钟），下载 artifact
   `cargo-tauri-aarch64-linux-android-<version>`。
3. 在 Termux 里安装（见下方）。

## 在 Termux 上安装

```fish
cd ~/storage/downloads
unzip -o cargo-tauri-aarch64-linux-android-2.11.4.zip
mkdir -p ~/.cargo/bin
mv -f cargo-tauri ~/.cargo/bin/
chmod +x ~/.cargo/bin/cargo-tauri
~/.cargo/bin/cargo-tauri --version
```

> 需要先执行过 `termux-setup-storage` 才能访问 `~/storage/downloads`；
> 也可以用手机浏览器下载后用文件管理器放到这里。

## 在项目里接上

`node_modules/@tauri-apps/cli/tauri.js` 换成一个转发 shim，把调用转给
`~/.cargo/bin/cargo-tauri`（`node_modules/` 被 `.gitignore` 忽略，不算改仓库）。

之后照常：

```fish
cd ~/ws/epub-read
bash scripts/build-android.sh build --debug
```

## 说明

- 只编译 `tauri-cli`，不含 iOS / Windows 专用逻辑。
- 目标是 API 24（Termux 最低支持 Android 7）。
- 公开仓库的 Actions 免费不限量；私有仓库会占用每月 2000 分钟的免费额度。
