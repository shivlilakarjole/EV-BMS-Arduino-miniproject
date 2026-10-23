# EV-BMS-Arduino-miniproject
“BMS for EV with real-time monitoring and thermal protection using Arduino UNO”
# BMS for EV with Real-Time Charge Monitoring and Thermal Fire Protection

A Battery Management System (BMS) prototype for electric vehicles, built with an **Arduino UNO**, **ACS712** current sensor, **LM35** temperature sensor and a **3S Li-ion battery pack**. It monitors voltage, current and temperature in real time, shows them on an LCD, and disconnects the load automatically when a fault is detected.

**Mini Project | Department of Electronics Engineering, Walchand Institute of Technology, Solapur | 2025-26**

---

## Problem Statement

Electric two-wheelers rely on lithium-ion batteries. Without proper monitoring, a pack can suffer from:

- **Overcharging**: risk of electrolyte decomposition and fire
- **Overheating / thermal runaway**: dangerous exothermic reactions inside the cell
- **Overcurrent**: internal heating and faster degradation
- **Over-discharge**: irreversible capacity loss

This project builds a low-cost, real-time BMS to detect these conditions and protect the battery.

## Features

- Real-time monitoring of battery **voltage, current and temperature**
- Live readings on a **16x2 I2C LCD** (address 0x27)
- Protection against **overvoltage, undervoltage, overcurrent and overtemperature**
- **MOSFET / relay switching** to disconnect the load on a fault
- **Red / Yellow / Green LEDs and a buzzer** for status and fault indication
- **12 V DC motor** used as the test load
- Fault response within one loop cycle (500 ms)

---

## Components Used

| Component | Specification | Purpose |
|---|---|---|
| Arduino UNO (ATmega328P) | 5 V, 16 MHz, 10-bit ADC | Main controller |
| 3S Li-ion battery pack | 3 x 18650, 11.1 V nominal, 12.6 V full | Energy source |
| ACS712 current sensor | 20 A / 30 A variant | Current sensing |
| LM35 temperature sensor | 10 mV/°C | Temperature sensing |
| Voltage sensor | Resistive divider (30 kΩ / 10 kΩ), scales 12.6 V to 3.15 V | Voltage sensing |
| 16x2 LCD with I2C module | I2C address 0x27 | Display |
| MOSFET | Switching element controlled by Arduino | Load disconnect |
| LEDs (R/Y/G) + 220 Ω resistors | | Status indication |
| Buzzer | | Fault alert |
| 12 V DC motor | | Load |
| Breadboard and jumper wires | | Assembly |

---

## System Architecture


Three sensors (voltage, current, temperature) feed analog signals to the Arduino UNO. The Arduino drives two outputs: the LCD for live display, and the protection switch that connects or disconnects the battery from the load.

### Circuit Diagram

<img width="816" height="745" alt="image" src="https://github.com/user-attachments/assets/32a8d357-9224-4776-b835-e85b46f3b294" />

### Pin Connections

| Component | Arduino Pin |
|---|---|
| Voltage sensor | A0 |
| Current sensor (ACS712) | A1 |
| Temperature sensor (LM35) | A2 |
| LCD SDA | A4 |
| LCD SCL | A5 |
| Green / Yellow / Red LEDs | D8 / D9 / D10 *(confirm with your code)* |
| Buzzer | D7 *(confirm with your code)* |
| MOSFET gate | D_ *(fill from your code)* |

### Conversion Formulas

```
Battery voltage  V = (ADC / 1023) x 5 x 4          (divider ratio 4:1)
Current          I = ((ADC / 1023) x 5 - 2.5) / 0.1   (20 A module)
Temperature      T = (ADC / 1023) x 5 x 100        (LM35, 10 mV/°C)
```

---



---

## Working Principle

