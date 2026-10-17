---
title: 教程一 · 小智 AI 语音助手（ESP32-C6 + OLED + 麦克风 + 功放）
order: 1
difficulty: 3
---

# 教程一 · 小智 AI 语音助手（ESP32-C6 + OLED + 麦克风 + 功放）

> 做一台能联网对话的桌面语音助手。按下按钮配网，OLED 显示表情，说句话它就回答。

**难度** ★★★☆☆
**硬件成本** 约 ¥80
**动手时间** 约 3 小时
**成品** 一块能听懂人话、能显示表情、能开口回答的桌面 AI 助手

这一篇是基础篇。后面的[机器狗教程](02-xiaozhi-dog.md)是在**同一块板子、同一份固件**
上再加外设，所以做完这篇记得给固件存个 git 标签。

本文按动手顺序写，可以一路跟着做下来：

```
① 看成品   →   ② 备材料   →   ③ 接线   →   ④ 烧录   →   ⑤ 配网   →   ⑥ 验收
                                    ↓ 卡住了
                              ⑦ 常见问题
```

---

## 1. 做完是什么样

```
┌────────────────────────────────────┐
│  ┌────────┐                🔴 LED  │
│  │ OLED   │      ESP32-C6           │
│  │ 128×64 │      (面包板)           │
│  └────────┘                         │
│    ↑I2C              [BOOT]         │
│  GPIO10/11                           │
│                                     │
│  🎤麦克风 ──I2S──► ESP32            │
│  ESP32 ──I2S──► 🔊 MAX98357A 功放   │
└────────────────────────────────────┘
```

做完之后它能做到：

- 长按 BOOT 键进入配网，用微信「小智 AI」小程序扫码联网
- 联网后 OLED 显示待机表情，说唤醒词开始对话
- 语音输入 → AI 处理 → 语音合成 → 功放播放出来
- OLED 实时显示状态文字和表情
- 三个按键：音量减、音量加、说话

---

## 2. 材料清单

### 2.1 必备件

| 物料 | 规格 | 数量 | 备注 |
|---|---|---|---|
| 主控板 | ESP32-C6 面包板开发板 | 1 | 已焊好排针，带 CH343 串口芯片 |
| OLED 显示屏 | SSD1306 128×64，I2C **四脚** | 1 | 128×32 也行，固件会自动适配 |
| 数字麦克风 | I2S 接口 | 1 | 常见型号 INMP441 |
| 功放模块 | MAX98357A，I2S 接口 | 1 | |
| 喇叭 | 4Ω 3W 左右 | 1 | 配 MAX98357A 用 |
| 面包板 | 半幅或全幅 | 1 | |
| 杜邦线 | 公对母、公对公 | 若干 | |

### 2.2 工具

- Type-C 数据线 ×1（烧录用）
- 装了 Windows / macOS / Linux 的电脑 ×1（后面编译固件要用）
- 万用表 ×1（备查，不是必须）
- 小螺丝刀 ×1（有些模块要拧端子）

### 2.3 ★ 买之前一定要确认的两件事

**第一，OLED 要买四脚 I2C 版本，不是七针 SPI 版本。**

两者外观长得几乎一样，但协议完全不同。四脚版只有 VCC / GND / SDA / SCL 四个脚，
七针版除了这四个还有 CS / RES / DC。**买错插上去不会有任何反应**，而且很难看出原因。

**第二，主控板要认准型号。**

本文用的是小智固件里叫 `bread-compact-c6` 的那一款：ESP32-C6 焊在小面包板上，
已经引出全部引脚和 BOOT 键。买的时候搜「ESP32-C6 面包板」「紧凑版」。

> ⚠️ 如果你手里是普通 ESP32-C6 开发板（DevKitC 之类），**引脚定义不一样，
> 第三节的接线表不能照抄**，固件也要重新配置。

---

## 3. 接线

### 3.1 认一下板子上的脚

