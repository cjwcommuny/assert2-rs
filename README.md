# assert2

All-purpose [`assert!(...)`](https://docs.rs/assert2/latest/assert2/macro.assert.html) and [`check!(...)`](https://docs.rs/assert2/latest/assert2/macro.check.html) macros, inspired by [Catch2](https://github.com/catchorg/Catch2).
There is also a [`debug_assert!(...)`](https://docs.rs/assert2/latest/assert2/macro.debug_assert.html) macro that is disabled on optimized builds by default.

## Why these macros?

These macros offer some benefits over the assertions from the standard library:
  * The macros parse your expression to detect comparisons and adjust the error message accordingly.
    No more `assert_eq` or `assert_ne`, just write `assert!(1 + 1 == 2)`, or even `assert!(1 + 1 > 1)`!
  * You can test for pattern matches: `assert!(let Err(_) = File::open("/non/existing/file"))`.
  * The `check` macro can be used to perform multiple checks before panicking.
  * The macros provide more information when the assertion fails.
  * Colored failure messages!

The macros also accept additional arguments for a custom message, so it is fully comptabible with `std::assert`.
That means you don't have to worry about overwriting the standard `assert` with `use assert2::assert`.

## Examples

```rust
check!(6 + 1 <= 2 * 3);
```

![Assertion error](https://github.com/de-vri-es/assert2-rs/raw/2db44c46e4580ec87d2881a698815e1ec5fcdf3f/binary-operator.png)

----------

```rust
check!(true && false);
```

![Assertion error](https://github.com/de-vri-es/assert2-rs/raw/2db44c46e4580ec87d2881a698815e1ec5fcdf3f/boolean-expression.png)

----------

```rust
check!(let Ok(_) = File::open("/non/existing/file"));
```

![Assertion error](https://github.com/de-vri-es/assert2-rs/raw/2db44c46e4580ec87d2881a698815e1ec5fcdf3f/pattern-match.png)

## `assert` vs `check`
The crate provides two macros: `check!(...)` and `assert!(...)`.
The main difference is that `check` is really intended for test cases and doesn't immediately panic.
Instead, it will print the assertion error and fail the test.
This allows you to run multiple checks and can help to determine the reason of a test failure more easily.
The `assert` macro on the other hand simply prints the error and panics,
and can be used outside of tests just as well.

Currently, `check` uses a scope guard to delay the panic until the current scope ends.
Ideally, `check` doesn't panic at all, but only signals that a test case has failed.
If this becomes possible in the future, the `check` macro will change, so **you should not rely on `check` to panic**.

## Difference between stable and nightly.
If available, the crate uses the `proc_macro_span` feature to get the original source code.
On stable and beta, it falls back to stringifying the expression.
This makes the output a bit more readable on nightly,
but the differences are limited to the displayed expression.

## Controlling colored output.

Colored output can be controlled using environment variables,
as per the [clicolors spec](https://bixense.com/clicolors/):

 * `CLICOLOR != 0`: ANSI colors are supported and should be used when the program isn't piped.
 * `CLICOLOR == 0`: Don't output ANSI color escape codes.
 * `CLICOLOR_FORCE != 0`: ANSI colors should be enabled no matter what.
