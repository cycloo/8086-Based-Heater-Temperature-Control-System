# Heater Control System (8086 + 8255 + ADC0804)

This project implements a temperature control system using the Intel 8086 microprocessor, the 8255 PPI, and the ADC0804 analog-to-digital converter. The system reads temperature input, allows the user to adjust a setpoint using push buttons, and displays both the real-time temperature and setpoint values on 7-segment displays. Hardware simulation is built in Proteus, and the control logic is written entirely in x86 assembly.

---

## Features
- Reads temperature data from a simulated thermocouple through ADC0804  
- Adjustable temperature setpoint using push buttons  
- Real-time temperature and setpoint display on 7-segment modules  
- Output control logic for heater activation  
- Fully simulated hardware environment using Proteus  
- Clean and organized 8086 assembly code

---

## Hardware Components
- Intel 8086 microprocessor  
- Intel 8255 Programmable Peripheral Interface  
- ADC0804 analog-to-digital converter  
- Thermocouple (Type-J) / simulated input  
- 7-segment displays  
- Push buttons  
- IC 74HC373 latch  
- Supporting resistors, wiring, and power modules

---

## How It Works
1. ADC0804 reads the analog temperature signal and sends digital data to the microprocessor.  
2. Push buttons allow increment/decrement of the user setpoint.  
3. The system compares actual temperature to the setpoint.  
4. Heater output is controlled based on the comparison result.  
5. Temperature and setpoint values are shown on multiplexed 7-segment displays.  

---

## Usage
1. Open the Proteus project file and load the compiled assembly program into the 8086.  
2. Run the simulation.  
3. Adjust the temperature setpoint using the push buttons.  
4. Observe temperature and setpoint updates on the 7-segment displays.  

---

## Requirements
- Proteus (for hardware simulation)  
- MASM/TASM (for assembling the code)  

---

## License
MIT License

---

