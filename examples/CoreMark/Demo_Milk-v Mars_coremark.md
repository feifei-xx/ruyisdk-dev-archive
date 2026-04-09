# 在 Milk-V Mars 上运行 CoreMark 基准测试

## 环境说明

- **硬件环境**：Milk-V Mars 开发板 (StarFive JH7110)
- **编译环境**：Windows Subsystem for Linux (WSL) - Ubuntu
- **工具链**：RuyiSDK (gnu-plct)

## 一、 Ruyi 环境搭建

#### 更新 Ruyi 索引并安装工具链

在 Ubuntu 终端执行以下命令，确保 Ruyi 索引最新并安装 PLCT 提供的 GNU 工具链：

```bash
ruyi update
ruyi install gnu-plct
```

#### 创建并激活 Ruyi 虚拟环境

使用 `ruyi venv` 创建一个独立的开发环境。对于 Milk-V Mars，我们使用 `generic` 配置。

```bash
# 创建虚拟环境，命名为 coremark-venv，使用 generic profile
ruyi venv -t gnu-plct generic coremark-venv

# 进入虚拟环境目录
cd coremark-venv

# 激活虚拟环境
source ./bin/ruyi-activate
```

#### 验证 GCC 版本

激活环境后，检查编译器是否已正确指向 Ruyi 虚拟环境中的交叉编译器：

```bash
riscv64-plct-linux-gnu-gcc --version
make --version
```

## 二、 获取 CoreMark 源码并编译

### 克隆 CoreMark 源码

在虚拟环境的工作目录下克隆官方仓库：

```bash
# 创建项目工作目录
mkdir -p ~/projects/coremark && cd ~/projects/coremark

# 克隆仓库
git clone [https://github.com/eembc/coremark.git](https://github.com/eembc/coremark.git)
cd coremark
```

### 执行交叉编译

针对 Linux 环境进行编译。在 CoreMark 的 Makefile 体系中，通过 `CC` 参数指定 Ruyi 的交叉编译器。

```bash
# 执行交叉编译
# PORT_DIR=linux 指定运行环境为 Linux
# CC 指定交叉编译器路径
make PORT_DIR=linux CC=riscv64-plct-linux-gnu-gcc compile
```

### 验证编译产物

检查当前目录下是否生成了名为 `coremark.exe` 的可执行文件：

```bash
ls -l coremark.exe
file coremark.exe
```

验证结果应包含：`ELF 64-bit LSB executable, UCB RISC-V`。

## 三、 运行 CoreMark 基准测试

将生成的 `coremark.exe` 拷贝至 U 盘并插入 Milk-V Mars 开发板。通过串口终端登录开发板系统。

### 挂载 U 盘并准备程序

```bash
# 创建挂载点
sudo mkdir -p /mnt/usb

# 挂载 U 盘（通常设备名为 sda1）
sudo mount /dev/sda1 /mnt/usb

# 拷贝程序至本地并赋予执行权限
cp /mnt/usb/coremark.exe ~/
cd ~/
chmod +x coremark.exe

# 卸载 U 盘
sudo umount /mnt/usb
```

### 执行跑分测试

运行程序，测试过程大约持续 15-20 秒：

```bash
./coremark.exe
```

测试完成后，屏幕会输出详细的跑分数据。请重点关注 **Correct operation validated** 字样，这代表测试结果有效。

## 四、 返回上级目录并退出 Ruyi 虚拟环境

```bash
# 返回上级目录
cd ..

# 退出 Ruyi 虚拟环境
ruyi-deactivate
```

