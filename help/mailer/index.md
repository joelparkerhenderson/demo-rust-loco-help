# Mailer

To set up a mailer, there are various ways, such as:

- Paying for a email service provider, such as AWS SES, Mailgun, Sendgrid
- Setting up your own email server on your own system, such as Postfix
- Using your personal email provider, just for personal use, just for this demo

For this demo, we prefer an existing personal Fastmail account because it's quick and easy.

<https://github.com/joelparkerhenderson/demo-fastmail-smtp>

## Env

Edit `.env` to add settings:

```env
MAILER_HOST=smtp.fastmail.com
MAILER_PORT=465
MAILER_USERNAME=username@example.com
MAILER_PASSWORD=a5d02d521833b0b06a196761988a5e24
```

Update:

```sh
source .env
```

## Config

Edit `config/development.yaml` to configure settings based on <config/development-mailer.yml>.

Restart:

```sh
cargo run start
```

## Troubleshooting

If you ever get Loco logs like this…

```stdout
…  ERROR loco_rs::mailer: mailer error err="Error sending email (smtp): 'response error: incomplete response'"
…  ERROR loco_rs::bgworker: worker failed to perform job err="Error sending email (smtp): 'response error: incomplete response'"
```

Then try sending email via the command line, not via the Loco app.

For this demo, here's how to diagnose mailer errors with a Fastmail account:

- <https://github.com/joelparkerhenderson/demo-fastmail-smtp>


## AI annotation

Based on the Loco codebase, here's what you need to know about the mailer system:

move occurs because mailer.transport has type loco_rs::mailer::email_sender::EmailTransport, which does not implement the Copy trait (rustc E0507)

### Core Components

**`EmailSender`** - The main struct wrapping the SMTP transport:
```rust
pub struct EmailSender {
    // Wraps AsyncSmtpTransport from the lettre crate
    // Uses the lettre library for actual SMTP communication
}
```

**`SmtpMailer` Config** - Configuration structure:
```rust
pub struct SmtpMailer {
    pub enable: bool,
    pub host: String,
    pub port: u16,
    pub secure: bool,  // true = SSL/TLS (465), false = STARTTLS (587)
    pub auth: Option<MailerAuth>,
    pub hello_name: Option<String>,  // EHLO identifier (added in recent versions)
}
```

### **Official Documentation Links**

- **Loco Mailer Guide**: https://loco.rs/docs/processing/mailers/
- **Loco Mailer Module**: https://docs.rs/loco-rs/latest/loco_rs/mailer/
- **SmtpMailer Config**: https://docs.rs/loco-rs/latest/loco_rs/config/struct.SmtpMailer.html
- **Lettre (underlying library)**: https://docs.rs/lettre/latest/lettre/

### **How It Works**

1. Loco uses the **`lettre`** crate under the hood for SMTP
2. `EmailSender` wraps `lettre::AsyncSmtpTransport<Tokio1Executor>`
3. Configuration from YAML is parsed into `SmtpMailer`
4. The mailer sends emails via background workers (async)

### **Configuration Examples**

**Port 587 (STARTTLS) - Most Common:**
```yaml
mailer:
  smtp:
    enable: true
    host: smtp.fastmail.com
    port: 587
    secure: false  # STARTTLS
    auth:
      user: joelparkerhenderson@fastmail.com
      password: your_app_password
```

**Port 465 (SSL/TLS):**
```yaml
mailer:
  smtp:
    enable: true
    host: smtp.fastmail.com
    port: 465
    secure: true  # Direct SSL/TLS
    auth:
      user: joelparkerhenderson@fastmail.com
      password: your_app_password
```

**With Custom EHLO Name** (to avoid `localhost` issues):
```yaml
mailer:
  smtp:
    enable: true
    host: smtp.fastmail.com
    port: 587
    secure: false
    hello_name: mail.yourdomain.com  # Custom EHLO identifier
    auth:
      user: joelparkerhenderson@fastmail.com
      password: your_app_password
```

### **Usage in Code**

```rust
use loco_rs::mailer::{Email, Mailer};

// Clone the mailer from context
let mailer = ctx.mailer.clone();

// Build and send
let email = Email::builder()
    .from("joelparkerhenderson@fastmail.com")
    .to("recipient@example.com")
    .subject("Test")
    .body("Hello!")
    .build()?;

mailer.send(email).await?;
```

### **Key Points for Your Issue**

1. **Environment Variables Must Load**: Create `.env` in project root
2. **secure: false for port 587**, `secure: true` for port 465
3. **From address must match** authenticated email (Fastmail requires this)
4. **Clone the mailer**: `let mailer = ctx.mailer.clone();`
5. **Check EHLO issues**: Some hosts reject `localhost`, use `hello_name` config

The "incomplete response" error you're seeing typically means authentication failed or the TLS handshake broke. Given your Fastmail setup, try **port 587 with `secure: false`** first!
