# FIFO Architecture

## Overview

This module implements a **16 × 8 synchronous FIFO** using a circular memory buffer.

All operations occur on a **single clock domain** (`clk`), making the design simple and suitable for FPGA datapaths.

---

## Architectural Blocks

              +----------------------+
              |      Control Logic   |
              | fifo_counter         |
              | buf_empty / buf_full |
              +----------+-----------+
                         |
         +---------------+----------------+
         |                                |
    +----v----+                      +----v----+
    | Write   |                      | Read    |
    | Pointer |                      | Pointer |
    | wr_ptr  |                      | rd_ptr  |
    +----+----+                      +----+----+
         |                                |
         +------------+  +----------------+
                      |  |
                +-----v--v------+
                |  FIFO Memory  |
                | buf_mem[16]   |
                +---------------+

---

## Memory Organization

```
buf_mem[0] ... buf_mem[15]
```

Each location stores:

```
8-bit data word
```

The FIFO behaves as a **circular queue**.

---

## Pointer Operation

### Write Pointer (`wr_ptr`)
- Advances on valid write:

```
wr_en && !buf_full
```
- Wraps automatically (4-bit overflow).

### Read Pointer (`rd_ptr`)
- Advances on valid read:

```
rd_en && !buf_empty
```

---

## Data Flow

### Write Path

```
buf_in → buf_mem[wr_ptr]
```

Occurs on rising clock edge.

---

### Read Path

```
buf_mem[rd_ptr] → buf_out
```

Registered output ensures synchronous timing.

---

## Occupancy Tracking

The FIFO uses a counter:

```
fifo_counter
```

### Counter Behavior

| Condition | Counter Update |
|----------|----------------|
Write only | +1 |
Read only  | −1 |
Simultaneous R/W | No change |
Idle | No change |

---

## Status Flag Generation

Flags are derived from `fifo_counter`:

```
buf_empty = (fifo_counter == 0)
buf_full  = (fifo_counter == 16)
```

These indicate FIFO state to upstream/downstream logic.

---

## Simultaneous Read/Write Support

If both `wr_en` and `rd_en` are asserted:

- One word enters
- One word leaves
- FIFO depth remains constant

This allows continuous streaming.

---

## Reset Behavior

On `rst`:

```
wr_ptr = 0
rd_ptr = 0
fifo_counter = 0
buf_out = 0
```

FIFO returns to empty state.

---

## Timing Model

All actions are synchronous:

```
posedge clk → evaluate enables → update memory, pointers, counter
```

No combinational data path between modules → safe for FPGA timing closure.

---

## Design Tradeoffs

| Choice              | Reason             |
|---------------------|--------------------|
| Single clock        | Simplicity         |
| Pointer wrap-around | Low hardware cost  |
| Counter-based status| Easy to understand |
| Registered output   | Stable timing      |

---

## Limitations (Intentional for Simplicity)

This FIFO is not optimized for ASIC-grade robustness:

- No metastability handling (single clock only)
- Counter width mismatch for true "16" detection
- No protection against illegal enable usage
- No Gray-coded pointers (not async-safe)

---

## Intended Use

Educational / prototyping FIFO suitable for:

- UART buffering
- Lab experiments
- FPGA demonstrations
- RTL learning projects

---

## Possible Architectural Extensions

Future versions may include:

- Parameterized depth
- Dual-clock asynchronous FIFO
- Gray-coded pointer synchronization
- Overflow / underflow detection
- Backpressure signaling
- Almost-full thresholds