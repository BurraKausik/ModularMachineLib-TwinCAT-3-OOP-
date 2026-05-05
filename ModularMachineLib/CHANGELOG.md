# Changelog

## [1.0.0] - 2026-04-27
### Added
- FB_Base: abstract base class with error management and reset handling
- FB_StateMachine: state machine base with entry/exit event hooks
- FB_Motor: full motor state machine with timeout detection and I_Motor interface
- FB_Conveyor: conveyor belt with blocking detection, product sensor, I_Conveyor interface
- FB_CuttingUnit: cutting unit with homing, cycle control, service counter
- FB_MachineBase: top-level machine coordinator with E-Stop handling
- FB_ErrorHandler: 32-entry ring buffer error log with severity levels
- I_StateMachine, I_Motor, I_Conveyor, I_ErrorHandler interfaces
- ST_MotorConfig, ST_ConveyorConfig, ST_CuttingConfig, ST_ErrorInfo structures
- E_MotorState, E_ConveyorState, E_MachineState, E_Severity enumerations
- GVL_ErrorCodes: centralised error code constants
- PRG_MotorDemo: two-motor example with hardware I/O mapping
- PRG_ConveyorDemo: 3-station infeed/cutter/outfeed example
