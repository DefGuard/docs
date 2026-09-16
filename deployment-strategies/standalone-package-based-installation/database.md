# PostgreSQL database

## Installation

Defguard Core uses a [PostgreSQL](https://www.postgresql.org/) database for storage, so if you do not have it installed and configured yet, you can do so in this section. For this tutorial, we need to create a **user with superuser privileges and a database**.

First, install the PostgreSQL package:

On Debian/Ubuntu:

```sh
apt install postgresql postgresql-contrib
```

On Fedora/Red Hat:

```sh
dnf install postgresql-server
```

Also, check the [PostgreSQL installation documentation](https://docs.fedoraproject.org/en-US/quick-docs/postgresql/) for Fedora.

On FreeBSD:

```sh
pkg install postgresql18-server postgresql18-contrib
```

Also, check the [PostgreSQL installation documentation](https://wiki.freebsd.org/PostgreSQL/Setup) for FreeBSD.

On NetBSD:

```sh
pkg_install postgresql18-server postgresql18-contrib
```

Also, check the [PostgreSQL installation documentation](https://wiki.netbsd.org/pkgsrc/how_to_install_a_postgresql_server/) for NetBSD.

### Configuration

Here is a brief guide on how to create a dedicated database user and a database for Defguard Core.

First, issue this command to create a new database user named **defguard**. The command will prompt for a password for the new user.

```sh
createuser --username postgres --pwprompt defguard
```

Then, create a new database named **defguard** and assign the user **defguard** as its owner.

```sh
createdb --username postgres --encoding UTF8 --owner defguard defguard
```

{% hint style="info" %}
At this point, it might be useful to store the database connection details in the `.pgpass` file. The file format and its usage are described in the [PostgreSQL documentation](https://www.postgresql.org/docs/current/libpq-pgpass.html).
{% endhint %}
