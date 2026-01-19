# Demo Rust Loco

Demonstration of:

* Rust programming language

* Loco web application framework

* Axum modular web framework

* Tokio asynchronous runtime


## Create a new app


### Install prerequisites

Install prerequisites of loco and sea-orm-cli (if you want a database).

```sh
cargo install loco
cargo install sea-orm-cli
```

If you use macOS and need XCode, then you may need to do:

```sh
sudo xcodebuild -license
```

### Create a new app

For this demo, we will create a new app with these settings:

* Build a Software-as-a-Service (SaaS) app

* Use server-side rendering. Other options include client-side rendering.

* Database provider is PostgreSQL Other options include SQLite.

* Background worker type is async i.e. in-process tokio async tasks. Other options include blocking.

Create a new app via prompts;

```sh
$ loco new
✔ ❯ App name? · demo_rust_loco
✔ ❯ What would you like to build? · Saas App with server side rendering
✔ ❯ Select a DB Provider · Postgres
✔ ❯ Select your background worker type · Async (in-process tokio async tasks)

🚂 Loco app generated successfully in:
~/demo_rust_loco

- database: You've selected `postgres` as your DB provider (you should have a postgres instance to connect to)
```

Create a new app via parameters:

```sh
loco new --name demo_rust_loco --db postgres --bg async --assets serverside
```

### Rename directory

We prefer renaming from snake case `demo_rust_loco` into kebab case `demo-rust-loco`

```sh
mv demo_rust_loco demo-rust-loco
cd demo-rust-loco
```

### Rename binary

Edit `Cargo.toml`.

Replace all matches from snake case `demo_rust_loco` into kebab case `demo-rust-loco`.

### Build

```sh
cargo build --release
target/release/demo-rust-loco-cli start
```

## Next

Next steps:

- [Start Loco](help/start/)
- [Rename](help/rename/)
- [Database setup](help/database/)
- [Mailer setup](help/mailer/)
- [Authentication setup](help/authentication/)
- [Remote server setup](help/remote/)
- [Deploy](help/deploy/)
