# Xiaozhi Robot Dog · Ep1 Voice-Over (English · Share Version)

> Pairs with the Chinese shooting script `ep1-dog-zh-script.md`.
> **This is a share video, not a tutorial.** Show what was built and what it looks like — don't teach.
> Everything in `[brackets]` is a note to you, not to be read aloud.

---

## Basics

| Item | Value |
|---|---|
| Length | **1:27** (VO ≈ 0:50 + pauses and screen time) |
| Pace | Slow and easy, **~140 words per minute** |
| Word count | **116 words** (measured by `_count_vo_en.py`) |
| Tone | Sharing. Like showing a friend a video on your phone. |
| On camera | Face to camera, holding the parts |
| Never say | "subscribe", "like and follow", "it's super easy", "you can do it too" |

**Five steps**: parts → wired per the diagram → what it looks like → flash it → done.

**Cut points are identical to the Chinese version.** Shoot the footage once, lay two audio tracks
on it. English runs a bit longer (116 words ≈ 0:50 vs 180 Chinese characters ≈ 0:40), so both versions sit on at
least 6 seconds of silence here — same 16-second slot either way.

**Zero wiring explanation.** One sentence, one diagram, then straight to the finished build.
No colours, no pin numbers, no "this one goes here".

**Logical flow**: no step numbers, no "you must", no troubleshooting.

**The 180° servos get one adjective, not a sentence.** They're listed as "four 180-degree servos"
and that's it — no follow-up explanation. The moment you explain why, it turns into a class.
Just hold one up long enough for the marking to be readable, then move on.

---

## [00:00] Block 0 · Opening (8 s)

*(On camera. Parts spread out on the table, the four legs set in front.)*

> This is the robot dog.
>
> Today I'm walking through the whole build.

---

## [00:08] Block 1 · The parts (22 s)

*(On camera. Point at each item in turn, unhurried. When you reach the servos, pick one up and turn it
side-on — say "180-degree", put it back down, carry on with the breadboards.)*

> Here's everything it takes: an ESP32-C6, an OLED screen, a microphone,
> an amplifier and a speaker, four 180-degree servos, two 400-hole breadboards, and a set of jumper wires.
>
> Plus a buck converter, a power supply, and a Type-C cable to flash it with.

*(Half a beat as you set the servo down, so the marking reads on camera. Don't add anything.)*

---

## [00:30] Block 2 · Wired per the diagram (16 s)

*(**Cut to the full-screen wiring diagram.** Hold it still. VO ends, then 6 s of silence — say nothing.)*

> I'm not filming the wiring.
>
> Just follow this diagram. If you know a little about how a breadboard works, half an hour is enough.

*(Diagram holds 6 s. Nobody talks. Then hard cut.)*

---

## [00:46] Block 3 · What it looks like (12 s)

*(**Empty breadboard photo** → cut to **the finished build photo**. Same position, same angle, hard cut — no transition.)*

> On the left, an empty board. On the right, what it actually looks like.

*(About 5 s on each image.)*

---

## [00:58] Block 4 · Flashing the firmware (18 s)

*(Pick up the USB cable → cut to screen recording, tool already open. This clip can run at 1.5×.)*

> Once it's wired up, I just flash the firmware onto the board.

*(Let the progress bar finish. No narration over it.)*

---

## [01:16] Block 5 · Done (11 s)

*(Medium shot. Hand on the power switch. Flip it. The legs stand up. **One take, no cuts.**)*

> Power on.
>
> And it stands up on its own.

*(Hold 2 s. Let it finish the motion.)*

> That's it.

---

## Chinese ↔ English, block by block

Use this to check the English says what you meant. **Do not read the left column.**

| Block | 中文原句 | English |
|---|---|---|
| 0 | 这是机器狗，今天做一个简单的教学。 | This is the robot dog. Today I'm walking through the whole build. |
| 1 | 用到的就这些：一个 ESP32-C6，一块 OLED 屏，麦克风、功放、喇叭，四个 180 度的舵机，两块 400 孔的面包板，一把杜邦线。再加一个降压模块、一个电源，还有一根 Type-C 数据线——烧固件靠它。 | Here's everything it takes: an ESP32-C6, an OLED screen, a microphone, an amplifier and a speaker, four 180-degree servos, two 400-hole breadboards, and a set of jumper wires. Plus a buck converter, a power supply, and a Type-C cable to flash it with. |
| 2 | 怎么接我就不讲了，照这张图接的。如果你稍微懂一点面包板的原理，半个小时就可以了。 | I'm not filming the wiring. Just follow this diagram. If you know a little about how a breadboard works, half an hour is enough. |
| 3 | 左边是空板，右边是接好的。 | On the left, an empty board. On the right, what it actually looks like. |
| 4 | 接好之后，我们就把固件烧录到开发板中。 | Once it's wired up, I just flash the firmware onto the board. |
| 5 | 烧完，打开电源。它自己站起来了。到这儿就成了。 | Power on. And it stands up on its own. That's it. |

---

## Say it like this, not like that

These are the lines most likely to come out stiff. Left is the trap, right is natural.

| Don't say | Say |
|---|---|
| "This is a simple teaching." | "I'm walking through the whole build." |
| "I will not teach you how to connect." | "I'm not going to film the wiring." |
| "You need to know the principle of the breadboard." | "If you know a little about how a breadboard works." |
| "The left side is the jumper wire picture." | "On the left, an empty board." |
| "We burn the firmware into the development version." | "I just flash the firmware onto the board." |
| "It stood up by itself." | "It stands up on its own." |
| "I chose the 180-degree version." | "four 180-degree servos" — inside the list, nothing more |

---

## Vocab (keep these exact — they match the tutorial)

| 中文 | English |
|---|---|
| ESP32-C6 | ESP32-C6 |
| OLED 屏 | OLED screen |
| 麦克风 | microphone (INMP441) |
| 功放 | amplifier (MAX98357) |
| 喇叭 | speaker |
| 舵机 | servo (SG90) |
| 180 度版 / 连续旋转 | 180-degree / continuous rotation |
| 面包板（400 孔） | breadboard (400-hole / half-size) |
| 降压模块 | buck converter |
| 杜邦线 | jumper wires |
| Type-C 数据线 | Type-C cable (data, not charge-only) |
| 接线图 | wiring diagram |
| 固件 / 烧录 | firmware / flash it |

---

## Recording notes

- **Don't read the script.** Memorise one idea per block and say it your own way.
- **Look at the lens** — especially on the last word of a sentence.
- **Hold the thing you're talking about.** Servo in hand exactly on the word "180-degree",
  then put it down — that's the whole treatment it gets.
- **Silence on the diagram.** The moment the diagram is up, stop talking. Let it sit 4 seconds.
  That silence does more than ten extra sentences.
- **English is slower than you think.** Read it out loud twice with a timer before shooting.
  If you land under 0:48, you're rushing — slow down rather than adding words.
- **Don't cut the stand-up shot.** One take, room sound, no music.

---

## Self-check before you publish

```
□ Opens on content — no self-introduction
□ Parts count right (4 servos, 2 breadboards, jumper wires, 1 Type-C cable)
□ "four 180-degree servos" sits inside the list — no separate servo sentence
□ Wiring covered in one sentence, no colours or pin numbers
□ Diagram on screen a full 6 s with nobody talking
□ Empty board and finished board both shown
□ "flash the firmware onto the board" — not "burn into the development version"
□ Stand-up shot is one unbroken take
□ No "subscribe / like / follow", no "super easy"
```
