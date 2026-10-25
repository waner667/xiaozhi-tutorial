# 小智机器狗 · Ep1 口播稿

> 配套《小智机器狗 · Ep1 拍摄脚本》使用。
> **分享视频，不是教学视频**：讲"我做了什么、长什么样"，不要求观众跟着做对。
> 括号里是动作/画面提示，不念。

---

## 基本信息

| 项 | 值 |
| --- | --- |
| 时长 | **1:27**（念白 0:40 + 动作停顿与看图时间） |
| 语速 | 正常偏慢，约 4.5 字/秒 |
| 字数 | **180 字**（不含标点，纯念 40 秒）；英文 116 词，念 0:50 |
| 语气 | 分享。像给朋友看手机里的视频，边说边指 |
| 出镜 | 正面出镜，手里拿着实物讲 |
| 不出现 | "关注我""点赞""订阅"；也不出现"我教你""跟着做""很简单" |

**流程一共五步**：材料 → 照图接好 → 接好的样子 → 烧固件 → 好了。

**接线一个字都不讲。** 不解释哪根线去哪、为什么这么接。画面放一次电路图，一句话带过，然后就展示接好的样子——观众要看的是结果，不是接线过程。

**180° 不单独起一句。** 它只是材料清单里的一个定语（"四个 180 度的舵机"），说完就过，不停下来解释——一解释就变成上课了。

**中英共用同一批切点**，拍一次画面套两条音轨。英文比中文慢约 10 秒，段2 的静场两边都留足，同样凑满 16 秒。

---

## [00:00] 段0 · 开场（8 秒）

（出镜。桌上零件摊开，四条腿的支架放最前面）

**中**　这是机器狗，今天做一个简单的教学。

**英**　This is the robot dog. Today I'm walking through the whole build.

---

## [00:08] 段1 · 材料（22 秒）

（出镜。手边逐件点过去，不快；点到舵机时拿起来转侧面，说完"180 度"就放回去，接着点面包板）

**中**　用到的就这些：一个 ESP32-C6，一块 OLED 屏，麦克风、功放、喇叭，四个 180 度的舵机，两块 400 孔的面包板，一把杜邦线。再加一个降压模块、一个电源，还有一根 Type-C 数据线——烧固件靠它。

**英**　Here's everything it takes: an ESP32-C6, an OLED screen, a microphone, an amplifier and a speaker, four 180-degree servos, two 400-hole breadboards, and a set of jumper wires. Plus a buck converter, a power supply, and a Type-C cable to flash it with.

（放回舵机时手停半秒，让"180°"的丝印露在镜头里，但你不再说话）

---

## [00:30] 段2 · 照图接好（16 秒）

（**切全屏电路图**，停住不动）

**中**　怎么接我就不讲了，照这张图接的。如果你稍微懂一点面包板的原理，半个小时就可以了。

**英**　I'm not filming the wiring. Just follow this diagram. If you know a little about how a breadboard works, half an hour is enough.

（图停：中文静 6 秒、英文静 5 秒，不配旁白，然后硬切不加转场）

---

## [00:46] 段3 · 接好的样子（12 秒）

（**空面包板照片** → 硬切**接好的照片**，同机位同角度，各约 5 秒）

**中**　左边是空板，右边是接好的。

**英**　On the left, an empty board. On the right, what it actually looks like.

---

## [00:58] 段4 · 烧固件（18 秒）

（出镜拿起 Type-C 线 → 切录屏，工具已打开。录屏操作这段可以 1.5× 加速）

**中**　接好之后，我们就把固件烧录到开发板中。

**英**　Once it's wired up, I just flash the firmware onto the board.

（进度条走完，不配旁白）

---

## [01:16] 段5 · 好了（11 秒）

（中景，手放电源开关上，打开。四条腿自己站起来。**一镜到底**）

**中**　烧完，打开电源。它自己站起来了。

**英**　Power on. And it stands up on its own.

（停 2 秒，让它做完动作）

**中**　到这儿就成了。

**英**　That's it.

---

## 段3 附：两张照片怎么拍

这两张是这集的画面主体，也是最容易白拍的地方。**决定成败的只有一条：两张必须同机位。**

**机位**

