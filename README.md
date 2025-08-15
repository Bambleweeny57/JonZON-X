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

![AY Control Logic](Images/AY_Control_Logic.png)

This schematic snippet shows how BDIR and BC1 are generated using 74HC02, 74HC08, and 74HC11 gates. Address selection is handled via A0–A4 (or A5/A6 for additional PSGs).

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
- Configurable PSG selection

---

## 🧪 Notes

This is my first KiCad project and GitHub repo. While I’ve used other EDA tools and GitHub before, this is a learning curve—and a preservation effort.

---