先把面包板摆好，ESP32-C6 横着插在中间，两排针脚分别压住面包板中间那条沟的两侧。

面包板中间那条沟是**断开的**，左右两半互不相通；竖着的那些行是**全部打通**的。
所有元件都靠这个规律接线。

板子上你要用到这些脚：

| 丝印 | 位置 | 用途 |
|---|---|---|
| `3V3` | 一侧 | 给 OLED 供电 |
| `5V` | 一侧 | 备用（机器狗那篇要用） |
| `GND` | 多处 | 地，随便找一个 |
| `GPIO10` ~ `GPIO23` | 两侧排针 | 外设信号 |

### 3.2 接 OLED（四根线）

这是最容易接错的一步，先看清楚 OLED 板上的丝印。

| OLED 丝印 | 接到面包板 |
|---|---|
| `VCC` | `3V3` |
| `GND` | `GND` |
| `SDA` | **`GPIO11`** |
| `SCL` | **`GPIO10`** |

> ⚠️ **SDA 接 11、SCL 接 10，和大多数人「左边 SDA 右边 SCL」的直觉是反的。**
>
> 四脚 OLED 的两个信号脚紧挨着，插反概率很高。
> 接的时候对着模块上的丝印数，别凭左右位置猜。

接的时候，把 OLED 的四个针脚用杜邦线接到面包板对应的行上。
四脚 OLED 通常直接插在面包板上也行，但**信号脚一定要数清楚**。

> 💡 **I2C 上拉电阻**：SDA 和 SCL 各并一个 **4.7kΩ** 到 3V3。
> ESP32 内部弱上拉只有约 45kΩ，偏弱，长线或干扰环境下会不稳。
> 手上没有的话可以先不装，屏不亮时再考虑。

### 3.3 接数字麦克风（五根线）

INMP441 这类 I2S 麦克风一般是五根线：

| 麦克风丝印 | 接到面包板 |
|---|---|
| `VDD` | `3V3` |
| `GND` | `GND` |
| `SCK` | `GPIO5` |
| `WS` | `GPIO4` |
| `SD` | `GPIO6` |

### 3.4 接功放和喇叭（六根线）

MAX98357A 功放模块：

| 功放丝印 | 接到面包板 |
|---|---|
| `VIN` | `3V3` |
| `GND` | `GND` |
| `BCLK` | `GPIO5` ← 和麦克风共用 |
| `LRC` | `GPIO4` ← 和麦克风共用 |
| `DIN` | `GPIO7` |
| `SD`（静音） | **`3V3`** ← 硬接高电平 |
| `GAIN` | 悬空即可 |

喇叭两根线接到功放的 `SPK+` / `SPK-`（有些模块叫 `OUT+` / `OUT-`）。

> ⚠️ **功放的 `SD` 和 `EN` 要硬接 3V3**，不要接到 GPIO21。
> GPIO21 是「说话键」，接上去的话按住说话的时候功放会静音。

> 💡 **为什么 GPIO4/5 上会接两根线？**
> ESP32-C6 只有一路 I2S，收发共用同一组时钟。所以麦克风的 `SCK/WS`
> 和功放的 `BCLK/LRC` 必须接在同一个脚上 —— 这不是接错，是设计如此。

### 3.5 接三个按键

板子上有三个已经焊好的轻触开关（音量减、音量加、说话），**不用你接**。
但要知道它们的脚位，换板子时才用得上：

| 按键 | GPIO | 有效电平 |
|---|---|---|
| 音量减 | 19 | 低 |
| 音量加 | 20 | 低 |
| 说话 / 触摸 | 21 | 低 |

### 3.6 引脚总表

对照这张表检查一遍再上电：

| 功能 | GPIO | 备注 |
|---|---|---|
| I2S WS / LRC | 4 | 麦克风功放共用 |
| I2S BCLK / SCK | 5 | 麦克风功放共用 |
| 麦克风数据 | 6 | |
| 功放数据 | 7 | |
| **OLED SDA** | **11** | ⚠️ 不是 10 |
| **OLED SCL** | **10** | ⚠️ 不是 11 |
| 音量减 / 音量加 | 19 / 20 | 板载 |
| 说话 | 21 | 板载 |

