# 🎵 JonZON-X AY-3-8910 Sound Expansion for ZX81

JonZON-X is a modern recreation of the ZON-X-81 Sound Box by Bi-Pak, designed for the ZX81. It uses the AY-3-8910 Programmable Sound Generator (PSG) and supports stereo line-out, optional multi-PSG expansion, and historically accurate address decoding.

This project prioritizes:
- ✅ Robust signal integrity
- ✅ Accurate control logic
- ✅ Modular expansion
- ✅ Historical preservation

---

## 🎯 Goals

- Recreate the original ZON-X-81 using AY-3-8910 (not AY-3-8912)
- Provide stereo line-out instead of onboard speaker
- Divide CPU clock as in the original design
- Support optional second and third PSGs via A5 and A6
- Document logic blocks and truth tables that match the actual schematic

---

## 🧠 Control Signal Logic

JonZON-X uses modern 74HC-series gates to derive control signals for the AY-3-8910. The address decoding is based on:

- **A0–A4**: Select the first PSG
- **A5**: Optional second PSG (replaces A4)
- **A6**: Optional third PSG (replaces A4)
- **A7**: Used in BC1 logic
- **/IOREQ**, **/WR**, **/RD**: Control timing

### Signal Derivation

| Signal | Logic Expression | Gates Used |
|--------|------------------|------------|
| **BDIR** | Derived from /IOREQ and /WR  
→ Routed through 74HC02 (inversion), 74HC08 (AND), and 74HC11 (3-input AND) | 74HC02, 74HC08, 74HC11 |
| **BC1** | Derived from /IOREQ, /WR, A2, A7, /RD  
→ Routed through 74HC02, 74HC08, and 74HC11 | 74HC02, 74HC08, 74HC11 |

There is **no AYSEL signal**—address selection is handled directly via A0–A4 (or A5/A6 for additional PSGs).

---

## 📊 Truth Table

| A0–A4 | A5/A6 | A2 | A7 | /IOREQ | /WR | /RD | BDIR | BC1 | Operation |
|------|-------|----|----|--------|-----|-----|------|-----|-----------|
| PSG 1 selected | —     | 1  | 1  | 0      | 0   | 1   | 1    | 1   | Write to register |
| PSG 1 selected | —     | 1  | 1  | 0      | 0   | 0   | 1    | 0   | Write to PSG |
| PSG 1 selected | —     | 1  | 1  | 0      | 1   | 0   | 0    | 1   | Read from PSG |
| PSG 2 selected | A5=1  | 1  | 1  | 0      | —   | —   | —    | —   | Optional second PSG |
| PSG 3 selected | A6=1  | 1  | 1  | 0      | —   | —   | —    | —   | Optional third PSG |
| Not selected   | —     | —  | —  | —      | —   | —   | 0    | 0   | Inactive |

---

## 🖼️ Visual Decode Logic

![AY Control Logic](Images/PSG%20Selection%20Logic.png)

This schematic snippet shows how BDIR and BC1 are generated using 74HC02, 74HC08, and 74HC11 gates. Address selection is handled via A0–A4 (or A5/A6 for additional PSGs).

---

## 🎚️ DIP Switch Mapping for PSG Selection

JonZON-X supports up to three AY-3-8910 PSGs using configurable address lines via DIP switches. The DIP switches control A4, A5, and A6 to select which PSG is active.

| PSG   | A4 | A5 | A6 | Address (Binary) | Address (Decimal) |
|-------|----|----|----|------------------|-------------------|
| PSG1  | 1  | 0  | 0  | `11111111`       | 255               |
| PSG2  | 0  | 1  | 0  | `11101111`       | 239               |
| PSG3  | 0  | 0  | 1  | `11011111`       | 223               |

- **A4, A5, A6** are mutually exclusive: only one should be active at a time.
- These bits are part of the PSG register select address.
- DIP switches override the default A4–A6 lines from the ZX81.

This mapping allows users to select which PSG responds to register writes, enabling multi-chip expansion and stereo mixing.

---

## 📚 Historical References

