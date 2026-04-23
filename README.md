# Shamir's Secret Sharing - Polynomial Root Finder

> **Archived.** Submitted as a take-home exam for a technical interview.

Reconstructs the secret constant `c` of a polynomial using **Lagrange Interpolation** over a set of encoded (x, y) share points - mirroring the core mechanic of [Shamir's Secret Sharing](https://en.wikipedia.org/wiki/Shamir%27s_secret_sharing).

---

## Problem

Given `n` share points where each y-value is encoded in an arbitrary base (binary, octal, hex, etc.), and a threshold `k`:

1. Decode all y-values to base 10.
2. Find the polynomial's **constant term** `f(0)` - the secret.
3. Identify any **imposter points** that don't lie on the true polynomial.

The input guarantees a degree `k-1` polynomial, so any subset of `k` valid points should yield the same `f(0)`.

---

## How It Works

```
JSON input
  └─ keys: { n, k }          ← total points, minimum threshold
  └─ "1": { base, value }    ← share point at x=1
  └─ "2": { base, value }    ← share point at x=2
  ...
```

**Algorithm (`main.js`):**

1. **Base conversion** - each `value` is parsed from its declared `base` into a decimal integer.
2. **Point extraction** - builds `(x, y)` pairs from all `n` share entries.
3. **Combination sweep** - generates every `C(n, k)` subset of points.
4. **Lagrange Interpolation** - evaluates `f(0)` for each subset.
5. **Majority vote** - the constant that appears `≥ k` times across combinations is the true secret; subsets that diverge flag their outlier point as an imposter.

---

## Test Cases

| File | n | k | Notes |
|------|---|---|-------|
| `data1.json` | 4 | 3 | Clean dataset - all points valid |
| `data2.json` | 9 | 6 | Contains imposter points that don't fit the polynomial |

---

## Running

No dependencies required - plain Node.js.

```bash
node main.js
```

Output is an array of the two recovered secrets:

```
[ <secret for data1>, <secret for data2> ]
```

---

## Project Structure

```
.
├── main.js       # Core logic: base conversion, interpolation, secret recovery
├── data1.json    # Test case 1 (n=4, k=3)
└── data2.json    # Test case 2 (n=9, k=6)
```

---

## Key Concepts

- **Lagrange Interpolation** - reconstructs a polynomial of degree `k-1` from exactly `k` points and evaluates it at `x=0` to recover the constant term.
- **Shamir's Secret Sharing** - a cryptographic scheme that splits a secret into `n` shares where any `k` shares suffice to reconstruct it, but fewer than `k` reveal nothing.
- **Imposter detection** - by checking consistency across all `C(n, k)` combinations, outlier points (those that yield a different `f(0)`) can be identified.