### 3.7 看一眼实物

![小智 AI 实物接线：ESP32-C6 面包板 + OLED + 功放 + 喇叭，左侧为外接喇叭](../assets/wiring-ai.jpg)

*图：小智 AI 整机接线实拍。ESP32-C6 装在面包板中段，上面三个小按键从上到下是
音量减 / 音量加 / 说话键；右下角绿色排针是四脚 I2C OLED；左侧黑色方盒是 4Ω 喇叭
（经 MAX98357A 功放驱动）；中间圆形蜂鸣器位置焊的是 INMP441 数字麦克风。*

对照实物，重点确认三处：

1. **OLED 靠面包板下沿**，插的是 SDA=11 / SCL=10 这一对
2. **功放和麦克风共用 BCLK（GPIO5）**，所以有三根线一起从 C6 拉过去
3. **功放的 SD / EN 硬接 3V3**

### 3.8 上电前检查

```
□ OLED 是四脚 I2C 版本，不是七针 SPI
□ OLED 的 SDA 接 GPIO11、SCL 接 GPIO10（对着丝印数的，不是凭左右）
□ 功放的 SD / EN 接了 3V3，不是接 GPIO21
□ 所有 GND 都接到了面包板的 GND 轨
□ 没有把 3V3 和 5V 短接
□ 插 USB 之前，先用万用表量一下 3V3 和 GND 之间是不是短路
```

最后一条别省。**短路状态下插 USB 有可能烧板子**，几秒钟的事，但不值得冒险。

---

## 4. 烧录固件

### 4.1 装编译环境

