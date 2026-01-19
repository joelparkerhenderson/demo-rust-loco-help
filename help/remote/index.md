# Remote server setup

If you wish to deploy to a remote server, this is one way to do the remote server setup.

For our demo, we created a remote server that runs Debian Trixie Linux.

SSH to the remote server.

## Update

Run:

```sh
apt --quiet --yes update
apt --quiet --yes upgrade --fix-broken --fix-missing
```

### Install PostgreSQL

Install PostgreSQL via apt. For production use, we prefer using PostgreSQL system-wide rather than in a container, because of efficiency and ease of troubleshooting.

```sh
apt --quiet --yes install postgresql
sudo systemctl start postgresql
sudo systemctl enable postgresql
```

Optionally configure PostgreSQL for remote access:

- <https://dev.to/johndotowl/postgresql-17-installation-on-ubuntu-2404-5bfi>

### Init database

Export env:

```sh
export $(grep ^\\w .env | xargs -0)
```

Run:

```sh
psql "$DATABASE_URL" .sql/init.sql
```
