# Aphelo Client

![PyPI](https://img.shields.io/pypi/v/aphelo-client)
![Python](https://img.shields.io/pypi/pyversions/aphelo-client)
<!-- ![License](https://img.shields.io/github/license/pranitgarje/aphelo-client) -->
<!-- ![Downloads](https://img.shields.io/pypi/dm/aphelo-client) -->

The official **Python SDK** for **Aphelo**, a blazing-fast, high-performance **in-memory caching database written in C++**.

Aphelo uses a custom **Tag-Length-Value (TLV) binary protocol** designed for **extreme throughput** through command pipelining and low-overhead memory operations.

---

# 🚀 Features

- ⚡ **Ultra-fast in-memory database**
- 🔗 **Custom TLV Binary Protocol**
- 📦 **O(1) String Operations**
- ⏱ **Millisecond TTL Expiration**
- 🧠 **Min-Heap Based Cache Eviction**
- 🏆 **Dual-Intrusive Sorted Sets (AVL Tree + Hash Table)**
- 🔁 **Command Pipelining for Maximum Throughput**
- 🐍 **Native Python SDK**

---

# 📦 Installation

Install directly from **PyPI**:

```bash
pip install aphelo-client
```

---

# 🐳 Running the Aphelo Database

Before connecting the client, ensure your **Aphelo database server** is running.

The easiest way is via **Docker**:

```bash
docker run -d -p 1234:1234 pranitgarje/aphelo-db
```

This will start the database server on:

```
localhost:1234
```

---

# 🔌 Connecting to the Database

```python
from aphelo import ApheloClient, ApheloError

# Connect to Aphelo
db = ApheloClient(host="127.0.0.1", port=1234)
```

Default connection:

```
127.0.0.1:1234
```

---

# 📚 Core Commands

## Key-Value Operations

Aphelo provides **O(1)** key-value storage using a custom **FNV-1a progressively rehashing dictionary**.

```python
# Store a value
db.set("username:101", "Pranit")

# Retrieve value
user = db.get("username:101")
print(user)

# Delete a key
db.delete("username:101")
```

Output:

```
Pranit
```

---

# ⏳ Cache Expiration (TTL)

Keys can be assigned **automatic expiration times in milliseconds**.

Aphelo manages expiration using an **array-backed Min-Heap**, allowing:

- **O(1)** lookup for nearest expiration
- **background eviction**

```python
import time

# Store temporary session
db.set("session_token", "abc123xyz")

# Expire in 2000 ms
db.pexpire("session_token", 2000)

print(db.get("session_token"))
# abc123xyz

time.sleep(2.5)

print(db.get("session_token"))
# None
```

---

# 🏆 Sorted Sets (Leaderboards)

Aphelo includes **high-performance sorted sets** using a **Dual-Intrusive Data Structure**:

- **AVL Tree → O(log N) ranking**
- **Hash Table → O(1) lookups**

Perfect for:

- leaderboards
- ranking systems
- score tracking

```python
db.zadd("game_leaderboard", 150.5, "Alice")
db.zadd("game_leaderboard", 200.0, "Bob")
db.zadd("game_leaderboard", 99.9, "Charlie")
```

---

# ⚠️ Error Handling

If a command fails or is malformed, the client raises an **ApheloError**.

```python
from aphelo import ApheloClient, ApheloError

db = ApheloClient()

try:
    db._execute("zadd", "leaderboard", "Alice")

except ApheloError as e:
    print(f"Database Error: {e}")
```

---

# ⚙️ Architecture

Aphelo is designed for **extreme performance** and low memory overhead.

Core components include:

| Component | Technology |
|--------|--------|
| Protocol | Custom TLV Binary Protocol |
| Key Storage | FNV-1a Hash Dictionary |
| TTL Expiration | Array-backed Min Heap |
| Sorted Sets | AVL Tree + Hash Table |
| Network Layer | Persistent TCP |

---
# 📊 Performance Benchmarks

To measure **raw throughput**, a custom **C++ benchmarking tool** was built to test the server's **command pipelining capabilities** over a TCP loopback interface (`127.0.0.1`).

The server successfully processes **high-volume pipelined commands with near-zero overhead**, competing with standard out-of-the-box instances of the **Redis database**.

---

## Test Configuration

**Benchmark Setup**

- **100,000 Pipelined Commands**
- **Custom TLV Binary Protocol**
- **TCP Loopback Interface (127.0.0.1)**
- **Single-threaded event loop**

---

## Benchmark Results

| Command | Internal Operation | Throughput (RPS) | Execution Time |
|--------|--------|--------|--------|
| **SET** | `O(1)` Hash Table Write | **172,716 RPS** | ~0.57 seconds |
| **GET** | `O(1)` Hash Table Read | **137,859 RPS** | ~0.72 seconds |
| **ZADD** | `O(log N)` AVL Tree + Hash Insert | **111,037 RPS** | ~0.90 seconds |

---

## Interpretation

These benchmarks demonstrate the efficiency of Aphelo's architecture:

- ⚡ **Custom `O(1)` dictionary implementation**
- 🧠 **Zero-allocation memory management**
- 🔄 **Command pipelining**
- 📡 **Non-blocking I/O multiplexing**
- 🌲 **Dual-intrusive sorted sets (AVL Tree + Hash Table)**

Even with a **single-threaded event loop**, Aphelo achieves **100k+ operations per second** on standard consumer hardware.

---

## Notes

> These benchmarks reflect **single-threaded performance** and are designed to measure **raw server throughput**, not distributed cluster performance.

Actual performance may vary depending on:

- hardware
- network conditions
- dataset size
- workload patterns

# 🧪 Example Use Cases

Aphelo works well for:

- API caching
- session storage
- leaderboards
- rate limiting
- distributed locks
- temporary tokens

---

# 📜 License

MIT License

---

# 👨‍💻 Author

**Pranit Garje**

GitHub:  
https://github.com/pranitgarje

---

# ⭐ Support

If you find Aphelo useful, consider giving the repository a **star ⭐**.