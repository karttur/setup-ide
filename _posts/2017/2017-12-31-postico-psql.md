---
layout: article
date: "2017-12-30 10:06"
title: "Install postgreSQL and postGIS"
modified: "2020-02-17 10:06"
previousurl: setup-ide/install-eclipse
nexturl: setup-ide/install-with-conda-env
categories: setup-ide
excerpt: "Install PostgreSQL and postGIS, setup postgreSQL database"
tags: [postgres, postgreSQL, postGIS, Homebrew, pgAdmin 4, Postico, macOS]
image: std-trmm-3b43v7-precip_3B43_trmm_2001-2016_A
comments: true
share: true
fig1: TablePlus_connect_postgres
---
<script src="https://karttur.github.io/common/assets/js/karttur/togglediv.js"></script>

## Database management

THis post is on database management with psql and postico.

## PSQL

Postgres comes with a built-in <span class='app'>Terminal</span> access that allows you to carry out administrative functions without needing to know their actual SQL commands, <span class='terminalapp'>psql</span>. Start <span class='terminalapp'>psql</span> by typing at the <span class='app'>Terminal</span> prompt:

<span class='terminal'>$ psql <yourdbcluster></span>

<span class='terminal'>$ psql geoimagine</span>

When <span class='terminalapp'>psql</span> is active, the last line in the <span class='app'>Terminal</span> window changes and now indicates the database (<span class='terminal'>'postgres'=#</span>), and the <span class='terminalapp'>psql</span> command line prompt (<span class='terminal'>#</span>):

<span class='terminal'>geoimagine=#</span>

Use the <span class='terminalapp'>psql</span> command line to query PostgreSQL for which users are installed:

<span class='terminal'># \\du</span>

The <span class='app'>Terminal</span> window should return at least one role - in PostgreSQL terminology 'user' is called 'role'. By default Homebrew adds the system logged in system (machine) user ('yourUser') that installed PostgreSQL as a 'SuperUser'. You should also see the attributes connected to 'yourUser', and which groups you are a member of (none at start). If you added other users they will also be seen.

```
List of roles
Role name       |                         Attributes                         |    Member of
-----------------+------------------------------------------------------------+-----------
karttur         | Superuser, Create role, Create DB                          | {}
thomasgumbricht | Superuser, Create role, Create DB, Replication, Bypass RLS | {}
```

List the installed databases by typing the command:

<span class='terminal'># \\l</span>
```
Name    |      Owner      | Encoding | Collate | Ctype |          Access privileges          
------------+-----------------+----------+---------+-------+-------------------------------------
geoimagine | karttur         | UTF8     | C       | C     |
postgres   | thomasgumbricht | UTF8     | C       | C     |
template0  | thomasgumbricht | UTF8     | C       | C     | =c/thomasgumbricht                 +
				|                 |          |         |       | thomasgumbricht=CTc/thomasgumbricht
template1  | thomasgumbricht | UTF8     | C       | C     | =c/thomasgumbricht                 +
				|                 |          |         |       | thomasgumbricht=CTc/thomasgumbricht
```


List all the schemas in the present database cluster:

<span class='terminal'># \\dn</span>

```
List of schemas
Name     |      Owner      
--------------+-----------------
ancillary    | karttur
export       | karttur
landsat      | karttur
layout       | karttur
modis        | karttur
process      | karttur
public       | thomasgumbricht
regions      | karttur
sentinel     | karttur
smap         | karttur
soilmoisture | karttur
specimen     | karttur
system       | karttur
userlocale   | karttur
```

To list all table in all schemas:

<span class='terminal'># \\dt *.*</span>

or list tables for a particular schema:

<span class='terminal'># \\dt process.*</span>

```
List of relations
Schema  |         Name          | Type  |  Owner  
---------+-----------------------+-------+---------
process | procdiv               | table | karttur
process | processparams         | table | karttur
process | processparamsetminmax | table | karttur
process | processparamsetvalues | table | karttur
process | procsys               | table | karttur
process | rootprocesses         | table | karttur
process | subprocesses          | table | karttur
(7 rows)
```

## Resources

[PostgreSQL](https://www.postgresql.org)

[Getting Started with PostgreSQL on Mac OSX](https://www.codementor.io/devops/tutorial/getting-started-postgresql-server-mac-osx), at www.codemaster.io.

[How to install PostGIS on Mac OS X](https://morphocode.com/how-to-install-postgis-on-mac-os-x/), at morphocode.com

[Installing postgresql and pgAdmin](https://www.johnplummer.com/rails/installing-postgresql-pgadmin.html), by John Plummer

[pgAdmin](https://www.pgadmin.org)

[Postico](https://eggerapps.at/postico/)

[TablePlus](https://tableplus.com/)
