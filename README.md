# 🎵 JonZON-X AY-3-8910 Sound Expansion for ZX81

This is my recreation of the **ZON-X-81 Sound Box** by Bi-Pak, adapted for modern components and improved documentation. It uses the **AY-3-8910** instead of the AY-3-8912 due to availability and cost. The output is a mixed stereo line-out rather than the original onboard speaker.

I’ve also divided the CPU clock as the original did, avoiding the need for a separate oscillator.

---

## 📚 Historical References

This project draws from multiple sources to ensure accuracy and compatibility:

- [ZON-X-81 Manual (1982)](https://archive.org/details/ZON_X-81_1982_Bi-Pak_GB)
- [RevSpace ZON-ZX81 PSG Expansion](https://revspace.nl/Zon_ZX-81_Programmable_Sound_Generator_expansion_for_ZX81PLUS35_with_working_SD-Card)
- [Mahjongg2’s ZON-Compatible PSG](https://github.com/mahjongg2/ZON-compatible-PSG-with-SD-interface-for-ZX81plus35-ZX81)
- [Micromusic Article](https://www.muzines.co.uk/articles/micromusic/4456)
- [ZX8081 Homebrew Clone](https://hackaday.io/project/184361-zx8081-homebrew-clone/log/203915-my-project-log-jan-mar2021)

---

## 🧠 Control Signal Logic

The AY-3-8910 requires three control signals: **BDIR**, **BC1**, and **AYSEL**. Here's how they are derived in JonZON-X:

### ✅ Signal Derivation

| Signal | Logic Expression | Gates Used |
|--------|------------------|------------|
| **AYSEL** | Not decoded dynamically  
→ A8 held high  
→ A9 held low | — |
| **BDIR** | /IOREQ and /WR combined via  
→ 74HC02 (inversion)  
→ 74HC08 (AND)  
→ 74HC11 (3-input AND) | 74HC02, 74HC08, 74HC11 |
| **BC1** | /IOREQ, /WR, A2, A7, /RD combined via  
→ 74HC02 (inversion)  
→ 74HC08 (AND)  
→ 74HC11 (3-input AND) | 74HC02, 74HC08, 74HC11 |

---

### 📊 Truth Table

| A8 | A9 | A2 | A7 | /IOREQ | /WR | /RD | BDIR | BC1 | Operation |
|----|----|----|----|--------|-----|-----|------|-----|-----------|
| 1  | 0  | 1  | 1  | 0      | 0   | 1   | 1    | 1   | Write to register |
| 1  | 0  | 1  | 1  | 0      | 0   | 0   | 1    | 0   | Write to PSG |
| 1  | 0  | 1  | 1  | 0      | 1   | 0   | 0    | 1   | Read from PSG |
| any| any| any| any| any    | any | any | 0    | 0   | Inactive |

---

## 🖼️ Visual Decode Logic

To help visualize the control signal derivation, here’s a schematic snippet showing how **/IOREQ**, **BDIR**, and **BC1** are shaped using **74HC02**, **74HC08**, and **74HC11** gates. **AYSEL** is statically constrained by tying **A8 high** and **A9 low**.

![AY Control Logic](Images/AY_Control_Logic.png)

This logic ensures compatibility with legacy ZON-X-81 software while simplifying address decoding and supporting future PSG expansion.

---

## 🛠️ Future Additions

If the initial revision works, future enhancements may include:

- RAM and ROM support
- Joystick port
- Second and third PSGs using A5 and A6

---

## 🧪 Development Notes

This is my first KiCad project and GitHub repo, so it’s a learning curve. I’ve used other EDA tools before, but this is my first full open-source release. Contributions and feedback are welcome!

