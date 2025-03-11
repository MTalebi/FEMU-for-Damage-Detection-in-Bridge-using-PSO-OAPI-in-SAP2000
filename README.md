# FEMU for Damage Detection in Bridge Structures Using PSO-OAPI in SAP2000

**Author:**  
**Mohammad Talebi-Kalaleh**

**GitHub Repository:**  
[https://github.com/MTalebi/FEMU-for-Damage-Detection-in-Bridge-using-PSO-OAPI-in-SAP2000/tree/main](https://github.com/MTalebi/FEMU-for-Damage-Detection-in-Bridge-using-PSO-OAPI-in-SAP2000/tree/main)

**Publish Date:**  
July 7, 2022

---

## Overview

This documentation outlines a **Finite Element Model Updating (FEMU)** process designed for **damage detection** in bridge beams via **Particle Swarm Optimization (PSO)** coupled with the **SAP2000** Open Application Programming Interface (OAPI). The principal objective is to adjust the moment-of-inertia modifier (representing damage or stiffness reduction) for each beam group to match the **measured** dynamic response in the vertical (Z) direction.

Key steps include:

1. **Main Script**  
   - Initializes MATLAB and prompts the user for a `.sdb` SAP2000 model file and a folder containing `.txt` measurement files.  
   - Identifies and groups beams by their **auto-select** section names.  
   - Sets an upper and lower bound on the stiffness modifier (moment of inertia) for each beam group.  
   - Configures and runs the PSO to find the optimal reduction factors.  
   - Compares simulated vs. measured accelerations, outputting final error plots.

2. **Objective_Fun.m**  
   - Applies the PSO-proposed stiffness modifiers to each beam group in SAP2000.  
   - Runs the time-history analysis for the load case of interest.  
   - Retrieves the SAP2000 accelerations, compares them against measured data, and computes a cost (error) for the PSO to minimize.

---

## Main Script Explanation

1. **Setup and File Selection**  
   - The script starts by clearing the MATLAB workspace (`clear all; clc; close all;`) and prompting the user to select a `.sdb` file and a directory with measured acceleration `.txt` data.  
   - Defines the SAP2000 OAPI paths and names for the target **load case** (`TH_LoadCase_Name`).

2. **SAP2000 OAPI Objects**  
   - Loads the `.NET` assembly for SAP2000 and references the main `SapObject` and `SapModel`.  
   - Extracts objects like `FrameObj`, `Analyze`, `PropFrame`, `Group`, etc., for easy data and model manipulation.

3. **Model Preparation**  
   - Saves the model under a new name (prefixed `"Identified_"`), unlocks it, and sets consistent units.  
   - Reads all frame names from the model and organizes them by their assigned auto-select property.  
   - For each auto-select group, creates a matching SAP2000 group (to facilitate assigning global modifiers).

4. **Defining Variables for PSO**  
   - The **number of variables** equals the number of auto-select beam groups.  
   - Each variable represents a stiffness modifier (moment-of-inertia scaling) for one group.  
   - **Lower** (`lb`) and **upper** (`ub`) bounds reflect the permitted range of stiffness reduction or enhancement (e.g., `0.70` to `1.01`).

5. **PSO Configuration**  
   - An initial swarm matrix (`x0`) is created (ones-vector of length `nvars`).  
   - The script invokes MATLAB’s `particleswarm` solver, specifying iteration limits, swarm size, plotting functions, etc.  
   - In each iteration, the PSO calls the objective function, passing the proposed vector of stiffness modifiers.

6. **Analysis and Measured Data Comparison**  
   - When PSO updates the stiffness modifiers, the script re-runs SAP2000’s time-history analysis for the specified case (`RHA Moveing Load`).  
   - Retrieves simulated accelerations in the Z-direction for each sensor point and compares them with the measured data.  
   - The final cost (mismatch) is displayed, and acceleration time-history plots are produced to show the measured vs. simulated responses.

---

## Objective_Fun.m

This function is responsible for:

1. **Applying Stiffness Modifiers**  
   - For each beam group, sets the user-defined moment-of-inertia modifier (`SetModifiers` in SAP2000).  
   - This is done through the group assignment, allowing batch updates to all frames under that group.

2. **Running Analysis**  
   - Calls `Analyze.RunAnalysis` to execute the time-history load case.

3. **Error Calculation**  
   - Extracts accelerations at sensor points and aligns them with the measured data time vector.  
   - Uses a **norm-based** discrepancy measure for each sensor point and sums over all sensors/time steps.  
   - Returns the normalized error to the PSO for minimization.

---

## How to Cite

If this approach or code contributes to your research or project work, please cite it as follows:

> **Talebi-Kalaleh, Mohammad.** (2022). *FEMU for Damage Detection in Bridge using PSO and OAPI in SAP2000.* Published July 7, 2022. GitHub repository: [https://github.com/MTalebi/FEMU-for-Damage-Detection-in-Bridge-using-PSO-OAPI-in-SAP2000/tree/main](https://github.com/MTalebi/FEMU-for-Damage-Detection-in-Bridge-using-PSO-OAPI-in-SAP2000/tree/main)

---

## Notes and Extensions

1. **Prerequisites**  
   - SAP2000 v23 (or a compatible version) with OAPI enabled.  
   - MATLAB with the Global Optimization Toolbox (for Particle Swarm Optimization).  
   - A `.sdb` model and `.txt` measurement files arranged for the sensor points.

2. **Usage**  
   - Run the main script in MATLAB.  
   - When prompted, select the `.sdb` model and the folder containing `.txt` files.  
   - The script will generate a new model file (`Identified_<original name>`) and execute PSO to identify stiffness modifiers.  
   - Plotting routines will display measured vs. simulated accelerations in the Z-direction.

3. **Potential Enhancements**  
   - Incorporate other damage parameters, such as column or brace stiffness modifiers.  
   - Use multiple load cases or additional sensor directions (X/Y) for a more comprehensive identification.  
   - Adapt the cost function for multi-objective optimization (e.g., combining acceleration, displacement, or frequency data).

For further information or collaboration, contact:  
**talebika@ualberta.ca**
