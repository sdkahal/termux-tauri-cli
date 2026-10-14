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

## 说明

- 只编译 `tauri-cli`，不含 iOS / Windows 专用逻辑。
- 目标是 API 24（Termux 最低支持 Android 7）。
