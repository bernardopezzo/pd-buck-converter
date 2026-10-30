# USB-C PD Sink & Synchronous Buck Converter (CH224K + SY8303AIC)

![KiCad Version](https://img.shields.io/badge/KiCad-10.0-blue?logo=kicad)
![Hardware](https://img.shields.io/badge/Hardware-Open%20Source-green)
![Fabrication](https://img.shields.io/badge/JLCPCB-SMT%20Ready-orange)

An all-in-one compact hardware module combining a **USB Type-C Power Delivery (PD) Sink Controller** and a **3A Synchronous Step-Down (Buck) DC-DC Converter**. 

Designed in **KiCad 10** using standard JLCPCB SMT components for direct, low-cost assembly.

Please verify the design before using, it is not finished and was not tested.

---

## 📖 Overview & Architecture

Modern USB-C chargers (USB-PD / QC adapters) can supply up to 20V at high currents, but most embedded microcontrollers, sensors, and development boards require lower voltages (such as 3.3V, 5V, or 12V).

This board integrates two stages onto a single PCB:
1. **USB-PD Sink Trigger (WCH CH224K)**: Negotiates high-voltage power profiles (`5V`, `9V`, `12V`, `15V`, or `20V`) from any standard USB-C PD power supply using a 3-position onboard DIP switch.
2. **Synchronous Buck Converter (Silergy SY8303AIC)**: Steps down the negotiated input voltage to an adjustable regulated DC output capable of delivering up to **3A continuous current** with high efficiency and minimal thermal dissipation.

```
                     ┌──────────────────┐
                     │ USB-C Connector  │
                     │  (TYPE-C-31-M-12)│
                     └────────┬─────────┘
                              │ VBUS (5V - 20V)
             ┌────────────────┴────────────────┐
             │                                 │
             ▼                                 ▼
   ┌────────────────────┐            ┌────────────────────┐
   │    WCH CH224K      │            │  Silergy SY8303AIC │
   │ USB-PD Controller  │            │ 3A Step-Down Buck  │
   └─────────┬──────────┘            └─────────┬──────────┘
             │                                 │
     [3-Pos DIP Switch]                        ▼
     Configures Request             Adjustable Output VOUT
     (5V/9V/12V/15V/20V)               via Trimmer (R6)
```

---

## ⚡ Technical Specifications

| Parameter | Specification |
|---|---|
| **Input Connector** | 16-Pin USB Type-C (Receptacle, SMT) |
| **Supported PD Profiles** | 5V, 9V, 12V, 15V, 20V (DIP switch selectable) |
| **PD Controller IC** | WCH CH224K (ESSOP-10) |
| **Buck Converter IC** | Silergy SY8303AIC (TSOT-23-8) |
| **Converter Topology** | High-efficiency synchronous rectification buck |
| **Maximum Output Current** | 3.0 A continuous |
| **Switching Frequency** | Up to 2.5 MHz (compact inductor footprint) |
| **Output Voltage Regulation**| Adjustable via 100kΩ precision potentiometer (R6) |
| **Passives Footprints** | 0603 (Resistors/Capacitors), 0805 & 1206 (Bulk MLCC) |
| **Self-Contained Libraries** | Includes complete 3D models (`.step`, `.wrl`) and symbols |

---

## 🎛️ DIP Switch Configuration (CH224K Voltage Selection)

The onboard 3-position DIP switch (`U2`, `SMXMS-03K-TP`) interfaces directly with the `CFG1`, `CFG2`, and `CFG3` pins of the CH224K controller to request the desired USB-PD power profile (check before applying):

| Desired VBUS | SW1 (CFG1) | SW2 (CFG2) | SW3 (CFG3) | Notes |
|:---:|:---:|:---:|:---:|---|
| **5V** | OFF | OFF | OFF | Default USB standard safe profile |
| **9V** | ON | OFF | OFF | Common for fast charging |
| **12V** | ON | ON | OFF | Ideal for 12V LED strips, relays & routers |
| **15V** | ON | ON | ON | Specialized audio/DSP supply |
| **20V** | OFF | ON | ON | Maximum PD power profile (up to 60W–100W) |

> **Note:** If the connected USB-C power supply does not support the requested profile, the CH224K automatically falls back to the nearest lower voltage profile supported by the adapter.

---

## 📋 Bill of Materials (BOM)

All components are standard parts readily available in the **JLCPCB / LCSC** SMT catalog:

| Designator | Qty | Value / Component | Package / Footprint | LCSC Part # | Description |
|---|---|---|---|---|---|
| **U1** | 1 | CH224K | ESSOP-10-150mil-1mm | `C970725` | USB-PD 3.0 sink power receiver controller |
| **U3** | 1 | SY8303AIC | TSOT-23-8 | `C97054` | 4.5V–40V, 3A synchronous step-down buck converter |
| **U2** | 1 | DIP-Switch (3-Pos) | SMXMS-03K-TP (P=1.27mm) | `C5439770` | 3-position SMD DIP switch |
| **USBC1** | 1 | USB Type-C Receptacle | TYPE-C-31-M-12 (16-Pin) | `C165948` | USB 2.0 / PD 20V 5A right-angle receptacle |
| **L1** | 1 | 3.3 µH | SLO0530H3R3MTT (5.4x5.2mm)| `C325963` | 5.5A / 28mΩ molded power inductor |
| **R6** | 1 | 100 kΩ Potentiometer | ST-32EA (SMD Trimmer) | `C132894` | Precision variable potentiometer for output tuning |
| **C2, C3** | 2 | 10 µF | 1206 (X7R, 50V) | `C89632` | High-voltage input ceramic decoupling capacitors |
| **C4, C5** | 2 | 22 µF | 0805 (X5R, 25V) | `C129303` | Low-ESR output smoothing capacitors |
| **C1, C6** | 2 | 100 nF | 0603 (X7R, 50V) | `C14663` | High-frequency IC bypass decoupling capacitors |
| **R1, R3, R4, R5** | 4 | 10 kΩ | 0603 (±1%) | `C25804` | Bias and pull-up configuration resistors |
| **R7, R8** | 2 | 5.1 kΩ | 0603 (±1%) | `C23186` | USB Type-C CC1 / CC2 detection pull-downs |
| **R2** | 1 | 1 kΩ | 0603 (±1%) | `C21190` | Feedback loop divider base resistor |
| **R9** | 1 | 100 kΩ | 0603 (±1%) | `C25803` | Auxiliary feedback / pull-down resistor |

---

## 📁 Repository Structure

```
pd-buck-converter/
├── PD_Buck.kicad_pro       # KiCad 10 main project configuration
├── PD_Buck.kicad_sch       # Complete schematic with design annotations
├── PD_Buck.kicad_pcb       # Two-layer PCB layout with routing and power copper pours
├── sym-lib-table           # Portable symbol library table (${KIPRJMOD} relative paths)
├── fp-lib-table            # Portable footprint library table (${KIPRJMOD} relative paths)
├── JLCImport.kicad_sym     # Self-contained KiCad symbol library for all BOM parts
├── JLCImport.pretty/       # KiCad footprint files (.kicad_mod)
├── JLCImport.3dshapes/     # 3D models (.step and .wrl) for 3D viewer rendering
├── .gitignore              # Ignores lock files (*.lck), backups (*.bak), and autosaves
└── README.md               # Detailed documentation, BOM, and hardware specs
```

> **Zero Missing Libraries**: All schematic symbols, footprints, and 3D shapes are contained locally in the repository using KiCad's `${KIPRJMOD}` path variable. It opens cleanly out-of-the-box on any computer without missing symbol or footprint warnings.

---

## 🛠️ How to Open & Modify in KiCad

1. Clone or download this repository.
2. Launch **KiCad 10** (or later).
3. Select **File > Open Project** and navigate to `PD_Buck.kicad_pro`.
4. Open the Schematic Editor (`PD_Buck.kicad_sch`) or the PCB Editor (`PD_Buck.kicad_pcb`).
5. In the PCB Editor, press `Alt + 3` to open the 3D Viewer and view the fully rendered 3D board assembly.

---

## 🏭 Manufacturing & Fabrication (JLCPCB Guide)

To order assembled boards from JLCPCB:
1. **Gerbers & Drill**:
   - In KiCad PCB Editor, click **File > Fabrication Outputs > Gerbers (.gbr)**.
   - Click **Plot**, then click **Generate Drill Files**.
   - Zip all resulting `.gbr` and `.drl` files together.
2. **BOM & CPL (Pick & Place)**:
   - Use KiCad's built-in export or the `kicad-jlcpcb-tools` plugin to generate `BOM.csv` and `CPL.csv` (positions).
   - All components in this project have pre-assigned LCSC part numbers matching the JLCPCB SMT parts library.