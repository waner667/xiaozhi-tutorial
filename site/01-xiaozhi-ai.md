---
title: 教程一 · 小智 AI 语音助手（ESP32-C6 + OLED + 麦克风 + 功放）
order: 1
difficulty: 3
---

# 教程一 · 小智 AI 语音助手（ESP32-C6 + OLED + 麦克风 + 功放）

> 做一台能联网对话的桌面语音助手：按键配网、OLED 显示表情、I2S 麦克风收音、
> I2S 功放播报。
>
> **难度**：★★★☆☆
> **硬件成本**：约 ¥80
> **动手时间**：约 3 小时（其中大半花在环境和屏幕排查上）
> **成品**：一块能听懂人话、能显示表情、能开口回答的桌面 AI 助手

这一篇是整个教程的基础篇。后面那篇「机器狗」是在**同一块板子、同一个固件**
上再加外设，所以做完这篇把固件存档留着。

---

## 1. 成品长什么样

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

做完之后你能得到：

- 长按 BOOT 键进入配网，微信「小智 AI」小程序扫码
- 联网后 OLED 显示待机表情，说唤醒词开始对话
- 语音输入 → AI 处理 → 语音合成 → 功放播放
- OLED 实时显示状态文字和表情
- 音量加减、停止对话等按键功能

---

## 2. 硬件清单

| 物料 | 规格 | 数量 | 备注 |
|---|---|---|---|
| 主控板 | ESP32-C6 面包板开发板 | 1 | 已焊好排针，带 CH343 串口芯片 |
| OLED | SSD1306 128×64，I2C **四脚** | 1 | 128×32 也行，驱动按 height 自适应 |
| 麦克风 | I2S 数字麦克风 | 1 | 常见型号 INMP441 |
| 功放 | MAX98357A I2S 数字功放 | 1 | |
| 喇叭 | 4Ω 3W 左右 | 1 | 配 MAX98357A 用 |
| 面包板 | 半幅或全幅 | 1 | |
| 杜邦线 | 公对母 / 公对公 | 若干 | |

**★ 买之前确认一件事**：OLED 要买**四脚 I2C 版本**，不是七针 SPI 版本。
两者外观很像但协议完全不同，插上去不会有任何反应。

### 关于 ESP32-C6 面包板开发板

本教程用的板型是小智固件里叫 **`bread-compact-c6`** 的那一款：
一块 ESP32-C6 焊在小面包板上，已经引出全部引脚和 BOOT 键。

买的时候认准"ESP32-C6 面包板 / 紧凑版"，普通 ESP32-C6 开发板
（DevKitC 之类）引脚定义不同，**固件要重新配置，不能直接照搬本文接线表**。

---

## 3. 引脚分配

**这张表是照着 `bread-compact-c6` 的丝印分配的。换板子必须重新对照。**

| 功能 | GPIO | 说明 |
|---|---|---|
| I2S WS | 4 | C6 只有一个 I2S，收发共享时钟 |
| I2S BCLK | 5 | 麦克风 SCK 与功放 BCLK 共用 |
| I2S DIN | 6 | 麦克风数据输入 |
| I2S DOUT | 7 | 功放数据输出 |
| BOOT 键 | 9 | 板载 |
| **OLED SDA** | **11** | ⚠️ 记住是 11，不是 10 |
| **OLED SCL** | **10** | ⚠️ 记住是 10，不是 11 |
| 音量减 | 19 | 低电平有效（内部上拉） |
| 音量加 | 20 | 低电平有效 |
| 说话 / 触摸 | 21 | 低电平有效 |
| 板载 LED | 2 | 状态灯 |

> ### ⚠️ OLED 这两个脚是反直觉的
>
> SDA→**GPIO11**、SCL→**GPIO10**，和大多数人"左边 SDA 右边 SCL"的直觉相反。
>
> 四脚 OLED 的两个信号脚**紧挨着**，插反概率很高。
> 接的时候对着模块上的丝印数，不要凭左右位置猜。

> **I2C 上拉电阻**：SDA / SCL 各并一个 **4.7kΩ** 到 3.3V。
> ESP32 内部弱上拉只有约 45kΩ，偏弱，不加外部电阻在长线或干扰环境下会不稳。

---

## 4. 实物接线

![小智 AI 实物接线：ESP32-C6 面包板 + OLED + 功放 + 喇叭，左侧为外接喇叭](../assets/wiring-ai.jpg)

*图：小智 AI 整机接线实拍。ESP32-C6 装在面包板中段，上面三个小按键从上到下是
音量减 / 音量加 / 说话键；右下角绿色排针是四脚 I2C OLED；左侧黑色方盒是 4Ω 喇叭
（经 MAX98357A 功放驱动）；中间圆形蜂鸣器位置焊的是 INMP441 数字麦克风。*

