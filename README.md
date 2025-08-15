# ZON-X Recreation
My attempt at recreating the ZON-X-81 Sound Box by Bit-Pak.

This would use the AY-3-8910 rather than the AY-3-8912 due to current costs.

it will have a mixed stereo line out rather than the inbuilt speaker of the origional.

I will also divide the CPU clock as the origional did to save a clock circuit.

Various sources have been used to create the starts of this project.

https://archive.org/details/ZON_X-81_1982_Bi-Pak_GB
https://revspace.nl/Zon_ZX-81_Programmable_Sound_Generator_expansion_for_ZX81PLUS35_with_working_SD-Card
https://github.com/mahjongg2/ZON-compatible-PSG-with-SD-interface-for-ZX81plus35-ZX81
https://www.muzines.co.uk/articles/micromusic/4456
https://www.youtube.com/watch?v=Kemn_Q3tHYs
https://hackaday.io/project/184361-zx8081-homebrew-clone/log/203915-my-project-log-jan-mar2021

If the initial revision works there may be additions to support, RAM, ROM and a Joystick Port as time permits.

This is my first attempt at a KiCad project and Github Repo, so a big learning curve, though I have used other EDA's and Github.

## 🔧 AY-3-8910 Control Logic

To interface the AY-3-8910 with the ZX81, we derive **BDIR** and **BC1** from address and control lines. The typical decode logic uses:

- `A2` and `A9` for port selection
- `/WR`, `/RD`, and `/IOREQ` for control timing

### 🧠 Logic Block

```
Inputs:
  A2, A9, /WR, /RD, /IOREQ

Derived signals:
  BC1  = NOT(A2) AND A9 AND /IOREQ AND /RD
  BDIR = NOT(A2) AND A9 AND /IOREQ AND /WR
```

This logic ensures that:

- **Write to register**: BDIR=1, BC1=1  
- **Write to PSG port**: BDIR=1, BC1=0  
- **Read from PSG port**: BDIR=0, BC1=1  
- **Inactive**: BDIR=0, BC1=0

---

## 📊 AY-3-8910 Truth Table

| Operation         | BDIR | BC1 | Description                    |
|------------------|------|-----|--------------------------------|
| Inactive         |  0   |  0  | No operation                   |
| Read from PSG    |  0   |  1  | Read from selected port        |
| Write to PSG     |  1   |  0  | Write to selected port         |
| Write to register|  1   |  1  | Select register to access next |

---

## 🧪 ZX81 Test Routine

This BASIC routine selects register 7 and writes a value to enable tone on Channel A:

```basic
10 OUT 127,7   : REM Select register 7
20 OUT 255,56  : REM Enable tone on Channel A (bitmask)
30 OUT 127,1   : REM Select register 1 (fine tune A)
40 OUT 255,200 : REM Set tone frequency
50 OUT 127,0   : REM Select register 0 (coarse tune A)
60 OUT 255,1   : REM Set coarse frequency
```

Assuming port 127 selects the register and port 255 writes data, this routine should produce audible output if the decode logic and PSG are wired correctly.
