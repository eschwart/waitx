# waitx

**waitx** is a minimal synchronization utility for signaling readiness between threads using a lightweight flag, a condition variable, and optional backoff. It provides a flexible alternative to channels or more heavyweight synchronization primitives when you just need to wait for a single "ready" event.

## Features

- **Waiter**: blocks until a flag is set, using backoff + condition variable.
- **Notifier**: sets the flag and notifies a waiting thread.
- **Setter**: sets the flag without notifying.
- **Spectator**: reads the state without modifying it.
- Lightweight and `no_std`-compatible (with `alloc`).
- Built on `parking_lot` and `crossbeam-utils`.

## Usage

Add to your `Cargo.toml`:

```toml
[dependencies]
waitx = "0.1"
```

## Example
```rust
use std::thread;
use waitx::Waiter;

let waiter = Waiter::default();
let notifier = waiter.notifier();

let handle = thread::spawn(move || {
    println!("Worker waiting...");
    waiter.wait();
    println!("Worker resumed!");
});

std::thread::sleep(std::time::Duration::from_millis(100));
notifier.notify();
handle.join().unwrap();
```

## When to Use
- Use waitx when you want a simple signaling mechanism:
- One thread signals readiness, another waits.
- A flag is reused multiple times with resets.
- You want fine control over notification vs. just setting state.

## Crate Goals
- Minimal API
- Efficient signaling
- No channels or locks unless needed
- Readable and ergonomic code