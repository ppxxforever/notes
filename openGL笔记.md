# openGL 渲染API

## 概述

openGL 是一个图形api，允许用户一定限度的操纵显卡，每个品牌的api细微的不同。

这个笔记以一个轻量级的图形库GLFW 库为例子。

### 安装GLFW
安装GLFW，以fedora为例

```bash
# 安装 GLFW
sudo dnf install glfw-devel

# x11 依赖
sudo dnf install libX11-devel libXcursor-devel libXrandr-devel libXinerama-devel libXi-devel mesa-libGL-devel

# wayland 依赖
sudo dnf install wayland-devel libxkbcommon-devel wayland-protocols-devel extra-cmake-modules
```

### 将GLFW引入到项目里

有两种方法，一种是将GLFW的源码嵌入到本地项目中,一种是下载GLFW的二进制，这个笔记用第二种,使用cmake管理项目。

所以创建对应的CMakeLists.txt

```txt
cmake_minimum_required(VERSION 3.20)
project(p1 LANGUAGES CXX)

add_executable(main main.cpp)

find_package(glfw3 3.3 REQUIRED)
find_package(OpenGL REQUIRED)

target_link_libraries(main glfw OpenGL::GL)
```