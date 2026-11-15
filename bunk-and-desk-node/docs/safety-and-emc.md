## 2. Power & Electrical Safety Requirements (REQ-SAFE)

| ID | Requirement Description | Target / Parameter | Verification Method | KiCad / DRC Link |
| :--- | :--- | :--- | :--- | :--- |
| **REQ-SAFE-01** | Direct universal AC mains input converted on-board to isolated low-voltage DC. | 230V AC 50Hz input to isolated 5V/3.3V output | Load regulation & thermal check | `hardware/sheet_power.kicad_sch` |
| **REQ-SAFE-02** | Over-current and transient surge protection on the AC mains entry. | 1A slow-blow PCB fuse + 10D471K MOV | Visual BOM & schematic ERC | `hardware/sheet_power.kicad_sch` |
| **REQ-SAFE-03** | Reinforced creepage and clearance between 230V AC primary and 3.3V/5V secondary circuits. | >= 5.0 mm clearance + 1.5 mm milled isolation slot | KiCad DRC (`Mains_HV` custom rule) | `hardware/step-and-study-hub.kicad_dru` |
| **REQ-SAFE-04** | Fully enclosed touch-safe housing suitable for a children's bedroom. | 3D-printed enclosure with strain-relief cable exits | Mechanical fit & STEP inspection | `enclosure/case_v1.step` |

## 3. EMC & EMI Requirements (REQ-EMC)

| ID | Requirement Description | Target / Parameter | Verification Method | KiCad / PCB Link |
| :--- | :--- | :--- | :--- | :--- |
| **REQ-EMC-01** | Conducted EMI suppression at the 230V AC mains input stage. | X2 safety capacitor + common-mode choke before AC-DC module | Schematic review & line noise check | `hardware/sheet_power.kicad_sch` |
| **REQ-EMC-02** | Uninterrupted low-impedance ground return path on the low-voltage section. | Solid `B.Cu` ground pour with edge stitching vias | PCB layout inspection | `hardware/step-and-study-hub.kicad_pcb` |
| **REQ-EMC-03** | Local power-supply decoupling on all active ICs and wireless modules. | 100 nF + 10 uF ceramic capacitors within 3 mm of VDD pins | PCB layout inspection | `hardware/step-and-study-hub.kicad_pcb` |