对照上面的表看实物，重点确认三处：

1. **OLED 靠面包板下沿**，插的是 SDA=11 / SCL=10 这一对，注意别数反
2. **功放和麦克风共用 BCLK（GPIO5）**，所以有三根线一起从 C6 拉过去
3. **MAX98357A 的 SD / EN 硬接 3V3**，不要接到 GPIO21（那是按键脚）

---

## 5. 固件准备

```bash
git clone https://github.com/78/xiaozhi-esp32.git
cd xiaozhi-esp32
git checkout v2.5.2          # 本教程实测的版本
```

配置板型和功能：

```bash
idf.py set-target esp32c6
idf.py menuconfig
```

在 menuconfig 里选：

- 板型 → **`bread-compact-c6`**
- OLED 型号 → SSD1306 128×64
- 音频 codec → 按你的实际接线选

然后编译烧录：

```bash
idf.py build
idf.py flash
idf.py monitor
```

### ★ 烧录前必读

ESP32-C6 配 CH343 串口芯片的板子，**自动复位基本不生效**，
必须手动进下载模式，否则 esptool 会一直空等直到超时：

```
1. 按住 BOOT 键 不放
2. 点一下 RST 键
3. 松开 RST
4. 继续按住 BOOT 约 2 秒
5. 松开 BOOT
```

完整烧录命令、串口识别方法、esptool 语法坑都在
[③ 外设不工作怎么查](../site/03-troubleshooting.md)。**第一次做建议先读一遍那一篇。**

---

## 6. 配网

1. 长按 **BOOT** 键约 3 秒
2. 屏幕提示进入配网
3. 用**微信「小智 AI」小程序**扫码

> **不要用浏览器打开配网页面** —— 那是 404。
> 本教程实测踩过：浏览器扫码跳转后一片空白，代码和接线都正常。
> 换小程序就好了。

---

## 7. 怎么确认成功了

日志里应该看到这些标志，**从上到下依次出现**：

```text
SKU=bread-compact-c6                    ← 板型识别正确
CompactC6Board
IoT Button Version: 4.2.1               ← 按键驱动加载
Duplex channels created                 ← I2S 初始化成功
rx channel switched master→slave
Certificate validated                    ← TLS 握手成功（会出现多次）
Activation successful                   ← 设备激活成功
OLED IS LIVE                            ← 屏幕点亮
DeferredDisplay: attached real display, replayed UI state
```

| 看到 | 含义 |
|---|---|
| `SKU=bread-compact-c6` | 板型配对了 |
| `Duplex channels created` | I2S 麦克风 + 功放通道都建起来了 |
| `Certificate validated` | 网络和 TLS 握手正常 |
| `OLED IS LIVE` | 屏幕已经点亮 |
| `DeferredDisplay: attached real display` | 屏幕接管完成（见下一节） |

