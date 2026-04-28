# CoppeliaSim Pick-and-Place: Path Planning 

A CoppeliaSim-based robotic pick-and-place simulation using offline path planning with Ferguson and Bézier curves to coordinate a UR3 manipulator with a vacuum gripper and conveyor belt.

---

## Overview

This project implements **two sequential pick-and-place tasks** where the robot:

- Reads pre-planned Cartesian waypoints from CSV files (paths 1–4)
- Executes smooth trajectories via IK-based motion (`moveToPose_viaIK`)
- Activates/releases a vacuum gripper to pick and place objects
- Avoids interference with the KUKA robot's workspace throughout execution
- Stops the simulation automatically after completing both cycles

All motion is driven by a single Lua threaded script — no manual teach-pendant input during execution.

---

## Demo

'coppelia-path-planning-pick-and-place.mp4' — full simulation run of both pick-and-place tasks.

---

## Project Structure

```
├── README.md
├── Coppelia_path_planning_scene.ttt              # CoppeliaSim scene file
├── path1.csv                                     # Approach to first pick pose
├── path2.csv                                     # First pick to first place pose
├── path3.csv                                     # First place to second pick pose
├── path4.csv                                     # Second pick to second place pose
├── curves-template.xlsx			  # Explaination on how curves calculate
└── Lab3.mp4                                      # Simulation execution video
```

---

## Requirements

**Software**

- CoppeliaSim 4.10 

---


## Quick Setup

### 1. Load the Scene

1. Open CoppeliaSim
2. **File → Open → `.ttt file`**
3. Scene loads with:
   - UR3 manipulator with vacuum gripper end-effector
   - Conveyor belt with objects to pick
   - KUKA robot in shared workspace (must not be interfered with)

### 2. Place CSV Files

Ensure all four path CSV files are accessible at the path referenced in your script (e.g., `D:/Lab/path1.csv`). Update the file paths in the Lua script if your directory differs.

### 3. Run the Simulation

1. Locate the threaded script attached to the robot in the Scene tree (left panel)
2. Click **Play (▶)** to start simulation
3. The robot executes both pick-and-place cycles automatically

---

## Script

```lua
function sysCall_thread()
    local i = 1
    waitForSensor()

    if i == 1 then
        positions = readFile("D:/Lab/path1.csv")
        for index, points in ipairs(positions) do
            moveToPose_viaIK(ikMaxVel, ikMaxAccel, ikMaxJerk, points, data)
        end
        activateVacuum()
        sim.wait(0.5)
        i = i + 1
    end

    if i == 2 then
        positions = readFile("D:/Lab/path2.csv")
        for index, points in ipairs(positions) do
            moveToPose_viaIK(ikMaxVel, ikMaxAccel, ikMaxJerk, points, data)
        end
        releaseVacuum()
        sim.wait(0.5)
        i = i + 1
    end

    if i == 3 then
        positions = readFile("D:/Lab/path3.csv")
        for index, points in ipairs(positions) do
            moveToPose_viaIK(ikMaxVel, ikMaxAccel, ikMaxJerk, points, data)
        end
        activateVacuum()
        sim.wait(0.5)
        i = i + 1
    end

    if i == 4 then
        positions = readFile("D:/Lab/path4.csv")
        for index, points in ipairs(positions) do
            moveToPose_viaIK(ikMaxVel, ikMaxAccel, ikMaxJerk, points, data)
        end
        releaseVacuum()
        moveToPose_viaIK(ikMaxVel, ikMaxAccel, ikMaxJerk, pose, data)
    end

    sim.stopSimulation()
end
```

---

## References

- [CoppeliaSim Documentation](https://www.coppeliarobotics.com/helpFiles/)
- [Lua Reference Manual](https://www.lua.org/manual/5.3/)
