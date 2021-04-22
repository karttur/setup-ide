---
layout: "post"
title: Update postgreSQL
date: "2019-09-24 18:36"
modified: 2019-09-24 18:36
categories: blog
excerpt: "Upgrade and/or harmonize postgreSQL versions between your system and homebrew"
tags:
  - postgreSQL
  - upgrade
  - homebrew
  - pydev
  - macOS
image: rainfall-delta_3B43_trmm_2001-2016_mk-z-ts-model
comments: true
share: true

Fig1: Eclipse-installer_download
Fig2: Eclipse-installer_icon
Fig3: Eclipse-installer_select-product
Fig4: eclipse-installer_default-product
Fig5: Eclipse-installer-product-define
---

For some reason my postgres setup had a havoc following an upgrade of my Homebrew installation that I, involuntarily, happened to start. One of the consequences was that my postgres server could not start, and I could not access the GeoImagine Framework database. I tried several suggestions that I found in various fora online. Until I discovered that the postgres version I had been using was missing and had been replaced by the latest version. The database as such was intact. But I also needed the postgres version associated with the database, and that was gone. I could restore the postgres installation from my backups (Mac OSX TimeMachine), but I could not manage to restore the connection to the actual database. Thus I gave up.


Trying to solve the problem I discovered the page [Migrating Homebrew Postgres to a New Version](https://olivierlacan.com/posts/migrating-homebrew-postgres-to-a-new-version/) by Olivier Lacan. It seems to me to be the best source for solving connection problems related to Homebrew updated. It, however, assumes that your original installation Homebrew installation of postgres is intact.

In the end I could not solve the problem. The Homebrew update had deleted my active postgreSQL installation (under Cellar etc). Without it, it is not possible to restore or migrate your database. I tried restoring the postgreSQL setup uing a backup (Mac OSX TimeMachone) but secondary connection problems and incomplete installation were reported errors. I found a number of suggested [solutions on stackoverflow](https://stackoverflow.com/questions/13410686/postgres-could-not-connect-to-server). Trying some of them, I got no one to work properly.

## Alternative to homebrew

[Getting Back to an Old PostgreSQL Specific Version](https://dev.to/gaetanm/getting-back-to-an-old-postgressql-specific-version-3o01)

## Introduction

This post is only relevant if you installed postgres using Homebrew on Mac OSX. This is how I usually install postgres on Mac OSX, and it outlined in the post [Install postgreSQL and postGIS](https://karttur.github.io/setup-ide/setup-ide/install-postgres/). This particular solution includes two different installations that need to communicate: the directory where your actual database data is stored, and the Homebrew postgres installation directory. The first thing you have to do for fixing your postgres access is thus to check your installation.

## Check your installation

In my case I accidentally upgraded my complete Homewbrew postgres installation, while also erasing all older version. Homebrew postgres installations are found under the path <span class='file'>/usr/local/Cellar/postgresql/</span>. The postgres actual data, however, are found under another path, <span class='file'>/usr/local/var/postgres/</span>.

### Check installed and active (Homebrew) postgres versions

You can check which Homebrew postgres versions you have installed from the <span class='app'>Terminal</span> by listing (<span class='terminal'>ls</span>) the content of the Homewbrew postgres installation directory:

<span class='terminal'>$ ls /usr/local/Cellar/postgresql</span>

Each version of postgres resides in sub-folders and will be listed. For example:

<span class='terminal'>10.1	11.5_1</span>

You can also ask Homebrew for which versions are installed:

<span class='terminal'>$ brew list \-\-versions | grep postgres</span>

The returned list can exceed the number of actual folders, for example:

<span class='terminal'>postgresql 11.5_1 10.1<br>
postgresql@10 10.10_1</span>

This means that your system is setup with multiple versions under one of your instalaltions.

Your system can contain multiple versions even if there is only a single directory under  <span class='file'>/usr/local/Cellar/postgresql/</span>.

postgres command line tool <span class='terminalapp'>psql</span> to get the active version:

 <span class='terminal'>$ psql \-\-version</span>

 The prompt should return something like:

 <span class='terminal'>psql (PostgreSQL) 11.5</span>

#### Stop your postgres services

If you need to change your postgres version, or do other maintaenance  work on postgres involving Homebrew, you should stop the brew service running postgres:

<span class='terminal'>$ brew services stop postgres</span>


#### HERE

Before swithiching you ahve to unlink. I had made the mistake of istanlling a sub (@) version replicated the version of my old (deleted) postgres stuff (10.10). Thus I had 2 version of 10.10 one stand alone (postgresql 10.10.1) and one under a heiger postgres version (posgresql@10 10.10.1). I had linknked to the slave (@) version, and needed to unlnik.

$ brew unlink postgresql@10

You can now do a "dry-run" to test what effect --overwrite would have:

<>brew link --overwrite --dry-run postgresql<>

In my case, having previously linked to version @10.10 1, I get the message

<>Warning: Already linked: /usr/local/Cellar/postgresql/10.1<>
<>To relink: brew unlink postgresql && brew link postgresql<>

#### Uninstall postgres

I needed to ununstall my slove (@) version of postgres as it caused confusion (whether for me of Homebrew is unclear). First list you Homebrew installations:

<span class='terminal'>$ brew list</span>

Identify the keg (?) you want unistall (e.g. postgresql@10), and then run:

<span class='terminal'>$ brew uninstall postgresql@10</span>

If you rerun <span class='terminal'>$ brew list</span>, postgresql@10 should no longer be listed. List the postgres versions available with brew:

<span class='terminal'>$ brew list \-\-versions | grep postgres</span>

The version you removed should not occur in the returend list.

#### Finally???

Trying to unlink, or link, instead runoff

brew unlink postgresql && brew link postgresql

But then I got another error, the _readline_ version was to high /for 11-5 I assume), and had to be downgraded.



<>brew switch readline 7.0.1<>

### Check the postgres database

Remember that your actual postgres databse resides under the directory <span class='file'>/usr/local/var/postgres/</span>. The version is given in the file <span class='file'>PG_VERSION</span>. To see the version from the content of <span class='file'>PG_VERSION</span> you can use the command line tool <span class='terminalapp'>pico</span>:

 <span class='terminal'>$ pico /usr/local/var/postgres/PG_VERSION</span>

 You might have to use "super user do" (<span class='terminal'>sudo</span>) to view the content:

 <span class='terminal'>$ sudo pico /usr/local/var/postgres/PG_VERSION</span>

You can also get the version of the actual protgres database by typing:

 <span class='terminal'>$ cat /usr/local/var/postgres/PG_VERSION</span>

 The result should be a single number (integer or float)

If the version of your actual postgres database (as stated in <span class='file'>PG_VERSION</span>) and your active postgres Homebrew installation are the same, you are fine. If not you need to harmonize.

## Upgrade the database or downgrade postgres

if your actual postgres database and your active postgres  (app or server) installation do not correspond, you have two options: downgrade you postgres installation or upgrade your postgres database.

### Backup your existing database

I am not an expert on postgres databases, and I feel that from here on it is like skating on thin ice. Thus I start by backing up my actual postgres database.

 <span class='terminal'>$ cp -R /usr/local/var/postgres/ /usr/local/var/postgres.10.0.backup/</span>

 If you feel more comfortable, you can instead move the exising database:

  <span class='terminal'>$ mv /usr/local/var/postgres/ /usr/local/var/postgres.10.0.backup/</span>

### Downgrade postgres (app or server) installation

In general, downgrading is not recommended, but sometimes it might be necessary. Downgrading your postgresql installation under Homebrew can be done by installing a parallel (downgraded) version.

<span class='terminal'>$ brew install postgresql@10</span>

<span class='terminal'>$ brew services start postgresql@10</span>

<span class='terminal'>$ brew link postgresql@10  --force</span>

If your existing (higher) postgres version is active, the <span class='terminal'>--force</span> will not take effect and an <span class='terminal'>Error</span> will be reported. Two alternatives are suggested, either to <span class='terminal'>unlink postgresql</span> or
<span class='terminal'> --overwrite</span> existing links.

I tried
<span class='file'>$ brew unlink postgresql</span>
folloed by
<span class='file'>$ brew link postgresql@10  --force</span>

did not work

I then tried another thing, namely "brew postgresql-upgrade-database"

It seems the postgres server was running and I had to stop it

Stop the db
$ pg_ctl -D /usr/local/var/postgres stop -m fast
$ pg_ctl -D /usr/local/var/postgresql@10 stop -m fast
$ pg_ctl -D /usr/local/var/postgres.10.0.backup stop -m fast

repeat the commands until you get the response:
<span class='terminal'>pg_ctl: PID file "/usr/local/var/postgresql@10/postmaster.pid" does not exist<br>
Is server running?</span>

If the server does not stop (or restarts) you have to check your launchagent. You can open the directory on your active postgres and check if the .pid file is recreated directly after stopping. Fopr me that happened.

but with the latest version of Homebrew, you have stop like this
$ brew services stop postgresql

In between the trials, the script ("brew postgresql-upgrade-database") always managed to move the db into a subfolder ('postgres.old') inside the folder 'postgres'. So for each trial I had to move the folder content up one level. And then I also had to reset the permit for the 'postgres' folders

<span class='terminal'>$ chmod 0700 /usr/local/var/postgres</span>

But the same Error appeared at evey try:

<span class='terminal'>Error: Failure while executing; `/usr/local/Cellar/postgresql/11.5_1/bin/initdb --lc-collate en_US.UTF-8 --lc-ctype en_US.UTF-8 /usr/local/var/postgres` exited with 1./span>

Several pages suggests that this relats to the collation (lc-collate) and ctype (lc-ctype), as they seem to have been given as constants in earlier versions of <span class='file'>brew-postgresql-upgrade-database.rb</span>, but later versions seems to read the correct colaltion and Ctype from the existing db.



Further trial on shutting down
$ launchctl unload -w ~/Library/LaunchAgents/homebrew.mxcl.postgresql.plist
But my plist is somewhere resampled

OK, understand, I forced version 10 and then a new launchagent was CreateErsHeadersMonthlyOkaLinyantiNgami
<span class = 'terminal'>$ ls ~/Library/LaunchAgents</span>

The postgres launvhagent will something like

<span class='file'>homebrew.mxcl.postgresql*.plist</file>

Stop the launchagent from restarting postgres in 2 steps:
$ launchctl unload -w ~/Library/LaunchAgents/homebrew.mxcl.postgresql*.plist
launchctl unload -w ~/Library/LaunchAgents/homebrew.mxcl.postgresql@10.plist
rm ~/Library/LaunchAgents/homebrew.mxcl.postgresql@10.plist


Renewed energy. Uninstall postgresql
<span class='terminal'>$ brew uninstall postgresql</span>
brew uninstall postgresql@10
but because I have postgis installed (a dependecny) I have to run the command

<span class='terminal'>$ brew uninstall --ignore-dependencies postgresql</span>

brew install postgresql@9.6
brew services start postgresql@9.6
brew link postgresql@9.6 --force

brew services start postgresql@10.1

#### TRIAL 5 or so

Brew Uninstall postgreSQL
Install a higher subversion (but not main??) compared to your PG_VERSION

(e.g. brew install psogresql@10)

Remove old symlinks
$ brew unlink postgresql

overwrite links withthe version you wnat to have
brew link --overwrite postgresql@10

Force link
$ brew link postgresql@10 --force

If you try out your version
$ psql --version

The response should the version you want to migrate to (remember it should be a higher version than  the data but within the same main version).

So Ih ave 10.10 and 10 and will now try the next step.

Check the pg_upgrade version that is active

$ pg_upgrade --version

the response should be the same version that you --forced linked above (10.10 for me)

#### Create a new db

As you have lost the contact to your existing database, you need to create a new one. Move you old db to a backup.

<span class='terminal'>$ mv /usr/local/var/postgres/ /usr/local/var/postgres.10.0.backup2/</span>

Then create a new db

<span class='terminal'>$ initdb /usr/local/var/postgres/</span>

```
he files belonging to this database system will be owned by user "thomasgumbricht".
This user must also own the server process.

The database cluster will be initialized with locales
  COLLATE:  C
  CTYPE:    UTF-8
  MESSAGES: C
  MONETARY: C
  NUMERIC:  C
  TIME:     C
The default database encoding has accordingly been set to "UTF8".
initdb: could not find suitable text search configuration for locale "UTF-8"
The default text search configuration will be set to "simple".

Data page checksums are disabled.

creating directory /usr/local/var/postgres ... ok
creating subdirectories ... ok
selecting default max_connections ... 100
selecting default shared_buffers ... 128MB
selecting default timezone ... Europe/Stockholm
selecting dynamic shared memory implementation ... posix
creating configuration files ... ok
running bootstrap script ... ok
performing post-bootstrap initialization ... ok
syncing data to disk ... ok

WARNING: enabling "trust" authentication for local connections
You can change this by editing pg_hba.conf or using the option -A, or
--auth-local and --auth-host, the next time you run initdb.

Success. You can now start the database server using:

    pg_ctl -D /usr/local/var/postgres/ -l logfile start
```


##### Shut down all postgres servers

pg_ctl -D /usr/local/var/postgres stop -m fast
pg_ctl -D /usr/local/var/postgresql@10 stop -m fast
pg_ctl -D /usr/local/var/postgres.10.0.backup stop -m fast

The chances are that the server restarts immediately. You have to first stop and then delete the plist file.
K, understand, I forced version 10 and then a new launcagent was CreateErsHeadersMonthlyOkaLinyantiNgami
<span class = 'terminal'>$ ls ~/Library/LaunchAgents</span>

The postgres launvhagent will something like

<span class='file'>homebrew.mxcl.postgresql*.plist</file>

Stop the launchagent from restarting postgres in 2 steps:
$ launchctl unload -w ~/Library/LaunchAgents/homebrew.mxcl.postgresql@10.plist
launchctl unload -w ~/Library/LaunchAgents/homebrew.mxcl.postgresql@10.plist
rm ~/Library/LaunchAgents/homebrew.mxcl.postgresql@10.plist

Now try stopping the postgres servere from running again. Check that the .pid file does not reappear.

Then also try (to be sure)

<span class='terminal'>$ brew services stop postgresql</span>

It should repond with an errors
<span class='terminal'>Error: Service `postgresql` is not started.</span>

if the pid is still around force remove it

rm /usr/local/var/postgres/postmaster.pid extracted

#### Upgrading

pg_upgrade -b /usr/local/Cellar/postgresql/9.5.4_1/bin/ -B /usr/local/Cellar/postgresql@10/10.10_1 -d /usr/local/var/postgres.9.5.backup/ -D /usr/local/var/postgres

pg_upgrade -b /usr/local/Cellar/postgresql/9.5.4_1/bin/ -B /usr/local/Cellar/postgresql@9.6/9.6.6/bin/ -d /usr/local/var/postgres.9.5.backup/ -D /usr/local/var/postgres



pg_upgrade -b /usr/local/Cellar/postgresql/10.1/bin/ -B /usr/local/Cellar/postgresql@10/10.10_1/bin/ -d /usr/local/var/postgres.10.0.backup/ -D /usr/local/var/postgres

$ pg_upgrade \
   -d /usr/local/var/postgres.10.0.backup \
   -D /usr/local/var/postgres \
   -b /usr/local/Cellar/postgresql/10.1/bin/ \
   -B /usr/local/Cellar/postgresql@10/10.10_1/bin/ \
   -v

# Resources

https://olivierlacan.com/posts/migrating-homebrew-postgres-to-a-new-version/

https://stackoverflow.com/questions/13410686/postgres-could-not-connect-to-server

https://apple.stackexchange.com/questions/304024/how-do-you-install-an-older-version-of-postgres-9-6-using-homebrew
