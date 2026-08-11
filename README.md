# ⚡ ESP32 Stroboscope

<p align="center">
  <img src="https://raw.githubusercontent.com/shuvabratadey/stroboscope-using-esp32/main/Stroboscope%20Pictures/ESP32_Stroboscope.png" alt="ESP32 Stroboscope" width="700">
</p>

<p align="center">
  <b>A compact ESP32-based digital stroboscope for RPM measurement and rotating machinery inspection.</b>
</p>

---

## 📖 What is a Stroboscope?

A **stroboscope** is a device that produces a rapid series of light flashes at a controlled frequency.

When the flashing frequency is synchronized with the rotational speed of a moving object, the object can appear to be **stationary** or moving very slowly.

This phenomenon makes a stroboscope extremely useful for observing and analyzing rotating machinery without physically stopping it.

Typical objects that can be inspected include:

* Motors
* Fans
* Shafts
* Gears
* Pulleys
* Rotating assemblies

---

## 🚀 About This Project

This project is an **ESP32-based digital stroboscope** designed to generate high-speed flashes of light for observing and estimating the rotational speed of moving objects.

The ESP32 generates precisely controlled pulses that drive a **high-power LED through an N-channel MOSFET**.

The user can adjust the strobe parameters while monitoring the current values on an OLED display.

### Displayed Parameters

The display shows:

* 🔄 **RPM**
* ⚡ **Flash Frequency**
* 💡 **Duty Cycle**

By adjusting the flash frequency until the rotating object appears stationary, its rotational speed can be estimated.

---

## 🧮 RPM Calculation

The rotational speed can be calculated from the flashing frequency using:

```text
RPM = Frequency (Hz) × 60
```

### Example

If the strobe frequency is:

```text
1.67 Hz
```

Then:

```text
RPM = 1.67 × 60
RPM ≈ 100
```

Therefore, the rotating object is running at approximately:

```text
100 RPM
```

> **Note:** Harmonics and sub-harmonics can sometimes make the rotating object appear stationary at multiple flash frequencies. For accurate measurements, verify the fundamental rotational frequency whenever possible.

---

## 🛠️ Hardware

The project uses the following main components:

| Component                                 | Purpose                                 |
| ----------------------------------------- | --------------------------------------- |
| **ESP32**                                 | Main controller and pulse generation    |
| **High-Power LED**                        | Generates the stroboscopic flashes      |
| **N-Channel MOSFET**                      | Drives the high-current LED             |
| **OLED Display**                          | Displays RPM, frequency, and duty cycle |
| **Potentiometer / Rotary Control**        | User adjustment of strobe parameters    |
| **LED Driver / Current Limiting Circuit** | Controls LED current safely             |
| **Power Supply**                          | Powers the ESP32 and LED circuit        |
| **Enclosure**                             | Protects and houses the complete system |

---

## 🔌 System Block Diagram

```text
                    +--------------------+
                    |       ESP32        |
                    |                    |
                    |  Frequency Control |
                    |  Duty Cycle Control|
                    +---------+----------+
                              |
                              | PWM / Pulse
                              v
                       +-------------+
                       |   N-Channel |
                       |    MOSFET   |
                       +------+------+
                              |
                              v
                       +-------------+
                       | High-Power  |
                       |     LED     |
                       +-------------+
                              |
                              |
                       Flashing Light
                              |
                              v
                  +-----------------------+
                  |    Rotating Object    |
                  |                       |
                  | Motor / Fan / Shaft   |
                  |   Gear / Pulley       |
                  +-----------------------+
```

---

## 🎯 Applications

This stroboscope can be used for:

* 🔄 Motor RPM measurement
* 🌀 Fan speed inspection
* ⚙️ Gear and pulley inspection
* 🔧 Shaft rotation analysis
* 🏭 Rotating machinery analysis
* 🔍 Mechanical defect visualization
* 🧪 Laboratory experiments
* 🎓 Educational demonstrations
* 🛠️ Preventive maintenance and troubleshooting

## ▶️ YouTube Demo

<p align="center">
  <a href="https://youtube.com/shorts/vkFnO6racog?feature=share">
    <img src="https://img.youtube.com/vi/vkFnO6racog/maxresdefault.jpg" alt="ESP32 Stroboscope YouTube Demo" width="600">
  </a>
</p>

<p align="center">
  <a href="https://youtube.com/shorts/vkFnO6racog?feature=share">
    <b>▶ Watch the ESP32 Stroboscope Demo on YouTube</b>
  </a>
</p>

---

## 🧠 How It Works

The ESP32 generates a pulse signal at a user-selected frequency.

This signal switches the **N-channel MOSFET**, which rapidly turns the high-power LED ON and OFF.

When the LED flashing frequency matches the rotational frequency of the object, the object appears stationary because it is illuminated repeatedly at approximately the same angular position.

```text
ESP32
  |
  | Pulse Signal
  v
MOSFET
  |
  | High-Current Switching
  v
High-Power LED
  |
  | Stroboscopic Light
  v
Rotating Object
  |
  v
Object Appears Stationary
```

The frequency at this point can then be used to estimate the object's RPM.

---

## ⚠️ Safety

High-power LEDs can produce extremely bright flashes.

Please take appropriate precautions:

* Do not look directly into the LED.
* Avoid prolonged exposure to rapidly flashing light.
* Use appropriate current limiting for the LED.
* Ensure the MOSFET and LED driver are rated for the required current.
* Use proper thermal management for high-power LEDs.
* Disconnect power before modifying the circuit.

> ⚠️ **Photosensitive Epilepsy Warning:** Rapidly flashing lights may trigger seizures or discomfort in people with photosensitive epilepsy or other light sensitivities.

---

## 🕰️ Previous Prototype — ATmega328P

Before developing the ESP32 version, an earlier prototype of the stroboscope was built using an **ATmega328P-based implementation**.

The original project documentation is available here:

👉 [**View the ATmega328P Prototype Documentation**](https://github.com/shuvabratadey/stroboscope-using-esp32/blob/main/README_OLD.md)
