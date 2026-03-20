<!-- Copyright © 2018–2026 Trevor Spiteri -->

<!-- Copying and distribution of this file, with or without
modification, are permitted in any medium without royalty provided the
copyright notice and this notice are preserved. This file is offered
as-is, without any warranty. -->

# Fixed-point numbers

The [*fixed* crate] provides fixed-point numbers.

  * [`FixedI8`] and [`FixedU8`] are eight-bit fixed-point numbers.
  * [`FixedI16`] and [`FixedU16`] are 16-bit fixed-point numbers.
  * [`FixedI32`] and [`FixedU32`] are 32-bit fixed-point numbers.
  * [`FixedI64`] and [`FixedU64`] are 64-bit fixed-point numbers.
  * [`FixedI128`] and [`FixedU128`] are 128-bit fixed-point numbers.

An <i>n</i>-bit fixed-point number has
<span style="white-space: nowrap;"><i>f</i> = `Frac`</span>
fractional bits where
<span style="white-space: nowrap;">0 ≤ <i>f</i> ≤ <i>n</i></span>,
and
<span style="white-space: nowrap;"><i>n</i> &minus; <i>f</i></span>
integer bits. For example, <code>[FixedI32]\<[U24]></code> is a 32-bit signed
fixed-point number with
<span style="white-space: nowrap;"><i>n</i> = 32</span>
total bits,
<span style="white-space: nowrap;"><i>f</i> = 24</span>
fractional bits, and
<span style="white-space: nowrap;"><i>n</i> &minus; <i>f</i> = 8</span>
integer bits. <code>[FixedI32]\<[U0]></code> behaves like [`i32`], and
<code>[FixedU32]\<[U0]></code> behaves like [`u32`].

The difference between any two successive representable numbers is constant
throughout the possible range for a fixed-point number:
<span style="white-space: nowrap;"><i>Δ</i> = 1/2<sup><i>f</i></sup></span>.
When
<span style="white-space: nowrap;"><i>f</i> = 0</span>,
like in <code>[FixedI32]\<[U0]></code>,
<span style="white-space: nowrap;"><i>Δ</i> = 1</span>
because representable numbers are integers, and the difference between two
successive integers is 1. When
<span style="white-space: nowrap;"><i>f</i> = <i>n</i></span>,
<span style="white-space: nowrap;"><i>Δ</i> = 1/2<sup><i>n</i></sup></span>
and the value lies in the range
<span style="white-space:
nowrap;">&minus;0.5 ≤ <i>x</i> < 0.5</span>
for signed numbers like <code>[FixedI32]\<[U32]></code>, and in the range
<span style="white-space: nowrap;">0 ≤ <i>x</i> < 1</span>
for unsigned numbers like <code>[FixedU32]\<[U32]></code>.

The main features are

  * Representation of binary fixed-point numbers up to 128 bits wide.
  * Conversions between fixed-point numbers and numeric primitives.
  * Comparisons between fixed-point numbers and numeric primitives.
  * Parsing from strings in decimal, binary, octal and hexadecimal.
  * Display as decimal, binary, octal and hexadecimal.
  * Arithmetic and logic operations.

This crate does *not* provide decimal fixed-point numbers. For example 0.001
cannot be represented exactly, as it is 1/10<sup>3</sup>. It is binary fractions
like 1/2<sup>4</sup> (0.0625) that can be represented exactly, provided there
are enough fractional bits.

This crate does *not* provide general analytic functions.

  * No algebraic functions are provided, for example no `pow`.
  * No trigonometric functions are provided, for example no `sin` or `cos`.
  * No other transcendental functions are provided, for example no `log` or
    `exp`.

These functions are not provided because different implementations can have
different trade-offs, for example trading some correctness for speed.
Implementations can be provided in other crates.

  * The [*cordic* crate] provides various functions implemented using the
    [CORDIC] algorithm.

