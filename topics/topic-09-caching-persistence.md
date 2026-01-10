## **Topic 9 - Caching & Persistence (memory as a strategy, not a reflex)**

### Caching
- Caching is a **memory of computation**, not a speed trick
- Spark is lazy; without cache it **recomputes full lineage** for every action
- Cache tells Spark: **“remember this result once computed”**

### What caching actually does
- Stores **partition outputs** of a dataset after the first action
- **Cuts the DAG** so upstream work is not repeated
- Future actions **reuse cached partitions** instead of recomputing

When you **cache** a dataset, Spark:
- keeps the result of transformations
- stores partitions in executor memory
- reuses them across actions

Without caching:
- every action recomputes from source
- DAG runs again
- CPU and I/O repeat
---
### When caching makes sense
>Caching makes sense **only when it prevents expensive recomputation that would otherwise happen again**.

Strong signals that caching is worth it
- The same dataset is **used multiple times**
- Upstream work is **expensive** (joins, shuffles, UDFs)
- The dataset is **bounded and manageable in size**
- You control **when to unpersist**
- The workload is **iterative or exploratory**

Typical good cases:
- Multiple aggregations on the same joined data
- Repeated `count`, `show`, and `write`
- ML training loops
- Interactive analytics / notebooks

### Where caching fits in a pipeline
Caching is most valuable:
- **After joins**
- **After heavy filters**
- **After expensive transformations**
- **Before multiple downstream actions**

Caching too early (raw data) or too late (final output) usually wastes memory.

---
## Storage Levels

- **MEMORY_ONLY**  - Fastest access, but evicted data is recomputed if memory runs out.
- **MEMORY_AND_DISK**  - Try memory first, spill to disk if needed — the safest default.
- **DISK_ONLY**  - No memory pressure, but every read hits disk and is slow.
- **MEMORY_ONLY_SER**  - Uses less memory via serialization, trades CPU for space.
- **MEMORY_AND_DISK_SER**  - Serialized and spill-safe — balanced for large datasets.
- **OFF_HEAP**  - Stores data outside JVM heap to reduce GC, adds complexity.

---
### Cache vs Persist
- **Cache** = convenience
- **Persist** = explicit control over storage level
- Both cut lineage; only storage guarantees differ.
### `cache()` - in memory
- A **convenience shortcut**
- Equivalent to: `persist(MEMORY_ONLY)`
- Uses memory only
- Fast, but risky for larger datasets
- Best for small, reusable datasets
- Use **`cache()`**  
	When:
	- dataset is small
	- memory is plentiful
	- you want quick iteration

### `persist()` - where you want
- The **explicit, controlled version**
- Lets you choose **how and where** data is stored
- Supports memory, disk, serialization, off-heap
- Safer and more production-friendly
- Use **`persist()`**  
	When:
	- dataset is large
	- recomputation is expensive
	- you care about stability
	- you’re in production
---
## What is `unpersist`?
> **`unpersist` tells Spark: “You can forget this cached data now.”**

It:
- removes cached partitions from memory and/or disk
- frees executor resources
- does **not** delete source data
- does **not** break your code

If you cached something, **you own it**.  `unpersist` is how you clean up.

---

![](images/9.Caching&Persistence.png)

---
**Mental Model**
- Cache trades memory for recomputation
- Cache only reused data
- Memory is finite and shared
- Unpersist aggressively

Here’s the model that never fails:

> **Spark will happily recompute anything you don’t explicitly tell it to keep.**
> 
> **Caching is telling Spark what is worth remembering.**
