# SRAM Controller (Verilog)

## 📘 Overview

This project implements a **SRAM Controller** in Verilog HDL for interfacing with an external SRAM memory. The controller handles **read** and **write** operations, provides a ready signal, and manages the SRAM control signals. It is designed to be **parameterizable** for different address and data widths.

The controller is useful in FPGA or ASIC designs where an SRAM module is used for temporary storage or buffering data.

---

## 🧩 Project Structure

```
SRAM_Controller/
├── sram_controller.v   # SRAM controller Verilog module
├── sram_controller_tb.v # Testbench for simulation and waveform verification
└── README.md           # Project documentation
```

---

## ⚙️ Design Description

### 🧠 Top Module: `sram_controller`

* Parameters:

  * `ADDR_WIDTH`: Width of the SRAM address bus (default 16-bit)
  * `DATA_WIDTH`: Width of the SRAM data bus (default 8-bit)
* Inputs:

  * `clk`: System clock
  * `rst`: Asynchronous reset
  * `addr`: Address for read/write operation
  * `write_data`: Data to write to SRAM
  * `read_en`: Enable read operation
  * `write_en`: Enable write operation
* Outputs:

  * `read_data`: Data read from SRAM
  * `ready`: High when operation is completed
  * SRAM Interface Signals:

    * `sram_addr`: Address bus to SRAM
    * `sram_data`: Bidirectional data bus to SRAM
    * `sram_ce_n`: Chip enable (active low)
    * `sram_oe_n`: Output enable (active low)
    * `sram_we_n`: Write enable (active low)

### 🔹 Internal FSM

The controller uses a **simple finite state machine (FSM)** with three states:

1. **IDLE**: Wait for `read_en` or `write_en` signal.
2. **READ**: Activate SRAM chip and output enable, read data, assert `ready`.
3. **WRITE**: Activate SRAM chip and write enable, drive `write_data` to SRAM, assert `ready`.

### 🔹 Data Bus Management

* `sram_data` is bidirectional and controlled via `data_dir`:

  * `data_dir = 0`: Read mode (controller inputs from SRAM)
  * `data_dir = 1`: Write mode (controller drives data to SRAM)
* Tristate buffer implemented using `assign sram_data = (data_dir) ? data_out : {DATA_WIDTH{1'bz}};`

### 🔹 Ready Signal

* The `ready` output indicates completion of the read/write operation.
* Allows external modules to synchronize with the controller.

### 🔹 Reset Behavior

* Asynchronous reset (`rst`) sets FSM to **IDLE** state.
* Ensures predictable startup and prevents accidental writes or reads.

---

## 🧪 Testbench: `sram_controller_tb.v`

* Generates read and write requests to the SRAM controller.
* Monitors `read_data` and `ready` signals.
* Validates that data is correctly written to and read from SRAM.
* Can simulate timing delays for real SRAM modules.

### Example Test Sequence

```verilog
// Write data to address 0x0010
write_en = 1'b1;
addr = 16'h0010;
write_data = 8'hAB;
#10;
write_en = 1'b0;

// Read data from address 0x0010
read_en = 1'b1;
addr = 16'h0010;
#10;
read_en = 1'b0;
```

* Observe `read_data` = 8'hAB and `ready` signal assertion.

---

## 📊 Waveform Verification

1. Compile `sram_controller.v` and `sram_controller_tb.v`.
2. Run simulation in your simulator (ModelSim, Vivado, eSim).
3. Observe:

   * `sram_addr` matches requested address.
   * `sram_data` correctly reflects written or read data.
   * `sram_ce_n`, `sram_oe_n`, `sram_we_n` asserted correctly.
   * `ready` toggles after each operation.

---

## 🎯 Key Learning Points

* SRAM interfacing in FPGA/ASIC designs.
* Bidirectional data bus handling with tristate buffers.
* FSM design for read/write control.
* Synchronization using ready signals.
* Parameterized Verilog design for flexible data and address widths.

---

## 📂 References

* FPGA SRAM interfacing guides
* Verilog HDL Reference Manual
* Digital system design and FSM textbooks
