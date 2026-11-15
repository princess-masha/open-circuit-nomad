# System Requirements Specification: Bunk & Desk Node

**Project:** `bunk-and-desk-node`  
**Company / Entity:** Nomad (`open-circuit-nomad`)  
**Revision:** v0.1  

## 1. Use Cases & Functional Requirements (`REQ-FUNC`)

| ID | Use Case / Requirement | Acceptance Criteria | Verification Method | Linked Artifact |
| :--- | :--- | :--- | :--- | :--- |
| **REQ-FUNC-01** | **Unified Dual-Mode Hardware:** One common PCB supports both bunk-bed stair lighting and study desk automation. | Hardware mode selection via 2-pin jumper (`J_MODE` closed = Stair, open = Desk). | Bench test in both modes | `hardware/sheet_mcu_core.kicad_sch` |
| **REQ-FUNC-02** | **Night-Time Stair Guidance:** Smoothly illuminate bunk stairs when a child approaches at night. | Drive 5V WS2812B LED strip via 3.3V-to-5V logic level shifter; fade on/off over 1.5s. | Oscilloscope & LED strip test | `hardware/sheet_outputs.kicad_sch` |
| **REQ-FUNC-03** | **Automatic Motion Trigger:** Detect movement near bunk stairs without false triggers. | Support 3.3V PIR/mmWave sensor (`J_PIR`) with hardware RC noise filter. | Motion trigger latency test | `hardware/sheet_sensors.kicad_sch` |
| **REQ-FUNC-04** | **Study Desk Light & Break Chime:** Provide desk task lighting, ambient monitoring, and 45-min study break alerts. | Support external push button (`J_BTN`), piezo buzzer, and I2C sensors (`J_I2C`). | Functional button, buzzer & I2C scan | `hardware/sheet_sensors.kicad_sch` |
| **REQ-FUNC-05** | **Home Assistant & Bedtime Cue:** Allow wireless control and scheduled evening amber wind-down lighting. | 2.4 GHz Wi-Fi connectivity via ESP32 module with OTA firmware updates. | Home Assistant integration test | `firmware/` |

## 2. Electrical Safety Requirements (`REQ-SAFE`)

| ID | Requirement | Acceptance Criteria | Verification Method | Linked Artifact |
| :--- | :--- | :--- | :--- | :--- |
| **REQ-SAFE-01** | **Direct Mains Operation:** Operate directly from 230V AC 50Hz bedroom wiring without external wall adapters. | Integrated isolated AC-DC module (`HLK-PM01` / `HLK-5M05`) outputting 5V DC + 3.3V LDO. | Load regulation & thermal check | `hardware/sheet_power_hv.kicad_sch` |
| **REQ-SAFE-02** | **Primary Overcurrent & Surge Protection:** Protect against short circuits and Indian grid voltage spikes. | 1A/250V slow-blow PCB fuse (`F1`) + `10D471K` MOV (`RV1`) on AC input. | Schematic ERC & BOM inspection | `hardware/sheet_power_hv.kicad_sch` |
| **REQ-SAFE-03** | **Reinforced Primary-to-Secondary Isolation:** Prevent electric shock hazard between 230V AC and low-voltage touch circuits. | Minimum `>= 5.0 mm` creepage/clearance gap + `1.5 mm` milled isolation slot (`Edge.Cuts`). | KiCad DRC (`Mains_HV` custom rule) | `hardware/bunk-and-desk-node.kicad_pcb` |
| **REQ-SAFE-04** | **Child-Safe Enclosure:** Prevent accidental finger contact with live mains terminals. | Fully enclosed 3D-printed housing secured with M3 screws and cable strain relief. | Mechanical fit & STEP inspection | `enclosure/bunk-and-desk-node.step` |

## 3. EMC & EMI Requirements (`REQ-EMC`)

| ID | Requirement | Acceptance Criteria | Verification Method | Linked Artifact |
| :--- | :--- | :--- | :--- | :--- |
| **REQ-EMC-01** | **Conducted EMI Suppression:** Prevent switching supply noise from radiating onto house AC wiring. | `0.1 uF` X2 safety capacitor + `10 mH` common-mode choke before AC-DC module. | Schematic & layout review | `hardware/sheet_power_hv.kicad_sch` |
| **REQ-EMC-02** | **Low-Impedance Ground Return:** Minimize radiated emissions on the low-voltage digital section. | Unbroken `B.Cu` ground plane (`GND`) under secondary side with perimeter stitching vias. | PCB layout inspection | `hardware/bunk-and-desk-node.kicad_pcb` |
| **REQ-EMC-03** | **Local Power Decoupling & Data Damping:** Prevent high-frequency ringing on MCU and LED lines. | `100 nF + 10 uF` ceramic caps within 3 mm of VDD pins; `330 ohm` series resistor on LED data. | PCB layout & waveform check | `hardware/sheet_mcu_core.kicad_sch` |
