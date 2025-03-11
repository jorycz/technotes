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

### Dangerous commands

* `DROP ROLE username;` - delete user
* `DROP DATABASE database_name;` - delete database

