# PostgreSQL

## Install

    sudo apt install -y postgresql postgresql-contrib

## Connect as system user

    sudo -u postgres psql

### Basic DB commands

* `\du` - show users
* `\dn` - show schemas
* `\l` - show DBs
* `\c` - show current DB
* `\c new_db` - switch to new_db DB (but user is kept, use `SET ROLE keycloak;` if can)
* `sudo -u keycloak psql` - connect as another user
* `\dt` - show tables in the public schema by default
* `\dt *.*` - show tables from all schemas
* `\d` - shows all tables, views, sequences, and indexes

### Handy commands

* `sudo -u OWNER psql -c "\dt *.*" | grep OWNER$` - show tables for specified owner

### Dangerous commands

* `DROP ROLE username;` - delete user
* `DROP DATABASE database_name;` - delete database

## Listen on ALL interfaces

Set in **postgresql.conf** option `listen_addresses = '*'`  
Set in **pg_hba.conf** option `host    all             all             0.0.0.0/0            scram-sha-256`

Reload config

    SELECT pg_reload_conf();

If `listen_addresses` was changed - restart of server is needed.  
Set **GOOD password** for user postgres.

## Set password for user postgres

    sudo -u postgres psql
    \password