The conversions supported cover the following cases.

  * Infallible lossless conversions between fixed-point numbers and numeric
    primitives are provided using [`From`] and [`Into`]. These never fail
    (infallible) and do not lose any bits (lossless).
  * Infallible lossy conversions between fixed-point numbers and numeric
    primitives are provided using the [`LossyFrom`] and [`LossyInto`] traits.
    The source can have more fractional bits than the destination.
  * Checked lossless conversions between fixed-point numbers and numeric
    primitives are provided using the [`LosslessTryFrom`] and
    [`LosslessTryInto`] traits. The source cannot have more fractional bits than
    the destination.
  * Checked conversions between fixed-point numbers and numeric primitives are
    provided using the [`FromFixed`] and [`ToFixed`] traits, or using the
    [`from_num`] and [`to_num`] methods and [their checked
    versions][`checked_from_num`].
  * Additionally, [`az`] casts are implemented for conversion between
    fixed-point numbers and numeric primitives.
  * Fixed-point numbers can be parsed from decimal strings using [`FromStr`],
    and from binary, octal and hexadecimal strings using the
    [`from_str_binary`], [`from_str_octal`] and [`from_str_hex`] methods. The
    result is rounded to the nearest, with ties rounded to even.
  * Fixed-point numbers can be converted to strings using [`Display`],
    [`Binary`], [`Octal`], [`LowerHex`], [`UpperHex`], [`LowerExp`] and
    [`UpperExp`]. The output is rounded to the nearest, with ties rounded to
    even.
  * All fixed-point numbers are plain old data, so [`bytemuck`] bit casting
    conversions can be used.

## What’s new

### Version 1.31.0 news (2026-03-20)

  * The crate now requires rustc version 1.93.0 or later.
  * The [`unchecked_neg`][f-un-1-31] method was added to all signed fixed-point
    numbers and to the [`FixedSigned`][tfs-1-31] trait.
  * The following methods were added to all fixed-point numbers and to the
    [`Fixed`][tf-1-31] trait:
      * [`unchecked_shl`][f-ushl-1-31], [`unchecked_shr`][f-ushr-1-31]

[f-un-1-31]: https://docs.rs/fixed/~1.31/fixed/struct.FixedI32.html#method.unchecked_neg
[f-ushl-1-31]: https://docs.rs/fixed/~1.31/fixed/struct.FixedI32.html#method.unchecked_shl
[f-ushr-1-31]: https://docs.rs/fixed/~1.31/fixed/struct.FixedI32.html#method.unchecked_shr
[tf-1-31]: https://docs.rs/fixed/~1.31/fixed/traits/trait.Fixed.html
[tfs-1-31]: https://docs.rs/fixed/~1.31/fixed/traits/trait.FixedSigned.html

### Other releases

Details on other releases can be found in [*RELEASES.md*].

[*RELEASES.md*]: https://gitlab.com/tspiteri/fixed/blob/master/RELEASES.md

## Quick examples

```rust
use fixed::types::I20F12;

// 19/3 = 6 1/3
let six_and_third = I20F12::from_num(19) / 3;
// four decimal digits for 12 binary digits
assert_eq!(six_and_third.to_string(), "6.3333");
// find the ceil and convert to i32
assert_eq!(six_and_third.ceil().to_num::<i32>(), 7);
// we can also compare directly to integers
assert_eq!(six_and_third.ceil(), 7);
```

The type [`I20F12`] is a 32-bit fixed-point signed number with 20 integer bits
and 12 fractional bits. It is an alias to <code>[FixedI32]\<[U12]></code>. The
unsigned counterpart would be [`U20F12`]. Aliases are provided for all
combinations of integer and fractional bits adding up to a total of eight, 16,
32, 64 or 128 bits.

```rust
use fixed::types::{I4F4, I4F12};

// -8 ≤ I4F4 < 8 with steps of 1/16 (~0.06)
let a = I4F4::from_num(1);
// multiplication and division by integers are possible
let ans1 = a / 5 * 17;
// 1 / 5 × 17 = 3 2/5 (3.4), but we get 3 3/16 (~3.2)
assert_eq!(ans1, I4F4::from_bits((3 << 4) + 3));
assert_eq!(ans1.to_string(), "3.2");

// -8 ≤ I4F12 < 8 with steps of 1/4096 (~0.0002)
let wider_a = I4F12::from(a);
let wider_ans = wider_a / 5 * 17;
let ans2 = I4F4::from_num(wider_ans);
// now the answer is the much closer 3 6/16 (~3.4)
assert_eq!(ans2, I4F4::from_bits((3 << 4) + 6));
assert_eq!(ans2.to_string(), "3.4");
```

The second example shows some precision and conversion issues. The low precision
of `a` means that `a / 5` is 3⁄16 instead of 1⁄5, leading to an inaccurate
result `ans1` = 3 3⁄16 (~3.2). With a higher precision, we get `wider_a / 5`
equal to 819⁄4096, leading to a more accurate intermediate result `wider_ans` =
3 1635⁄4096. When we convert back to four fractional bits, we get `ans2` = 3
6⁄16 (~3.4).

