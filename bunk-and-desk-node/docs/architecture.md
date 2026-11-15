# System Architecture: Step & Study Hub

## 1. Hardware Block Diagram
The board is divided into an **AC Mains Primary Zone (230V AC)** and a **Reinforced-Isolated Secondary Zone (5V / 3.3V DC)** separated by a >= 5.0 mm creepage barrier and a 1.5 mm milled PCB slot.

```mermaid
flowchart LR
    subgraph Primary ["230V AC Primary Zone (High Voltage)"]
        AC["230V AC Input<br/>5.08mm Screw Terminal"] --> FUSE["1A Slow-Blow Fuse<br/>+ 10D471K MOV"]
        FUSE --> EMI["Conducted EMI Filter<br/>0.1uF X2 Cap + 10mH CM Choke"]
    end

    EMI ==>|"Reinforced Isolation<br/>(>= 5.0mm Gap + 1.5mm Slot)"| HLK["Hi-Link HLK-5M05 / PM01<br/>230V AC to 5V DC"]

    subgraph Secondary ["Low-Voltage Secondary Zone (3.3V / 5V Safe)"]
        HLK -->|"5V Rail"| LDO["AP2112K-3.3 LDO<br/>(5V to 3.3V)"]
        HLK -->|"5V Power"| LS["74AHCT1G125<br/>5V Level Shifter"]
        
        LDO -->|"3.3V Rail"| MCU["ESP32-C3-MINI-1<br/>Wi-Fi Microcontroller"]
        
        MODE["J_MODE Jumper<br/>(Stair vs Desk)"] -->|"GPIO4"| MCU
        MCU <-->|"I2C (GPIO8/9)"| SENS["J_I2C (Qwiic)<br/>BH1750 Lux + SHT40 Temp"]
        PIR["J_PIR (3-Pin JST)<br/>AM312 / mmWave Motion"] -->|"GPIO5 + RC Filter"| MCU
        BTN["J_BTN (2-Pin JST)<br/>Study Desk Button"] -->|"GPIO6 + RC Filter"| MCU
        MCU -->|"GPIO7"| BZR["Piezo Buzzer<br/>(NPN Driver)"]
        MCU -->|"GPIO10 (3.3V)"| LS
        LS -->|"5V Data + 330R"| LED["J_LED (3-Pin JST)<br/>WS2812B LED Strip"]
    end

    style Primary fill:#ffe6e6,stroke:#cc0000,stroke-width:2px
    style Secondary fill:#e6f3ff,stroke:#0066cc,stroke-width:2px
```


## 2. Power Budget Breakdown

| Rail | Source | Max Available | Typical Load | Consumers |
| :--- | :--- | :--- | :--- | :--- |
| **230V AC** | Mains Input | 1A (Fused) | ~25 mA | Hi-Link HLK-PM01 AC-DC Module |
| **5.0V DC** | HLK-PM01 | 600 mA (3W) | ~350 mA | WS2812B LEDs (brightness-capped in firmware), 3.3V LDO |
| **3.3V DC** | 3.3V LDO | 600 mA | ~120 mA | ESP32 Wi-Fi active, PIR/mmWave sensor, I2C sensors |

## 3. Dual-Mode Operating States

```mermaid
stateDiagram-v2
    [*] --> Boot: Power On (230V AC)
    Boot --> CheckMode: Read Mode Jumper / Flash Config
    
    CheckMode --> BunkStairMode: Mode = STAIR
    CheckMode --> StudyDeskMode: Mode = DESK

    state BunkStairMode {
        [*] --> IdleDark
        IdleDark --> FadeOn: PIR / mmWave Triggered
        FadeOn --> HoldLight: Soft Warm-White Step Glow
        HoldLight --> FadeOff: No Motion for 30s
        FadeOff --> IdleDark
    }

    state StudyDeskMode {
        [*] --> AmbientStandby
        AmbientStandby --> TaskLighting: Button Press / Home Assistant
        TaskLighting --> BreakReminder: 45-Min Study Timer Chime
        BreakReminder --> TaskLighting: Button Acknowledge
    }
```
## 4. Microcontroller Pin Allocation Table (ESP32-C3-MINI-1)
Locking in pin assignments before schematic capture prevents strapping-pin boot conflicts:

| ESP32-C3 Pin | Net Name | Sheet | Direction | Function & Notes |
| :--- | :--- | :--- | :--- | :--- |
| **GPIO4** | `MODE_SEL` | `sheet_mcu_core` | Input (Pull-up) | Closed to `GND` = Stair Mode; Open = Desk Mode |
| **GPIO5** | `PIR_IN` | `sheet_sensors` | Input | Motion sensor trigger (`1k` + `100nF` low-pass filter) |
| **GPIO6** | `BTN_IN` | `sheet_sensors` | Input (Pull-up) | Desk tactile switch (`10k` + `100nF` debounce) |
| **GPIO7** | `BUZZER_PWM` | `sheet_outputs` | Output | PWM tone output to MMBT2222A buzzer driver |
| **GPIO8** | `I2C_SDA` | `sheet_sensors` | Bidir | I2C Data (`4.7k` pull-up to `+3V3`) |
| **GPIO9** | `I2C_SCL` | `sheet_sensors` | Output | I2C Clock (`4.7k` pull-up to `+3V3`, also Boot strapping pin) |
| **GPIO10** | `LED_DATA_3V3` | `sheet_outputs` | Output | Feeds `74AHCT1G125` buffer -> `5V` WS2812B data |
| **EN / GPIO9** | `RESET` / `BOOT` | `sheet_mcu_core` | Input | On-board tactile buttons + 4-pin UART header for flashing |