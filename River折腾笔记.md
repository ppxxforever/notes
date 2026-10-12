# River折腾笔记

## 概述

River 是一个用 Zig 语言编写的 Wayland 合成器，从 0.4.0 版本开始，不提供窗口管理，只做合成器

## 安装

### 依赖安装

以Arch linux为例

```bash

sudo pacman -S --needed zig base-devel wayland wayland-protocols wlroots libxkbcommon libevdev pixman pkg-config scdoc xorg-xwayland

```

### 克隆源码

```bash
git clone https://github.com/riverwm/river.git --depth 1
```

### 开始构建

```bash
zig build -Dllvm -Doptimize=ReleaseSafe -Dxwayland --prefix ~/.local install
```

- -Doptimize=ReleaseSafe：以安全优化模式编译，这是官方推荐的通用构建选项。
- -Dxwayland：启用 Xwayland 支持的关键选项。
- --prefix ~/.local：指定安装路径为用户目录
- install：执行构建并安装。

