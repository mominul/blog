+++
title = "Setting up Github Actions for repositories targeting various version of Rust"
date = 2023-12-22
+++

It is crucial to check if your project is building and executing tests with every commit, which can be easily achievable by using the Github Actions CI (Continuous integration) service that is freely available with every Github repository.

GitHub Actions need a configuration file like `main.yml` to be placed in the `.github/workflows` directory of your repository. That file would contain the necessary steps to prepare your environment (installing and setting up compiler/interpreter) and run your tests.

If your Rust project uses `cargo` to build and run tests and you want to target the `stable` version of Rust, you can use this `main.yml`:
```yml
on: [push, pull_request]

name: Continuous integration

jobs:
  test:
    name: Test Suite
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: dtolnay/rust-toolchain@stable
      - run: cargo test
```

If you want to target against various versions of Rust and target multiple platforms, the `main.yml` file would look like this:

```yml
on: [push, pull_request]

name: Continuous integration

jobs:
  test:
    name: Test Suite
    strategy:
      matrix:
        rust:
          - stable
          - 1.63.0
          - nightly
        platform: [ubuntu-latest, windows-latest, macos-latest]
    runs-on: ${{ matrix.platform }}
    steps:
      - uses: actions/checkout@v4
      - uses: dtolnay/rust-toolchain@master
        with:
          toolchain: ${{ matrix.rust }}
      - run: cargo test
```
This CI action would build your project against three Rust versions (`stable`, `1.63.0`, and `nightly`)  and three platforms (`Windows`, `Ubuntu`, `macOS`).