- 手机**正上方垂直俯拍**，镜头与面包板平行。斜着拍孔会变椭圆，两张对不齐，也没法和电路图对照。
- 手机固定在支架或一摞书上，拍完第一张**不要碰它**，插完元件再按一次快门。
- 用 **2× 变焦**（减少广角畸变），两张都别改焦段和距离。

**画面**

- 两块 400 孔面包板**都要入画**，别一张拍一块。
- 板边到画面边留 **5~10% 余量**，不要切边。
- 垫白纸或深色布，电源线、舵机腿、工具**全部移出画面**。
- 两侧来光，**关闪光灯**，别让顶灯直射（面包板是白的，容易过曝糊成一片）。

**"接好的"那张**

- 拍之前把线理顺：**别压住板上的丝印**，别跨过舵机区。整齐本身就是画面信息。
- 后期若要标编号，先想好标哪儿，别落在元件上。

**导出**

- 原图直出，长边 **≥2000px**，JPG 质量 90 以上。
- **别用微信传**（会压到 1080），用数据线或网盘。
- 命名 `ep1-board-empty.jpg` / `ep1-board-wired.jpg`，放进 `xiaozhi-tutorial/assets/`。

**验收**：把两张来回切换着看，**元件位置几乎不跳动**才算过。跳得多就是机位动了，重拍比后期修省事。

---

## 中英术语对照（两边必须一致）

| 中文 | English | 备注 |
| --- | --- | --- |
| 小智 / 机器狗 | Xiaozhi / robot dog | 片名用小写 dog |
| ESP32-C6 | ESP32-C6 | 不译 |
| OLED 屏 | OLED screen | 不是 display |
| 麦克风 | microphone | INMP441 |
| 功放 | amplifier | MAX98357 |
| 喇叭 | speaker | 不是 horn |
| 舵机 | servo | SG90 |
| 180 度版 / 连续旋转 | 180-degree / continuous rotation | |
| 面包板（400 孔） | breadboard (400-hole) | 也叫 half-size |
| 降压模块 | buck converter | 不是 voltage regulator |
| 杜邦线 | jumper wires | 不是 dupont |
| Type-C 数据线 | Type-C cable | 烧固件用，**必须能传数据** |
| 接线图 | wiring diagram | 不是 circuit picture |
| 固件 / 烧录 | firmware / flash it | **不用 burn** |

---

## 英文别写成中式腔

这几句最容易翻硬。左边是坑，右边是自然说法。

| 别这么说 | 这么说 |
| --- | --- |
| This is a simple teaching. | I'm walking through the whole build. |
| I will not teach you how to connect. | I'm not going to film the wiring. |
| You need to know the principle of the breadboard. | If you know a little about how a breadboard works. |
| The left side is the jumper wire picture. | On the left, an empty board. |
| We burn the firmware into the development version. | I just flash the firmware onto the board. |
| It stood up by itself. | It stands up on its own. |
| I chose the 180-degree version. | four 180-degree servos — 放进清单里，不多说 |

---

## 录制提醒（中英共通）

- **别念稿**。记住每段一句核心话，其他用自己的话说。
- **看镜头**，一句话的结尾尤其要看镜头。
- **手里有东西**：讲舵机拿舵机，讲面包板举面包板。
- **"180 度"说完就走**。别补"因为 360 度转不出角度"这类解释，一解释就变上课。
- **电路图那段别抢话**。图一放上去就闭嘴，静够时间——那几秒比多说十句有用。
- **英文比你以为的慢**。开拍前带计时器念两遍；不到 0:48 说明在赶，放慢而不是加词。
- **站起来的镜头别剪碎**，也别配 BGM，就用现场声。

---

## 自检

```
□ 第一句就进内容，没有自我介绍
□ 材料点完，数量对（4 个舵机、2 块面包板、1 把杜邦线、1 根 Type-C 数据线）
□ "180 度"在材料里顺带说，没有单独起一句
□ 接线只用一句话带过，没有展开讲颜色/引脚
□ 电路图完整出现，静够约 6 秒没人说话
□ 空板/满板两张图都出现
□ 烧录用 "flash the firmware onto the board"，不是 "burn into the development version"
□ 站起来那段一镜到底
□ 没有"关注/点赞/订阅"，没有"我教你"
```
