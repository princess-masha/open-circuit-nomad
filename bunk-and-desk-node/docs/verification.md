# Hardware Bring-Up & Verification Log

| Test ID | Req ID | Test Procedure | Expected Result | Status |
| :--- | :--- | :--- | :--- | :--- |
| **VER-01** | `REQ-SAFE-03` | Run KiCad DRC with `Mains_HV` 5.0 mm rule enabled. | 0 clearance or creepage violations | Pending |
| **VER-02** | `REQ-SAFE-01` | Power board via current-limited isolation transformer; measure `5V` and `3.3V` rails. | `5.0V ± 5%` and `3.3V ± 2%` with < 50 mV ripple | Pending |
| **VER-03** | `REQ-FUNC-01` | Boot board with `J_MODE` closed vs. open; check serial boot log. | Correctly selects `STAIR` and `DESK` profiles | Pending |
| **VER-04** | `REQ-FUNC-02` | Connect 30-pixel WS2812B strip and trigger `PIR_IN`. | Smooth warm-white fade-in without flicker | Pending |
