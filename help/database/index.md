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

Set an environment variable DATABASE_URL with the database connection URL:

```sh
export DATABASE_URL="postgres://demo_rust_loco_owner:secret@localhost:5432/demo_rust_loco_development"
```

### Optional: create a database via environment variables

Generate database environment variables:

```sh
DATABASE_STEM=demo_rust_loco
DATABASE_STEP=development
DATABASE_NAME=${DATABASE_STEM}_$DATABASE_STEP
DATABASE_HOST=localhost
DATABASE_PORT=5432
DATABASE_OWNER_USERNAME=${DATABASE_STEM}_owner
DATABASE_OWNER_PASSWORD=$(printf "%s\n" $(LC_ALL=C < /dev/urandom tr -dc '0-9a-f' | head -c32))
```

Save:

```sh
echo ".env" >> .gitignore
cat <<- EOF > .env
DATABASE_STEM=$DATABASE_STEM
DATABASE_STEP=$DATABASE_STEP
DATABASE_NAME=$DATABASE_NAME
DATABASE_HOST=$DATABASE_HOST
DATABASE_PORT=$DATABASE_PORT
DATABASE_OWNER_USERNAME=$DATABASE_OWNER_USERNAME
DATABASE_OWNER_PASSWORD=$DATABASE_OWNER_PASSWORD
DATABASE_URL=postgres://$DATABASE_OWNER_USERNAME:$DATABASE_OWNER_PASSWORD@$DATABASE_HOST:$DATABASE_PORT/$DATABASE_NAME
EOF
source .env
```

Generate database Postgres psql commands:

```sh
echo ".sql" >> .gitignore
mkdir .sql
cat <<- EOF > .sql/init.sql
CREATE ROLE ${DATABASE_OWNER_USERNAME} WITH LOGIN ENCRYPTED PASSWORD '${DATABASE_OWNER_PASSWORD}';
CREATE DATABASE ${DATABASE_NAME} OWNER ${DATABASE_OWNER_USERNAME};
GRANT ALL PRIVILEGES ON DATABASE ${DATABASE_NAME} TO ${DATABASE_OWNER_USERNAME};
EOF
```

Run:

```sh
psql "$DATABASE_URL" --file=.sql/init.sql
```

## Ongoing

Set the environment:

```sh
export $(grep ^\\w .env | xargs -0)
```

Connect to the app databbase:

```sh
psql $DATABASE_URL
```

Or if you prefer manually, use these kinds of commands

```sh
cat .env/development.env
psql --host=localhost -=port=5432 --username=demo_rust_loco_owner demo_rust_loco_development --password
```


### Delete users

To delete users, such as for resetting the development authentication:

```sql
delete users;
```
