# Video Script — Xiaozhi Robot Dog (English, for publishing)

Two lengths: **Short (~50s)** for TikTok / Reels / Shorts, **Full (~4–5 min)** for YouTube.
Everything in `[brackets]` is a note to you, not to be read aloud.

---

## Part 1 — Short version (~50 seconds)

### [0:00–0:05] HOOK
*(straight to camera, dog visible in frame)*

> "This is a voice assistant with four legs. No remote, no app — I just talk to it."

### [0:05–0:14] DEMO
> "Xiaozhi, stand up."

*(dog stands — let it fully settle, 2 seconds)*

> "Xiaozhi, sit down."

*(dog sits — hold the shot)*

> "Everything you just saw runs on one ESP32-C6. That's a RISC-V chip that costs about four dollars."

### [0:14–0:25] HOW IT WORKS
> "When I speak, an INMP441 microphone picks it up, the audio goes over Wi-Fi to a cloud AI, and the answer comes back through a small amplifier and speaker.
>
> The exact same command that makes it talk also drives four SG90 servos. One per leg."

### [0:25–0:38] THE BUILD
> "Four servos on GPIO 3, 18, 22 and 23.
>
> And here's the part most people get wrong: the servos run on their own 5-volt, 3-amp supply. Power them from the board and the whole thing reboots the moment they move.
>
> That big capacitor taped next to it? That's not decoration. That's what stops the voltage dipping when all four stall at once."

### [0:38–0:50] CLOSER + CTA
> "The firmware is open source — I'll link my board config and the servo controller below.
>
> If you build one, tag me. I genuinely want to see your version."

---

## Part 2 — Full version (~4–5 minutes)

### [0:00–0:20] COLD OPEN — demo montage
*(no talking. Three quick cuts: stand → sit → it reacting to a question. Music off, room sound only.)*

### [0:20–0:45] INTRO
> "A few months ago I built a voice assistant on a breadboard — microphone, speaker, a tiny OLED screen for its face. It worked, but it just sat there.
>
> So I gave it legs.
>
> Four servos, one voice command at a time. Here's how it's built."

### [0:45–1:30] DEMO
*(clean single take, dog centered, you off-camera)*

> "Xiaozhi, stand up."

*(wait for full stand)*

> "Xiaozhi, sit down."

*(wait)*

> "The screen shows what it's doing — listening, thinking, speaking. Same face the desktop version had, now on something that moves.
>
> There's no remote anywhere in this. Every single motion comes from a spoken sentence."

### [1:30–2:30] WHAT'S ACTUALLY IN IT
*(hold each part up to camera as you name it — B-roll over the table)*

> "The whole thing is six parts.
>
> An ESP32-C6 — RISC-V, Wi-Fi 6, about four dollars. That's the only computer in the build.
>
> Four SG90 servos. Nine grams each, about two dollars each. The 180-degree kind — not the continuous-rotation kind, because I need it to know what angle it's at.
>
> An INMP441 digital microphone, an SSD1306 OLED screen for the face, a MAX98357 amplifier and a 4-ohm speaker for the voice.
>
> And one 1000-microfarad capacitor, which I'll come back to because it matters more than anything else on this list."

### [2:30–3:20] THE VOICE PIPELINE
*(diagram on screen: mic → C6 → Wi-Fi → cloud → speaker + servos)*

> "Here's the path your voice takes.
>
> The microphone feeds the C6 over I2S. The audio goes up over Wi-Fi to the AI in the cloud — speech to text, the actual thinking, then text to speech. The reply comes back down and plays through the speaker.
>
> Now, the interesting part: that same sentence also gets parsed for commands. 'Stand up' isn't audio the dog hears — it's a string the firmware matches, and that match is what fires the servos.
>
> So voice and motion come out of one pipeline. I didn't build two systems."

### [3:20–4:10] THE SERVOS
*(close-up of a servo, then the four legs)*

