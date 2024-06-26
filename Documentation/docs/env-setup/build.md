---
title: "编译及配置"
description: ""
summary: ""
date: 2024-03-09T07:30:43Z
lastmod: 2024-03-09T07:30:43Z
draft: false
weight: 205
toc: true
seo:
  title: "" # custom title (optional)
  description: "" # custom description (recommended)
  canonical: "" # custom canonical URL (optional)
  noindex: false # false (default) or true
---

如果您是从github，或者gitee拉取的工程，在进行编译之前，应该先更新子模块，方式如下：
```shell
# 位于工程根目录下
git submodule update --init
```
此方式对网络环境有要求，子模块源来自github，可以通过代理等方式。
我们正在尝试将子模块迁移至gitee，保证国内用户可以正常下载工程。

## 编译工程

### pico_dm_fpc032mra003_freertos

!!! note "小贴士"

    本工程基于[pico_dm_8080_template](https://github.com/embeddedboys/pico_dm_8080_template)，这是一个基于树莓派Pico开发的针对于8080 TFT的通用型工程，只需要简单的配置，或轻量的代码开发，就能适配一个全新的组合。 同时这也是我们的主线开发工程，最新的特性都会在本工程中先进行实验。

根目录结构
```shell
CMakeLists.txt  # 根目录cmake配置
LICENSE         # 许可证
README.md       # 自述文件
include/        # 头文件
lib/            # 第三方库
pico_sdk_import.cmake # pico sdk前置文件
src/            # 工程源码
```

src目录结构
```shell
backlight.c     # 背光驱动
cmake           # 一些cmake配置文件，板级或驱动级配置
CMakeLists.txt  # src cmake配置
factory       # 工厂测试相关
FreeRTOSConfig.h  # FreeRTOS配置文件
ft6236.c    # ft6236 电容触摸屏驱动
gt911.c     # gt911 电容触摸屏驱动
i2c_tools.c # i2c工具
indev.c     # 输入驱动框架
lv_conf.h   # lvgl配置头文件
lvgl        # lvgl源码
main.c      # 程序入口
ns2009.c    # ns2009 电阻触摸屏驱动
pio         # pio相关驱动
porting     # lvgl移植文件
tft_1p5623.c  # 1p5623 面板驱动
tft.c         # 显示驱动框架
tft_ili9488.c # ili9488 显示驱动
tft_r61581.c  # r61581 显示驱动
tft_st6201.c  # st6201 显示驱动
tft_st7789.c # st7789 显示驱动
tsc2007.c   # tsc2007 电阻触摸屏驱动
```

**1. 拉取代码**

```bash
git clone https://gitee.com/embeddedboys/pico_dm_fpc032mra003_freertos
cd pico_dm_fpc032mra003_freertos
git submodule update --init
```

**2. 选择分支**

对于lvgl v8.3版本
```bash
./scripts/switch_branch.sh main
```
对于lvgl v9版本
```bash
./scripts/switch_branch.sh v9
```

用法：
```bash
Usage: ./scripts/switch_branch.sh <branch_name>
    Available branches:
    - main
    - HEAD -> origin/main
    - main
    - v9
    - v9
```

**3. 编译生成固件**

手动进行编译
```bash
mkdir -p build
cd build
cmake .. -G Ninja
ninja
```

或使用自动编译脚本，仅linux平台
```bash
./compile.sh
```

**4. 烧录**

手动执行命令
```bash title="位于build目录下"
openocd -f interface/cmsis-dap.cfg -c "adapter speed 5000" -f target/rp2040.cfg -s tcl -c "program src/rp2040-freertos-template.elf verify reset exit"
```

或使用自动部署脚本，仅linux平台
```bash
./scripts/deploy.sh
```

### MicroPython

拉取并进入工程目录
```bash
git clone https://github.com/embeddedboys/lv_micropython.git
cd lv_micropython
```

切换你需要编译的分支版本，对于LVGL V8.3
```bash
git checkout release/v8
```
对于LVGL V9
```bash
git checkout release/v9
```
!!! warning "注意"

    如果已经进行过编译，若切换分支，则需要在切换分支之后运行如下命令同步切换子模块版本
    ```bash
    git submodule update --init
    ```

若拉取模块过程失败则需重新执行，如果还是报错，请在对应make命令最后加上clean进行清理，然后重新执行对应命令。

```bash title="Ubuntu"
sudo apt install cmake build-essenital gcc-arm-none-eabi -y

git submodule update --init --recursive lib/lv_bindings
make -C ports/rp2 BOARD=PICO submodules
make -j -C mpy-cross
make -j -C ports/rp2 BOARD=PICO DISP_BOARD=PICO_DM_FPC032MRA003 USER_C_MODULES=../../lib/lv_bindings/bindings.cmake
```

或者执行工程根目录下`build.sh`脚本，仅linux平台
```bash
./build.sh
```

在编译步骤最后一部中，指定`DISP_BOARD`参数，选择对应的显示拓展板，比如
```bash
make -j -C ports/rp2 BOARD=PICO DISP_BOARD=PICO_DM_FPC032MRA003 USER_C_MODULES=../../lib/lv_bindings/bindings.cmake
```
选择的是本项目的`PICO_DM_FPC032MRA003`拓展板

该参数会决定工程[lv_binding_micropython](https://github.com/embeddedboys/lv_binding_micropython)中对应的显示拓展板驱动是否启用，`lv_binding_micropython`的一个MicroPython用户模块，由LVGL官方开发，其`release/v8`或`release/v9`分支，`driver/rp2`目录下有我们编写的的拓展板驱动文件，如下图所示：

<figure markdown="span">
  ![Image title](../assets/lv_binding_micropython.png){ loading=lazy }
  <figcaption>拓展板驱动文件并非由官方维护</figcaption>
</figure>

## 烧录

可参考[固件烧录](/docs/get-started/固件烧录/)章节，此处给出示例

### UF2

按住核心板上的BOOTSEL键，然后插入USB线缆，或者在插入线缆的情况下按下RUN按键，
此时RP2040将进入BOOTROM USB下载模式。

```shell
cp src/rp2040-freertos-template.uf2 /media/$USER/RPI-RP2/
```

!!! note "说明"

    Windows 用户可右键 uf2 文件选择发送到 RPI-RP2

### openocd

```shell
openocd -f interface/cmsis-dap.cfg -c "adapter speed 5000" -f target/rp2040.cfg -s tcl -c "program src/rp2040-freertos-template.elf verify reset exit"
```
program后面跟的参数是需要烧录的elf文件，当然也可以烧录bin文件，方式如下
```shell
openocd -f interface/cmsis-dap.cfg -c "adapter speed 10000" -f target/rp2040.cfg -s tcl -c "program src/rp2040-freertos-template.bin verify reset exit 0x10000000"
```
写入到`0x10000000`处地址，也就是rp2040映射Flash的地方

如何制作一个picoprobe（debugprobe）调试器，可以参考[这个章节](/docs/get-started/固件烧录/#debugprobe)

!!! note "说明"

    WSL用户需要先将daplink连接至WSL中，可使用[usbipd](https://github.com/dorssel/usbipd-win)

## `CMakeLists.txt` 配置说明

此处列出可供用户自定义修改的选项

### 是否开启超频
```cmake
set(OVERCLOCK_ENABLED 0)    # 1: enable, 0: disable
```

!!! danger "危险"

    过度超频可能会导致核心板稳定性下降，**但并不会对本拓展板造成影响。**

    适当的超频可以达到更流畅的运行效果，用户自行承担其风险。

### 超频预设

```cmake title="src/CMakeLists.txt"
# Overclocking profiles
#      SYS_CLK  | FLASH_CLK | Voltage
#  1  | 240MHz  |  120MHZ   |  1.10(V) (default, stable, recommended for most devices)
#  2  | 266MHz  |  133MHz   |  1.10(V)
#  3  | 360MHz  |  90MHz    |  1.20(V)
#  4  | 400MHz  |  100MHz   |  1.30(V)
#  5  | 416MHz  |  104MHz   |  1.30(V)
set(OVERCLOCK_PROFILE 1)
```

用户可根据核心板稳定程度，自行调整超频幅度。

!!! warning "注意"

    `Flash Clock = SYS_CLK / PICO_FLASH_SPI_CLKDIV`

    `PICO_FLASH_SPI_CLKDIV`的默认值为`2`

    简单来说，当CPU频率处于`266MHz`的情况下，Flash工作频率为`133MHz`

    在本工程中，当CPU频率大于`266MHz`时，`PICO_FLASH_SPI_CLKDIV`自动修改为`4`，否则将超出一般`Flash`的最大工作频率限制。

### LCD 相关

可在此处配置引脚、时钟、是否启用PIO等。
若存在缺失配置项，则表示该工程不支持调整。
```cmake title="src/cmake/r61581.cmake"
# LCD Pins for 8080 interface
set(LCD_PIN_DB_BASE  0)  # 8080 LCD 数据总线第0脚
set(LCD_PIN_DB_COUNT 8) # 8080 LCD 数据总线宽度
set(LCD_PIN_CS  29)  # 8080 LCD 片选引脚
set(LCD_PIN_WR  19)  # 8080 LCD 写信号引脚
set(LCD_PIN_RS  20)  # 8080 LCD 数据/寄存器选择引脚
set(LCD_PIN_RD  29)  # 8080 LCD 读信号引脚
set(LCD_PIN_RST 18)  # 8080 LCD 复位引脚
set(LCD_PIN_BL  28)  # 8080 LCD 背光引脚
set(LCD_HOR_RES 480)    # LCD 横向最大分辨率
set(LCD_VER_RES 320)    # LCD 纵向最大分辨率
set(DISP_OVER_PIO 1) # LCD驱动模式 1: PIO, 0: GPIO
set(PIO_USE_DMA   1)   # 是否启用DMA 1: use DMA, 0: not use DMA
set(I80_BUS_WR_CLK_KHZ 40000) # 8080 LCD 写信号频率
```

### 选择显示驱动
```cmake title="src/CMakeLists.txt"
# LCD driver type
set(LCD_DRV_USE_ST7789  0)
set(LCD_DRV_USE_ILI9488 0)
set(LCD_DRV_USE_ILI9806 0)
set(LCD_DRV_USE_R61581  1)  # 启用R61581显示驱动
set(LCD_DRV_USE_ST6201  0)
set(LCD_DRV_USE_1P5623  0)
```

#### 设置显示buffer大小
```cmake title="src/CMakeLists.txt"
math(EXPR MY_DISP_BUF_SIZE "${LCD_HOR_RES} * ${LCD_VER_RES} / 10")
```

### 选择触摸驱动
```cmake title="src/CMakeLists.txt"
# Input device driver type
set(INDEV_DRV_USE_FT6236  0)
set(INDEV_DRV_USE_NS2009  0)
set(INDEV_DRV_USE_TSC2007 1)    # 启用TSC2007显示驱动
set(INDEV_DRV_USE_GT911   0)
```

### 配置杂项
```cmake title="src/CMakeLists.txt"
set(CMAKE_EXPORT_COMPILE_COMMANDS ON) # 设置自动生成compile_commands.json
pico_enable_stdio_usb(${PROJECT_NAME} 0)  # 设置stdio通过usb cdc输出
pico_enable_stdio_uart(${PROJECT_NAME} 1) # 设置stdio通过uart输出
pico_add_extra_outputs(${PROJECT_NAME}) # 输出额外的编译文件，例如uf2等
```
