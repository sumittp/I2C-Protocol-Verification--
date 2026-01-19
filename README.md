# I2C Protocol Verification Using SystemVerilog

## Overview

This project implements a **SystemVerilog-based self-checking verification environment** for an **I2C (Inter-Integrated Circuit)** protocol.  
The design includes a fully functional **I2C Master and I2C Slave**, along with a class-based verification testbench to validate **read and write transactions**.

The verification flow is automated and verifies correct address decoding, data transfer, acknowledgment handling, and stop condition detection.

---

## Design Under Test (DUT)

The DUT consists of the following RTL modules:

- `i2c_master` – Generates I2C transactions
- `i2c_slave` – Responds to master operations with internal memory
- `i2c_top` – Top-level integration of master and slave

### DUT Features

- Standard I2C protocol support (START, STOP, ACK/NACK)
- Parameterized system and I2C clock frequencies
- Bidirectional `sda` line with tri-state control
- Supports both **read** and **write** operations
- Internal slave memory for data storage and retrieval
- ACK error detection from both master and slave
- Status signals:
  - `busy`
  - `done`
  - `ack_err`

---

## Verification Architecture

The verification environment is implemented using **SystemVerilog classes** and follows a **transaction-level approach**.

### Components

#### Transaction
- Represents a single I2C operation
- Randomized fields:
  - Read/Write operation (`op`)
  - Slave address
  - Write data
- Includes constraints to limit valid address and data ranges

#### Generator
- Randomly generates I2C transactions
- Controls number of test iterations
- Synchronizes with driver and scoreboard using events

#### Driver
- Drives I2C master inputs via a virtual interface
- Initiates read or write transactions using `newd`
- Waits for `done` signal from DUT
- Supports both read and write operations

#### Monitor
- Observes completed I2C transactions
- Captures address, operation type, input data, and output data
- Sends observed transactions to the scoreboard

#### Scoreboard
- Maintains a reference memory model
- For write operations:
  - Updates reference memory
- For read operations:
  - Compares DUT output data with expected memory contents
- Reports **DATA MATCHED** or **DATA MISMATCHED**

---

## Verification Flow

1. Reset DUT and initialize signals
2. Generator creates randomized I2C transactions
3. Driver applies transactions to the DUT
4. I2C master communicates with I2C slave
5. Monitor captures completed transactions
6. Scoreboard validates data correctness
7. Simulation terminates automatically after all tests

---

## Key Features

- Class-based SystemVerilog testbench
- Self-checking scoreboard with reference memory
- Randomized read/write I2C transactions
- Address and data constraints
- Mailbox-based communication
- Event-based synchronization
- Bidirectional SDA line modeling
- ACK/NACK and error detection
- VCD waveform generation for debugging

---

## Simulation & Debug

- Console output displays:
  - Generated transactions
  - Read/write operations
  - Scoreboard comparison results
- Waveforms are dumped to `dump.vcd` for signal-level analysis
