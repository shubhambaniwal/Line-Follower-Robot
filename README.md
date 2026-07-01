# STM32 Line Follower Robot

An autonomous embedded Line Follower Robot (LFR) built using the STM32F103C8T6 Blue Pill microcontroller, dual IR reflectance sensors, and an L298N H-Bridge motor driver.

The robot continuously detects and follows a black line on a white surface using real-time sensor feedback and differential steering control.

---

# Project Overview

This project was developed as a complete embedded systems implementation involving:

* Sensor interfacing
* Motor driver integration
* Real-time control systems
* Embedded C firmware development
* GPIO configuration using STM32 HAL
* Hardware-software integration
* Power distribution design
* Real-time decision-making algorithms

The system was designed, assembled, programmed, debugged, and tested completely from scratch using the STM32CubeIDE ecosystem.

---

# Key Features

* Autonomous line following
* Real-time sensor-based navigation
* Differential steering mechanism
* STM32 ARM Cortex-M3 architecture
* GPIO-based motor control
* Low-latency embedded control loop
* Battery-powered mobile platform
* HAL-based firmware architecture
* Lightweight and modular embedded design

---

# Hardware Components

| Component                | Description          |
| ------------------------ | -------------------- |
| STM32F103C8T6 Blue Pill  | Main microcontroller |
| 2x TCRT5000 IR Sensors   | Line detection       |
| L298N H-Bridge Driver    | Motor control        |
| 2x DC Geared Motors      | Robot propulsion     |
| 7.4V Li-ion Battery Pack | Power supply         |
| SPST Power Switch        | Main power control   |
| 2WD Chassis              | Mechanical structure |

---

# System Architecture

The robot follows a layered embedded systems architecture:

```text
+----------------------+
| IR Sensor Modules    |
| (Sensing Layer)      |
+----------+-----------+
           |
           v
+----------------------+
| STM32F103 Blue Pill  |
| (Processing Layer)   |
+----------+-----------+
           |
           v
+----------------------+
| L298N Motor Driver   |
| (Actuation Layer)    |
+----------+-----------+
           |
           v
+----------------------+
| DC Geared Motors     |
+----------------------+
```

---

# Working Principle

The robot uses two IR reflectance sensors mounted at the front underside of the chassis.

## Sensor Logic

* BLACK line → Sensor output HIGH (1)
* WHITE surface → Sensor output LOW (0)

The STM32 continuously reads both sensors and decides robot movement based on the sensor state combination.

---

# Control Logic

| Left Sensor | Right Sensor | Robot State      | Action       |
| ----------- | ------------ | ---------------- | ------------ |
| 1           | 1            | Centered on line | Move Forward |
| 1           | 0            | Drifted Right    | Turn Left    |
| 0           | 1            | Drifted Left     | Turn Right   |
| 0           | 0            | Line Lost        | Stop         |

---

# Differential Steering Mechanism

The robot uses differential steering:

* Both motors forward → straight movement
* Left motor stop → left pivot
* Right motor stop → right pivot
* Both motors stop → halt

This enables smooth directional correction without requiring complex steering hardware.

---

# Development Workflow

The project was developed incrementally in multiple phases.

---

## Phase 1 — Requirements Analysis

Initial functional goals:

* Detect black line on white track
* Autonomous navigation
* Battery-powered operation
* Stable embedded control
* Low-cost implementation
* Real-time response

---

## Phase 2 — Component Selection

Components were selected based on:

* cost,
* availability,
* STM32 compatibility,
* embedded learning value.

### Selected Hardware

* STM32F103C8T6 Blue Pill
* L298N Motor Driver
* TCRT5000 IR Sensors
* 7.4V Li-ion Battery Pack

---

## Phase 3 — Hardware Assembly

The chassis was assembled manually.

Major tasks:

* Motor mounting
* Sensor positioning
* Wheel balancing
* Battery placement
* Wiring organization

Special attention was given to:

* sensor alignment,
* wiring stability,
* center-of-gravity balance.

---

## Phase 4 — Power Distribution Design

A centralized power architecture was implemented.

### Power Flow

```text
Battery → L298N → 5V Regulator → STM32 + Sensors
```

The L298N onboard regulator powers:

* STM32
* IR sensors

while the battery directly powers the motors.

---

# Firmware Development

Firmware was developed using:

* STM32CubeMX
* STM32CubeIDE
* STM32 HAL Library
* ST-Link Utility

---

# STM32CubeIDE Configuration

GPIO configuration included:

## Input Pins

* PA0 → Left IR Sensor
* PA3 → Right IR Sensor

Configured as:

* Digital Input
* Pull-down enabled

---

## Output Pins

