# ESP32 Stroboscope

## What is a Stroboscope?

A stroboscope is a device that produces a rapid series of flashes of light. When the flashing frequency is synchronized with the rotational speed of a moving object, the object can appear to be stationary or moving very slowly.

This effect is useful for visual inspection and measurement of rotating machinery. It allows mechanical parts such as motors, fans, shafts, gears, and rotating assemblies to be observed without physically stopping them.

---
<img src= https://github.com/shuvabratadey/stroboscope-using-esp32/blob/main/Stroboscope%20Pictures/ESP32_Stroboscope.png/>

# Stroboscope Using ESP32

This project is an **ESP32-based digital stroboscope** designed to generate high-speed flashes of light for observing and measuring the rotational speed of moving objects.

The ESP32 controls the flashing frequency of a high-power LED and provides a user interface for adjusting the strobe frequency and duty cycle.

The display shows the current:

- RPM
- Flash frequency
- Duty cycle

By adjusting the stroboscope frequency until a rotating object appears stationary, the rotational speed of the object can be estimated.

For example, when the strobe frequency is synchronized with the rotational speed:

```text
RPM = Frequency (Hz) × 60
```
For example:
```text
1.67 Hz × 60 ≈ 100 RPM
```
# Hardware
* ESP32
* High-power LED
* N-channel MOSFET
* OLED display
* Potentiometer / rotary control
* LED driver/current limiting circuit
* Power supply
* Enclosure

```text
                 +----------------+
                 |     ESP32      |
                 |                |
                 | Frequency Ctrl |
                 +-------+--------+
                         |
                         | PWM / Pulse
                         v
                  +-------------+
                  |   MOSFET    |
                  +------+------+
                         |
                         v
                  +-------------+
                  | High Power  |
                  |     LED     |
                  +-------------+

                         ^
                         |
                  Flashing Light
                         |
                         v

              +---------------------+
              | Rotating Object     |
              | Motor / Fan / Shaft |
              +---------------------+
```
# Applications
* Motor RPM measurement
* Fan and shaft inspection
* Gear and pulley inspection
* Rotating machinery analysis
* Mechanical defect visualization
* Educational experiments

# YouTube Link:- [https://youtu.be/it-jdhMR7sY](https://youtube.com/shorts/QEk9EGT3YsI?feature=share)

# Old Prototype using AT-MEGA328P

An older version of this stroboscope prototype was previously developed using a different hardware/software implementation.

The original documentation is available here:

[readme_old.md](https://github.com/shuvabratadey/stroboscope-using-esp32/blob/main/README_OLD.md)
