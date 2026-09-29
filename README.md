![picokit-49-fleet](https://raw.githubusercontent.com/mytechnotalent/picokit-49-fleet/main/picokit-49-fleet.png)

<br>

## FREE Reverse Engineering Self-Study Course [HERE](https://github.com/mytechnotalent/reverse-engineering)
## FREE Embedded Hacking Course [HERE](https://github.com/mytechnotalent/Embedded-Hacking)

<br>

# PICOKIT-49 FLEET

### Fleet and Config Table with Multi-Node Gateway
#### Lesson 49 of the Picokit Series

<br>

***
**LEGAL DISCLAIMER:**
The information, tools, and code provided in this repository and course are strictly for educational, research, and defensive purposes only.

You are explicitly prohibited from using any materials contained herein to access, test, modify, or exploit any device, network, or system that you do not own 100% or for which you do not have explicit, documented, and legally binding authorization to interact with.

By using this repository and course, you acknowledge and agree that:

1. Any illegal, unauthorized, or malicious use of this information is solely your responsibility.
2. The author(s) and contributor(s) of this repository and course shall not be held liable for any damages, legal repercussions, criminal charges, or unauthorized actions resulting from the use, misuse, or abuse of the contents herein.
3. You will comply with all applicable local, state, national, and international laws regarding cybersecurity and computer fraud.

**IF YOU DO NOT AGREE WITH THESE TERMS, DO NOT USE THIS REPOSITORY AND COURSE.**
***

<br>
<br>

## Overview

The fleet capstone. The node carries a fleet and config table and walks it as
it runs, so every heartbeat reports the active fleet identifier. The gateway
tracks several node identifiers at once and keeps the newest frame for each
node, which turns the dashboard into a small fleet view of the classroom.

<br>

## What it teaches

- A fleet and config table embedded in the node and walked at runtime.
- A heartbeat that reports the active fleet: `{"n":49,"s":<seq>,"f":<fleet>}`.
- A gateway that tracks several node identifiers and keeps the latest per node.
- The gateway side: receive, authenticate, reject, log, and display.

<br>

## Hardware

| Peripheral | Pico 2 pin | Role |
| --- | --- | --- |
| Red / Yellow / Green | GP16 / GP18 / GP17 | the chase |
| Onboard LED | GP25 | heartbeat, one blink per step |
| RYLR998 | GP8 TX / GP9 RX | fleet heartbeat |
| Debug Probe | SWCLK/SWDIO/GND, GP0/GP1 | SWD and the console |

<br>

## How it works

The node runs `monitor_step` in a loop. It advances the chase and the fleet
and config table every 2 seconds and every 5 seconds it seals
`{"n":49,"s":<seq>,"f":<fleet>}` with the field key and sends it over LoRa. The
gateway authenticates each frame, stores it, and its node table keeps the
newest frame for every node identifier it has seen.

<br>

## Build and flash

```bash
cd firmware
cmake -S . -B build -G Ninja -DPICO_BOARD=pico2 -DPICO_PLATFORM=rp2350-arm-s
cmake --build build
openocd -f interface/cmsis-dap.cfg -f target/rp2350.cfg \
  -c "program build/picokit_49_fleet.elf verify reset exit"
```

<br>

## Watch the node

Open the console at 115200 and reset:

```text
BOOT
I2C scan:
  no devices
=== PICOKIT-49 FLEET // LED CHASE + AUTHENTICATED HEARTBEAT ===
STEP 0 LED=RED seq=0 fleet=1
STEP 1 LED=YELLOW seq=1 fleet=2
STEP 2 LED=GREEN seq=2 fleet=3
RX from 0x0002, N bytes
```

<br>

## The gateway

```bash
cd gateway
python3 -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
python3 listen.py --port /dev/cu.usbserial-A50285BI --hub 0001 --network 18 --db gateway.db
```

It prints `OK node=49 rssi=...` per authenticated heartbeat. The terminal
dashboard `python3 tui.py --db gateway.db` and the web dashboard
`python3 web/app.py --db gateway.db` show the newest frame for every node in
the fleet.

<br>

## Verify

```bash
python3 .opencode/skill/embedded-c-standard/audit_c_standard.py
python3 .opencode/skill/embedded-python-standard/audit_python_standard.py
python3 .opencode/skill/iot-readme-standard/validate_readme.py
python3 .opencode/skill/iot-banner-standard/validate_banner.py
python3 scripts/run_tests.py
python3 scripts/check_coverage.py
```

<br>

# Next
[picokit-50-finale](https://github.com/mytechnotalent/picokit-50-finale)

<br>

# License
[MIT License](https://github.com/mytechnotalent/picokit-49-fleet/blob/main/LICENSE)