固件用 ESP-IDF 编译，去 [乐鑫官方文档](https://docs.espressif.com/projects/esp-idf/zh_CN/latest/esp32c6/get-started/index.html)
按对应你操作系统的章节装一遍。装完在终端里验证：

```bash
idf.py --version
```

能打印版本号就说明环境好了。C6 需要 ESP-IDF **5.4 以上**。

### 4.2 拉源码

```bash
git clone https://github.com/78/xiaozhi-esp32.git
cd xiaozhi-esp32
git checkout v2.5.2          # 本教程实测的版本
```

> 💡 **为什么要指定版本**：小智固件更新很快，不同版本的配置项名字和
> 板型列表会变。不锁版本的话，你看到的报错多半是对不上文档，不是你做错了。

### 4.3 配置

```bash
idf.py set-target esp32c6
idf.py menuconfig
```

在菜单里选：

| 菜单项 | 选什么 |
|---|---|
| Board type（板型） | **`bread-compact-c6`** |
| Display → OLED | SSD1306 128×64 |
| Audio codec（音频） | 按你 3.3 / 3.4 节实际接的线选 |

选完保存退出。

### 4.4 编译

```bash
idf.py build
```

编译要几分钟。成功的判据是**这两行**：

```text
BUILD_EXIT=0
Successfully created ESP32-C6 image.
```

> ⚠️ **不要只看退出码。** 如果你用管道重定向输出，管道提前关闭会让它假失败。
> 认这两行日志。

### 4.5 找准串口

插上 USB，Windows 上会多出一个 COM 口。但**端口号会变**，别写死。

用 VID:PID 认才靠谱：

```bash
python -m serial.tools.list_ports -v
```

找带 `1A86:55D3` 的那一行（CH343 是这家的芯片，PID 也可能是 `7523`）：

```text
COM<n>  | USB-Enhanced-SERIAL CH343 | USB VID:PID=1A86:55D3
                        ↑ 记这个端口号
```

> ⚠️ Windows 上 COM3、COM5、COM9 这类小号通常是**蓝牙虚拟口**，没有真实设备。
> 往这些口烧录会安静地空等然后超时。

### 4.6 ★ 手动进下载模式

**这一步是 ESP32-C6 板子最容易卡住的地方。**

配了 CH343 的板子自动复位基本不生效，直接烧会一直等到超时。要手动操作：

```
1. 按住 BOOT 键 不放
2. 点一下 RST 键
3. 松开 RST
4. 继续按住 BOOT 约 2 秒
5. 松开 BOOT
```

### 4.7 烧录

把 `<COM11>` 换成 4.5 步记下的端口号：

```bash
idf.py -p <COM11> flash
```

如果还是连不上，用完整命令（`--baud` 是全局参数必须在前面，
`--flash-*` 是子命令参数必须在后面）：

```bash
esptool --chip esp32c6 --port <COM11> --baud 460800 \
        --before no-reset --after no-reset \
        write-flash --flash-mode dio --flash-size 16MB --flash-freq 80m \
        0x0      build/bootloader/bootloader.bin \
        0x8000   build/partition_table/partition-table.bin \
        0xd000   build/ota_data_initial.bin \
        0x20000  build/xiaozhi.bin \
        0x800000 build/generated_assets.bin
```

> 💡 **偏移地址不要手抄**，从 `build/flasher_args.json` 里读，那里是准的。

### 4.8 复位进固件

```bash
esptool --chip esp32c6 --port <COM11> run
```

或者直接按一下板子上的 RST 键。

### 4.9 听日志

```bash
idf.py -p <COM11> monitor
```

按 `Ctrl + ]` 退出。

---

## 5. 配网

1. 长按 **BOOT** 键约 3 秒
2. 屏幕提示进入配网
3. 用**微信「小智 AI」小程序**扫码

> ⚠️ **不要用浏览器打开配网页面。**
> 本教程实测踩过：浏览器扫码跳转后一片空白，代码和接线都正常，
> 换小程序就好了。

---

## 6. 怎么确认成功了

### 6.1 日志里程碑

`idf.py monitor` 里应该从上到下依次看到这些：

```text
SKU=bread-compact-c6                    ← 板型识别正确
CompactC6Board
IoT Button Version: 4.2.1               ← 按键驱动加载
Duplex channels created                 ← I2S 麦克风 + 功放都起来了
rx channel switched master→slave
Certificate validated                    ← 网络和 TLS 握手正常（会出现多次）
Activation successful                   ← 设备激活成功
OLED IS LIVE                            ← 屏幕点亮
DeferredDisplay: attached real display, replayed UI state
```

| 看到这行 | 说明 |
|---|---|
| `SKU=bread-compact-c6` | 板型配对了 |
| `Duplex channels created` | 音频通道建起来了 |
| `Certificate validated` | 网络正常 |
| `OLED IS LIVE` | 屏亮了 |
| `Activation successful` | 设备激活成功 |

### 6.2 功能验收

按顺序做，每一步都该有明确反应：

| # | 操作 | 预期 |
|---|---|---|
| 1 | 看 OLED | 有画面（表情或文字），不一直黑屏 |
| 2 | 对它说唤醒词 | OLED 状态变化，喇叭出声 |
| 3 | 说一句完整的话 | 它回答，OLED 显示对话内容 |
| 4 | 按音量加 / 减 | 音量有变化 |
| 5 | 按住说话键说话 | 松开后它开始回答 |

---

## 7. 常见问题

### 7.1 编译或烧录阶段

| 症状 | 原因 | 怎么办 |
|---|---|---|
| `idf.py: command not found` | 环境没装或没激活 | 重装 ESP-IDF，或跑一遍它的 `export.sh` |
| 烧录一直等到超时 | 没进下载模式 | 按 4.6 手动进模式再试 |
| 烧录还是超时 | 端口号认错了 | 按 4.5 用 VID:PID 重新认，别用猜的端口号 |
| `Failed to connect` | 端口是蓝牙虚拟口 | 同上 |
| 编译报找不到板型 | 版本不对 | 确认 `git checkout v2.5.2` 执行过 |
| 编译假失败（退出码非 0） | 管道重定向导致 | 认 `Successfully created ESP32-C6 image.` 这一行 |
| 两个编译任务互相卡死 | 并行跑 `build` 了 | 同时只跑一个 |

### 7.2 上电后没反应

| 症状 | 先查这个 |
|---|---|
| 板子上电完全没反应 | 万用量 3V3 有没有电压；USB 线是不是只供电不传数据 |
| 日志停在最开头 | 复位键按住不放试试；再不行就是串口速率不对 |
| OLED 一直黑 | 看 [7.3](#73-屏幕黑屏) |
| 有声音但听不清 | 喇叭阻抗对不对（要 4Ω 或 8Ω）；`GAIN` 悬空了吗 |
| 完全没声音 | 功放 `SD` 接 3V3 了吗；喇叭接 `SPK+/SPK-` 了吗 |
| 能联网但不收音 | 麦克风 `SD` 接 GPIO6、`SCK` 接 GPIO5 了吗 |

### 7.3 屏幕黑屏

这是这一步最高频的问题。**先别急着怀疑屏坏了**，按下面顺序查：

**第一步，等 5 秒再看。**

SSD1306 的内部电荷泵上电后需要约 3 秒才应答。**刚上电等两三秒才亮是正常的，不是坏了。**
如果你等够 5 秒还是全黑，继续往下。

**第二步，看后台日志有没有重试。**

```text
OLED WATCH: still no ACK
```

这行每隔 2 秒打一次。**它反复出现说明屏在应答但不成功**，
不打说明固件压根没去探它。

**第三步，量电压。**

| 测到 | 含义 |
|---|---|
| 3V3 正常（约 3.3V） | 供电没问题 |
| SDA/SCL 电压在 0.6~0.7V 之间飘 | **屏侧 VCC 没接通，信号被 ESD 二极管钳位了** → 去查 OLED 供电线 |
| 3V3 只有 2.6~2.7V | 供电本身不够 → 换根好点的 USB 线或电源 |

> ⚠️ 第三行那个 0.6~0.7V 特别容易误判成「屏在工作」。它其实是**屏没供电**的信号。

**第四步，如果还是不行，临时用下面这段验证固件侧。**

这是固件里加的一段延迟探测代码，编译烧录后看后台日志里有没有打 `HIT`：

```text
delay 600ms  → MISS
delay 2500ms → MISS
running      → HIT      ← 说明屏是好的，只是启动时序问题
```

如果你现在正在排查这个问题，建议先读完
[排查手册](03-troubleshooting.md) 的「异步初始化外设」那一章，
那里的解法（代理对象模式）能彻底解决它，不用每次靠延时。

### 7.4 抓不到完整日志

CH343 有个老毛病：**收到复位脉冲后会断流**，只能抓到开头 700ms 左右。

| 问题 | 怎么办 |
|---|---|
| 管道里抓不到原生日志 | 输出落盘成文件，再用正则筛 |
| 反复调复位它来不及重新枚举 | 烧录完**立刻**开始监听；或者复位后等 2 秒再开端口 |
| 开一次端口只能抓一轮 | 一次开端口，循环复位抓多轮 |

开串口后**立刻**把 DTR 和 RTS 拉低，否则开串口的瞬间会把板子复位掉：

```python
import serial
ser = serial.Serial("<COM11>", 115200, timeout=1)
ser.setDTR(False)
ser.setRTS(False)
```

---

## 8. 存个存档

**这一步请认真做**，下一篇要改固件。

```bash
cd xiaozhi-esp32
git add -A && git commit -m "baseline: 语音助手已跑通"
git tag baseline-ai-working          # ← 关键

git checkout -b feature-dog          # 下一篇在这个分支上做
```

> ⚠️ **固件回退一律走 git，不要走 `read_flash`。**
>
> 实测过：全片 4MB 读取极不稳定（串口丢字节），分块最多稳定读到约 2MB，
> 拿回来的镜像不含 bootloader 和分区表，只能作应急参考。
>
> 而 git 一个 tag 就够了，回退的还是**完整可修改的源码**。

---

## 9. 做完之后

- 能对话了 → [教程二 · 小智机器狗](02-xiaozhi-dog.md)，在这套基础上加四条腿
- 卡住了 → [教程三 · 外设不工作怎么查](03-troubleshooting.md)
- 屏老是慢半拍才亮 → 见[附录 A](#附录-a屏幕为什么会晚亮约-3-秒)

---

# 附录：原理与排查

> 前面八节是动手主线，照着走就能做完。下面是主线里压缩掉的判断依据，
> **卡住了再回来读**。

## 附录 A：屏幕为什么会晚亮约 3 秒

同一个探测程序，换不同时间点跑，结果完全不同：

| 探测时机 | 结果 |
|---|---|
| 启动时立即探 | 全 miss |
| 延时 600ms | 全 miss |
| 延时 2500ms | 仍全 miss |
| 运行几秒后（后台任务里探） | **稳定 HIT** |

**根因：SSD1306 的 charge pump 上电极需要时间建立。** 屏物理上是好的，
只是应答之前内部还没准备好。实测稳定命中的时刻是**约 2.9 秒**，前 4 次全 miss。

所以固件要改成「启动不等屏」：

```cpp
// 启动时快速探一次，探不到就不管它，不拖慢开机
if (TryDetectOnce()) {
    AttachRealDisplay();
    return;                              // 启动即点亮
}

// 探不到 → 起后台任务慢慢重试
xTaskCreate(OledWatchTask, "oled_watch", 4096, this, 3, nullptr);
```

好处是开机不被拖慢、屏什么时候醒都能正确显示、重试过程有日志可观察。
后台任务每 2 秒打一次 `OLED WATCH: still no ACK`，
**这给了你一个不依赖启动时序的观测手段** —— 排查这类问题时非常有用。

### A.1 为什么不能直接换指针

一开始的做法是：探测到屏 → 把 `display_` 指针从 `NoDisplay` 换成真屏。

**症状：屏闪一下，然后一直黑。**

原因是 `Application::Initialize()` 会把 `board.GetDisplay()` **缓存成一个局部指针**，
之后所有 `SetupUI()` / `SetChatMessage()` 都用那个缓存。运行期换掉 `display_` 之后，
**没人再往新屏里画东西了**。那一闪是驱动 init 时刷了一帧 RAM，之后就没人管它。

**看起来像屏坏了，其实是没人再往里画东西。**

正确解法是代理对象，指针终身不变：

```cpp
class DeferredDisplay : public Display {
    // 缓存最后一次被要求显示的状态
    std::string last_status_, last_emotion_, last_chat_role_, last_chat_content_;

    void Attach(Display* real) {
        real_ = real;
        real_->SetupUI();                          // 按顺序重放缓存的状态
        real_->SetStatus(last_status_);
        real_->SetEmotion(last_emotion_);
        real_->SetChatMessage(last_chat_role_, last_chat_content_);
    }

    // 所有 Set* 方法都转发给 real_；没有 real_ 就先存进缓存
};
```

**核心：`display_` 整个生命周期只赋值一次，永远指向 `DeferredDisplay`。**

这个模式适用于任何需要延迟或异步初始化的外设：屏幕、音频 codec 芯片、
任何「开机时还不存在、之后才会出现」的东西。

## 附录 B：ESP-IDF 6.1 上的三个坑

如果你用的是 **6.1**，遇到下面三个问题不是你配置错了，是 IDF 本身的问题。
用 5.4 或某些厂商 fork 可能不受影响。

### B.1 `esp_crt_bundle` 越界崩溃

**现象**：TLS 证书校验时崩溃，`MCAUSE 0x2`，栈在 TLS 层。

**解法**：关掉交叉签名校验

```
CONFIG_MBEDTLS_CERTIFICATE_BUNDLE_CROSS_SIGNED_VERIFY=n
```

### B.2 I2C 总线销毁会导致悬垂指针

6.1 的 `i2c_del_master_bus()` 内部实现是：

```c
i2c_master_bus_destroy() { free(handle); handle = NULL; }
```

**只置空了自己的局部副本**，调用方手里那个指针变成悬垂指针。
紧接着再调 `i2c_new_master_bus` 就 `Load access fault`。

**结论：6.1 没有公开的 bus 销毁 API，也没有 GPIO 交换 API。
运行期想换 SDA/SCL 不可行 —— 要换接线只能改 `config.h` 重新编译。**

### B.3 I2C 跑 50kHz 会让设备卡死

I2C master 跑在 50kHz 时，日志断在探测中途，之后无任何输出。
**速率只用 400kHz 或 100kHz。**

另外 `esp_lcd_panel_io_tx_param(panel_io_, 0xAE, NULL, 0)` 会触发 `abort()` ——
6.1 的 i2c master 内部有 `ESP_ASSERT` 拦截空数据指针。
改成传单字节缓冲，或直接跳过这条命令。

## 附录 C：三次误判

排屏幕问题的时候，下面三个假设全部是错的：

| 曾经的假设 | 实际情况 | 靠什么推翻的 |
|---|---|---|
| SDA / SCL 接反了 | **接线从头到尾都是对的** | 万用表 + 实测 |
| OLED VCC 虚焊 | 量到 2.67V 属正常，屏能应答就是好的 | 应答成功 |
| 地址是 0x3D 不是 0x3C | **真实地址就是 0x3C**，0x3D 那个"命中"是 ESD 钳位假象 | 长时间复测 |

另外两次：

| 曾经的怀疑 | 实际情况 | 靠什么推翻的 |
|---|---|---|
| 供电不稳导致崩溃 | 崩溃点每次固定在同一行 | 栈回溯 |
| 板子烧坏了 | 能播报、联网、激活、屏能亮 | 功能实测 |

**共同点：所有「硬件/环境坏了」的怀疑，最后都被日志或一次简单测量推翻了。**

> 如果你现在正在怀疑自己的硬件，先读完
> [排查手册](03-troubleshooting.md) 的「五条铁律」再动手。

## 附录 D：速查

```bash
# 编译
idf.py build
# 判成功：BUILD_EXIT=0 + Successfully created ESP32-C6 image.

# 烧录（手动进下载模式 + 单次连接，中间不复位）
esptool --chip esp32c6 --port <COM11> --baud 460800 \
        --before no-reset --after no-reset \
        write-flash --flash-mode dio --flash-size 16MB --flash-freq 80m \
        0x0      build/bootloader/bootloader.bin \
        0x8000   build/partition_table/partition-table.bin \
        0xd000   build/ota_data_initial.bin \
        0x20000  build/xiaozhi.bin \
        0x800000 build/generated_assets.bin

# 复位进固件
esptool --chip esp32c6 --port <COM11> run

# 找串口（认 VID:PID，不认端口号）
python -m serial.tools.list_ports -v
# CH343 特征：USB VID:PID=1A86:55D3

# 回退固件
git checkout baseline-ai-working && idf.py build && idf.py flash
```

## 附录 E：可优化的方向

这一版已经能用，但还有改进余地：

1. **补 I2C 上拉电阻** —— 硬件上加 4.7kΩ 到 3V3，别只依赖 ESP32 内部弱上拉
2. **清理诊断日志** —— OLED 探测的 `ESP_LOGW` 可以降级，重试次数可以精简
3. **屏镜像 / 偏移** —— `DISPLAY_MIRROR_X/Y` 按实际屏型号调整
4. **换 128×32 屏** —— 改 `sdkconfig` 即可，其余不动
