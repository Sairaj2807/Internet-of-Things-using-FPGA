# Internet-of-Things-using-FPGA

This project showcases the implementation of an Internet of Things (IoT) system using an FPGA. Specifically, we demonstrate how to use an FPGA to monitor temperature data and transmit it over a network using AT commands.

## Table of Contents
1. [Introduction](#introduction)
2. [Why FPGA for IoT](#why-fpga-for-iot)
3. [Hardware Required](#hardware-required)
4. [Setup and Implementation](#setup-and-implementation)
5. [VHDL Code](#vhdl-code)
6. [AT Commands](#at-commands)
7. [Demo](#demo)
8. [References](#references)

## Introduction

The aim of this project is to use an FPGA to collect temperature data from a sensor and transmit this data to the cloud using a Wi-Fi module. The FPGA used in this project is the Xilinx Spartan-6 (XC6SLX16-2FTG256C).



## Why FPGA for IoT

- **Concurrency in Data Processing**
- **Rich I/O Capabilities**
- **Flexible and Reconfigurable**
- **Low Latency and Real-Time Processing**
- **Extended Product Lifecycles**
- **Scalability for Multiple Sensor Integration**

## Hardware Required

- **Spartan 6 FPGA Board (XC6SLX16-2FTG256C)**
- **MCP3202 ADC**
- **Temperature Sensor**
- **Wi-Fi Module**
- **Power Supply (5V DC, 1A)**

### Board Specifications
- **On-Board FPGA:** XC6SLX16-2FTG256C
- **External Crystal Frequency:** 50MHz
- **Block RAM:** 576Kb
- **Logic Cells:** 14,579
- **SPI Flash:** 1M byte
- **DDR3 Memory:** 256MB
- **Power Supply:** 3.3V via MP2359 DC/DC converter
- **User I/Os:** Two 64p headers
- **User Switches:** 3
- **User LEDs:** 4
- **JTAG Interface:** 6p header
- **PCB Size:** 6.7cm x 8.4cm

## Setup and Implementation

1. **Connect the Hardware:**
   - Interface the temperature sensor with the MCP3202 ADC.
   - Connect the MCP3202 ADC to the FPGA.
   - Connect the Wi-Fi module to the FPGA for data transmission.
   - Ensure the power supply is properly connected.

## VHDL Code

### Library and Port Declaration
```vhdl
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;
use ieee.std_logic_arith.all; 
use ieee.std_logic_unsigned.all; 

entity IoT_Temperature_Monitoring is
    Port ( 
        clk : in  STD_LOGIC;
        txd : out  STD_LOGIC;
        cs : out std_logic;
        sc : out std_logic;
        do : out std_logic;
        din : in std_logic
    );
end IoT_Temperature_Monitoring;
```

### Signals Declaration
```vhdl
-- Signals for SPI Communication
type state is (spi,conversion);
signal presentstate : state := spi;

-- Signal for Temperature Sensor
signal temp1: integer := 0;

-- Signals for Binary to BCD and BCD to ASCII
type reg2 is array(1 to 3) of std_logic_vector(7 downto 0);    
signal arr_rp1 : reg2; 
signal siga, sigb: std_logic_vector(2 downto 0) := (others => '0');

-- Signals for UART Transmission
type stat1 is (ready2, start2, stop2);
signal ps2 : stat1 := ready2; 
signal start, stop : std_logic;
signal store : std_logic_vector(7 downto 0);
signal baud_clk : std_logic; 
type arr is array (1 to 160) of std_logic_vector(7 downto 0);
```

## AT Commands

The following AT commands are used for data transmission:

```plaintext
-- AT+RST
-- AT+CWJAP="WIFINAME","Password"
-- AT+CIPSTART="TCP","184.106.153.149",80
-- AT+CIPSEND=49
-- GET /update?api_key=XXXXXXXXXXXXXXXX&field1=000
-- AT+CLOSE
```

### Example Command Sequence
```vhdl
constant str : arr :=  ( 
    X"41", X"54", X"2b", X"52", X"53", X"54", X"0d", X"0a", -- AT+RST
    X"41", X"54", X"2b", X"43", X"57", X"4a", X"41", X"50", X"3d", X"22", X"57", X"49", X"46", X"49", X"4e", X"41", X"4d", X"45", X"22", X"2c", X"22", X"50", X"41", X"53", X"53", X"57", X"4f", X"52", X"44", X"22", X"0d", X"0a", -- AT+CWJAP="WIFINAME","PASSWORD"
    X"41", X"54", X"2b", X"43", X"49", X"50", X"53", X"54", X"41", X"52", X"54", X"3d", X"22", X"54", X"43", X"50", X"22", X"2c", X"22", X"31", X"38", X"34", X"2e", X"31", X"30", X"36", X"2e", X"31", X"35", X"33", X"2e", X"31", X"34", X"39", X"22", X"2c", X"38", X"30", X"0d", X"0a", -- AT+CIPSTART="TCP","184.106.153.149",80
    X"41", X"54", X"2b", X"43", X"49", X"50", X"53", X"45", X"4e", X"44", X"3d", X"34", X"39", X"0d", X"0a", -- AT+CIPSEND=49
    X"47", X"45", X"54", X"20", X"2f", X"75", X"70", X"64", X"61", X"74", X"65", X"3f", X"61", X"70", X"69", X"5f", X"6b", X"65", X"79", X"3d", X"58", X"58", X"58", X"58", X"58", X"58", X"58", X"58", X"26", X"66", X"69", X"65", X"6c", X"64", X"31", X"3d", X"30", X"30", X"30", X"0d", X"0a", -- GET /update?api_key=XXXXXXXXXXXX&field1=000
    X"41", X"54", X"2b", X"43", X"49", X"50", X"43", X"4c", X"4f", X"53", X"45", X"0d", X"0a" -- AT+CLOSE
);
```

## Demo

- **Step 1:** Power up the FPGA board and ensure all connections are secure.
- **Step 2:** The FPGA reads temperature data from the sensor via the MCP3202 ADC.
- **Step 3:** The data is processed and converted to ASCII for transmission.
- **Step 4:** The FPGA uses AT commands to connect to a Wi-Fi network and send the data to the cloud.

## References

- [MCP3202 ADC Datasheet](https://ww1.microchip.com/downloads/en/DeviceDoc/21298e.pdf)
- [Xilinx Spartan-6 FPGA Specifications](https://www.xilinx.com/products/silicon-devices/fpga/spartan-6.html)

---