Note that we can convert from [`I4F4`] to [`I4F12`] using [`From`], as the
target type has the same number of integer bits and a larger number of
fractional bits. Converting from [`I4F12`] to [`I4F4`] cannot use [`From`] as we
have less fractional bits, so we use [`from_num`] instead.

## Comparing fixed-point numbers

With floating-point numbers like [`f32`], checking two numbers for equality can
require calculating both the absolute difference
(<span style="white-space: nowrap;">|<i>a</i> &minus; <i>b</i>|</span>)
and the relative difference
(<span style="white-space: nowrap;">|<i>a</i> &minus; <i>b</i>| / <i>a</i></span>)
to account for rounding errors and different precisions.

With fixed-point numbers, this is simpler. Since the point is fixed, and very
tiny numbers simply underflow to zero, it is usually enough to just check that
the absolute difference between two numbers is smaller than a specific
threshold.

You can do this using [`saturating_dist`] and [`DELTA`]. For example to ensure
that the difference between two numbers is less than
<span style="white-space: nowrap;">4 × <i>Δ</i></span>:

```rust
use fixed::types::I20F12;

let a = I20F12::from_num(1.5);
// b is not exactly 1.5 because of rounding errors
let b = a.sqrt() * a.sqrt();
// check for equality by comparing to 4 × Δ
assert!(a.saturating_dist(b) < 4 * I20F12::DELTA);
```

Note that [`saturating_dist`] was used instead of [`dist`] to avoid overflow if
<i>a</i> and <i>b</i> are very different, for example [`MIN`] and [`MAX`].

## Writing fixed-point constants and values literally

The [`lit`] method, which is available as a `const` function, can be used to
parse literals. It supports
  * underscores as separators;
  * prefixes “`0b`”, “`0o`” and “`0x`” for binary, octal and hexadecimal
    numbers;
  * an optional decimal exponent with separator “`e`” or “`E`” for decimal,
    binary and octal numbers, or with separator “`@`” for all supported radices
    including hexadecimal.

```rust
use fixed::types::I16F16;

// 0.1275e2 is 12.75
const TWELVE_POINT_75: I16F16 = I16F16::lit("0.127_5e2");
// 1.8 hexadecimal is 1.5 decimal, and 18@-1 is 1.8
const ONE_POINT_5: I16F16 = I16F16::lit("0x_18@-1");
// 12.75 + 1.5 = 14.25
let sum = TWELVE_POINT_75 + ONE_POINT_5;
assert_eq!(sum, 14.25);
```

## Using the *fixed* crate

The *fixed* crate is available on [crates.io][*fixed* crate]. To use it in your
crate, add it as a dependency inside [*Cargo.toml*]:

```toml
[dependencies]
fixed = "1.31"
```

The *fixed* crate requires rustc version 1.93.0 or later.

## Optional features

The *fixed* crate has these optional feature:

 1. `arbitrary`, disabled by default. This provides the generation of arbitrary
    fixed-point numbers from raw, unstructured data. This feature requires the
    [*arbitrary* crate].
 2. `borsh`, disabled by default. This implements serialization and
    deserialization using the [*borsh* crate].
 3. `compat-readonly-static`, disabled by default. This enables compatibility
    with platforms where statics are read-only by disabling caching of
    formatting flags in atomic statics. Note that this may incur a small
    performance overhead when formatting many numbers using the hexadecimal
    debug specifiers `{:x?}` or `{:X?}`.
 4. `defmt`, disabled by default. This implements formatting using the [*defmt*
    crate]. Note that the implementation delegates to the [`Display`] trait.
    This means formatting is performed on the device and the resulting string is
    sent over the wire. To minimize log bandwidth, you can transmit the integer
    representation, for example `value.to_bits()`, and then decode on the host.
 5. `serde`, disabled by default. This provides serialization support for the
    fixed-point types. This feature requires the [*serde* crate].
 6. `std`, disabled by default. This is for features that are not possible under
    `no_std`: currently this is only required for the `serde-str` feature.
 7. `serde-str`, disabled by default. Fixed-point numbers are serialized as
    strings showing the value when using human-readable formats. This feature
    requires the `serde` and the `std` optional features. **Warning:** numbers
    serialized when this feature is enabled cannot be deserialized when this
    feature is disabled, and vice versa.

