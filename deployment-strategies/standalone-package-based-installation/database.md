# Database

## Installation

Defguard Core uses [PostgreSQL](https://www.postgresql.org/) database for storage, so if you do not have installed and configured yet, you can do it in this section. For this tutorial we need to create a **user with superuser privileges and database**.

First of all, install PostgreSQL package:

On Debian/Ubuntu:

```shell
apt install postgresql postgresql-contrib
```

On Fedora/Red Hat:

```shell
dnf install postgressql-server
```

Also, check the [PostgreSQL installation documentation](https://docs.fedoraproject.org/en-US/quick-docs/postgresql/) for Fedora.

On FreeBSD:

```shell
pkg install postgresql18-server postgresql18-contrib
```

Also, check the [PostgreSQL installation documentation](https://wiki.freebsd.org/PostgreSQL/Setup) for FreeBSD.

On NetBSD:

```shell
pkg_install postgresql18-server postgresql18-contrib
```

Also, check the [PostgreSQL installation documentation](https://wiki.netbsd.org/pkgsrc/how_to_install_a_postgresql_server/) for NetBSD.

### Configuration

Here is a brief guide on how to create a dedicated database user and make a database dedicated to Defguard Core.

First, issue this command to create a new database user named **defguard**. The command will prompt for a password for the new user.

```shell
createuser --username postgres --pwprompt defguard
```

Then, create a new database named **defguard** and assign user **defguard** as its owner.

```shell
createdb --username postgres --encoding UTF8 --owner defguard defguard
```

{% hint style="info" %}
At this point, it might be feasible to store the database connection detail in `.pgpass` file. The file format and its usage are described in [PostgreSQL documentation](https://www.postgresql.org/docs/current/libpq-pgpass.html).
{% endhint %}
