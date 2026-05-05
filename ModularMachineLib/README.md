# ModularMachineLib
> **A reusable, object-oriented TwinCAT 3 library for industrial machine automation**

[![TwinCAT](https://img.shields.io/badge/TwinCAT-3.1.4024-blue)](https://www.beckhoff.com/twincat3)
[![IEC 61131-3](https://img.shields.io/badge/IEC%2061131--3-Structured%20Text-green)](https://en.wikipedia.org/wiki/IEC_61131-3)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

## Overview

ModularMachineLib is a professional TwinCAT 3 / Beckhoff PLC library built with full **object-oriented programming (OOP)** principles.
It provides reusable, well-tested function blocks for motor drives, conveyor systems, cutting units, and general machine control — with consistent state machines, error handling, and HMI interfaces.

---

## Architecture

```
ModularMachineLib/
├── DUTs/                    # Enumerations, Structures, Global Variable Lists
│   ├── E_MotorState         # Motor states (Idle, Starting, Running, Stopping, Error)
│   ├── E_ConveyorState      # Conveyor states
│   ├── E_MachineState       # Generic machine states
│   ├── E_Severity           # Error severity levels
│   ├── ST_MotorConfig       # Motor configuration structure
│   ├── ST_ConveyorConfig    # Conveyor configuration structure
│   ├── ST_CuttingConfig     # Cutting unit configuration structure
│   ├── ST_ErrorInfo         # Error record structure
│   └── GVL_ErrorCodes       # Centralised error code constants
│
├── Interfaces/              # IEC 61131-3 OOP Interfaces
│   ├── I_StateMachine       # Base: Enable, Disable, Reset, GetState, HasError
│   ├── I_Motor              # Motor: Start, Stop, GetSpeed, IsRunning, IsStopped
│   ├── I_Conveyor           # Conveyor: Start, Stop, SetSpeed, IsBlocked, ProductPresent
│   └── I_ErrorHandler       # ErrorHandler: AddError, ClearErrors, HasErrors, GetError
│
├── POUs/
│   ├── Base/
│   │   ├── FB_Base          # Abstract base: enable/disable, error management, reset
│   │   └── FB_StateMachine  # State machine base: transitions, timing, state events
│   ├── Drive/
│   │   └── FB_Motor         # Motor control with full state machine + timeout detection
│   ├── Conveyor/
│   │   └── FB_Conveyor      # Conveyor belt with blocked detection + interlocking
│   ├── Machine/
│   │   ├── FB_MachineBase   # Top-level coordinator: E-Stop, auto cycle, error mgmt
│   │   └── FB_ErrorHandler  # 32-entry ring buffer error log
│   └── Cutting/
│       └── FB_CuttingUnit   # Rotary/linear blade cutting unit with service counter
│
└── Examples/
    ├── PRG_MotorDemo        # Two independent motors with I/O mapping
    └── PRG_ConveyorDemo     # 3-station line: Infeed → Cutter → Outfeed
```

---

## Key Features

- ✅ **Full OOP** — Interfaces, EXTENDS (inheritance), IMPLEMENTS, Methods, Properties
- ✅ **Consistent state machines** — Every FB uses E_MachineState with entry/exit events
- ✅ **Timeout detection** — Every start/stop/cycle operation has configurable timeouts
- ✅ **Centralised error handling** — FB_ErrorHandler with 32-entry ring buffer
- ✅ **Parametrisable** — All timing and speed values in config structs (no magic numbers)
- ✅ **Interlocking** — FB_Conveyor supports downstream blocking and product sensors
- ✅ **Service tracking** — FB_CuttingUnit counts cycles and triggers service alerts
- ✅ **HMI-ready** — All states and errors exposed via clean outputs and methods

---

## Getting Started

### Import into TwinCAT 3

1. Open your TwinCAT project in TwinCAT XAE
2. Right-click on the PLC project → **Add Existing Item**
3. Select all `.TcPOU`, `.TcDUT`, `.TcGVL` files from this repository
4. Build the project (`F11`)

### Minimal Motor Example

```pascal
VAR
    fbMotor : FB_Motor;
    stCfg   : ST_MotorConfig := (
        sName         := 'ConveyorDrive',
        fNominalSpeed := 1500.0,
        tStartTimeout := T#10S,
        tStopTimeout  := T#10S
    );
END_VAR

// In cyclic task body:
fbMotor(
    bEnable       := bPanelEnable,
    bReset        := bPanelReset,
    bStart        := bStartCmd,
    bStop         := bStopCmd,
    fSetSpeed     := 1500.0,
    bMotorRun     := bDriveFeedback,    // from drive
    fActSpeed     := fDriveActSpeed,    // from drive
    bOvertemp     := bTempSwitch,
    bDriveError   := bDriveFault,
    stConfig      := stCfg,
    // outputs
    bRunCmd       => bDriveRunOutput,
    fSpeedCmd     => fDriveSpeedRef
);

// Check status
IF fbMotor.bRunning THEN
    // Motor is at setpoint speed
END_IF
IF fbMotor.bError THEN
    // fbMotor.nErrorCode contains the error (see GVL_ErrorCodes)
    // fbMotor.sErrorMsg contains the description
END_IF
```

---

## Error Codes

All error codes are defined in `GVL_ErrorCodes`:

| Range | Area |
|-------|------|
| 0 | No error |
| 1000–1999 | Motor / Drive errors |
| 2000–2999 | Conveyor errors |
| 3000–3999 | Cutting unit errors |
| 9000–9999 | Machine-level / E-Stop |

---

## Requirements

- TwinCAT 3.1 Build 4024 or later
- Structured Text (ST) PLC language
- For `FB_Drive` (not in this lib): TF5000 NC/PTP or TC3 Motion

---

## Author

**Kausik Burra** — Automation & SPS Consultant  
[LinkedIn](https://www.linkedin.com/in/kausik-burra) | Ingolstadt, Bavaria  
Available for freelance TwinCAT 3 / Beckhoff projects (Remote + up to 50km on-site)

---

## License

MIT License — free to use in commercial projects with attribution.
