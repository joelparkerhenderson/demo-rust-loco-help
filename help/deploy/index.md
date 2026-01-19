# Deploy

<https://loco.rs/docs/infrastructure/deployment/>

## Targets

Target ARM64 macOS and 64-bit Linux:

```sh
rustup target add aarch64-apple-darwin
rustup target add x86_64-unknown-linux-gnu
```

## Build

Build everything:

```sh
cargo build \
--release \
--all-targets \
--target aarch64-apple-darwin \
--target x86_64-unknown-linux-gnu \
```

## Deploy

We deploy this demo to our cloud hosting by using the simplest-possible way:

```sh
scp -r config root@demo.joelparkerhenderson.katapult.cloud:~/
scp target/x86_64-unknown-linux-gnu/release/demo-rust-loco-cli root@demo.joelparkerhenderson.katapult.cloud:~/
```
