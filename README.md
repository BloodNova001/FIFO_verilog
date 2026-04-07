# Synchronous FIFO (16 × 8) — Verilog Implementation

This project implements a simple **single-clock synchronous FIFO** in Verilog.

The FIFO is designed as a lightweight buffering module for digital systems such as:
- UART data buffering
- Producer–consumer decoupling
- Stream-based data pipelines
- FPGA-based communication interfaces

---

## 📌 Features

- 8-bit data width
- 16-word depth
- Single clock domain (synchronous FIFO)
- Separate read/write enable control
- Status flags:
  - `buf_empty`
  - `buf_full`
- Internal occupancy tracking via `fifo_counter`
- Circular buffer using read/write pointers

---

## ⚙️ How It Works

* Data is written into the FIFO when:

  ```
  wr_en = 1 and buf_full = 0
  ```

* Data is read from the FIFO when:

  ```
  rd_en = 1 and buf_empty = 0
  ```

* The FIFO uses:

  * `wr_ptr` → write location
  * `rd_ptr` → read location
  * automatic wrap-around (circular buffer)

---

## ⏱ Operation Timing

| Operation        | Latency       |
| ---------------- | ------------- |
| Write            | 1 clock cycle |
| Read             | 1 clock cycle |
| Simultaneous R/W | Supported     |

---

## 📊 FIFO Depth

```
Depth = 16 entries
Address width = 4 bits
```

---

## ⚠️ Notes / Current Limitations

This implementation is intentionally simple and educational.

* `fifo_counter` is 4 bits → represents 0–15.
* `buf_full` is checked against value 16 (cannot be represented with 4 bits).
* Intended for functional understanding, not production silicon.
* No overflow / underflow error flags.
* Designed for **single clock domain only** (not async FIFO).

---

## 🔧 Possible Future Improvements

* Parameterize depth and width
* Add overflow / underflow detection
* Improve full detection logic
* Add "almost full / almost empty" flags
* Convert to dual-clock asynchronous FIFO
* Add formal verification or constrained random testing

---

## 📚 Learning Objective

This FIFO demonstrates:

* Circular buffer design
* Pointer-based memory addressing
* Flow control between hardware modules
* Synchronous digital system design

---

## 🛠 Recommended Use

Ideal for:

* FPGA prototyping
* UART buffering
* Teaching RTL concepts
* Small embedded datapaths

## 👨‍💻 Author

Priyansh

B.Tech ECE — Pandit Deendayal Energy University