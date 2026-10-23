# Video Script — Xiaozhi AI Voice Assistant (English, for publishing)

Two lengths: **Short (~50s)** for TikTok / Reels / Shorts, **Full (~4–5 min)** for YouTube.
Everything in `[brackets]` is a note to you, not to be read aloud.

---

## Part 1 — Short version (~50 seconds)

### [0:00–0:05] HOOK
*(straight to camera, breadboard visible in frame)*

> "This is a voice assistant. The whole thing is on a breadboard, and the chip running it costs about four dollars."

### [0:05–0:14] DEMO
*(press the BOOT button, OLED lights up)*

> "One button to get on Wi-Fi — I scan the QR code with my phone, and it's online."

*(speak to it — let it answer in full)*

> "No app. No remote. It's just a microphone, a speaker, and a tiny screen for its face."

### [0:14–0:25] HOW IT WORKS
> "When I talk, an INMP441 microphone picks it up. The audio goes over Wi-Fi to the AI in the cloud — speech to text, the actual thinking, then text to speech. The reply comes back down and plays through this little amplifier and speaker.

> The screen shows what it's doing: listening, thinking, speaking."

### [0:25–0:38] THE BUILD
> "Everything runs on one ESP32-C6. That's a RISC-V chip with Wi-Fi 6, for about four dollars.

> Four parts on the breadboard: the microphone, the amplifier, the speaker, and the OLED screen. That's the entire bill of materials.

> And the firmware is open source — it's the Xiaozhi project. I'll link it below."

### [0:38–0:50] CLOSER + CTA
> "I've got the full written build guide — parts list, every wire, how to flash it — linked below.

> If you build one, tag me. I want to see your version."

---

## Part 2 — Full version (~4–5 minutes)

### [0:00–0:20] COLD OPEN — demo montage
*(no talking. Three quick cuts: press BOOT → screen comes alive → ask it something → it answers. Room sound only, no music.)*

### [0:20–0:45] INTRO
> "Voice assistants are usually sealed boxes. You can't see inside, you can't change anything, and when the company kills the servers, it's a brick.

> So I built one I could actually open.

> One four-dollar chip, a microphone, a speaker, and a tiny screen. Here's how it goes together."

### [0:45–1:30] DEMO
*(clean single take, board centered, you off-camera)*

> "It starts offline. I hold the BOOT button, and the screen says it's waiting for Wi-Fi.

> I scan the QR code with my phone, pick the network, and it's online. From now on it remembers — every boot after this just connects by itself.

> Now I just talk to it. The screen changes with what it's doing — listening, thinking, speaking. That face is the entire user interface."

### [1:30–2:25] WHAT'S ACTUALLY IN IT
*(hold each part up to camera as you name it — B-roll over the table)*

> "It's four parts plus the board.

> An ESP32-C6 — RISC-V, Wi-Fi 6, about four dollars. That's the only computer here. Everything else is plugged into it.

> An INMP441 digital microphone. It outputs digital audio directly over I2S, so there's no analog hiss to fight with.

> A MAX98357 amplifier and a 4-ohm speaker for the voice.

> An SSD1306 OLED screen — 128 by 64, I2C, four pins. This is its face.

> And the board already has three buttons soldered on: volume down, volume up, and talk. I didn't wire a single one of those."

### [2:25–3:15] THE VOICE PIPELINE
*(diagram on screen: mic → C6 → Wi-Fi → cloud → amp → speaker, with OLED states branching off)*

> "Here's the path your voice takes.

> The microphone feeds the C6 over I2S — that's a digital audio bus, three wires: a clock, a word-select, and the data line.

> The audio goes up over Wi-Fi to the AI in the cloud. Speech to text, then the actual language model, then text to speech back into audio.

> The reply comes back down and goes straight out to the amplifier and speaker.

> Meanwhile the same events drive the screen — listening, thinking, speaking. One state machine, two outputs. I didn't build two systems."

### [3:15–4:00] WIRING — THE THREE THINGS PEOPLE GET WRONG
*(close-up of the breadboard, point at each as you say it)*

> "Three wiring mistakes I see all the time.

> First: the OLED. You want the four-pin I2C version, not the seven-pin SPI one. The seven-pin one plugs in and does absolutely nothing — it looks fine, it's just dark.

> Second: SDA and SCL are reversed on this board. SDA is GPIO 11, SCL is GPIO 10. Everyone assumes it's the other way around and then spends an hour debugging a perfectly good screen.

> Third: the amplifier's SD pin goes to 3.3 volts, not to a GPIO. It's a shutdown pin, and if you leave it floating the amplifier stays silent no matter what the code does.

> And one thing that isn't a bug: after you flash it, the screen stays black for about three seconds before it lights up. The display initializes late, on purpose. Give it a moment."

### [4:00–4:40] FLASHING
*(screen recording of the terminal or the Flash tool, whichever you used)*

> "There are two ways to get the firmware on.

> The developer way: clone the repo, run one build command, one flash command, done. That's what I use because I keep changing things.

> The no-command-line way: download the release, open Espressif's Flash Download Tool, pick your chip, load the merged binary at address zero, hit START. Same result, no terminal.

> One C6-specific gotcha: this board doesn't auto-enter download mode. You hold BOOT, tap RST, let go of RST, then let go of BOOT. If flashing hangs at 'connecting', that's why."

### [4:40–5:20] FINAL DEMO + OUTRO
> "So: one four-dollar chip, four parts, one open-source firmware, and a voice that answers.

> The full written guide — parts list, every wire, the flashing steps, the three wiring mistakes — is linked below.

> If you build one, tag me. I want to see it."

---

## Title options

Short / punchy:
- I Built a Voice Assistant on a Breadboard
- This Voice Assistant Costs $4
- No App. No Cloud Bill. Just a $4 Chip.

Search-friendly:
- Building a Xiaozhi AI Voice Assistant with ESP32-C6 (Full Build Guide)
- ESP32-C6 Voice Assistant: Mic, Speaker, OLED, Open Source

Curiosity-led:
- The $4 Chip That Talks Back
- I Unboxed a Voice Assistant and Built It Myself

---

## Description (YouTube / long-form)

```
A voice assistant you can actually open: ESP32-C6, INMP441 mic, MAX98357
amplifier, SSD1306 OLED face, and open-source firmware. No app, no remote.

Full build guide (parts, wiring, flashing, the three wiring mistakes):
[LINK]

Chapters
0:00  Cold open
0:20  Intro
0:45  Demo — provisioning and conversation
1:30  What's inside
2:25  How the voice pipeline works
3:15  Wiring: the three things people get wrong
4:00  Flashing — two routes
4:40  Final demo

Hardware
- ESP32-C6 (bread-compact-c6 board)
- INMP441 I2S microphone
- MAX98357A amplifier + 4-ohm speaker
- SSD1306 128x64 OLED, 4-pin I2C

Firmware: xiaozhi-esp32 (open source), board type bread-compact-c6
```

---

## Short caption (TikTok / Reels / Shorts / X)

```
Built a voice assistant on a breadboard. $4 chip, open firmware, no app.

The screen staying black for 3 seconds after boot is normal — it initializes
late on purpose. I thought I'd broken mine.
Full build guide in the description.
```

---

## Tags

`#ESP32 #VoiceAssistant #DIY #OpenSource #IoT #Maker #EmbeddedSystems #RISC-V #Xiaozhi #Breadboard`