| Pin  | Function  |
| ---- | --------- |
| PB0  | Motor IN1 |
| PB1  | Motor IN2 |
| PB6  | ENA       |
| PB7  | ENB       |
| PB10 | Motor IN3 |
| PB11 | Motor IN4 |

Configured as:

* Push-pull outputs
* Medium speed

---

# Clock Configuration

The STM32 uses:

* Internal 8 MHz HSI oscillator

Reasons:

* simpler hardware,
* reduced power consumption,
* no external crystal required.

---

# Firmware Architecture

The firmware follows a simple real-time polling loop.

## Main Loop Workflow

```text
Read Sensors
     ↓
Evaluate Logic
     ↓
Drive Motors
     ↓
Delay 5 ms
     ↓
Repeat
```

The loop executes at approximately:

* 200 Hz
* 5 ms cycle time

---

# Motor Control Functions

Four dedicated control functions were implemented:

## forward()

Drives both motors forward.

## left()

Stops left motor and drives right motor.

## right()

Stops right motor and drives left motor.

## stop_robot()

Stops both motors immediately.

---

# Software Design Decisions

Several engineering trade-offs were made.

---

## Why Only Two Sensors?

Advantages:

* simpler logic,
* fewer GPIO pins,
* reduced complexity,
* lower cost,
* easier debugging.

Two sensors were sufficient for:

* straight paths,
* moderate curves,
* standard line tracks.

---

## Why GPIO On-Off Control Instead of PWM?

Reasons:

* predictable behavior,
* simpler implementation,
* sufficient for moderate speeds,
* reduced firmware complexity.

PWM-based PID control is planned for future versions.

---

# Major Problems Encountered

---

## Problem 1 — Incorrect Motor Direction

### Issue

Robot moved opposite to expected direction.

### Cause

Motor polarity mismatch.

### Solution

Motor terminal wires were swapped.

---

## Problem 2 — Sensor Instability

### Issue

Random sensor readings and false detections.

### Cause

Floating GPIO inputs.

### Solution

Enabled STM32 internal pull-down resistors.

---

## Problem 3 — Robot Oscillation

### Issue

Robot continuously over-corrected while following line.

### Cause

Improper sensor spacing and excessive speed.

### Solution

Adjusted:

* sensor placement,
* chassis alignment,
* track speed.

---

## Problem 4 — Uneven Turning

### Issue

Robot turned inconsistently.

### Cause

Motor mismatch and wheel friction differences.

### Solution

Mechanical alignment and repeated calibration.

---

## Problem 5 — Sudden Movement During Startup

### Issue

Motors briefly activated during initialization.

### Cause

Undefined GPIO states before initialization.

### Solution

All GPIO output pins were explicitly reset LOW before enabling motors.

---

# Testing & Validation

The robot was tested on:

* straight tracks,
* curved tracks,
* sharp turns,
* varying lighting conditions.

---

# Performance Results

The system successfully demonstrated:

* reliable line tracking,
* stable embedded control,
* consistent directional correction,
* smooth autonomous movement,
* low-latency response.

The 5 ms polling loop provided sufficient responsiveness for real-time navigation.

---

# Software Tools Used

| Tool            | Purpose                  |
| --------------- | ------------------------ |
| STM32CubeMX     | Peripheral configuration |
| STM32CubeIDE    | Firmware development     |
| ST-Link Utility | Flash programming        |
| HAL Library     | Peripheral abstraction   |

---

# Concepts Learned

This project provided hands-on experience in:

* Embedded Systems
* ARM Cortex-M Architecture
* GPIO Interfacing
* Motor Driver Control
* Real-Time Embedded Programming
* Hardware Debugging
* Sensor Calibration
* Power Electronics
* Differential Drive Robotics
* Embedded C Programming

---

# Future Enhancements

Future versions may include:

* PWM speed control
* PID-based line tracking
* Wheel encoders
* Bluetooth control
* OLED/LCD telemetry
* Obstacle avoidance
* Multi-sensor arrays
* Wireless debugging
* RTOS integration
* Autonomous path optimization

---

# Applications

* Robotics education
* Embedded systems learning
* Industrial AGV concepts
* Autonomous navigation research
* Sensor-based control systems

---

# Project Outcome

The project successfully demonstrated the complete lifecycle of an embedded robotics system involving:

* hardware design,
* firmware development,
* sensor interfacing,
* real-time control,
* debugging,
* integration,
* and testing.

The Line Follower Robot serves as a strong foundation for advanced embedded robotics and autonomous control systems.

---

# Author

Shubham Baniwal
B.Tech Electronics & Communication Engineering
Delhi Technological University (DTU)
