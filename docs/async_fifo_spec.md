# Async FIFO Functional Specification

**Status:** Draft  
**Version:** 0.1

## 1. Purpose

The Async FIFO transfers data from a write clock domain to a read clock domain. The two clock domains do not require a fixed frequency or phase relationship.

The FIFO shall preserve the order of accepted writes. Each accepted write shall be returned exactly once by an accepted read, provided no reset occurs between the write and the read.

A write request while the FIFO is full shall not add or overwrite data. A read request while the FIFO is empty shall not remove data.

The read output is registered. It shall update after an accepted read and shall not preview the oldest unread word.

A reset asserted in either domain is intended to flush all FIFO data and reset both domains. Reset recovery uses `w_ready` and `r_ready`; the detailed recovery behavior is specified in the reset requirements.

## 2. Parameters

| Parameter | Default | Supported values | Description |
|---|---:|---|---|
| `DATA_WIDTH` | 8 | Positive integer | Number of bits in each FIFO word |
| `DEPTH` | 16 | Power of two; minimum value TBD | Number of words the FIFO can store |

The FIFO shall store up to `DEPTH` unread words.

Unsupported parameter values shall cause an elaboration-time or simulation-start error.

**Open item:** Decide the minimum supported `DEPTH`.

## 3. Interface

| Signal | Direction | Description |
|---|---|---|
| `wclk` | Input | Write-domain clock |
| `wrst_n` | Input | Active-low reset input for the write domain |
| `w_en` | Input | Write request |
| `w_data[DATA_WIDTH-1:0]` | Input | Data presented for writing |
| `full` | Output | Indicates that the FIFO is currently blocking write requests |
| `w_ready` | Output | Indicates that the write side has completed reset recovery |
| `rclk` | Input | Read-domain clock |
| `rrst_n` | Input | Active-low reset input for the read domain |
| `r_en` | Input | Read request |
| `r_data[DATA_WIDTH-1:0]` | Output | Registered data from the most recently accepted read |
| `empty` | Output | Indicates that the FIFO is currently blocking read requests |
| `r_ready` | Output | Indicates that the read side has completed reset recovery |

Write-side inputs shall meet timing requirements relative to `wclk`. Read-side inputs shall meet timing requirements relative to `rclk`.

There is no required frequency or phase relationship between `wclk` and `rclk`.