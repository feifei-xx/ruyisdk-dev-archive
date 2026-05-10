# Milk-V Mars 2048 终端应用测试实战 Demo

## 1. 概述
本文档演示了如何使用 RuyiSDK 在交叉编译环境下，为 Milk-V Mars 开发板（RISC-V 64 架构）编译并运行无外部依赖的纯 C 语言终端游戏 `2048.c`。

## 2. 环境与前置要求
* **编译宿主机**：Windows Subsystem for Linux (WSL) - Ubuntu
* **交叉编译工具链**：RuyiSDK (采用 `gnu-plct` 工具链)
* **目标硬件**：Milk-V Mars 开发板
* **目标操作系统**：Milk-V 官方 Debian 镜像

---

## 3. RuyiSDK 交叉编译环境搭建

首先，我们需要在 WSL 中配置专为通用 RISC-V 64 架构准备的交叉编译环境。

### 3.1 更新索引与安装工具链
更新 Ruyi 包管理器的索引，并安装 PLCT 提供的 GNU 工具链：

```bash
ruyi update
ruyi install gnu-plct
```

### 3.2 创建并激活虚拟环境
创建一个名为 `mars-venv` 的虚拟环境，并使用 `generic` 配置文件：

```bash
ruyi venv -t gnu-plct generic mars-venv
cd mars-venv
source ./bin/ruyi-activate
```

### 3.3 验证工具链
激活环境后，确认编译器已正确就绪，终端提示符前应出现 `(mars-venv)`：

```bash
riscv64-plct-linux-gnu-gcc --version
```

---

## 4. 获取源码与交叉编译

### 4.1 获取 2048 源码
在虚拟环境工作目录下，创建一个项目文件夹，并使用 `wget` 直接下载 `2048.c` 的单文件源码：

```bash
mkdir -p ~/projects/2048 && cd ~/projects/2048
wget [https://raw.githubusercontent.com/mevdschee/2048.c/master/2048.c](https://raw.githubusercontent.com/mevdschee/2048.c/master/2048.c)
```

### 4.2 执行交叉编译
直接调用 RuyiSDK 提供的交叉编译器进行编译，将输出文件命名为 `2048_riscv`：

```bash
riscv64-plct-linux-gnu-gcc 2048.c -o 2048_riscv
```

### 4.3 产物验证
编译结束后，检查当前目录是否成功生成了可执行文件，并确认其为 RISC-V 架构：

```bash
ls -l 2048_riscv
file 2048_riscv
```

---

## 5. 部署与测试执行

### 5.1 物理转移程序 (U盘离线方式)
1. **宿主机提取**：在 WSL 终端执行 `explorer.exe .` 打开资源管理器，将生成的 `2048_riscv` 复制到您的物理 U 盘中。
2. **开发板挂载**：将 U 盘插入 Milk-V Mars，在开发板终端执行挂载（假设您的 U 盘识别为 `sdb1`）：
   ```bash
   sudo mkdir -p /mnt/usb
   sudo mount /dev/sdb1 /mnt/usb
   ```
3. **拷贝到主目录**：
   ```bash
   cp /mnt/usb/2048_riscv ~/
   ```

### 5.2 赋予权限并运行
在 Milk-V Mars 终端中，进入主目录，赋予执行权限并启动游戏：

```bash
cd ~/
chmod +x 2048_riscv
./2048_riscv
```

### 5.3 运行结果
执行成功后，终端将被清屏并渲染出游戏界面。
* **操作方式**：使用键盘的方向键（上、下、左、右）或 `W、A、S、D` 键来合并数字。
* **退出方式**：按下 `q` 键即可退出程序返回终端。