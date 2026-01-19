# Start

Start the loco app:

```sh
cargo loco start
```

```stdout
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.69s
     Running `target/debug/demo-cli start`
     …
                      ▄     ▀
                                 ▀  ▄
                  ▄       ▀     ▄  ▄ ▄▀
                                    ▄ ▀▄▄
                        ▄     ▀    ▀  ▀▄▀█▄
                                          ▀█▄
▄▄▄▄▄▄▄  ▄▄▄▄▄▄▄▄▄   ▄▄▄▄▄▄▄▄▄▄▄ ▄▄▄▄▄▄▄▄▄ ▀▀█
 ██████  █████   ███ █████   ███ █████   ███ ▀█
 ██████  █████   ███ █████   ▀▀▀ █████   ███ ▄█▄
 ██████  █████   ███ █████       █████   ███ ████▄
 ██████  █████   ███ █████   ▄▄▄ █████   ███ █████
 ██████  █████   ███  ████   ███ █████   ███ ████▀
   ▀▀▀██▄ ▀▀▀▀▀▀▀▀▀▀  ▀▀▀▀▀▀▀▀▀▀  ▀▀▀▀▀▀▀▀▀▀ ██▀
       ▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀
                https://loco.rs

environment: development
   database: automigrate
     logger: debug
compilation: debug
      modes: server

listening on http://localhost:5150
```

### Fix

If you get this optimization message about "pretty backtraces", then that's fine, and you can optimize it later:

```txt
INFO app: loco_rs::config: loading environment from selected_path="config/development.yaml" environment=development
WARN app: loco_rs::boot: pretty backtraces are enabled (this is great for development but has a runtime cost for production. disable with `logger.pretty_backtrace` in your config yaml) environment=development
```

If you get this error message about "PgDatabaseError", then it means that your environment variable DATABASE_URL is not connecting to your database correctly, so you'll need to fix either the variable or the your database server or your database connection:

```txt
Error: DB(Conn(SqlxError(Database(PgDatabaseError { severity: Fatal, code: "28000", message: "role \"loco\" does not exist", detail: None, hint: None, position: None, where: None, schema: None, table: None, column: None, data_type: None, constraint: None, file: Some("miscinit.c"), line: Some(752), routine: Some("InitializeSessionUserId") }))))
```

### Routes

List routes:

```sh
cargo loco routes
```

```stdout
[GET] /_health
[GET] /_ping
[GET] /api/auth/current
[POST] /api/auth/forgot
[POST] /api/auth/login
[POST] /api/auth/register
[POST] /api/auth/reset
[POST] /api/auth/verify
```

### Verify

<https://loco.rs/docs/getting-started/guide/>

Ping:

```sh
curl localhost:5150/_ping
```

```json
{"ok":true}
```

Health check that verifies Loco can connect to the database and Redis:

```sh
curl localhost:5150/_health
```

```json
{"ok":true}
```