**如果停在某一行不动，那就是那一层的问题**，对照[第 8 节](#8-屏幕点不亮怎么办)排查。

---

## 8. 屏幕点不亮怎么办

**这一节值得完整读。** 屏点不亮是这一步最高频的问题，
而且它的解法是通用的 —— 后面做机器狗时异步初始化的外设也用同一套。

### 8.1 症状

屏一直黑。看起来像坏了、接错了、或者供电有问题。

### 8.2 先别怀疑硬件：把探测时机拉长

真正的线索来自**同一个探测程序，换不同时间点跑**：

| 探测时机 | 结果 |
|---|---|
| 启动时立即探 | 全 miss |
| 延时 600ms | 全 miss |
| 延时 2500ms | 仍全 miss |
| 运行几秒后（后台任务里探） | **稳定 HIT**，连续多轮 |

**根因：SSD1306 的 charge pump 上电极需要时间建立。**
屏在物理上是好的，只是应答之前内部还没准备好。

实测稳定命中的时刻是**约 2.9 秒**，前 4 次探测全部 miss。

> ### 💡 所以"刚上电等两三秒才亮"是正常的，不是坏了。
>
> 后面 [10.1 节的代理对象](#101-架构教训不能用指针替换掉屏) 就是为此做的。

### 8.3 三个曾经的误判

排这个问题的过程中，下面三个假设全部是错的：

| 曾经的假设 | 实际情况 |
|---|---|
| SDA / SCL 接反了 | **接线从头到尾都是对的** |
| OLED VCC 虚焊 | 量到 2.67V 属正常，屏能应答就是好的 |
| 地址是 0x3D 不是 0x3C | **真实地址就是 0x3C**。0x3D@50kHz 那个"命中"是 ESD 钳位假象 |

> 第三个特别值得说：读到的 `0x6~0x7V` 附近的电压**不是"屏在工作"**，
> 而是**屏侧 VCC 没接通、信号被 ESD 保护二极管钳位了**。
> 见到这个读数，先查供电，不是查驱动。

### 8.4 解法：启动不等屏

```cpp
// 启动时快速探一次，探不到就不管它，不拖慢开机
if (TryDetectOnce()) {
    AttachRealDisplay();
    return;                              // 启动即点亮
}

// 探不到 → 起后台任务慢慢重试
xTaskCreate(OledWatchTask, "oled_watch", 4096, this, 3, nullptr);
```

好处：

- **开机不被拖慢**（不用干等 3 秒）
- 屏什么时候醒都能正确显示
- 重试过程有日志，可以观察

后台任务每 2 秒打印一次 `OLED WATCH: still no ACK`，
**这给了你一个不依赖启动时序的观测手段** —— 排查这类问题时非常有用。

---

## 9. IDF 6.1 上的三个具体坑

如果你用 ESP-IDF 6.1，遇到下面三个问题不是你的配置错了，是 IDF 本身的问题。

### 9.1 `esp_crt_bundle` 越界崩溃

**现象**：TLS 证书校验时崩溃，`MCAUSE 0x2`，栈在 TLS 层。

**解法**：关掉交叉签名校验

```bash
CONFIG_MBEDTLS_CERTIFICATE_BUNDLE_CROSS_SIGNED_VERIFY=n
```

> IDF v5.4 或某些厂商 fork 不受影响。**这是 6.1 的问题。**

### 9.2 I2C 总线销毁会导致悬垂指针

IDF 6.1 的 `i2c_del_master_bus()` 内部实现是：

```c
i2c_master_bus_destroy() { free(handle); handle = NULL; }
```

**只置空自己的局部副本**，调用方手里那个指针变成悬垂指针。
紧接着再调 `i2c_new_master_bus` 就 `Load access fault`。

**结论：IDF 6.1 没有公开的 bus 销毁 API，也没有 GPIO 交换 API。
运行期想换 SDA/SCL 不可行 —— 要换接线只能改 `config.h` 重新编译。**

### 9.3 I2C 跑 50kHz 会让设备卡死

I2C master 跑在 50kHz 时，日志断在探测中途，之后无任何输出。

**速率只用 400kHz 或 100kHz。**

另外 `esp_lcd_panel_io_tx_param(panel_io_, 0xAE, NULL, 0)` 会触发 `abort()` ——
IDF 6.1 的 i2c master 内部有 `ESP_ASSERT` 拦截空数据指针。改成传单字节缓冲，或直接跳过这条命令。

---

## 10. 架构教训：异步初始化的外设不能用指针替换

**这是整篇教程最值钱的一条，而且和硬件完全无关。**

### 10.1 问题

一开始的做法是：探测到屏 → 把 `display_` 指针从 `NoDisplay` 换成真屏。

**症状：屏闪一下，然后一直黑。**

### 10.2 为什么

`Application::Initialize()` 会把 `board.GetDisplay()` **缓存成一个局部指针**，
之后所有 `SetupUI()` / `SetChatMessage()` 调用都用那个缓存。

运行期把 `display_` 换掉之后 —— **没人再往新屏里画东西了。**
那一闪是驱动 init 时刷了一帧 RAM，之后就没人管它。

**看起来像屏坏了，其实是没人再往里画东西。**

### 10.3 正确解法：代理对象，指针终身不变

```cpp
class DeferredDisplay : public Display {
    // 缓存最后一次被要求显示的状态
    std::string last_status_, last_emotion_, last_chat_role_, last_chat_content_;

    void Attach(Display* real) {
        real_ = real;
        // 按顺序重放缓存的状态
        real_->SetupUI();
        real_->SetStatus(last_status_);
        real_->SetEmotion(last_emotion_);
        real_->SetChatMessage(last_chat_role_, last_chat_content_);
    }

    // 所有 Set* 方法都转发给 real_；没有 real_ 就先存进缓存
};
```

**核心：`display_` 整个生命周期只赋值一次，永远指向 `DeferredDisplay`。**
真屏什么时候来都不影响 —— 来了就 Attach，然后重放之前缓存的状态。

### 10.4 什么时候需要这个模式

任何需要延迟或异步初始化的外设：

- 屏幕
- 音频 codec 芯片
- 任何"开机时还不存在、之后才会出现"的外设

> **不要在运行期替换别人的指针，要保持指针稳定、把状态缓存起来。**

---

## 11. 抓串口日志的坑

CH343 有个老毛病：**收到复位脉冲后必断流**，只能抓到前 700ms 左右。

| 问题 | 解法 |
|---|---|
| 管道抓不到原生日志 | 输出落盘，再用正则筛文件 |
| 反复调复位，CH343 来不及重新枚举 | 烧录后**立即**监听；或复位后等 2 秒再开端口 |
| 一次开端口只能抓一轮 | 一次开端口、循环复位多轮 |

**开串口后必须立刻拉低 DTR / RTS**：

```python
ser = serial.Serial(port, 115200, timeout=1)
ser.setDTR(False)     # 否则开串口瞬间会把板子复位掉
ser.setRTS(False)     # 开机日志就永久丢了
```

> **更好的办法**：如果代码里有周期性重试打印（比如上面那个 OLED 探测任务），
> 纯监听就能抓到完整过程，**不依赖启动窗口**。
> 排查异步初始化类问题时优先用这招。

### 编译成功的判据

```
BUILD_EXIT=0
Successfully created ESP32-C6 image.
```

**不要只看退出码** —— 管道提前关闭会让它假失败，以这两行日志为准。

另外：**不要同时跑两个编译任务**，它们会互相锁 obj 文件。

---

## 12. 三次误判（请认真读）

这一节把上面的排查过程压缩成三行。**每一次我都在还没拿到物理事实之前
就下结论，然后引导去改硬件。**

| 曾经的怀疑 | 实际情况 | 靠什么推翻的 |
|---|---|---|
| 供电不稳导致崩溃 | 崩溃点每次固定在同一行 | 栈回溯 |
| 板子烧坏了 | 能播报、联网、激活、屏能亮 | 功能实测 |
| SDA / SCL 接反了 | 接线从头到尾是对的 | 万用表 + 实测 |

**共同点：所有"硬件/环境坏了"的怀疑，最后都被日志或一次简单测量推翻了。**

如果你现在正在怀疑自己的硬件，先读完
[③ 外设不工作怎么查](../site/03-troubleshooting.md) 再动手。

---

## 13. 速查

```bash
# 编译
idf.py build
# 判成功：BUILD_EXIT=0 + Successfully created ESP32-C6 image.

# 烧录（必须手动进下载模式 + 单次连接，中间不复位）
esptool --chip esp32c6 --port <COM> --baud 460800 \
        --before no-reset --after no-reset \
        write-flash --flash-mode dio --flash-size 16MB --flash-freq 80m \
        0x0      build/bootloader/bootloader.bin \
        0x8000   build/partition_table/partition-table.bin \
        0xd000   build/ota_data_initial.bin \
        0x20000  build/xiaozhi.bin \
        0x800000 build/generated_assets.bin

# 复位进固件
esptool --chip esp32c6 --port <COM> run

# 监听（开端口后立即拉低 DTR/RTS）
# ser.setDTR(False); ser.setRTS(False)
```

**串口按 VID:PID 认，不看 COM 号**：

```python
from serial.tools import list_ports
for p in list_ports.comports():
    print(p.device, '|', p.description, '|', p.hwid)
```

CH343 的特征是 `USB VID:PID=1A86:55D3`。
Windows 上 COM3/4/5/6/8/9 这类通常是蓝牙虚拟口，**往这些口烧录会安静地空等然后超时**。

烧录偏移**不要手抄**，从 `build/flasher_args.json` 里读。

---

## 14. 存档：给下一篇留一个退路

这一步请认真做，下一篇要改固件。

```bash
git add -A && git commit -m "baseline: 语音助手已跑通"
git tag baseline-ai-working          # ← 关键

git checkout -b feature-dog          # 下一篇在这个分支上做
```

**★ 固件回退一律走 git，不要走 `read_flash`。**

实测把 `read_flash` 这条路试穿了：全片 4MB 读取极不稳定（串口丢字节），
分块最多稳定读到约 2MB，拿回来的镜像不含 bootloader 和分区表，
只能作应急参考。

而 git 一个 tag 就够了，回退的还是**完整可修改的源码**。

---

## 15. 下一步

- 能说话了吗？→ [教程二 · 小智机器狗](02-xiaozhi-dog.md)，在这套基础上加四条腿
- 卡住了？→ [③ 外设不工作怎么查](03-troubleshooting.md)
- 舵机不动这个坑值得先看 → [教程二的 PWM 章节](02-xiaozhi-dog.md#pwm-参数)

---

## 附：可优化的方向

这一版已经能用，但还有改进余地：

1. **清理诊断日志** —— OLED 探测的 `ESP_LOGW` 可以降级，8 次重试可以精简
2. **屏镜像 / 偏移** —— `DISPLAY_MIRROR_X/Y` 按实际屏型号调整
3. **补 I2C 上拉电阻** —— 硬件上加 4.7kΩ 到 3.3V，别只依赖 ESP32 内部弱上拉
4. **换 128×32 屏** —— 改 `sdkconfig` 即可，其余不动