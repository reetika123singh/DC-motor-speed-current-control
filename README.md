# DRV8833 Dual H-Bridge Motor Driver — PCB & Cascade Speed & Current Control

A DC motor drive system built around the DRV8833 dual H-bridge driver, with a custom KiCad PCB and a Simulink/Simscape simulation of a cascade control architecture for precise speed regulation.

## Key Specifications

| Parameter | Value |
|---|---|
| Supply Voltage | 12 V |
| PWM Switching Frequency | 10 kHz |
| Speed Setpoint (simulated) | 1500 RPM |
| Current Limit (inner loop saturation) | 0 – 10 A |
| Control Architecture | Cascade: outer speed loop → inner current loop |

## Control Architecture

The system uses a **cascade (dual-loop) control structure**, which regulates motor speed while keeping motor current within a safe limit:

1. **Outer speed loop** — PI controller (P = 0.002, I = 0.02) compares the RPM setpoint against measured speed and outputs a current command.
2. **Saturation stage** — the current command is clamped to 0–10 A, acting as a current limiter between the two loops.
3. **Inner current loop** — PI controller (P = 0.5, I = 5) compares the (limited) current command against sensed motor current and drives the H-Bridge PWM input.
4. **Plant** — an averaged H-Bridge model (12V, 10kHz) driving a Simscape DC Motor model (Ra = 2Ω, La = 1mH, Kv = 0.0072 V/rpm, J = 1e-5 kg·m²), with feedback from a Current Sensor (inner loop) and an Ideal Rotational Motion Sensor (outer loop, converted from rad/s to RPM).

This cascade structure is a standard technique for motor drives: the fast inner current loop protects the motor/driver from overcurrent and improves disturbance rejection, while the slower outer loop handles the actual speed tracking.

## Repository Structure

- `Motor driver DRV8833.kicad_pro` (+ associated schematic/PCB files) — KiCad project for the DRV8833 driver board, including power rail decoupling and charge pump circuitry.
- `Motor_speed_control.slx` — Simulink/Simscape model implementing the cascade speed/current control loop described above.

## How to Run the Simulation

1. Open MATLAB and load `Motor_speed_control.slx` in Simulink.
2. Run the simulation to observe the motor speed settling toward the 1500 RPM setpoint and the current staying within the 0–10 A limit.
3. View the Scope block for speed and current response over time.


## PCB

<img width="720" height="495" alt="image" src="https://github.com/user-attachments/assets/112bbe8b-37ad-4d30-b8da-ea126c0c19b1" />
<img width="1091" height="577" alt="image" src="https://github.com/user-attachments/assets/def4e3a5-526f-4666-9f47-1b85dc4253c7" />


## Results

<img width="1897" height="510" alt="image" src="https://github.com/user-attachments/assets/9bf838b7-8b4e-486b-b490-1e3507278dbc" />
<img width="1906" height="522" alt="image" src="https://github.com/user-attachments/assets/6fa85e50-a6cc-4664-8e66-6997f2b5b7c1" />


**Settling time:** 0.12s
**Steady-state speed error:** +100 RPM
**Peak current during startup:** 1.4 A