> "Each servo gets three wires: brown to ground, red to 5 volts, and the signal line to a GPIO.
>
> GPIO 3, 18, 22 and 23 — one per leg. I kept 14 and 15 free on purpose, because there are pins on this chip you simply cannot use. GPIO 24 through 30 are wired to the internal flash. GPIO 16 and 17 are the debug UART. Touch those and the board won't boot at all.
>
> The signal is standard 50-hertz PWM — a 20-millisecond period, and the pulse width between 500 and 2500 microseconds sets the angle.
>
> One detail that costs people an afternoon: the left legs are mirrored. In code, the right side is just 180 minus the left. Get that wrong and your dog walks in circles."

### [4:10–4:50] POWER — THE PART EVERYONE GETS WRONG
*(close-up of the 5V supply and the capacitor)*

> "If you copy one thing from this video, copy this.
>
> Do not power the servos from the board. Four SG90s pulling at once can hit two and a half amps. The board's regulator can't do that — the voltage dips, and the ESP32 browns out and reboots. Your dog twitches and dies.
>
> So the servos get their own 5-volt, 3-amp supply, wired straight in with thick leads, not through the breadboard rails. Breadboard rails top out around one amp.
>
> The board gets fed from a separate branch of the same supply, and the two grounds are tied together. That common ground isn't optional — without it the PWM signal has no reference, and the servos just sit there locked and silent.
>
> And the capacitor — 1000 microfarads, positive to 5V, negative to ground, as close to the board as you can get it. It's there for the instant a servo stalls and drags the rail down. Without it, the screen flickers and goes black. With it, nothing happens, which is exactly what you want."

### [4:50–5:20] FINAL DEMO
> "So: one four-dollar chip, four two-dollar servos, one voice pipeline, no remote."

*(look at the dog)*

> "Xiaozhi, stand up."

*(it stands. Hold. Let it sit there for a beat.)*

> "That's it. That's the whole trick."

### [5:20–5:40] OUTRO
> "The firmware is open source, and I've got the full written build guide — parts list, every wire, the servo code, the power diagram — linked below.
>
> If you build one, tag me. I want to see it."

---

## Title options

Short / punchy:
- I Gave a Voice Assistant Four Legs
- This AI Dog Has No Remote
- $30 Voice Assistant. Now It Walks.

Search-friendly:
- Building a Voice-Controlled Robot Dog with ESP32-C6 and 4 Servos
- ESP32-C6 Robot Dog: Voice Commands, No Remote, Open Source

Curiosity-led:
- I Taught a $4 Chip to Walk
- No App. No Remote. Just My Voice.

---

## Description (YouTube / long-form)

```
I gave the Xiaozhi voice assistant four legs — four SG90 servos, one ESP32-C6,
and no remote anywhere. You talk, it moves.

Full build guide (parts, wiring, servo code, power diagram):
[LINK]

The part most builds get wrong is power — details at 4:10.

Chapters
0:00  Cold open
0:20  Intro
0:45  Demo
1:30  What's inside
2:30  How the voice pipeline works
3:20  Servos and pin choice
4:10  Power: the part everyone gets wrong
4:50  Final demo

Hardware
- ESP32-C6 (bread-compact-c6 board)
- 4x SG90 servo, 180-degree
- INMP441 I2S microphone
- MAX98357A amplifier + 4-ohm speaker
- SSD1306 128x64 OLED
- 5V 3A supply + 1000uF capacitor

Firmware: xiaozhi-esp32 (open source), board type bread-compact-c6
```

---

## Short caption (TikTok / Reels / Shorts / X)

```
Gave a voice assistant four legs. No remote, no app — I just talk to it.

The 1000µF capacitor is doing more work than any line of code.
Full build guide in the description.
```

---

## Tags

`#ESP32 #Robotics #DIY #VoiceAssistant #Servo #OpenSource #IoT #Maker #EmbeddedSystems #Xiaozhi`
