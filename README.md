# simfix

A modern C++ FIX/FAST protocol library built for the wire. Zero heap allocation on the hot path. SIMD-accelerated parsing. Session management that doesn't make you choose between performance and correctness.

---

## The problem

Every serious FIX library in existence makes at least one of the following mistakes:

- Allocates on every message (QuickFIX)
- Punts on session management entirely (hffix)
- Doesn't compile past C++03 without warnings
- Has no benchmark harness, so you have no idea how fast it actually is

simfix fixes all of them.

---

## Design principles

**Zero allocation on the hot path.** Messages are parsed directly from I/O buffers. No intermediate objects. No heap. Stack only.

**SIMD-first parsing.** Tag scanning uses AVX2/AVX-512 for bulk delimiter search. The theoretical throughput ceiling on modern hardware is treated as a target, not a curiosity.

**Modern C++23.** `std::span`, `std::string_view`, concepts for field type constraints. No legacy workarounds.

**io_uring native.** The session layer is built on `io_uring` with zero-copy send (`IORING_OP_SEND_ZC`). No epoll. No blocking syscalls on the hot path.

**FIX and FAST.** Both tagvalue FIX (classic) and FAST (FIX Adapted for STreaming) are first-class. FAST delta decoding and presence map handling included.

**Honest benchmarks.** Every claim is backed by a reproducible benchmark against real CME market data traces. Numbers are in the repo, not just the README.

---

## Status

Early development. Not production ready.

- [ ] Tagvalue FIX parser (SIMD)
- [ ] FIX message writer (zero-copy)
- [ ] Session management (io_uring)
- [ ] FAST decoder (delta + presence map)
- [ ] Benchmark harness
- [ ] CME historical data replay

---

## Requirements

- C++23 compiler (GCC 13+, Clang 16+)
- Linux kernel 5.19+ (for io_uring zero-copy send)
- AVX2 support (AVX-512 optional, detected at runtime)

---

## Building

```sh
git clone https://github.com/averyw/simfix
cd simfix
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build
```

Run benchmarks:

```sh
./build/bench/simfix_bench
```

---

## Prior art

[hffix](https://github.com/jamesdbrock/hffix) — the closest thing to what simfix is trying to be. Great zero-allocation design, C++98, parser only. simfix picks up where it stops.

[QuickFIX](https://github.com/quickfix/quickfix) — the industry default. Full session management. Allocates everywhere, threading model is not great.

[simdjson](https://github.com/simdjson/simdjson) — not FIX, but the right mental model for what SIMD-first protocol parsing looks like.

---

## License

MIT