To enable features, you can add the dependency like this to [*Cargo.toml*]:

```toml
[dependencies.fixed]
features = ["serde"]
version = "1.31"
```

## Experimental optional features

It is not considered a breaking change if the following experimental features
are removed. The removal of experimental features would however require a minor
version bump. Similarly, on a minor version bump, optional dependencies can be
updated to an incompatible newer version.

 1. `num-traits`, disabled by default. This implements some traits from the
    [*num-traits* crate]. (The plan is to promote this to an optional feature
    once the [*num-traits* crate] reaches version 1.0.0.)
 2. `nightly-float`, disabled by default. This requires the nightly compiler,
    and implements conversions and comparisons with the experimental [`f16`] and
    [`f128`] primitives. (The plan is to always implement the conversions and
    comparisons and remove this experimental feature once the primitives are
    stabilized.)

[`f128`]: https://doc.rust-lang.org/nightly/std/primitive.f128.html
[`f16`]: https://doc.rust-lang.org/nightly/std/primitive.f16.html

## Deprecated optional features

The following optional features are deprecated and will be removed in the next
major version of the crate.

 1. `az`, has no effect. Previously required for the [`az`] cast traits. Now
    these cast traits are always provided.
 2. `f16`, has no effect. Previously required for conversion to/from
    <code>[half]::[f16][half::f16]</code> and
    <code>[half]::[bf16][half::bf16]</code>. Now these conversions are always
    provided.

## License

This crate is free software: you can redistribute it and/or modify it under the
terms of either

  * the [Apache License, Version 2.0][LICENSE-APACHE] or
  * the [MIT License][LICENSE-MIT]

at your option.

### Contribution

Unless you explicitly state otherwise, any contribution intentionally submitted
for inclusion in the work by you, as defined in the Apache License, Version 2.0,
shall be dual licensed as above, without any additional terms or conditions.