- [ZON-X-81 Archive (1982 Bi-Pak)](https://archive.org/details/ZON_X-81_1982_Bi-Pak_GB)
- [RevSpace ZX81 PSG Expansion](https://revspace.nl/Zon_ZX-81_Programmable_Sound_Generator_expansion_for_ZX81PLUS35_with_working_SD-Card)
- [Mahjongg2 ZON-Compatible PSG](https://github.com/mahjongg2/ZON-compatible-PSG-with-SD-interface-for-ZX81plus35-ZX81)
- [Micromusic Article](https://www.muzines.co.uk/articles/micromusic/4456)
- [ZX8081 Clone Log](https://hackaday.io/project/184361-zx8081-homebrew-clone/log/203915-my-project-log-jan-mar2021)

---

## 🛠️ Future Plans

If the initial revision works, future additions may include:
- RAM and ROM expansion
- Joystick port
- Enhanced stereo mixing

---

### 🧪 Test 1: Test Routine

```basic
10 REM ZONTEST1
20 LET A=16514
30 POKE A,62
40 LET A=A+1
50 POKE A,0
60 LET A=A+1
70 POKE A,211
80 LET A=A+1
90 POKE A,0
100 LET A=A+1
110 POKE A,201
120 POKE 16515,7
130 POKE 16516,255
140 RAND USR 16514
150 POKE 16515,8
160 POKE 16516,191
170 RAND USR 16514
180 POKE 16515,13
190 POKE 16516,9
200 RAND USR 16514
210 GOTO 210
```
- 🔊 ZONTEST1 – Basic Tone Setup: [zontest1.p](https://github.com/jgratton72/JonZON-X/blob/master/Tests/zontest1.p)

### 🧪 Test 2: Envelope & Stereo Test

```basic
10 REM ZONTEST2
20 LET A=16514
30 POKE A,62
40 LET A=A+1
50 POKE A,0
60 LET A=A+1
70 POKE A,211
80 LET A=A+1
90 POKE A,0
100 LET A=A+1
110 POKE A,201
120 POKE 16515,7
130 POKE 16516,255
140 RAND USR 16514
150 POKE 16515,8
160 POKE 16516,191
170 RAND USR 16514
180 POKE 16515,13
190 POKE 16516,9
200 RAND USR 16514
210 LET E=0
220 POKE 16515,13
230 POKE 16516,E
240 RAND USR 16514
250 LET I=0
260 LET I=I+1
270 IF I<1000 THEN GOTO 260
280 LET E=E+1
290 IF E<16 THEN GOTO 220
300 GOTO 210
```
- 🎛️ ZONTEST2 – Envelope & Stereo: [zontest2.p](https://github.com/jgratton72/JonZON-X/blob/master/Tests/zontest2.p)

### 🧪 Test 3: Envelope Period Sweep

```basic
10 REM ZONTEST3
20 LET A=16514
30 POKE A,62
40 LET A=A+1
50 POKE A,0
60 LET A=A+1
70 POKE A,211
80 LET A=A+1
90 POKE A,0
100 LET A=A+1
110 POKE A,201
120 POKE 16515,7
130 POKE 16516,255
140 RAND USR 16514
150 POKE 16515,8
160 POKE 16516,191
170 RAND USR 16514
180 POKE 16515,13
190 POKE 16516,9
200 RAND USR 16514
210 LET P=0
220 POKE 16515,11
230 POKE 16516,P
240 RAND USR 16514
250 POKE 16515,12
260 POKE 16516,0
270 RAND USR 16514
280 LET I=0
290 LET I=I+1
300 IF I<1000 THEN GOTO 290
310 LET P=P+16
320 IF P<256 THEN GOTO 220
330 GOTO 210
```
- 📈 ZONTEST3 – Envelope Sweep: [zontest3.p](https://github.com/jgratton72/JonZON-X/blob/master/Tests/zontest3.p)

### 🧪 Test 4: DIP-Selectable PSG Verification

```basic
10 REM ZONTEST4
20 LET A=16514
30 POKE A,62
40 LET A=A+1
50 POKE A,0
60 LET A=A+1
70 POKE A,211
80 LET A=A+1
90 POKE A,0
100 LET A=A+1
110 POKE A,201
120 POKE 16515,8
130 POKE 16516,255
140 RAND USR 16514
150 LET I=0
160 LET I=I+1
170 IF I<500 THEN GOTO 160
180 POKE 16515,8
190 POKE 16516,239
200 RAND USR 16514
210 LET I=0
220 LET I=I+1
230 IF I<500 THEN GOTO 220
240 POKE 16515,8
250 POKE 16516,223
260 RAND USR 16514
270 LET I=0
280 LET I=I+1
290 IF I<500 THEN GOTO 280
300 GOTO 120
```
- 🎚️ ZONTEST4 – DIP-Selectable PSGs: [zontest4.p](https://github.com/jgratton72/JonZON-X/blob/master/Tests/zontest4.p)

---

## 📅 Revision History

### 🔧 Rev 1.0 Testing Note — Stereo Jack Adaptation
- **Socket Type:** 3-pin stereo jacks used in place of shunted 5-pin variants.
- **Pad Bridging:** Inner pads manually jumped to outer signal pads; ground positioned at center front.
- **Trade-off:** Left and Right channels will be reversed — acceptable for initial validation.
- **Rationale:** Simplifies early testing without requiring shunt headers. Prioritizes proof-of-function over stereo fidelity

### 🔄 Rev 1.1 — Audio Jack Correction
- **Issue:** Left and Right channels were misrouted via Shunts A and B.
- **Fix:** Channel A and B were reversed in the schematic to compensate for the physical swap.
- **Impact:** Stereo output now matches expected channel mapping. Updated KiCad files reflect this correction.


### 🔄 Rev 1.2 – Stereo Output Mapping Adjustment

- **Issue**: Legacy ZON-X-81 software frequently routes noise to Channel B, but JonZON-X previously mixed Channel C to both outputs (a common pattern in other PSG devices, including the ZX Spectrum 128K). This caused noise effects to be isolated to the right channel or lost in stereo playback.
- **Fix**: Remapped stereo output:
  - Channel A → Left
  - Channel B → Mixed (Left + Right)
  - Channel C → Right  
  This ensures Channel B (the legacy noise channel) is centered in stereo output.
- **Impact**: Improved compatibility with original ZON-X-81 programs and demos. Noise effects now play as intended across both channels, preserving the sonic balance of legacy software.

[![Legacy Noise Compliance Certified™](https://img.shields.io/badge/Noise-Certified-blue)]()

### 🔄 Rev 1.3 – Logic Footprint Correction
- **Issue**: 74HTC Logic IC footprints where set as 400mil wide not 300mil.
- **Fix**: Change footprints:
  - Corrected 74HCT logic IC footprints from 400mil to 300mil for proper socket fit.
  - Updated KiCad symbols and PCB layout to reflect accurate DIP sizing.
- **Impact**: Improves build reliability and simplifies future revisions.

### 🔄 Rev 1.4 – Dual PSG Support & Expansion-Ready Logic

- 🧩 **Added AY-3-8912 schematic symbol** to Rev 1.3 base, enabling direct support for legacy demos and stereo output
- 🧠 **Dual PSG compatibility**: Supports both AY-3-8910 and AY-3-8912 via shared footprint and modular decoding logic
- 🧰 **Decoding logic updated** to include `/RD` line—reserved for future expansion (e.g. SD card interfacing via Port A)
- 🛠️ **Maintains Rev 1.3 write logic** for AY-3-8910 while enabling seamless upgrade path to AY-3-8912
- 🎶 **Verified compatibility** with original ULA and both HCT and F-series logic—multi scroll and Viaduct demos pass cleanly with AY-3-8912
- ⚠️ **VLA81 compatibility issues noted**: Viaduct demo fails to run reliably due to timing sensitivity and address decoding quirks. Detaching A15 improves behavior in some cases but does not fully resolve playback issues. Original ULA remains the recommended option for stable demo performance.
- 📘 **README and onboarding overlays updated** to reflect dual-chip support and future expansion notes

[![Dual PSG Certified™](https://img.shields.io/badge/Dual%20PSG-Certified-blue)]()

> Rev 1.4 preserves the modular ethos of JonZON-X while enabling legacy fidelity and future-proofed decoding. Component back-mounting and through-hole edge connector support are planned for a future revision.

---

## 📝 Notes

This is my first KiCad project and GitHub repo. While I’ve used other EDA tools and GitHub before, this is a learning curve and a preservation effort.
