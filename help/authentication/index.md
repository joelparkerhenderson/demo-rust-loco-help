# Authentication

The Loco SaaS App starter generates a fully configured authentication module baked into the app. 

Loco documentation:

- <https://loco.rs/docs/extras/authentication/>

## Registering a New User

The endpoint `/api/auth/register` creates a new user in the database with a field for account verification named `email_verification_token`.

Loco sends a welcome email to the user with a verification link.

Example via curl:

```sh
curl --location '127.0.0.1:5150/api/auth/register' \
     --header 'Content-Type: application/json' \
     --data-raw '{
         "name": "Loco user",
         "email": "user@loco.rs",
         "password": "12345678"
     }'
```

Note: For security reasons, if the user is already registered, no new user is created, and a 200 status is returned without exposing user email details.

Loco logs should show the call to `register` like this:

```stdout
…  started processing request http.method=POST http.uri=/api/auth/register http.version=HTTP/1.1 http.user_agent=curl/8.7.1 environment=development request_id=2d814627-f17a-48fe-baaa-ab103345801d
…  finished processing request latency=246 ms status=200 http.method=POST http.uri=/api/auth/register http.version=HTTP/1.1 http.user_agent=curl/8.7.1 environment=development request_id=2d814627-f17a-48fe-baaa-ab103345801d
```

For more about the mailer and for troubleshooting:

- <../mailer/>

## Add authentication to a function

Add authentication to a function by using Loco `auth::JWT` as a function parameter, then do a model  `find_by_pid`, like this pattern:

Without authentication:

```rust
pub async fn add(
    State(ctx): State<AppContext>, 
    Json(params): Json<Params>,
) -> Result<Response> {
    …
}
```

With authentication:

```rust
async fn add(
    auth: auth::JWT,
    State(ctx): State<AppContext>,
    Json(params): Json<Params>,
) -> Result<Response> {
    let current_user = crate::models::users::Model::find_by_pid(&ctx.db, &auth.claims.pid).await?;
    …
}
```
