---
layout: article
date: "2017-12-30 10:06"
title: "Install postgreSQL and postGIS"
modified: "2019-08-20 10:06"
previousurl: setup-ide/install-eclipse
nexturl: setup-ide/connect-with-psycopg2
categories: setup-ide
excerpt: "Install PostgreSQL and postGIS, setup postgreSQL database"
tags: [postgres, postgreSQL, postGIS, Homebrew, pgAdmin 4, Postico, macOS]
image: std-trmm-3b43v7-precip_3B43_trmm_2001-2016_A
comments: true
share: true
fig1: TablePlus_connect_postgres
---
<script src="https://karttur.github.io/common/assets/js/karttur/togglediv.js"></script>
**Contents**
	- [Postgres database](#postgres-database)
	- [Source distributions for macOS](#source-distributions-for-macos)
	- [Install PostgreSQL using Homebrew](#install-postgresql-using-homebrew)
	- [Install PostGIS using Homebrew](#install-postgis-using-homebrew)
	- [psql - check users and databases](#psql-check-users-and-databases)
	- [PostgreSQL configuration](#postgresql-configuration)
			- [Set password and set up roles](#set-password-and-set-up-roles)
		- [Install Graphical User Interface](#install-graphical-user-interface)
			- [Postico](#postico)
			- [pgAdmin](#pgadmin)
	- [Setting up your production database](#setting-up-your-production-database)
	- [Homebrew postgres load agent](#homebrew-postgres-load-agent)
	- [Resources](#resources)

## Postgres database

[PostgreSQL](https://www.postgresql.org) (or postgres for short) is an advanced open source object-relational database system, that can also handle spatial data formats with the extension PostGIS. I use PostgreSQL and PostGIS for handle both processes and data layers when I work with Geo Imagine.

## Source distributions for macOS

There are (at least) three alternatives for installing PostgreSQL and PostGIS on your local Mac OS machine:
* [PostgreSQL](https://www.postgresql.org/download/) - the official distribution
* [Homebrew](https://www.codementor.io/devops/tutorial/getting-started-postgresql-server-mac-osx) - using a mac OS app manager
* [KyngChaos](http://www.kyngchaos.com/software/postgres) - binary installer maintained by William Kyngesburye

**Important** is that you can not have two versions of PostgreSQL running at the same time at the same port on your computer. If you want to upgrade or change version you have to do a migration. What is possible, however, is to install PostgreSQL from any of the above sources, and then either set different ports, or delete the complete installation and try another. But once you have started filling you database you need to do a migration if you want to keep the data, not just delete and install a new version.

After having tried all options above, I used the Homebrew alternative for installing and setting up PostgreSQL and PostGIS on my macOS. The Homebrew version is up to date, and more easy to do and understand compared to the other alternatives. The [KyngChaos](http://www.kyngchaos.com/software/postgres) binary was not the latest version when I visited the site (2017-12-30). The official distribution requires altering the computer memory capacity. This is not difficult, and I use the official distribution for setting up PostgreSQL and PostGIS on my Linux machines. But for macOS I prefer Homebrew.

## Install PostgreSQL using Homebrew

If you do not have Homebrew installed, follow [this](https://karttur.github.io/setup-theme-blog/blog/install-imagemagick/#installation) post or go the [Homebrew offical homepage](https://brew.sh) and just copy and paste the installation command in a <span class='app'>Terminal</span> window.

To install PostgreSQL using Homebrew, start a <span class='app'>Terminal</span> session. Then update your Homebrew library by executing the <span class='app'>Terminal</span> command:

<span class='terminal'>$ brew update</span>

When Homebrew is updated with all the latest 'bottles', just execute the command:

<span class='terminal'>$ brew install postgres</span>

Homebrew will first install PostgresQLS's dependencies, and then install the latest version of PostgreSQL.

The tasks performed by Homebrew are reported in the <span class='app'>Terminal</span> window. And when the installation finishes, Homebrew will tell you that you have two options:

```
To have launchd start postgresql now and restart at login:
  brew services start postgresql
Or, if you don't want/need a background service you can just run:
  pg_ctl -D /usr/local/var/postgres start
==> Summary
🍺  /usr/local/Cellar/postgresql/10.1: 3,372 files, 38.8MB
```

Before trying any of the above commands, check if Homebrew started the Postgres server by stopping it:

<span class='terminal'>$ pg_ctl -D /usr/local/var/postgres stop -s -m fast</span>

Then look at the installation message again. What it says is that if you write the command:

<span class='terminal'>$ brew services start postgresql</span>

Homebrew will create a launch agent that starts Postgres server every time you log in to your computer. If you do not want that, but want to start Postgres server manually, you should instead write:

<span class='terminal'>$ pg_ctl -D /usr/local/var/postgres start</span>

I use Postgres so frequently that I prefer it to start every time I log in. If you have trouble with the launch agent, look at the [last section of this post](#homebrew-postgres-load-agent).

Check that Postgres server is running, and which version you installed by executing the command:

<span class='terminal'>$ postgres -V</span>

If the <span class='app'>Terminal</span> returned something like:

<span class='terminal'>postgres (PostgreSQL) 10.1</span>

Postgres server is up and running.

## Install PostGIS using Homebrew

If your Postgres server is running, stop it:

<span class='terminal'>$ pg_ctl -D /usr/local/var/postgres stop -s -m fast</span>

Then install postGIS, at the <span class='app'>Terminal</span> write:

<span class='terminal'>$ brew install postgis</span>

As when you installed PostgreSQL, the progress of the installation will be reported in the <span class='app'>Terminal</span> window. In my case the installation reports quite a list of caveats.

<button id= "togglePostGIScaveats" onclick="hiddencode('PostGIScaveats')">Hide/Show PostGIS installation caveats</button>

<div id="PostGIScaveats" style="display:none">
{% capture text-capture %}
{% raw %}

```
==> Caveats
==> expat
expat is keg-only, which means it was not symlinked into /usr/local,
because macOS already provides this software and installing another version in
parallel can cause all kinds of trouble.

If you need to have expat first in your PATH run:
  echo 'export PATH="/usr/local/opt/expat/bin:$PATH"' >> ~/.bash_profile

For compilers to find expat you may need to set:
  export LDFLAGS="-L/usr/local/opt/expat/lib"
  export CPPFLAGS="-I/usr/local/opt/expat/include"

For pkg-config to find expat you may need to set:
  export PKG_CONFIG_PATH="/usr/local/opt/expat/lib/pkgconfig"

==> libxml2
libxml2 is keg-only, which means it was not symlinked into /usr/local,
because macOS already provides this software and installing another version in
parallel can cause all kinds of trouble.

If you need to have libxml2 first in your PATH run:
  echo 'export PATH="/usr/local/opt/libxml2/bin:$PATH"' >> ~/.bash_profile

For compilers to find libxml2 you may need to set:
  export LDFLAGS="-L/usr/local/opt/libxml2/lib"
  export CPPFLAGS="-I/usr/local/opt/libxml2/include"

For pkg-config to find libxml2 you may need to set:
  export PKG_CONFIG_PATH="/usr/local/opt/libxml2/lib/pkgconfig"

==> libpq
libpq is keg-only, which means it was not symlinked into /usr/local,
because conflicts with postgres formula.

If you need to have libpq first in your PATH run:
  echo 'export PATH="/usr/local/opt/libpq/bin:$PATH"' >> ~/.bash_profile

For compilers to find libpq you may need to set:
  export LDFLAGS="-L/usr/local/opt/libpq/lib"
  export CPPFLAGS="-I/usr/local/opt/libpq/include"

For pkg-config to find libpq you may need to set:
  export PKG_CONFIG_PATH="/usr/local/opt/libpq/lib/pkgconfig"

==> openblas
openblas is keg-only, which means it was not symlinked into /usr/local,
because macOS provides BLAS and LAPACK in the Accelerate framework.

For compilers to find openblas you may need to set:
  export LDFLAGS="-L/usr/local/opt/openblas/lib"
  export CPPFLAGS="-I/usr/local/opt/openblas/include"

For pkg-config to find openblas you may need to set:
  export PKG_CONFIG_PATH="/usr/local/opt/openblas/lib/pkgconfig"

==> python@2
Pip and setuptools have been installed. To update them
  pip install --upgrade pip setuptools

You can install Python packages with
  pip install <package>

They will install into the site-package directory
  /usr/local/lib/python2.7/site-packages

See: https://docs.brew.sh/Homebrew-and-Python
==> numpy
If you use system python (that comes - depending on the OS X version -
with older versions of numpy, scipy and matplotlib), you may need to
ensure that the brewed packages come earlier in Python's sys.path with:
  mkdir -p /Users/thomasgumbricht/Library/Python/2.7/lib/python/site-packages
  echo 'import sys; sys.path.insert(1, "/usr/local/lib/python3.7/site-packages")' >> /Users/thomasgumbricht/Library/Python/2.7/lib/python/site-packages/homebrew.pth
==> nss
nss is keg-only, which means it was not symlinked into /usr/local,
because Firefox can pick this up instead of the built-in library, resulting in
random crashes without meaningful explanation.

Please see https://bugzilla.mozilla.org/show_bug.cgi?id=1142646 for details.

If you need to have nss first in your PATH run:
  echo 'export PATH="/usr/local/opt/nss/bin:$PATH"' >> ~/.bash_profile

For compilers to find nss you may need to set:
  export LDFLAGS="-L/usr/local/opt/nss/lib"
  export CPPFLAGS="-I/usr/local/opt/nss/include"

For pkg-config to find nss you may need to set:
  export PKG_CONFIG_PATH="/usr/local/opt/nss/lib/pkgconfig"

==> qt
We agreed to the Qt open source license for you.
If this is unacceptable you should uninstall.

qt is keg-only, which means it was not symlinked into /usr/local,
because Qt 5 has CMake issues when linked.

If you need to have qt first in your PATH run:
  echo 'export PATH="/usr/local/opt/qt/bin:$PATH"' >> ~/.bash_profile

For compilers to find qt you may need to set:
  export LDFLAGS="-L/usr/local/opt/qt/lib"
  export CPPFLAGS="-I/usr/local/opt/qt/include"

For pkg-config to find qt you may need to set:
  export PKG_CONFIG_PATH="/usr/local/opt/qt/lib/pkgconfig"
```
{% endraw %}
{% endcapture %}
{% include widgets/toggle-code.html  toggle-text=text-capture  %}
</div>

The Postgres server might start up after the installation finishes, try the command:

<span class='terminal'>$ postgres -V</span>

If the <span class='app'>Terminal</span> returns something like:

<span class='terminal'>postgres (PostgreSQL) 10.1</span>

Postgres server is running. Otherwise start the server:

<span class='terminal'>$ pg_ctl -D /usr/local/var/postgres start</span>

Test if everything is OK, by entering the following command at the <span class='app'>Terminal</span> (note that the path given <span class='file'>/usr/local/var/</span> is the path to the folder where Homebrew installs Postgres):

<span class='terminal'>$ export PGDATA='/usr/local/var/postgres'</span>

followed by:

<span class='terminal'>$ pg_ctl status</span>

If everything is OK so far, the Terminal should return a PID:

<span class='terminal'>pg_ctl: server is running (PID: xxxxx)</span>

## psql - check users and databases

Postgres comes with a built-in <span class='app'>Terminal</span> access that allows you to carry out administrative functions without needing to know their actual SQL commands, <span class='terminalapp'>psql</span>. Start <span class='terminalapp'>psql</span> by typing at the <span class='app'>Terminal</span> prompt:

<span class='terminal'>$ psql postgres</span>

If you are denied starting <span class='terminalapp'>psql</span>, try:

<span class='terminal'>$ sudo psql postgres</span>

If this does not work either, you have to create the database cluster 'postgres' before continuing.

<span class='terminal'>$ initdb /usr/local/var/postgres</span>.

Then retry starting psql.

When <span class='terminalapp'>psql</span> is active, the last line in the <span class='app'>Terminal</span> window changes and now indicates the database (<span class='terminal'>'postgres'=#</span>), and the <span class='terminalapp'>psql</span> command line prompt (<span class='terminal'>#</span>):

<span class='terminal'>postgres=#</span>

Use the <span class='terminalapp'>psql</span> command line to query PostgreSQL for which users are installed:

<span class='terminal'># \\du</span>

The <span class='app'>Terminal</span> window should return a single role - in PostgreSQL terminology 'user' is called 'role'. By default Homebrew adds the system logged in user ('yourUser') that installed PostgreSQL as a 'SuperUser'. You should also see the attributes connected to 'yourUser', and which groups you are a member of (none at start).

List the the databases that Homebrew installed, by typing the command:

<span class='terminal'># \\l</span>

The <span class='app'>Terminal</span> window returns the installed databases; there should be three: postgres, template0 and template1.  Your role (user) should be the owner of all of them.

Quit <span class='terminalapp'>psql</span> by typing

<span class='terminal'># \\q</span>

When you hit return ou will get back to the ordinary command line prompt <span class='terminal'>$</span>.

## PostgreSQL configuration

Start by creating a new database (db), that will be used for verifying the installation. You can delete (DROP) it later:

<span class='terminal'>$ createdb mytest</span>

Enable the created db by accessing it using <span class='terminalapp'>psql</span>:

<span class='terminal'>$ psql mytest</span>

The <span class='app'>Terminal</span> should change to:

<span class='terminal'>mytest=#</span>

To extend mytest with PostGIS, write the command:

<span class='terminal'>mytest=# CREATE EXTENSION postgis;</span>

If it worked, the <span class='app'>Terminal</span> window should return:

<span class='terminal'>mytest=# CREATE EXTENSION</span>

To see the version of PostGIS, execute:

<span class='terminal'>mytest=# SELECT PostGIS_Version();</span>

You should get some information on your PostGIS installation,

If you want to delete 'mytest', exit <span class='terminalapp'>psql</span>:

<span class='terminal'># \\q</span>

And then write:

<span class='terminal'>$ dropdb mytest</span>

#### Set password and set up roles

Other posts following this, build on the assumption that the db cluster 'postgres' is your production db, and that you have two (2) users: the default user ('yourUser'), and a production user ('prodUser'). There is no problem in doing it in other ways, following the suggestions might just make it easier to remember.

Assuming that you are going to use the db cluster 'postgres' for you production, start <span class='terminalapp'>psql</span> for the 'postgres' db cluster at the <span class='app'>Terminal</span> prompt:

<span class='terminal'>$ psql postgres</span>

The default user added by Homebrew ('yourUser') does not have a password, to set a password, execute the <span class='terminalapp'>psql</span> command:

<span class='terminal'># \\password \'yourUser\'</span>

You must repeat the password twice. If you want to use standard SQL syntax, you can instead write:

<span class='terminal'># ALTER USER \'yourUser\' WITH PASSWORD \'quoted password\';</span>

The basic <span class='terminalapp'>psql</span> command for creating a role is

```
CREATE ROLE username WITH LOGIN PASSWORD 'quoted password' [OPTIONS]
```
where username is the user you want to create, and the password is given with quotes. If you look at the list of users (in the Terminal, after you executed the command <span class='terminal'># \\du</span>), the attributes listed in the central column are typical [OPTIONS]. If you create a role, but give no options, the new role (user) can only read the database, neither create, nor alter nor add anything. Such powers must be explicitly stated as [OPTIONS]. The PostgreSQL documentation contains extensive information on how to use [psql](https://www.postgresql.org/docs/current/static/app-psql.html), and [this page at www.codementor.oi](https://www.codementor.io/devops/tutorial/getting-started-postgresql-server-mac-osx) presents a more digestible summary.

When I set up my postgres db cluster, I set a complex password and keep my own user ('yourUser'). Then I create a second production user ('prodUser') that I use when interacting with Postgres from other applications. I use the <span class='terminalapp'>psql</span> command <span class='terminal'>CREATE USER</span>, that is merely a wrapper to <span class='terminal'>CREATE ROLE</span>, but by default allows the created role(user) to be used for session log in:

<span class='terminal'># CREATE USER prodUser WITH LOGIN PASSWORD \'quoted password\' SUPERUSER CREATEDB CREATEROLE;</span>

In this and the following posts I will call the role (user) and password created here 'prodUser' and 'prodPassword'.

After creating the user you should see 'prodUser' if you list all users:

<span class='terminal'>#\\du</span>

There are so far no tables or schemas in the Postgres db cluster, to check that out you can try the command:

<span class='terminal'># \\dt</span>

<span class='terminalapp'>psql</span> can be used for creating and managing Postgres databases, but you are instead going to install a Graphical User Interface (GUI), and in the next post you will connect Postgres to Python and then use Python for managing the database. Quit <span class='terminalapp'>psql</span>:

<span class='terminal'># \\q</span>

### Install Graphical User Interface

Handling the PostgreSQL database using <span class='app'>psql</span> will become tedious when it grows. Instead you should download a Graphical User Interface (GUI). The primary free alternatives are [pgAdmin](https://www.pgadmin.org) and the light version of [Postico](https://eggerapps.at/postico/). pgAdmin is more comprehensive compared to the (never expiring) trial version of Postico, but Postico feels more modern. Getting tired of both these alternative since I wrote the original post in December 2017, I have now (August 2019) started using [TablePlus](https://tableplus.com) instead.

#### Postico

If downloading Postico it comes as a <span class='file'>.zip</span> file. Just double click to open and drag the <span class='app'>Postico.app</span> to the <span class='file'>/Applications</span> folder.

Start <span class='app'>Postico</span>.

In the window that opens, fill in <span class='textbox'>Nickname</span>, User: <span class='textbox'>'prodUser'</span>, Password: <span class='textbox'>'prodPassword'</span>. Host:  <span class='textbox'>localhost</span>, and Port: <span class='textbox'>5432</span>, are already set. Click <span class='button'>Connect</span>, and you are able to explore your postgreSQL db. As I am not used to Postico, I will instead use pgAdmin to explore the db.

#### pgAdmin

pgAdmin is downloaded as a diskimange <span class='file'>.dmg</span>. Just double click, agree to the licence, and when the diskimage opens, just drag the <span class='app'>pgAdmin</span> to the <span class='file'>/Applications</span> folder.

Start <span class='app'>pgAdmin</span>.

In the window that opens, look for the tab <span class='tab'>Quick links</span> and the text/icon 'Add New Server', and click the latter. In the <span class='tab'>Create server</span> window that opens, select the <span class='tab'>General</span> tab (should be the default), add a <span class='textbox'>Name</span> of your choice, set Server Groups to <span class='textbox'>Servers</span>, add any Comment (or leave blank). Change to the <span class='tab'>Connections</span> tab, and fill in: Host name/address: <span class='textbox'>localhost</span>, Port: <span class='textbox'>5432</span>, Maintenance database:  <span class='textbox'>postgres</span>, Username: <span class='textbox'>'prodUser'</span>, Password: <span class='textbox'>'prodPassword'</span>. You can leave <span class='textbox'>Role</span> blank or set it to 'prodUser'.

#### TablePlus

TablePlus is downloaded as a diskimange <span class='file'>.dmg</span>. Just double click and when the diskimage opens, just drag the <span class='app'>TablePlus</span> to the <span class='file'>/Applications</span> folder. Connect TablePlus to PostgreSQL as shown in figure 1.

<figure>
  <img src="{{ site.commonurl }}/images/{{ site.data.images[page.fig1].file }}" alt="image">
  <figcaption>Figure 1 {{ site.data.images[page.fig1].caption }} </figcaption>
</figure>

## Setting up your production database

Assuming that your 'postgres' database cluster is your production environment, make sure it is the selected db (whether in the <span class='app'>Terminal</span>, <span class='app'>Postico</span>, <span class='app'>pgAdmin</span> or span class='app'>TablePlus</span>). And execute the following SQL command:

```
CREATE EXTENSION postgis;
CREATE EXTENSION postgis_topology;
```

You should get the response:
```
CREATE EXTENSION
CREATE EXTENSION
```

regardless of your environment.

To check if the extensions are in place using <span class='app'>pgAdmin</span> click the small '+' signs to expand the contents of the <span class='tab'>Browse</span> pane:

<span class='menu'>'Servers', 'postgres','Databases','postgres','Extensions'</span>

You then see the extensions, and both postgis and postgis_topology should be included.

## Homebrew postgres load agent

_This section is just if you have problems, and want to, automatically start postgres server at system log in._

Getting a homebrew installed postgres server to start every time you log in to your macOS can be tricky. If you need to manage the automatic service, here is a short introduction.

In macOS the services that start at login are given in <span class='file'>.plist</span> (short for 'property list') files.  These <span class='file'>.plist</span> _launchagents_ are usually either stored in <span class='file'>~/Library/LaunchAgents</span> or <span class='file'>/Library/LaunchAgents</span>. If Homebrew created a launch agent for postgres server, it will be under your home directory, and you can check if it is exists using the <span class='app'>Terminal</span>:

<span class='terminal'>$ ls ~/Library/LaunchAgents</span>

If the <span class='app'>Terminal</span> window returns
<span class='terminal'>No such file or directory</span> you do not have any user specific launch agents (just read on, it will be solved further down).

If the <span class='app'>Terminal</span> window instead returns a list of files, and the list includes a file called <span class='file'>homebrew.mxcl.postgresql.plist</span> (or similar) you have a launch agent for postgres.

If you do not have a postgres associated <span class='file'>.plist</span>, but want Postgres server to start at login, you can copy the Postgres internal <span class='file'>.plist</span> file. The Homebrew installed Postgres <span class='file'>.plist</span> file should be under the path:

<span class='file'>/usr/local/Cellar/postgresql/'version'/</span>

In my system that translates to:

<span class='file'>/usr/local/Cellar/postgresql/10.1/</span>

Thus I can find my Postgres <span class='file'>.plist</span> file by writing at the <span class='app'>Terminal</span>:

 <span class='terminal'>ls /usr/local/Cellar/postgresql/10.1/</span>

 And I see that I have the <span class='file'>.plist</span> file <span class='file'>homebrew.mxcl.postgresql.plist</span>.

If your home directory did not have a launchagents directory (<span class='file'>~/Library/LaunchAgents</span>), create that directory using the <span class='app'>Terminal</span>:

<span class='terminal'>mkdir -p ~/Library/LaunchAgents</span>

Then use the <span class='app'>Terminal</span> to copy the Postgres <span class='file'>.plist</span> you found under <span class='file'>/usr/local/Cellar/postgresql</span> to <span class='file'>~/Library/LaunchAgents</span>

<span class='terminal'> cp /usr/local/Cellar/postgresql/10.1/homebrew.mxcl.postgresql.plist ~/Library/LaunchAgents/</span>

For macOS to actually load a launch agent, you must load it to _launchctl_:

<span class='terminal'>launchctl load  ~/Library/LaunchAgents/homebrew.mxcl.postgresql.plist</span>

And if you want to remove (unload) a <span class='file'>.plist</span> file from launchctl:

<span class='terminal'>launchctl unload  ~/Library/LaunchAgents/homebrew.mxcl.postgresql.plist</span>

## Resources

[PostgreSQL](https://www.postgresql.org)

[Getting Started with PostgreSQL on Mac OSX](https://www.codementor.io/devops/tutorial/getting-started-postgresql-server-mac-osx), at www.codemaster.io.

[How to install PostGIS on Mac OS X](https://morphocode.com/how-to-install-postgis-on-mac-os-x/), at morphocode.com

[Installing postgresql and pgAdmin](https://www.johnplummer.com/rails/installing-postgresql-pgadmin.html), by John Plummer

[pgAdmin](https://www.pgadmin.org)

[Postico](https://eggerapps.at/postico/)

[TablePlus](https://tableplus.com/)
