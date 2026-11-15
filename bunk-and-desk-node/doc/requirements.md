# System Requirements Specification: Step & Study Hub

## 1. Functional Requirements (REQ-FUNC)

| ID | Requirement Description | Target / Parameter | Verification Method | KiCad / Firmware Link |
| :--- | :--- | :--- | :--- | :--- |
| **REQ-FUNC-01** | Dual-mode operation supporting bunk-bed stair lighting and study desk automation via one common PCB. | Configurable via firmware mode flag / jumper | Bench test in both modes | `firmware/config.yaml` |
| **REQ-FUNC-02** | Drive addressable RGB LED strips for gradual night-time stair illumination. | 5V WS2812B data output (level-shifted to 5V) | Oscilloscope & LED strip test | `hardware/sheet_outputs.kicad_sch` |
| **REQ-FUNC-03** | Support motion detection for automatic stair light activation. | 3.3V PIR / mmWave sensor header | Trigger & response latency test | `hardware/sheet_sensors.kicad_sch` |
| **REQ-FUNC-04** | Support ambient environmental sensing and user interaction at the study desk. | I2C sensor header + tactile button + piezo buzzer | Functional I2C scan & audio test | `hardware/sheet_sensors.kicad_sch` |