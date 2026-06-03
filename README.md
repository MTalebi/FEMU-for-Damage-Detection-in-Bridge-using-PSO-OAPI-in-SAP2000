# FEMU for Damage Detection in Bridge Structures using PSO and SAP2000 OAPI

This repository contains MATLAB scripts that update a SAP2000 bridge model by matching model acceleration to measured acceleration.

The workflow uses particle swarm optimization (PSO) to find stiffness reduction factors for beam groups. These factors are applied through SAP2000 OAPI, the model is analyzed, and the mismatch between measured and simulated acceleration is minimized.

## What is in this repository

- `Main_SteelBridgeDDTH_Model_Updating.m`  
  Main script. Loads files, builds optimization groups, runs PSO, and plots measured versus simulated acceleration.
- `Objective_Fun.m`  
  Objective function called by PSO. Updates group stiffness modifiers, runs analysis, reads acceleration results, and returns the error value.
- `Bridge Example under Moving Load in SAP2000/`  
  Example SAP2000 model and measured acceleration data.

## Main features in the current code

- Reads a SAP2000 `.sdb` model selected by the user.
- Reads measured acceleration files from the selected folder.
- Uses frame auto-select section names to create optimization groups.
- Optimizes one beam stiffness modifier per group with lower and upper bounds.
- Runs direct history analysis for one target load case (`RHA Moveing Load`).
- Compares measured and simulated Z-direction acceleration at sensor points.
- Reports final error and plots measured versus simulated responses.
- Saves a new model file with prefix `Identified_` in the same folder as the input model.

## Requirements

- MATLAB with Global Optimization Toolbox (`particleswarm`).
- SAP2000 v23 with OAPI available.
- SAP2000 API DLL path and executable path set in the script:
  - `APIDLLPath = "C:\Program Files\Computers and Structures\SAP2000 23\SAP2000v1.dll"`
  - `ProgramPath = "C:\Program Files\Computers and Structures\SAP2000 23\SAP2000.exe"`

## Input expectations

- A SAP2000 model file (`.sdb`).
- A folder with measured acceleration text files named as:
  - `Point<ID>.txt` (example: `Point3.txt`)
- Each measured file must contain two columns:
  - column 1: time
  - column 2: acceleration
- Sensor joints in SAP2000 must be assigned to group `Sensor_Points`.

## How to run

1. Open the model in SAP2000 so MATLAB can attach to the running SAP2000 instance.
2. Run `Main_SteelBridgeDDTH_Model_Updating.m` in MATLAB.
3. Select:
   - the `.sdb` model file
   - any `.txt` measurement file from the measurement folder
4. The script then:
   - creates frame groups by auto-select section assignment
   - runs PSO with bounds `0.70` to `1.01`
   - updates beam modifiers and runs analysis repeatedly
   - calculates mismatch cost from acceleration responses
   - plots measured and simulated acceleration for each sensor point

## Current optimization settings in code

- Max iterations: `5`
- Swarm size: `5`
- Initial swarm: all ones
- Cost metric: average norm error between measured and simulated acceleration after interpolation to model time steps

## Notes

- The load case name is fixed in code as `RHA Moveing Load`.
- The script turns off all run flags, then enables `RHA Moveing Load` and `MODAL`.
- Units are set to `kgf_cm_C`.

## Citation

If you use this work, please cite:

Talebi-Kalaleh, Mohammad (2022). FEMU for Damage Detection in Bridge using PSO and OAPI in SAP2000. GitHub repository: https://github.com/MTalebi/FEMU-for-Damage-Detection-in-Bridge-using-PSO-OAPI-in-SAP2000

## Contact

Mohammad Talebi-Kalaleh  
talebika@ualberta.ca
