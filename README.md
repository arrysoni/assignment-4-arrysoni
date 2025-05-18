# 🧠 CMPSC 311 – Assignment 4: mdadm Caching System (Fall 2024)

This repository contains my solution for **Assignment 4** of CMPSC 311: Systems Programming. In this lab, I extended the `mdadm` linear storage system by implementing a **block-level cache** to optimize read/write efficiency and minimize direct disk access.

---

## 📦 Objectives

- ✅ Implement a working **cache system** (`cache.c` and `cache.h`)
- ✅ Modify `mdadm_read()` and `mdadm_write()` to use cache logic
- ✅ Use unit tests and **trace files** to verify correctness and performance
- ✅ Report cache hit rates and total operation costs
- ✅ Bonus: Implement honors features such as **Clock Replacement Algorithm** or **Multi-Level Caching**

---

## 🧩 Cache Features Implemented

### 1. `cache_create(int num_entries)`
Dynamically allocates the cache array based on size and initializes control variables. Fails if called twice without `cache_destroy()`.

### 2. `cache_destroy(void)`
Frees memory and resets global cache state. Fails if no cache was created before.

### 3. `cache_lookup(int disk_num, int block_num, uint8_t *buf)`
Returns a copy of the block if it exists in the cache. Increments `num_queries`, and on a hit, increments `num_hits` and updates `clock_accesses`.

### 4. `cache_insert(int disk_num, int block_num, uint8_t *buf)`
Inserts a block into the cache. If the cache is full, applies **Most Recently Used (MRU)** replacement.

### 5. `cache_update(int disk_num, int block_num, const uint8_t *buf)`
Updates an existing block in the cache with new data and access timestamp.

### 6. `cache_enabled(void)`
Returns `true` if the cache is currently initialized and active.

### 7. `cache_resize(int num_entries)`
Dynamically resizes the cache using new entry size.

---

## 🚀 Caching in Read/Write Operations

After implementing the cache core, I integrated caching into:
- `mdadm_read()` – Prioritizes reading from cache. Falls back to disk on cache miss.
- `mdadm_write()` – Implements **write-through**: updates cache and writes to disk.

---

## 🧪 Testing Instructions

### 🔹 Unit Tests
```bash
make clean
make
./tester



./tester -w traces/random-input         # No cache
./tester -w traces/random-input -s 1024 # With 1024-entry cache
./tester -w traces/random-input -s 4096 # Max cache


diff -u my-output traces/random-expected-output
