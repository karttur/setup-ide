---
layout: "post"
title: Spatial Data Integrated Development Environment for Ubuntu 18.04
date: "2019-05-16 05:47"
modified: 2019-05-16 05:47
categories: setup-ide
previousurl: setup-ide/install-anaconda
nexturl: setup-ide/install-postgres
excerpt: "Install all Ubuntu packages required for running Karttur's GeoImagine Framework"
tags:
  - Ubuntu
  - SPIDE
  - install
  - setup
  - snappy
  - snapcraft
image: rainfall-delta_3B43_trmm_2001-2016_mk-z-ts-model
comments: true
share: true
---

This post goes through the steps for installing all software components required for a Spatial Data Integrated Development Environment (SPIDE) in Linux Operating System (OS) Ubuntu 18.04 (bionic). The installed software match the requirements of [Karttur's GeoImagine Framework](https://karttur.github.io/geoimagine/). The instructions are shorthand and refer extensively to online resources with more elaborate and detailed information. Perhaps the best source for hints and tricks on how to get different software packages to work in Linux is [Linux Hint](https://linuxhint.com).

This post assumes that you have already installed Ubuntu 18.04 on your machine.

## Check and update your Ubuntu Installation

To check the version of your Ubuntu installation, open the <span class='app'>Terminal</span> (pressing the keys ctrl+alt+T simultaneously will do that), and enter the command:

<span class='terminal'>$ sudo lsb_release -a</span>

To update and then upgrade your installation, use the two following commands:

<span class='terminal'>$ sudo apt update</span>,

and then

<span class='terminal'>$ sudo apt upgrade</span>.

To execute both together, including answering <span class ='terminal'>yes</span> if, and when, asked to proceed, instead write:

<span class='terminal'>$ sudo apt update -y && sudo apt upgrade -y</span>

## Ubuntu installations

The usual manner in which to add packages (applications, or apps) from the terminal is to use the command <span class='terminal'>sudo apt install</span>:

<span class='terminal'>$ sudo apt install "package"</span>

The install command searches repositories (online libraries) for the _package_ and then installs it, if found. When you install Ubuntu, the default repository for your version is included. You can see which repositories are included by starting the app <span class='app'>Software & Updates</span>. The official documentation for Ubuntu contains a good page on [Repositories](https://help.ubuntu.com/community/Repositories/), and on [Repositories/Commandline](https://help.ubuntu.com/community/Repositories/CommandLine).

To start an app in Ubuntu, click the [window] key or the [Show Application] icon in the screen, either will open the <span class='textbox'>Type to search ...</span> box, in which you start writing (e.g. "Software & Updates"), when the app you search for appears double click in the usual manner.

In the main window of <span class='app'>Software & Updates</span> click the tab <span class='tab'>Other Software</span> and you will see the linked repositories. You can directly add, edit or delete repositories in <span class='app'>Software & Updates</span>. It is, however, faster and more safe to use the <span class='app'>Terminal</span>.

The list of repositories in <span class='app'>Software & Updates</span> comes from a text file <span class='file'>~/etc/apt/sources.list</span>. To inspect the file from the <span class='app'>Terminal</span>, open it using the minimalistic text editor <span class='terminalapp'>pico</span> with the command:

<span class='terminal'>$ pico /etc/apt/sources.list</span>

You will not be able to edit the file unless you open it as the system administrator using <span class='terminalapp'>sudo</span> and then also giving the correct password:

<span class='terminal'>$ sudo pico /etc/apt/sources.list</span>

### Ubuntu Personal Package Archives (PPA)

Less commonly used Ubuntu packages, or later versions of the more common, are not available via the default repositories. To access additional packages (or more updated/nightly built etc versions) you need to app, sorry add, the Personal Package Archives (PPA) in which they reside. The general idea and access to PPA are described in the article [How to Add or Remove PPA in Ubuntu Using GUI and Terminal](https://www.tecmint.com/add-remove-purge-ppa-in-ubuntu/).  

Karttur's GeoImagine Framework rely on a set of packages for spatial data processing, and, very conveniently, there is a PPA for Ubuntu dedicated to Geographical Information Systems (GIS): [**ubuntugis**](https://launchpad.net/~ubuntugis/+archive/ubuntu/ppa). Even if the default Ubuntu repository includes some GIS packages, the **ubuntugis** PPA is more up to date and also includes additional packages.

To add the **ubuntugis** repository to your system (i.e. to the file <span class='file'>sources.list</span>) run the following command in the terminal window:

<span class='terminal'>$ sudo add-apt-repository -y ppa:ubuntugis/ppa</span>

followed by an update of the system for package installations:

<span class='terminal'>$ sudo apt-get update</span>

If you now check using Graphical User Interface (GUI) of <span class='app'>Software & Updates</span> under the tab <span class='tab'>Other Software</span> you should see the link to **ubuntugis/ppa**

For other alternatives on adding/removing repositories and managing the file <span class='file'>sources.list</span> see the article on [How to Add or Remove PPA in Ubuntu Using GUI and Terminal](https://www.tecmint.com/add-remove-purge-ppa-in-ubuntu/).

### Snapcraft - The app store for Linux

Snap, or snappy, is an alternative software deployment and package management system for Linux OS created by Canonical. The main page for snap is [snapcraft.io - The app store for Linux](https://snapcraft.io). Linux 18.04 (bionic) is prepared for using snap and you will use snap for some installations later in this post. In case it does not work, please refer to the [official snapcraft page on installing snap on Ubuntu](https://snapcraft.io/docs/installing-snap-on-ubuntu). To learn more about snap, have a look at the page [How to install and use Snap on Ubuntu 18.04](https://codeburst.io/how-to-install-and-use-snap-on-ubuntu-18-04-9fcb6e3b34f9).

## Package installations

You should now have an idea on how to install software (app) packages on your Ubuntu machine. The rest of the post steps through all the packages required to use your Ubuntu machine as a Spatial Data Integrated Development Environment (SPIDE), with the specific aim of later setting up [Karttur's GeoImagine Framework](https://karttur.github.io/geoimagine/). The instructions in this post summarises the instructions in my blogs on [Install and setup spatial data IDE](https://karttur.github.io/setup-ide/), [Setup Jekyll Theme Blog](https://karttur.github.io/setup-theme-blog/), [Set up blog tools: Jekyll and Atom](https://karttur.github.io/setup-blog/) and [Setup GitHub pages](https://karttur.github.io/setup-github/). These other blogs were written from mac osx.

### GIS packages

In this section you are going to install three GIS software packages: GDAL, QGIS and GRASS. How to install these packages on mac osx is described in [this](https://karttur.github.io/setup-ide/setup-ide/install-gis/) page.

The latest version of the GIS software packages that you are going to install are available from the UbuntuGIS-Stable PPA (**ubuntugis/ppa**), that you added above. If you visit the [UbuntuGIS-Stable PPA](https://launchpad.net/~ubuntugis/+archive/ubuntu/ppa), you can see all spatial data processing packages available via this PPA.

#### Install GDAL

Install the latest version of the [Geographic Data Abstraction Library (GDAL)](https://gdal.org) (2.4.0 at time of writing in August 2019) by the following terminal command:

<span class='terminal'>$ sudo apt -y install gdal-bin python3-gdal</span>

If you are using Python 2, replace "python3" with "python2"

Confirm that the installation went through and the version of GDAL installed by typing:

<span class='terminal'>$ gdalinfo \-\-version</span>

#### Install QGIS

How to install QGIS depends on how you intend to use it. I use QGIS mainly as a viewer; all actual processing in Karttur's GeoImagine Framework are done using Python, GDAL and GRASS. Hence I do not bother too much about my QGIS version, and I can install it with the standard command as described in the [QGIS Installers page](https://www.qgis.org/en/site/forusers/alldownloads.html#debian-ubuntu):

<span class='terminal'>$ sudo apt-get install qgis qgis-plugin-grass</span>

At time of writing (August 2019) this installs the latest Long-Term-Supported (LTS) version 3.4 (Madeira), which is what I wanted. If you want to get a particular version it gets rather more complicated, as described by Shahriar Shovon [How to Install and Get Started with QGIS 3 on Ubuntu 18.04](https://linuxhint.com/install-qgis3-geospatial-ubuntu/).

#### Install GRASS

The latest GRASS version is available via **ubuntugis/ppa** and you can install it by typing (as outlined on the offical GRASS page on [Download GRASS GIS for Linux](https://grass.osgeo.org/download/software/linux/)):

<span class='terminal'>$ sudo apt-get install grass</span>

### Install Anaconda

[Anaconda](https://www.anaconda.com) is a free and open-source Python (and R) distribution for scientific computing that simplify package management and deployment. In Karttur´s GeoImagine Framework Anaconda is the core package manager for linking hundreds of Python packages together. My installation instructions for mac osx is [here](https://karttur.github.io/setup-ide/setup-ide/install-anaconda/).

The official Anaconda instructions for [Installing on Linux](https://docs.anaconda.com/anaconda/install/linux/#), starts by telling how to setup your system to also include the GUI (Anaconda Navigator), which I do not use. So just skip that part unless you want to use the GUI rather than the <span class='app'>Terminal</span> for managing anaconda/conda.

You can also follow the [shorthand instuctions by Lisa Tagliaferri](https://www.digitalocean.com/community/tutorials/how-to-install-anaconda-on-ubuntu-18-04-quickstart), or just go for the minimalistic solution suggested here:

Go to the [Anaconda official downland page](https://www.anaconda.com/distribution/), or click directly on the link to get the [64-Bit (x86) Installer (517 MB)](https://repo.anaconda.com/archive/Anaconda3-2019.07-Linux-x86_64.sh).

The download should end up in your <span class='file'>Downloads</span> folder. You can check the download (_md5_) and then run the installation shell script (the file you actually downloaded) by calling <span class='terminalapp'>bash</span> while giving the full path to the downloaded installer:

<span class='terminal'>$ bash ~/Downloads/Anaconda3-2019.03-Linux-x86_64.sh</span>

Answer <span class='terminal'>yes</span> to all questions and use the [enter] key to flip over the license agreement, and accept it (if you find the text agreeable).

if you answered <span class='terminal'>yes</span> to all questions, your installation will be complete and ready. But you need to stop and restart the terminal to let it take effect. Then you can test if it worked by writing:

<span class='terminal'>$ conda -V</span>

Doing the installation in August 2019 I ended up with version 4.7.10.

To update your Anaconda installation write:

<span class='terminal'>$ conda update conda</span>

In my case, conda got updated to version 4.7.11.

To list all the packages available with your conda installation:

<span class='terminal'>$ conda list</span>

In Karttur's GeoImagine Framework, conda is used for [setting up virtual python environments](https://karttur.github.io/setup-ide/setup-ide/conda-environ/) that are then used by <span class='app'>Eclipse</span> (installed in the next section).

### Install Eclipse

<span class='app'>Eclipse</span>is a Java based Integrated Development Environment (IDE). It is flexible and extensible and the IDE that I use for developing Karttur's GeoImagine Framework. My instructions installing <span class='app'>Eclipse</span> for mac osx are [here](https://karttur.github.io/setup-ide/setup-ide/install-eclipse//). The latter also contains instructions on how to setup <span class='app'>Eclipse</span> for PyDev and write some basic python programmes. Once you have finished the installation for Ubuntu you can continue with [Setup Eclipse for Python development](https://karttur.github.io/setup-ide/setup-ide/install-eclipse/#setup-eclipse-for-python-development).

To install Eclipse you first need to install the correct version of Java Development Kit (JDK) - at time of writing this is either 8, 11 or 12. For our purposes it is usually better to have an older version as the Eclipse development is always behind that of JDK.

There are (at least) two ways to go about the installation: either using <span class='terminalapp'>snap</span> or download a package installer.

#### Snap installation

Installing <span class='app'>Eclipse</span> with <span class='terminalapp'>snap</span> is outlined the page [How to Install the Latest Eclipse IDE on Ubuntu 18.04](https://linuxize.com/post/how-to-install-the-latest-eclipse-ide-on-ubuntu-18-04/).

Before installing <span class='app'>Eclipse</span> install the latest version of JDK:

<span class='terminal'>$ sudo apt install default-jre</span>

Check out the Java installation by typing:

<span class='terminal'>$ java -version</span>

Then install the LTS ("stable") version of Eclipse using snap:

<span class='terminal'>$ sudo snap install \-\-classic eclipse</span>

This worked well for me in August 2019.

#### Controlled installations

Follow the page on [How To Install Eclipse Oxygen IDE On Ubuntu 16.04, 17.10, 18.04](https://websiteforstudents.com/how-to-install-eclipse-oxygen-ide-on-ubuntu-167-04-17-10-18-04/) to get more control over your installation, including versions and packages to install.

If you follow this route and aim at using <span class='app'>Eclipse</span> for setting up Karttur's GeoImagine Framework, the recommendation is to install (the smaller package called) <span class='button'>Eclipse IDE for Java Developers</span>.

### Install Atom

<span class='app'>Atom</span> is a free and open-source text and source code editor with support for plug-ins. It is very versatile and extensible and I use it for keeping track of the documentation related to Karttur's GeoImagine Framework.

The page [How to install Atom editor in Ubuntu](https://codeforgeek.com/install-atom-editor-ubuntu-14-04/) is recommended, and summarised here.

The PPA **webupd8team** contains goodies, including <span class='app'>Atom</span>. To link to the **webupd8team/atom** ppa enter the terminal command:

<span class='terminal'>$ sudo add-apt-repository ppa:webupd8team/atom</span>

Then update the system for package installation:

<span class='terminal'>$ sudo apt-get update</span>

Use <span class='terminal'>apt-get</span> to install <span class='app'>Atom</span>:

<span class='terminal'>$ sudo apt-get install atom</span>

Additional instructions for setting up <span class='app'>Atom</span> for use with the Karttur solution are [here](https://karttur.github.io/setup-blog/2017/12/21/setup-blog-tools.html#install-atom).

### Install and setup Jekyll

Jekyll is a simple, extendable and static web-site generator, and the solution I use for publishing my blogs and other information related to e.g. Karttur's GeoImagine Framework. My Jekyll pages are mainly based on the theme [So Simple](https://github.com/mmistakes/so-simple-theme), with some added functions described in my blog on [Setup Jekyll Theme Blog](https://karttur.github.io/setup-theme-blog/).

Jekyll is built on Ruby, but Ruby is also used for many machine system tasks. To setup Jekyll you need to create a Ruby solution set apart from the machine core system and then build Jekyll on that.

My [Mac osx installation of Jekyll](https://karttur.github.io/setup-blog/2017/12/21/setup-blog-tools.html#install-jekyll-dependencies) was based on the mac osx specific package manager _Homebrew_ and _Ruby Version Manager_ (RVM). For Ubuntu it turned out to be a bit more complicated to install the required Ruby "gems" separated from the system and then get Jekyll to work with these gems.

For this installation (Ubuntu) I started out by following the [Jekyll official installation instructions](https://jekyllrb.com/docs/installation/ubuntu/), but that did not lead to a functional environment. I thus complemented with the hints given by SvennD on [gem: Command not found](https://www.svennd.be/gem-command-not-found/). Alternatively you can follow the more comprehensive post [How to Install Jekyll on Ubuntu 18.04](https://computingforgeeks.com/how-to-install-jekyll-on-ubuntu-18-04/). Here is the sequence of terminal commands that worked for me. Start with installing ruby and ruby-dev:

<span class='terminal'>$ sudo apt-get install ruby ruby-dev</span>

Then you have to add the following lines to the hidden system file <span class='file'>~/.bashrc</span>.
```
# Install Ruby Gems to ~/gems
export GEM_HOME="$HOME/gems"
export PATH="$HOME/gems/bin:$PATH"
```
You can do that by using the terminal editor <span class='terminalapp'>pico</span>

<span class='terminal'>$ sudo pico .bashrc</span>,

and then add the lines manually followed by save and exit (ctrl+x keys pressed simultaneously).

Or use the terminal command <span class='terminalapp'>echo</span> to send the lines to the end of <span class='file'>.bashrc</span>:

<span class='terminal'>echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc</span>

<span class='terminal'>echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc</span>

<span class='terminal'>echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc</span>

Once you have edited <span class='file'>.bashrc</span>, <span class='terminalapp'>source</span> it to make the changes take effect:

<span class='terminal'>$ source ~/.bashrc</span>

Your system should now be ready to receive the Ruby "gems" and put them in the <span class='file'>/gems/</span> folder under your user (separated from any system folder).

<span class='terminal'>$ gem install bundler</span>

<span class='terminal'>$ gem install jekyll</span>

Your <span class='app'>Jekyll</span> installation should now work.

### Create site and start Jekyll

Create a new site from the terminal using Jekyll by typing:

<span class='terminal'>$ jekyll new "new-site-name"</span>

To get to the new site you have to change directory (cd) to it in the terminal:

<span class='terminal'>$ cd "new-site-name"</span>

Jekyll’s built-in lightweight web server will serve content on port 4000. If you have firewall service enabled, allow access to this port:

<span class='terminal'>$ sudo ufw allow 4000</span>

Then start the Jekyll server:

<span class='terminal'>$ bundle exec jekyll serve</span>

With this command, Jekyll takes the setup files and the markdown files it can find, and generates the entire blog, including all blogposts. At the same time Jekyll creates a local server on your computer allowing you to browse the blog. The local url of your blog is written at the prompt (default is http://127.0.0.1:4000/). Copy the url and paste it into your web-browser.

From this point my blog/post on [Set up blog tools: Jekyll and Atom](https://karttur.github.io/setup-blog/2017/12/21/setup-blog-tools.html) details how to use Jekyll. My blog on [Setup Jekyll Theme Blog](https://karttur.github.io/setup-theme-blog/) contains a dozen posts on how to use and customise Jekyll themes, add more bling and process images and videos for publication.

### GitHub Desktop

<span class='app'>GitHub Desktop</span> is a desktop GUI for managing files in the repository version manager [GitHub](www.github.com). This is were I publish my (Jekyll) blogs, but also the repository of Karttur's GeoImagine Framework.

At time of setting this system up on Ubuntu 18.04, the default version of <span class='app'>GitHub Desktop</span> (v2.1.0, or 63) is not fully compatible and will not start. (Otherwise it can be installed with <span class='terminal'>sudo snap install github-desktop \-\-beta \-\-classic</span>).

You can check which versions are avalilable at snapcraft by typing:

<span class='terminal'>snap info github-desktop</span>.

At time of writing this (August 2019) only version 2.1.0 is available (and we need 2.0.4 - to install a [beta] legacy version you could have, in theory, run the command <span class='terminal'>sudo snap install \-\-channel x.y.z/beta \-\-classic github-desktop</span>). To get <span class='app'>GitHub Desktop</span> you must instead download the snap installation file (version 2.0.4 or 62) and run a local installation. Here is how:

If you tried to install github-desktop using the snap command above, but it does not start, remove the installation with the command:

<span class='terminal'>snap remove github-desktop</span>

Then download GitHubDesktop-linux-2.0.4-linux1.snap directly from the [2.0.4 release assets](https://github.com/shiftkey/desktop/releases/tag/release-2.0.4-linux1), and run a local snap installation:

<span class='terminal'>snap install path/to/GitHubDesktop-linux-2.0.4-linux1.snap \-\-classic<span class='terminal'>

where "path/to" should probably be replaced by "~/Downloads", giving the command:

<span class='terminal'>snap install ~/Downloads/GitHubDesktop-linux-2.0.4-linux1.snap \-\-classic<span class='terminal'>

Trying the command most likely leads to an error:

<span class='terminal'>error: cannot find signatures with metadata for snap "..."</span>

This is because snap can not guarantee the security or source of a local file. To bypass the security setting, add the \-\-dangerous flag to the installation:

<span class='terminal'>$ sudo snap install \-\-dangerous GitHubDesktop-linux-2.0.4-linux1.snap \-\-classic</span>

And then I got <span class='app'>GitHub Desktop</span> to work properly.

### PostgreSQL

[PostgreSQL](https://www.postgresql.org) (or postgres for short) is an advanced open source object-relational database system, that can also handle spatial data formats with the extension PostGIS. I use PostgreSQL and PostGIS for handle both processes and data layers when I work with Geo Imagine. My instructions [Install postgreSQL and postGIS](https://karttur.github.io/setup-ide/setup-ide/install-postgres/) for mac osx are initially not useful for Ubuntu, the installations are completely different.

The following <span class='app'>Terminal</span> commands will install PostgreSQL 10 and PostGIS 2.4 un Ubuntu with some additional bling:

<span class='terminal'>$ sudo apt install postgresql-10</span>

<span class='terminal'>$ sudo apt install postgresql-contrib</span>

<span class='terminal'>$ sudo apt install postgresql-10-postgis-2.4</span>

<span class='terminal'>$ sudo apt install postgresql-10-postgis-scripts</span>

<span class='terminal'>$ sudo apt install postgresql-10-pgrouting</span>

Installing <span class='app'>postgres</span> this way creates a default superuser (role in the <span class='app'>postgres</span> jargon) called _postgres_ alongside three databases: postgres, template0 and template1. The latter will have the role _postgres_ as owner.

In order to access the database you need to switch over to the role _postgres_ in the <span class='app'>Terminal</span> by entering the command:

<span class='terminal'> sudo -i -u postgres</span>

If it worked, the prompt will change text and show something like <span class='terminal'>postgres@my-coputer:~$</span>.

You can now access the postgreSQL command line tool <span class='terminalapp'>psql</span> by simply typing <span class='terminal'>psql</span>:

<span class='terminal'>$ psql</span>

The prompt will change from <span class='terminal'>postgres@my-computer:$</span> to <span class='terminal'>postgres=#</span>.

Check the roles are defined by the <span class='terminal'>\\du</span> command:

 <span class='terminal'>postgres=# \\du</span>

 and you should see _postgres_ as a single user.

 To see which databases are installed in postgres, use the <span class='terminal'>\\l</span> command:

<span class='terminal'>postgres=# \\l</span>.

####  Create a new role reflecting your machine user

The role _postgres_ does not have any password and the postgres database is thus open to anyone using the _postrgres_ role. To simplify your access to your postgres database using <span class='terminalapp'>psql</span> you could also create a new user identical to your machine login.

To set a password, execute the <span class='terminalapp'>psql</span> command:

<span class='terminal'># \\password \'yourUser\'</span>

You must repeat the password twice. If you want to use standard SQL syntax, you can instead write:

<span class='terminal'># ALTER USER \'yourUser\' WITH PASSWORD \'quoted password\';</span>

The basic <span class='terminalapp'>psql</span> command for creating a role is

```
CREATE ROLE username WITH LOGIN PASSWORD 'quoted password' [OPTIONS]
```
where username is the user you want to create, and the password is given with quotes. If you look at the list of users (in the Terminal, after you executed the command <span class='terminal'># \\du</span>), the attributes listed in the central column are typical [OPTIONS]. If you create a role, but give no options, the new role (user) can only read the database, neither create, nor alter nor add anything. Such powers must be explicitly stated as [OPTIONS]. The PostgreSQL documentation contains extensive information on how to use [psql](https://www.postgresql.org/docs/current/static/app-psql.html). And the page [How To Install and Use PostgreSQL on Ubuntu 18.04](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-18-04) presents a digestible summary.

####  Postgres GUI

Handling the PostgreSQL database using the <span class='app'>Terminal</span> will become tedious when it grows. The alternative is to install and use a Graphical User Interface (GUI). My instructions on [Install postgreSQL for mac](https://karttur.github.io/setup-ide/setup-ide/install-postgres/) lists three different GUIs. For Ubuntu OS, only <span class='app'>pgAdmin</span> is available of the three. And it has to be installed as a localhost server.

I tested a set of different instructions, but all instructions including the standard installation with <span class='terminal'>$ sudo apt-get install pgadmin4</span> failed. The one instruction I found that worked was on Linux Hint by Fahmida Yesmin [Install PgAdmin4 on Ubuntu](https://linuxhint.com/install-pgadmin4-ubuntu/). This instruction was written in 2017, and you need to download a later version of the pgAdmin python installation file (in my case I downloaded version 4.9 instead of 2.1 as given in the original post).

Here is a quick summary of the required steps:

Update your installation system:

<span class='terminal'>$ sudo apt update -y && sudo apt upgrade -y</span>

Install the following packages:

<span class='terminal'>$ sudo apt-get install build-essential libssl-dev libffi-dev libgmp3-dev virtualenv python-pip libpq-dev python-dev</span>

Make a new directory, and inside create a virtual python environment where the server version of pgAdmin will reside:

<span class='terminal'>$ mkdir pgAdmin4</span>

<span class='terminal'>$ cd pgAdmin4</span>

<span class='terminal'>$ virtualenv pgAdmin4</span>

This will create a second directory also called **pgadmin4** (under the first **pgadmin4**) and you need to change directory into that,

<span class='terminal'>$ cd pgAdmin4</span>,

and then activate the virtual environment you created:

<span class='terminal'>$ source bin/activate</span>.

The prompt should now start with an indication of the virtual environment, <span class='terminal'>(pgadmin4)</span>.

Go to the url hosting the different versions of pgAdmin to check which is the latest: [https://ftp.postgresql.org/pub/pgadmin/pgadmin4](https://ftp.postgresql.org/pub/pgadmin/pgadmin4). At time of writing it is 4.9. Download it using web-get (<span class='terminal'>wget</span>) by entering the following command:

<span class='terminal'>$ wget https://ftp.postgresql.org/pub/pgadmin/pgadmin4/v4.9/pip/pgadmin4-4.9-py2.py3-none-any.whl</span>

When complete, install the downloaded version of pgAdmin using <span class='terminalapp'>pip</span>:

<span class='terminal'>$ pip install pgadmin4-4.9-py2.py3-none-any.whl</span>

To run pgAdmin as a server you need to create a python configuration file:

<span class='terminal'>$ pico lib/python2.7/site-packages/pgadmin4/config_local.py</span>

and add the following lines:
```
import os
DATA_DIR = os.path.realpath(os.path.expanduser(u'~/.pgadmin/'))
LOG_FILE = os.path.join(DATA_DIR, 'pgadmin4.log')
SQLITE_PATH = os.path.join(DATA_DIR, 'pgadmin4.db')
SESSION_DB_PATH = os.path.join(DATA_DIR, 'sessions')
STORAGE_DIR = os.path.join(DATA_DIR, 'storage')
SERVER_MODE = False
```
Save and exit <span class='terminalapp'>pico</span> by simultaneously holding the keys [ctrl]+[x].

If everything worked out, you can now start pgAdmin with the following command:

<span class='terminal'>python lib/python2.7/site-packages/pgadmin4/pgAdmin4.py</span>

To get access to the GUI, you have to use your web-browser. The url for the pgAdmin server is reported in the <span class='app'>Terminal</span> window (should be http://127.0.0.1:5050/). In the GUI you have to add the postgreSQL server that you just setup. How to do that is described in my post on [Install postgreSQL and postGIS (for mac osx)](https://karttur.github.io/setup-ide/setup-ide/install-postgres/#pgadmin) or on the page [Install PgAdmin4 on Ubuntu](https://linuxhint.com/install-pgadmin4-ubuntu/).

To stop the pgAdmin server, simultaneously hold down the keys [ctrl]+[c] in the <span class='app'>Terminal</span> window where it is running.

You should then get the prompt indicating your virtual machine:

<span class='terminal'>(pgadmin4) ... :~/pgadmin4/pgadmin4$</span>

To stop the virtual machine use the command <span class='terminal'>deactivate</span>:

<span class='terminal'>(pgadmin4) ... :~/pgadmin4/pgadmin4$ deactivate</span>

and the prompt indication <span class='terminal'>(pgadmin4)</span> should disappear.

When you want to start the pgAdmin server again, you must repeat the following two steps:

+ Activate the pgadmin4 virtual Environment
+ Run the python pgAdmin4 startup script

From your home directory (~) the commands look like this:

<span class='terminal'>$ source ./pgadmin4/pgadmin4/bin/activate</span>

<span class='terminal'>$ python ./pgadmin4/pgadmin4/lib/python2.7/site-packages/pgadmin4/pgAdmin4.py</span>

## ImageMagick

[ImageMagick](https://www.imagemagick.org) is a command-line (<span class='app'>Terminal</span>) tool for creating, editing, composing, and converting images. The [ImageMagic site](https://www.imagemagick.org) lists other options than the command-line for accessing the image manipulation functions. This post only covers installing ImageMagick in Ubuntu. Using ImageMagick is covered in several other of my blogs, starting [with this post](https://karttur.github.io/setup-theme-blog/blog/install-imagemagick/).

To get ImageMagick up and running I combined the instructions from two different pages:

+ [How to install ImageMagick 7 on Ubuntu 18.04 Linux](https://linuxconfig.org/how-to-install-imagemagick-7-on-ubuntu-18-04-linux)
+ [How to Install ImageMagick 7 on Debian and Ubuntu](https://www.tecmint.com/install-imagemagick-on-debian-ubuntu/)

Here follows a minimalist summary:

Edit the <span class='file'>sources.list</span> by opening it using <span class='terminalapp'>sudo</span> mode:

<span class='terminal'>$ sudo pico /etc/apt/sources.list</span>

then remove the comment <span class='terminal'>#</span> for the line
```
deb-src ... bionic main restricted
```
Run an update

<span class='terminal'>$ sudo apt update</span>

and then run the command <span class='terminal'>apt-get build-dep</span> "packagename" that installs all dependencies for "packagename", preparing the system for its installation:

<span class='terminal'>$ sudo apt build-dep imagemagick</span>

Grab the ImageMagick source code using <span class='terminalapp'>wget</span>:

<span class='terminal'>$ wget https://www.imagemagick.org/download/ImageMagick.tar.gz</span>

Explode the gunzipped (gz) tar-ball (tar):

<span class='terminal'>$ tar xvzf ImageMagick.tar.gz</span>

determine the version (get the folder name by exectuing the list <span class='terminal'>$ ls</span> command ) and change directory (<span class='terminal'>cd</span>) to the newly exploded version:

<span class='terminal'>$ cd ImageMagick-7.0.8-26/</span>

Compile ImageMagick:

<span class='terminal'>$ ./configure</span> (<span class='terminal'>$ ./configure/</span>)

Make the compilation:

<span class='terminal'>$ make</span>(<span class='terminal/'>$ make</span>)

Install the application:

<span class='terminal'>$ sudo make install</span>(<span class='terminal'>$ sudo make install/</span>)

<span class='terminal'>$ sudo ldconfig /usr/local/lib/</span>

If everything worked out, check if the <span class='terminalapp'>magick</span> command gives a response:

<span class='terminal'>$ magick -version</span>

My blog on [Setup Jekyll Theme Blog](https://karttur.github.io/setup-theme-blog/blog/) contains several blog posts on how to use <span class='terminalapp'>ImageMagick</span>, including for map layouts and time-series animations of satellite images.

### FFmpeg

[FFmpeg](https://www.FFmpeg.org/)  is a <span class='app'>Terminal</span> command-line driven "cross-platform solution to record, convert and stream audio and video".

The installation uses the default <span class='terminal'>apt install</span> and is elaborated on, for instance, the page [How to Install and Use FFmpeg on Ubuntu 18.04](https://linuxize.com/post/how-to-install-ffmpeg-on-ubuntu-18-04/). It only requires three steps:

1. Update <span class='terminal'>$ sudo apt update</span>
2. Install <span class='terminal'>$ sudo apt install ffmpeg</span>
3. Check <span class='terminal'>$ ffmpeg -version</span>

My blog on [Setup Jekyll Theme Blog](https://karttur.github.io/setup-theme-blog/blog/) contains several blog posts on how to use <span class='terminalapp'>FFmpeg</span>, including for time-series animations of satellite images.