[*Cargo.toml*]: https://doc.rust-lang.org/cargo/guide/dependencies.html
[*arbitrary* crate]: https://crates.io/crates/arbitrary
[*borsh* crate]: https://crates.io/crates/borsh
[*cordic* crate]: https://crates.io/crates/cordic
[*defmt* crate]: https://crates.io/crates/defmt
[*fixed* crate]: https://crates.io/crates/fixed
[*half* crate]: https://crates.io/crates/half
[*num-traits* crate]: https://crates.io/crates/num-traits
[*serde* crate]: https://crates.io/crates/serde
[*typenum* crate]: https://crates.io/crates/typenum
[CORDIC]: https://en.wikipedia.org/wiki/CORDIC
[FixedI32]: https://docs.rs/fixed/~1.31/fixed/struct.FixedI32.html
[FixedU32]: https://docs.rs/fixed/~1.31/fixed/struct.FixedU32.html
[LICENSE-APACHE]: https://www.apache.org/licenses/LICENSE-2.0
[LICENSE-MIT]: https://opensource.org/licenses/MIT
[U0]: https://docs.rs/fixed/~1.31/fixed/types/extra/type.U0.html
[U12]: https://docs.rs/fixed/~1.31/fixed/types/extra/type.U12.html
[U24]: https://docs.rs/fixed/~1.31/fixed/types/extra/type.U24.html
[U32]: https://docs.rs/fixed/~1.31/fixed/types/extra/type.U32.html
[`Binary`]: https://doc.rust-lang.org/nightly/core/fmt/trait.Binary.html
[`DELTA`]: https://docs.rs/fixed/~1.31/fixed/struct.FixedI32.html#associatedconstant.DELTA
[`Display`]: https://doc.rust-lang.org/nightly/core/fmt/trait.Display.html
[`Error`]: https://doc.rust-lang.org/nightly/std/error/trait.Error.html
[`FixedI128`]: https://docs.rs/fixed/~1.31/fixed/struct.FixedI128.html
[`FixedI16`]: https://docs.rs/fixed/~1.31/fixed/struct.FixedI16.html
[`FixedI32`]: https://docs.rs/fixed/~1.31/fixed/struct.FixedI32.html
[`FixedI64`]: https://docs.rs/fixed/~1.31/fixed/struct.FixedI64.html
[`FixedI8`]: https://docs.rs/fixed/~1.31/fixed/struct.FixedI8.html
[`FixedU128`]: https://docs.rs/fixed/~1.31/fixed/struct.FixedU128.html
[`FixedU16`]: https://docs.rs/fixed/~1.31/fixed/struct.FixedU16.html
[`FixedU32`]: https://docs.rs/fixed/~1.31/fixed/struct.FixedU32.html
[`FixedU64`]: https://docs.rs/fixed/~1.31/fixed/struct.FixedU64.html
[`FixedU8`]: https://docs.rs/fixed/~1.31/fixed/struct.FixedU8.html
[`FromFixed`]: https://docs.rs/fixed/~1.31/fixed/traits/trait.FromFixed.html
[`FromStr`]: https://doc.rust-lang.org/nightly/core/str/trait.FromStr.html
[`From`]: https://doc.rust-lang.org/nightly/core/convert/trait.From.html
[`I20F12`]: https://docs.rs/fixed/~1.31/fixed/types/type.I20F12.html
[`I4F12`]: https://docs.rs/fixed/~1.31/fixed/types/type.I4F12.html
[`I4F4`]: https://docs.rs/fixed/~1.31/fixed/types/type.I4F4.html
[`Into`]: https://doc.rust-lang.org/nightly/core/convert/trait.Into.html
[`LosslessTryFrom`]: https://docs.rs/fixed/~1.31/fixed/traits/trait.LosslessTryFrom.html
[`LosslessTryInto`]: https://docs.rs/fixed/~1.31/fixed/traits/trait.LosslessTryInto.html
[`LossyFrom`]: https://docs.rs/fixed/~1.31/fixed/traits/trait.LossyFrom.html
[`LossyInto`]: https://docs.rs/fixed/~1.31/fixed/traits/trait.LossyInto.html
[`LowerExp`]: https://doc.rust-lang.org/nightly/core/fmt/trait.LowerExp.html
[`LowerHex`]: https://doc.rust-lang.org/nightly/core/fmt/trait.LowerHex.html
[`MAX`]: https://docs.rs/fixed/~1.31/fixed/struct.FixedI32.html#associatedconstant.MAX
[`MIN`]: https://docs.rs/fixed/~1.31/fixed/struct.FixedI32.html#associatedconstant.MIN
[`Octal`]: https://doc.rust-lang.org/nightly/core/fmt/trait.Octal.html
[`ToFixed`]: https://docs.rs/fixed/~1.31/fixed/traits/trait.ToFixed.html
[`U20F12`]: https://docs.rs/fixed/~1.31/fixed/types/type.U20F12.html
[`UpperExp`]: https://doc.rust-lang.org/nightly/core/fmt/trait.UpperExp.html
[`UpperHex`]: https://doc.rust-lang.org/nightly/core/fmt/trait.UpperHex.html
[`az`]: https://docs.rs/az/^1/az/index.html
[`bytemuck`]: https://docs.rs/bytemuck/^1/bytemuck/index.html
[`checked_from_num`]: https://docs.rs/fixed/~1.31/fixed/struct.FixedI32.html#method.checked_from_num
[`dist`]: https://docs.rs/fixed/~1.31/fixed/struct.FixedI32.html#method.dist
[`f32`]: https://doc.rust-lang.org/nightly/core/primitive.f32.html
[`from_num`]: https://docs.rs/fixed/~1.31/fixed/struct.FixedI32.html#method.from_num
[`from_str_binary`]: https://docs.rs/fixed/~1.31/fixed/struct.FixedI32.html#method.from_str_binary
[`from_str_hex`]: https://docs.rs/fixed/~1.31/fixed/struct.FixedI32.html#method.from_str_hex
[`from_str_octal`]: https://docs.rs/fixed/~1.31/fixed/struct.FixedI32.html#method.from_str_octal
[`i32`]: https://doc.rust-lang.org/nightly/core/primitive.i32.html
[`lit`]: https://docs.rs/fixed/~1.31/fixed/struct.FixedI32.html#method.lit
[`saturating_dist`]: https://docs.rs/fixed/~1.31/fixed/struct.FixedI32.html#method.saturating_dist
[`to_num`]: https://docs.rs/fixed/~1.31/fixed/struct.FixedI32.html#method.to_num
[`u32`]: https://doc.rust-lang.org/nightly/core/primitive.u32.html
[half::bf16]: https://docs.rs/half/^2/half/struct.bf16.html
[half::f16]: https://docs.rs/half/^2/half/struct.f16.html
[half]: https://docs.rs/half/^2/half/index.html
