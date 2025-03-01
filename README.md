
### UART Transmit Module

## Overview
This module implements an **8N1 UART Transmitter**, enabling serial data transmission using an **8-bit data frame, no parity bit, and 1 stop bit**. It generates a **9600 baud clock** from a 12 MHz oscillator and provides a simple state-machine-based transmission mechanism.The code for this module can accessed [here](https://github.com/Skandakm29/Vsd_squadron_mini_Fpga_3/blob/main/uart_trx.v).


## **Study the Existing Code**
<details>
  
<summary> Understanding the code</summary>

### Top Module
The `top` module integrates the UART transmission logic with an internal **12 MHz oscillator** and an **RGB LED driver**.

- **Clock Generation:** Uses an internal oscillator to generate a **9600 Hz clock**.
- **UART Transmission:** Transmits the character `D` continuously.
- **LED Control:** The RGB LEDs toggle based on the internal frequency counter.

### `uart_tx_8n1` Module
The `uart_tx_8n1` module implements a simple **UART transmitter** using a **finite state machine (FSM)** with the following states:

#### **Baud Rate Generator**
The baud rate generator derives a **9600 baud clock** from a **12 MHz input clock** using a counter-based approach.

- A **counter** increments on every rising edge of the **12 MHz clock**.
- When the counter reaches **1249**, it resets and toggles the **baud clock enable** signal.
- This effectively generates a **9600 Hz clock**.
#### **State Machine States**

- **IDLE STATE (STATE_IDLE)**
  - If senddata = 1 and the state is STATE_IDLE, it:
  - Moves to the **STATE_STARTTX** state.
  - Loads txbyte (**8-bit data to transmit**) into buf_tx.
  - Clears txdone (**indicates transmission is ongoing**).

  - Otherwise, if still in **STATE_IDLE**, it:
  - Keeps txbit **high (1)** because **UART idles at high**.
  - Ensures txdone remains **low (0)**.

- **Start Bit Transmission (STATE_STARTTX)**
  - Once in **STATE_STARTTX**, it:
  - Sets txbit **low (0)** (**start bit** in UART communication).
  - Moves to **STATE_TXING** to transmit data bits.

- **Sending Data Bits (STATE_TXING)**
  - If state == STATE_TXING and bits_sent < 8, it:
  - Sends the **Least Significant Bit (LSB) of buf_tx**.
  - Shifts buf_tx right (>> 1).
  - Increments bits_sent.

- **Stp Bit Transmission (STATE_TXDONE)**
  - After **8 data bits** are transmitted, it:
  - Sends the **stop bit (1)**.
  - Resets bits_sent to 0.
  - Moves to **STATE_TXDONE**.

- **Transmission Complete (STATE_TXDONE → STATE_IDLE)**
  - In **STATE_TXDONE**, it:
  - Sets txdone = 1 (**indicates transmission complete**).
  - Returns to **STATE_IDLE**.
</details>

## **System Architecture** 

<details>

<summary> Block diagram</summary>

  ![Block diagram](https://github.com/user-attachments/assets/9c0cb07d-d1c9-4dab-aaeb-d002b9b3e716)


</details>

<details>
  <summary> Circuit diagram</summary>

  ![Circuit diagram](https://github.com/user-attachments/assets/d52e18ef-c421-4163-a42b-85cfb44e8397)

</details>

## **Synthesis & Programming** 
<details>
  <summary>Testing and Output</summary>

## **Clone & Setup Repository**
```bash
git clone https://github.com/Skandakm29/Vsd_squadron_mini_Fpga_3.git
cd "Vsd_squadron_mini_Fpga_3"
```

###  Build the Bitstream
```bash
make build
```
 Generates top.bin for the FPGA.

###  **Flash to FPGA**
```bash
sudo make flash
```
Uploads the bitstream to the FPGA.
### **UART Testing**
```bash
sudo make terminal
```
</details>

## **UART Transmission Showcase**

<details>
  <summary>Demo Video</summary>

[![Watch the Demo](https://github.com/user-attachments/assets/2e41d50e-7fb5-4c2e-9296-9f6e4c054e18)](https://github.com/user-attachments/assets/2e41d50e-7fb5-4c2e-9296-9f6e4c054e18)

</details>