1. The voltage, current and temperature sensors send analog signals to A0, A1 and A2.
2. The Arduino converts the raw ADC values into volts, amperes and °C.
3. The readings are shown on the LCD and printed on the Serial Monitor (9600 baud).
4. Each reading is compared with its safety threshold.
5. If everything is within limits, the load stays connected and the display shows live values.
6. If any limit is crossed, the Arduino switches the load off, shows a fault message (`OV FAULT`, `OC FAULT`, `OT FAULT`) and raises the alarm.
7. The loop repeats every 500 ms.


## Protection Thresholds

| Parameter | Limit |
|---|---|
| Maximum voltage (`VOLT_MAX`) | 12.6 V |
| Minimum voltage (`VOLT_MIN`) | 9.0 V |
| Maximum current (`CURR_MAX`) | 10.0 A |
| Maximum temperature (`TEMP_MAX`) | 60 °C |

### Status Indicators

| Indicator | Meaning |
|---|---|
| Green LED | Normal operation, load connected |
| Yellow LED | Warning, a value is nearing its limit |
| Red LED + Buzzer | Fault detected, load disconnected |

---

## Results

| Parameter | Applied Value | BMS Reading | Error |
|---|---|---|---|
| Voltage | 10.00 V | 9.98 V | 0.02 V |
| Voltage | 11.10 V | 11.08 V | 0.02 V |
| Voltage | 12.60 V (OV) | 12.57 V | 0.03 V |
| Current | 2.00 A | 2.03 A | 0.03 A |
| Current | 5.00 A | 5.04 A | 0.04 A |
| Current | 10.50 A (OC) | 10.47 A | 0.03 A |
| Temperature | 30 °C | 30.3 °C | 0.3 °C |
| Temperature | 55 °C | 55.4 °C | 0.4 °C |
| Temperature | 62 °C (OT) | 62.2 °C | 0.2 °C |

- All three fault-injection tests (overvoltage, overcurrent, overtemperature) triggered load disconnection and an LCD fault message within 500 ms.
- Accuracy: within ±0.5% for voltage, ±2% for current, ±0.5 °C for temperature.
- Stable operation over 30 minutes of repeated charge/discharge cycling.



### Testing Procedure

1. **Unit sensor testing**: each sensor tested individually against known inputs.
2. **Integration testing**: readings compared with a calibrated multimeter and thermometer.
3. **Fault injection**: overvoltage, overcurrent and overtemperature simulated to verify automatic protection.

### Issues Faced and Fixes

- Noisy ACS712 readings: fixed with a 10 µF and 100 nF capacitor across its supply.
- Slight voltage error from resistor tolerance: fixed with a software calibration offset.
- Garbled LCD characters: fixed by reducing I2C clock speed and adding 4.7 kΩ pull-ups on SDA/SCL.

---

## Code

Source code: [`src/bms_main/bms_main.ino`](src/bms_main/bms_main.ino)

### How to Run

1. Install the [Arduino IDE](https://www.arduino.cc/en/software).
2. Install the `LiquidCrystal_I2C` library (Sketch → Include Library → Manage Libraries). The `Wire` library is built in.
3. Open `bms_main.ino`, select **Arduino UNO** and the correct COM port.
4. Upload the sketch and open the Serial Monitor at 9600 baud.

---



---

## Limitations

- No per-cell voltage balancing
- Limited to a single 3S pack
- A single temperature sensor for the whole pack
- Prototype only, not suitable for real vehicle use

## Future Scope

- **IoT integration**: remote monitoring with ESP8266/ESP32
- **Cell balancing**: passive or active balancing circuits
- **Fast charging**: CC/CV charging support
- **GPS and vehicle monitoring**
- **AI-based fault prediction** from historical trends
- Flyback diode across the motor and a pull-down resistor on the MOSFET gate

---

## Safety Note

Lithium-ion batteries can overheat, catch fire or explode if misused. This is an educational prototype. Work with supervision and proper precautions.

## Team

- Rajashri Kalshetti
- Deepika Mittha
- Shivlila Karjole

**Guide:** Dr. Mrs. R. J. Shelke, Department of Electronics Engineering, Walchand Institute of Technology, Solapur

## License

This project is licensed under the MIT License.
