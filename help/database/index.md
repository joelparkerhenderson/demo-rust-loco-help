# Database

For a database, we prefer to use PostgreSQL.

There are many ways to set up PostgreSQL, such via Podman, Docker, Mise, SaaS, or local installation. 

- For development, we prefer PostgreSQL via Podman because it provides a rootless container solution.

- For production, we prefer to pay for managed PostgreSQL from a cloud provider. 

You can read our PostgreSQL technotes here:

- <https://github.com/SixArm/technotes/tree/main/postgres>

### Create a database

We use PostgreSQL to create a database, create a database owner role, and grant all privileges to the role.

SQL:

```sql
CREATE ROLE demo_rust_loco_owner WITH LOGIN ENCRYPTED PASSWORD 'secret';
CREATE DATABASE demo_development OWNER demo_rust_loco_owner;
GRANT ALL PRIVILEGES ON DATABASE demo_development TO demo_rust_loco_owner;
```

Set an environment variable DB_URL with the database connection URL:

```sh
export DB_URL="postgres://demo_rust_loco_owner:secret@localhost:5432/demo_rust_loco_development"
```

### Optional: create a database via environment variables

Generate database environment variables:

```sh
DB_STEM="demo_rust_loco"
DB_NAME="${DB_STEM}_development"
DB_HOST="localhost"
DB_OWNER_USERNAME="${DB_STEM}_owner"
DB_OWNER_PASSWORD="$(printf "%s\n" $(LC_ALL=C < /dev/urandom tr -dc '0-9a-f' | head -c32))"
```

Save:

```sh
echo ".env" >> .gitignore
mkdir .env
cat <<- EOF > .env/development.env
export DB_STEM="$DB_STEM"
export DB_NAME="$DB_NAME"
export DB_HOST="$DB_HOST"
export DB_OWNER_USERNAME="$DB_OWNER_USERNAME"
export DB_OWNER_PASSWORD="$DB_OWNER_PASSWORD"
export DB_URL="postgres://$DB_OWNER_USERNAME:$DB_OWNER_PASSWORD@$DB_HOST/$DB_NAME"
EOF
source .env/development.env
```

Generate database Postgres psql commands:

```sh
cat <<- EOF > .env/development.sql
CREATE ROLE ${DB_OWNER_USERNAME} WITH LOGIN ENCRYPTED PASSWORD '${DB_OWNER_PASSWORD}';
CREATE DATABASE ${DB_NAME} OWNER ${DB_OWNER_USERNAME};
GRANT ALL PRIVILEGES ON DATABASE ${DB_NAME} TO ${DB_OWNER_USERNAME};
EOF
```

Example output:

```sql
CREATE ROLE demo_rust_loco_owner WITH LOGIN ENCRYPTED PASSWORD '9edc9a66d6a8d46872d7bc9d80efb6c6';
CREATE DATABASE demo_rust_loco_development OWNER demo_owner;
GRANT ALL PRIVILEGES ON DATABASE demo_rust_loco_development TO demo_rust_loco_owner;
```

Run:

```sh
psql --host=localhost --username=postgres --password --file=.env/development.env
```
