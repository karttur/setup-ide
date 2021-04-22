---
layout: post
title: SPIDE for Ubuntu 20
date: "2021-03-18"
modified: 2021-03-18
categories: blog
excerpt: "Spatial Data Integrated Development Environment for Ubuntu 20"
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
Fig1: setupide-ubuntu_software-updates-ppa-gis
Fig2: setupide-ubuntu_eclipse-installer01
Fig3: setupide-ubuntu_eclipse-installer02
Fig4: setupide-ubuntu_eclipse-welcome
Fig5: setupide-ubuntu_eclipse-marketplace
---
**Contents**
	- [Introduction](#introduction)
	- [Prerequisites](#prerequisites)
	- [Part 1: Ubuntu software management](#part-1-ubuntu-software-management)
		- [Check and update your Ubuntu Installation](#check-and-update-your-ubuntu-installation)
		- [Ubuntu installations](#ubuntu-installations)
			- [Ubuntu Personal Package Archives (PPA)](#ubuntu-personal-package-archives-ppa)
			- [Snapcraft - The app store for Linux](#snapcraft-the-app- store-for-linux)
	- [Part II: SPIDE components](#part-ii-spide-components)
		- [GIS packages](#gis-packages)
			- [GDAL](#gdal)
			- [QGIS](#qgis)
			- [GRASS](#grass)
		- [Anaconda](#anaconda)
		- [Eclipse](#eclipse)
			- [Snap installation](#snap-installation)
			- [Controlled installations](#controlled-installations)
		- [PostgreSQL](#postgresql)
			- [Create a new role reflecting your machine user](#create-a-new-role-reflecting-your-machine-user)
			- [Postgres GUI](#postgres-gui)
	- [Part III: Editing and publishing apps](#part-iii-editing-and-publishing-apps)
		- [Atom](#atom)
		- [Jekyll](#jekyll)
			- [Create site and start Jekyll](#create-site-and-start-jekyll)
		- [GitHub and GitHub Desktop](#github-and-github-desktop)
	- [Part IV: Media tools](#part-iv-media-tools)
		- [ImageMagick](#imagemagick)
		- [FFmpeg](#ffmpeg)

## Introduction

This post contains four main parts. The first part introduces how to install and manage software in Linux Operating System (OS) Ubuntu 20.04 (Focal Fossa). The second part goes through the steps for installing all software components required for a Spatial Data Integrated Development Environment (SPIDE). The installed software match the requirements of [Karttur's GeoImagine Framework](https://karttur.github.io/geoimagine/). The third part covers installations of additional software for text editing, handling Git repositories and publishing this blog. In the fourth part you will install command-line tools for image and video processing. These tools are required by processes in Karttur's SPIDE related to exporting map layouts and animations of map time series.

The instructions are shorthand and refer extensively to online resources with more elaborate and detailed information. Perhaps the best source for hints and tricks on how to get different software packages to work in Linux is [Linux Hint](https://linuxhint.com). The instructions focuses on using the <span class='app'>Terminal</span>, it you prefer to use the Graphical User Interface (GUI), try out the [Ubuntu Software Center](https://help.ubuntu.com/community/UbuntuSoftwareCenter).

The instructions summarises my other blogs on [Install and setup spatial data IDE](https://karttur.github.io/setup-ide/), [Setup Jekyll Theme Blog](https://karttur.github.io/setup-theme-blog/), [Set up blog tools: Jekyll and Atom](https://karttur.github.io/setup-blog/) and [Setup GitHub pages](https://karttur.github.io/setup-github/). These other blogs were written for mac osx.

## Prerequisites

This post assumes that you have already installed Ubuntu 20.04 on your machine.


## Login or startup your Linux machine

If you have a local Linux machine just power up and login as usual. If coneceting to a sever use the terminal for your operating system:

<span class='terminal'>$ ssh user@machine.domian.country </span>

## Part 1: Ubuntu software management

### Check and update your Ubuntu Installation

To check the version of your Ubuntu installation, open the <span class='app'>Terminal</span> (pressing the keys [ctrl]+[alt]+[T] simultaneously will do that), and enter the command:

<span class='terminal'>$ sudo lsb_release -a</span>

To update and then upgrade your installation, use the two following commands:

<span class='terminal'>$ sudo apt update</span>,

and then

<span class='terminal'>$ sudo apt upgrade</span>.

To execute both commands together, including answering <span class ='terminal'>yes</span> if, and when, asked to proceed, instead write:

<span class='terminal'>$ sudo apt update -y && sudo apt upgrade -y</span>

#### Install Curl

You will need <span class='terminalapp'>Curl</span> fro downloading installation packages further on:

<span class='terminal'>$ sudo apt-get install curl</span>

### Ubuntu installations

The usual manner in which to add packages (applications, or apps) from the terminal is to use the command <span class='terminal'>sudo apt install</span>:

<span class='terminal'>$ sudo apt install \"package\"</span>

The install command searches repositories (online libraries) for the _package_ and then installs it, if found. When you install Ubuntu, the default repository for your version is included. You can see which repositories are included by starting the app <span class='app'>Software & Updates</span>. The official documentation for Ubuntu contains a good page on [Repositories](https://help.ubuntu.com/community/Repositories/), and on [Repositories/Commandline](https://help.ubuntu.com/community/Repositories/CommandLine).

To start an app in Ubuntu, click the [window] key or the [Show Application] icon in the screen, either will open the <span class='textbox'>Type to search ...</span> box, in which you start writing (e.g. "Software & Updates"), when the app you search for appears double click to launch it in the usual manner.

In the main window of <span class='app'>Software & Updates</span> click the tab <span class='tab'>Other Software</span> and you will see the linked repositories. You can directly add, edit or delete repositories in <span class='app'>Software & Updates</span>. It is, however, faster and more safe to use the <span class='app'>Terminal</span>.

The list of repositories in <span class='app'>Software & Updates</span> comes from a text file <span class='file'>~/etc/apt/sources.list</span> (where the tilde "~" denotes your home directory). To inspect the file from the <span class='app'>Terminal</span>, open it using the minimalistic text editor <span class='terminalapp'>pico</span> with the command:

<span class='terminal'>$ pico /etc/apt/sources.list</span>

You will not be able to edit the file unless you open it as the system administrator using <span class='terminalapp'>sudo</span> and then also give the correct password:

<span class='terminal'>$ sudo pico /etc/apt/sources.list</span>

#### Ubuntu Personal Package Archives (PPA)

Less commonly used Ubuntu packages, or later versions of the more common, are not available via the default repositories. To access additional packages (or more updated/nightly built etc versions) you need to app, sorry add, the Personal Package Archives (PPA) in which they reside. The general idea and access to PPA are described in the article [How to Add or Remove PPA in Ubuntu Using GUI and Terminal](https://www.tecmint.com/add-remove-purge-ppa-in-ubuntu/).  

Karttur's GeoImagine Framework rely on a set of packages for spatial data processing, and, very conveniently, there is a PPA for Ubuntu dedicated to Geographical Information Systems (GIS): [**ubuntugis**](https://launchpad.net/~ubuntugis/+archive/ubuntu/ppa). Even if the default Ubuntu repository includes some GIS packages, the **ubuntugis** PPA is more up to date and also includes additional packages.

To add the **ubuntugis** repository to your system (i.e. to the file <span class='file'>sources.list</span>) run the following command in the terminal window:

<span class='terminal'>$ sudo add-apt-repository -y ppa:ubuntugis/ppa</span>

followed by an update of the system for package installations:

<span class='terminal'>$ sudo apt-get update</span>

If you now check the file <span class='file'>sources.list</span>, for example by using <span class='app'>Software & Updates</span> you should see the link to **ubuntugis/ppa** under the tab <span class='tab'>Other Software</span>.

<figure>
<img src="{{ site.commonurl }}/images/{{ site.data.images[page.Fig1].file }}">
<figcaption> {{ site.data.images[page.Fig1].caption }} </figcaption>
</figure>

For other alternatives on adding/removing repositories and managing the file <span class='file'>sources.list</span> see the article on [How to Add or Remove PPA in Ubuntu Using GUI and Terminal](https://www.tecmint.com/add-remove-purge-ppa-in-ubuntu/).

#### Snapcraft - The app store for Linux

Snap, or snappy, is an alternative software deployment and package management system for Linux OS created by [Canonical](https://canonical.com). The main page for snap is [snapcraft.io - The app store for Linux](https://snapcraft.io). Linux 20.04 is prepared for using snap and you will use snap for some installations later in this post. In case it does not work, please refer to the [official snapcraft page on installing snap on Ubuntu](https://snapcraft.io/docs/installing-snap-on-ubuntu). To learn more about snap, have a look at the page [How to install and use Snap on Ubuntu](https://snapcraft.io/install/snap-store/ubuntu).

## Part II: SPIDE components

### GIS packages

In this section you are going to install three GIS software packages: GDAL, QGIS and GRASS. How to install these packages on macos is described in [this](https://karttur.github.io/setup-ide/setup-ide/install-gis/) page.

The latest version of the GIS software packages that you are going to install are available from the UbuntuGIS-Stable PPA (**ubuntugis/ppa**), that you added above. If you visit the [UbuntuGIS-Stable PPA](https://launchpad.net/~ubuntugis/+archive/ubuntu/ppa), you can see all spatial data processing packages available via this PPA.

#### GDAL

Install the latest version of the [Geographic Data Abstraction Library (GDAL)](https://gdal.org) (3.2.2 at time of writing in March 2021) by the following terminal command:

<span class='terminal'>$ sudo apt -y install gdal-bin python3-gdal</span>

If you are using Python 2, replace "python3" with "python2".

Confirm that the installation went through and the version of GDAL installed, by typing:

<span class='terminal'>$ gdalinfo \-\-version</span>

#### QGIS

How to install QGIS depends on how you intend to use it. I use QGIS mainly as a viewer; all actual processing in Karttur's GeoImagine Framework are done using Python, GDAL and GRASS. Hence I do not bother too much about my QGIS version, and I can install it with the standard command as described in the [QGIS Installers page](https://www.qgis.org/en/site/forusers/alldownloads.html#debian-ubuntu):

<span class='terminal'>$ sudo apt-get install qgis qgis-plugin-grass</span>

At time of writing (April 2021) this installs the latest Long-Term-Supported (LTS) version 3.16.4 (Hannover). If you want to get a particular version it gets rather more complicated, as described by Shahriar Shovon [How to Install and Get Started with QGIS 3 on Ubuntu 18.04](https://linuxhint.com/install-qgis3-geospatial-ubuntu/), or the youtube video [Lesson 1: Installing QGIS 3.10 on Ubuntu 20.04](https://www.youtube.com/watch?v=OmZ1BrvrTDI).

#### GRASS

The latest GRASS version is available via **ubuntugis/ppa** and you can install it by typing (as outlined on the official GRASS page on [Download GRASS GIS for Linux](https://grass.osgeo.org/download/software/linux/)):

<span class='terminal'>$ sudo apt-get install grass</span>

### Anaconda

[Anaconda](https://www.anaconda.com) is a free and open-source Python (and R) distribution for scientific computing that simplify package management and deployment. In Karttur´s GeoImagine Framework Anaconda is the core package manager for linking hundreds of Python packages together. My installation instructions for mac osx is [here](https://karttur.github.io/setup-ide/setup-ide/install-anaconda/).

#### GUI installation

The official Anaconda instructions for [Installing on Linux](https://docs.anaconda.com/anaconda/install/linux/#), starts by telling how to setup your system to also include the GUI (Anaconda Navigator), which I do not use. So just skip that part unless you want to use the GUI rather than the <span class='app'>Terminal</span> for managing anaconda/conda.

You can also follow the [shorthand instuctions by Lisa Tagliaferri](https://www.digitalocean.com/community/tutorials/how-to-install-anaconda-on-ubuntu-18-04-quickstart), or just go for the minimalistic solution suggested here:

Go to the [Anaconda official downland page](https://www.anaconda.com/distribution/), or click directly on the link to get the [64-Bit (x86) Installer (517 MB)](https://repo.anaconda.com/archive/Anaconda3-2019.07-Linux-x86_64.sh).

The download should end up in your <span class='file'>Downloads</span> folder. You can check the download (_md5_) and then run the installation shell script (the file you actually downloaded) by calling <span class='terminalapp'>bash</span> while giving the full path to the downloaded installer:

<span class='terminal'>$ bash ~/Downloads/Anaconda3-2019.07-Linux-x86_64.sh</span>

Answer <span class='terminal'>yes</span> to all questions and use the [enter] key to flip over the license agreement, and accept it (if you find the text agreeable).

If you answered <span class='terminal'>yes</span> to all questions, your installation will be complete and ready. But you need to stop and restart the terminal to let it take effect. Then you can test if it worked by writing:

<span class='terminal'>$ conda -V</span>

Doing the installation in April 2021 I ended up with version 4.7.10.

To update your Anaconda installation write:

<span class='terminal'>$ conda update conda</span>

In my case, conda got updated to version 4.10.1.

To list all the packages available with your conda installation:

<span class='terminal'>$ conda list</span>

In Karttur's GeoImagine Framework, conda is used for [setting up virtual python environments](https://karttur.github.io/setup-ide/setup-ide/conda-environ/) that are then used by <span class='app'>Eclipse</span> (installed in the next section).

### Eclipse

<span class='app'>Eclipse</span> is a Java based Integrated Development Environment (IDE). It is flexible and extensible and the IDE that I use for developing Karttur's GeoImagine Framework. Instructions for Mac OSX ["Setup Eclipse for PyDev"](https://karttur.github.io/setup-ide/setup-ide/install-eclipse//) are more extensive compared to the shorthand notes below. Including on how to setup <span class='app'>Eclipse</span> for PyDev and how to write some basic python programmes. Once you have finished the installation for Ubuntu below you can thus learn more by checking [Setup Eclipse for Python development](https://karttur.github.io/setup-ide/setup-ide/install-eclipse/#setup-eclipse-for-python-development).

There are (at least) two ways to go about the <span class='app'>Eclipse</span> installation: either via the <span class='app'>Eclipse Installer</span> or using <span class='terminalapp'>snap</span>. <span class='terminalapp'>snap</span> is easier, but the package installer gives more control. I recommend to use the package installer.

But before you can proceed you need to check your machine Java Development Kit.

#### Java Development Kit (JDK)

Check out the Java installation by typing:

<span class='terminal'>$ java -version</span>

If you get a response, just skip this part. if you get Java related errors when installing Eclipse in the next section, return to this section and follow through.

At time of writing this the available versions of Java Development Kit (JDK) are 8, 11 and 12. For our purposes it is usually better to have an older version as the Eclipse development is always behind that of JDK. Start by trying the default JDK defined with <span class='terminalapp'>apt install</span>

<span class='terminal'>$ sudo apt install default-jre</span>

#### Eclipse Installer

You can either download the default package of Eclipse via the [official homepage](http://www.eclipse.org), or choose a custom Eclipse version from the [package download page](http://www.eclipse.org/downloads/eclipse-packages/). You will get the option to **Try the Eclipse Installer YYYY‑MM R**, which links to a smaller download package. If you choose this option you will get to an Eclipse Installer app with all packages options. You can also download a [legacy version](https://www.eclipse.org/downloads/packages/release/), in which case you will again get the same options for selecting different Eclipse packages.

Once dowloaded and installed start the <span class='app'>Eclipse Installer</span>. Choose the alternative _Eclipse IDE for Java Developers_.

<figure>
<img src="{{ site.commonurl }}/images/{{ site.data.images[page.Fig2].file }}">
<figcaption> {{ site.data.images[page.Fig2].caption }} </figcaption>
</figure>

The system should automatically detect your JDK (either by installing it simultaneously or finding the correct version as as in the figure below). If not you have to search for your JDK installation. You also have to set the target directory for your Eclipse installation. Eclipse is completely stand-alone (it does not depend on any other software) and you can have any number of versions installed. Once you have your JDK identified and the installation folder set, click <span class='button'>INSTALL</span>.

<figure>
<img src="{{ site.commonurl }}/images/{{ site.data.images[page.Fig3].file }}">
<figcaption> {{ site.data.images[page.Fig3].caption }} </figcaption>
</figure>

More detailed instructions for setting up <span class='app'>Eclipse</span> is available on the page [How To Install Eclipse Oxygen IDE On Ubuntu 16.04, 17.10, 18.04](https://websiteforstudents.com/how-to-install-eclipse-oxygen-ide-on-ubuntu-167-04-17-10-18-04/).

#### Snap installation

Installing <span class='app'>Eclipse</span> with <span class='terminalapp'>snap</span> is outlined the page [How to Install the Latest Eclipse IDE on Ubuntu 18.04](https://linuxize.com/post/how-to-install-the-latest-eclipse-ide-on-ubuntu-18-04/). It boils down to the following command:

<span class='terminal'>$ sudo snap install \-\-classic eclipse</span>

#### Install PyDev IDE

Start <span class='app'>Eclipse</span>. Accept, or change, the default working directory and you should reach the Welcome page.

<figure>
<img src="{{ site.commonurl }}/images/{{ site.data.images[page.Fig4].file }}">
<figcaption> {{ site.data.images[page.Fig4].caption }} </figcaption>
</figure>

In the welcome page, choose _Launch the Eclipse Marketplace_ (last row in left column in the figure above). Once in the _Markeplace_ type "pydev" in the <span class='textbox'>Find</span> box and hit the <span class='button'>Go</span> button. One of the results, most likely the topmost, should be _PyDev - Python IDE for Eclipse X.Y.Z_ (as in the figure below). Select it, and click <spna class='button'>Install</span>.

<figure>
<img src="{{ site.commonurl }}/images/{{ site.data.images[page.Fig5].file }}">
<figcaption> {{ site.data.images[page.Fig5].caption }} </figcaption>
</figure>

When the installation finishes you will be asked to restart and the next time you start <span class='app'>Eclipse</span> your setup will be ready for PyDev coding.

### PostgreSQL

[PostgreSQL](https://www.postgresql.org) (or postgres for short) is an advanced open source object-relational database system, that can also handle spatial data formats with the extension PostGIS. I use PostgreSQL and PostGIS for handling both processes and data layers when I work with Geo Imagine. My instructions [Install postgreSQL and postGIS](https://karttur.github.io/setup-ide/setup-ide/install-postgres/) for Mac OSX are initially not useful for Ubuntu, the installations are completely different.

The following <span class='app'>Terminal</span> commands will install PostgreSQL 12 and PostGIS on Ubuntu with some additional bling:

<span class='terminal'>$ sudo apt install postgresql</span>

<span class='terminal'>$ sudo apt install postgresql-contrib</span>

<span class='terminal'>$ sudo apt install postgresql-12-postgis</span>

<span class='terminal'>$ sudo apt install postgresql-12-postgis-scripts</span>

<span class='terminal'>$ sudo apt install postgresql-12-pgrouting</span>

Installing <span class='app'>postgres</span> this way creates a default superuser (role in the <span class='app'>postgres</span> jargon) called _postgres_ alongside three databases: postgres, template0 and template1. The databases will have the role _postgres_ as owner.

In order to access the database you need to switch over to the role _postgres_ in the <span class='app'>Terminal</span> by entering the command:

<span class='terminal'>$ sudo -i -u postgres</span>

If it worked, the prompt will change text and show something like <span class='terminal'>postgres@my-coputer:~$</span>.

You can now access the postgreSQL command line tool <span class='terminalapp'>psql</span> by simply typing <span class='terminal'>psql</span>:

<span class='terminal'>$ psql</span>

The prompt will change from <span class='terminal'>postgres@my-computer:$</span> to <span class='terminal'>postgres=#</span>.

Check the roles defined by the <span class='terminal'>\\du</span> command:

 <span class='terminal'>postgres=# \\du</span>

 and you should see _postgres_ as a single user.

 If the prompt gets stuck, for example showing <span class='terminal'>(END)</span>, try _quitting_ by typing the <span class='terminal'>\\q</span> command. Even if you do not see the response in the terminal window when typing, try it anyway.

 To see which databases are installed in postgres, use the <span class='terminal'>\\l</span> command:

<span class='terminal'>postgres=# \\l</span>

#### Create a new role reflecting your machine user

In this section you will set users (roles in the postgres jargon) and passwords. Remember to note these roles and passwords, and then the next section outlines how to store them.

The role _postgres_ does not have any password and the postgres database is thus open to anyone using the _postgres_ role. To simplify your access to your postgres database using <span class='terminalapp'>psql</span> you could also create a new user identical to your machine login.

To set a password, execute the <span class='terminalapp'>psql</span> command:

<span class='terminal'># \\password \'yourUser\'</span>

You must repeat the password twice. If you want to use standard SQL syntax, you can instead write:

<span class='terminal'># ALTER USER yourUser WITH PASSWORD \'quoted password\';</span>

The basic command for creating a role is:

```
CREATE ROLE username WITH LOGIN PASSWORD 'quoted password' [OPTIONS]
```
where username is the user you want to create, and the password is given with quotes. If you look at the list of users (in the Terminal, after you executed the command <span class='terminal'># \\du</span>), the attributes listed in the central column are typical [OPTIONS]. If you create a role, but give no options, the new role (user) can only read the database, neither create, nor alter nor add anything. Such powers must be explicitly stated as [OPTIONS]. The PostgreSQL documentation contains extensive information on how to use [psql](https://www.postgresql.org/docs/current/static/app-psql.html). And the page [How To Install and Use PostgreSQL on Ubuntu 18.04](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-18-04) presents a digestible summary.

When I set up my postgres db cluster, I set a complex password and keep my own user ('yourUser'). To avoid having to use the command <span class='terminal'> sudo -i -u postgres</span> to login with <span class='terminalapp'>psql</span>, set 'yourUser' identical to you machine login (your standard user). I use the command <span class='terminal'>CREATE USER</span>, that is merely a wrapper to <span class='terminal'>CREATE ROLE</span>, but by default allows the created role(user) to be used for session log in:

<span class='terminal'># CREATE USER yourUser WITH LOGIN PASSWORD \'quoted password\' SUPERUSER CREATEDB CREATEROLE;</span>

Then I create a second, production user ('prodUser') that I use when interacting with Postgres from other applications. That is, from Karttur´s GeoImagine Framework:

<span class='terminal'># CREATE USER prodUser WITH LOGIN PASSWORD \'quoted password\' SUPERUSER CREATEDB CREATEROLE;</span>

In this and the following posts I will call the role (user) and password created here 'prodUser' and 'prodPassword'.

After creating the user you should see 'prodUser' if you list all users:

<span class='terminal'>#\\du</span>

There are so far no tables or schemas in the Postgres db cluster, to check that out you can try the command:

<span class='terminal'># \\dt</span>

<span class='terminalapp'>psql</span> can be used for creating and managing Postgres databases, but you are instead going to install a Graphical User Interface (GUI), and in the next post you will connect Postgres to Python and then use Python for managing the database. Quit <span class='terminalapp'>psql</span>:

<span class='terminal'># \\q</span>

#### Change password

If you need to change the password, the command for that is:

<span class='terminal'># ALTER USER user_name WITH PASSWORD \'quoted new password\';</span>

#### Return to system user

Return to your system user with the command _switch user_ <span class='terminalapp'>su</span>:

<span class0'terminal>$ su \"youruser\"</span>

#### Secure storing of roles passwords

If you are only going to use your Postgres database as localhost (on your own machine), security is less important. But if you want to protect your data you must set some level of security. The solution I use is primarily for macOS and UNIX/Linux systems, and is not very advanced. I use a combination of storing my password in my home directory (~) combined with a simple encryption.

Create a file in your home directory (~) called <span class='file'>.netrc</span> that defines your credentials. An [earlier post](https://karttur.github.io/setup-blog/2017/12/21/setup-blog-tools.html#opening-and-understanding-the-terminal) describes how to use the <span class='app'>Terminal</span> for creating and editing files in detail. In the <span class='app'>Terminal</span> go to your home directory:

<span class='terminal'>$ cd ~</span>

Then start the <span class='app'>Terminal</span> text editor <span class='terminalapp'>pico</span> for editing/creating the file:

<span class='terminal'>$ pico .netrc</span>

Enter the two lines below (but with your role/user and password), one for the default user (which should be 'psotgres' if you followed the instructions above), and one for the production user ('prodUser') that you just defined. If you only have the default user, enter the same login and password in both lines.
```
machine localhost0 login yourUser password yourPassword
machine localhost1 login prodUser password prodPassword
```
Exit <span class='terminalapp'>pico</span> (ctrl-X) and save the file (click Y when asked to save). You probably have to change the read and write permissions for <span class='file'>.netrc</span>, which you do by executing the following Terminal command:

<span class='terminal'>$ chmod og-rw .netrc</span>

With this solution your credentials will only be explicitly written out in a hidden file and you can always have a look in the file <span class='file'>.netrc</span> if you forget your user(s) and password(s).

#### Postgres GUI

Handling the PostgreSQL database using the <span class='app'>Terminal</span> will become tedious when it grows. The alternative is to install and use a Graphical User Interface (GUI). My instructions on [Install postgreSQL for mac](https://karttur.github.io/setup-ide/setup-ide/install-postgres/) lists three different GUIs. When originally writing this post (August 2019) only [<span class='app'>pgAdmin</span>]() was available for Linux. But since then an early version of [<span class='app'>TablePlus</span>](https://tableplus.com/blog/2019/10/tableplus-linux-installation.html) has also become available. TablePlus is much easier to install.

##### TablePlus

At time of writing this (March 2020) [<span class='app'>TablePlus</span>](https://tableplus.com/blog/2019/10/tableplus-linux-installation.html) is available in an alpha version for linux (ubuntu). The commands below are directly from the TablePlus instructions.

Add TablePlus gpg key

<span class='terminal'>$ wget \-O \- \-q http://deb.tableplus.com/apt.tableplus.com.gpg.key | sudo apt-key add \- </span>

Add TablePlus repo

<span class='terminal'>$ sudo add-apt-repository \"deb [arch=amd64] https://deb.tableplus.com/debian tableplus main\"</span>


Install

<span class='terminal'>$ sudo apt update
sudo apt install tableplus</span>

Then run a general update for your system, that will also affect the TablePlus installation:

<span class='terminal'>$ sudo apt update && sudo apt upgrade</span>

##### pgAdmin

I did not try to install pgAdmin for postgres 12 under Ubuntu 20.04. My post on [how to set up Karttur's GeoImagine Framework for Ubuntu 18.04](../ubuntu-setup-spide//#postgres-gui), however, contains the instructions for how to setup pgAdmin under Ubuntu.

## Part III: Editing and publishing apps

### Atom

<span class='app'>Atom</span> is a free and open-source text and source code editor with support for plug-ins. It is very versatile and extensible and I use it for keeping track of the documentation related to Karttur's GeoImagine Framework.

The page [How to Install Atom Text Editor on Ubuntu 20.04](https://linuxize.com/post/how-to-install-atom-text-editor-on-ubuntu-20-04/) outlins different ways to install <span class='app'>Atom</span> on Ubuntu 20.04.

The easiest way to install Atom is to use snap:

<span class='terminal'>$ sudo snap install atom --classic</span>

### Jekyll

Jekyll is a simple, extendable and static web-site generator, and the solution I use for publishing my blogs and other information related to e.g. Karttur's GeoImagine Framework. My Jekyll pages are mainly based on the theme [So Simple](https://github.com/mmistakes/so-simple-theme), with some added functions described in my [Setup Jekyll Theme Blog](https://karttur.github.io/setup-theme-blog/).

Jekyll is built on Ruby, but Ruby is also used for many machine system tasks. To setup Jekyll you need to create a Ruby solution set apart from the machine core system and then build Jekyll on that.

My [Mac osx installation of Jekyll](https://karttur.github.io/setup-blog/2017/12/21/setup-blog-tools.html#install-jekyll-dependencies) was based on the mac osx specific package manager _Homebrew_ and _Ruby Version Manager_ (RVM). For Ubuntu it turned out to be a bit more complicated to install the required Ruby "gems" separated from the system and then get Jekyll to work with these gems.

For this installation (Ubuntu) I started out by following the [Jekyll official installation instructions](https://jekyllrb.com/docs/installation/ubuntu/), but that did not lead to a functional environment. I thus complemented with the hints given by SvennD on [gem: Command not found](https://www.svennd.be/gem-command-not-found/). Alternatively you can follow the more comprehensive post [How to Install Jekyll on Ubuntu 18.04](https://computingforgeeks.com/how-to-install-jekyll-on-ubuntu-18-04/). Here is the sequence of terminal commands that worked for me.

Start with installing ruby and ruby-dev:

<span class='terminal'>$ sudo apt-get install ruby ruby-dev</span>

Then you have to add the following lines to the hidden system file <span class='file'>~/.bashrc</span>.
```
# Install Ruby Gems to ~/gems
export GEM_HOME="$HOME/gems"
export PATH="$HOME/gems/bin:$PATH"
```
You can do that by using the terminal editor <span class='terminalapp'>pico</span>

<span class='terminal'>$ sudo pico .bashrc</span>,

and then add the lines manually followed by save and exit ([ctrl]+[x] keys pressed simultaneously).

Or use the terminal command <span class='terminalapp'>echo</span> to send the lines to the end of <span class='file'>.bashrc</span>:

<span class='terminal'>echo \'# Install Ruby Gems to ~/gems\' >> ~/.bashrc</span>

<span class='terminal'>echo \'export GEM_HOME=\"$HOME/gems\"\' >> ~/.bashrc</span>

<span class='terminal'>echo \'export PATH=\"$HOME/gems/bin:$PATH\"\' >> ~/.bashrc</span>

Once you have edited <span class='file'>.bashrc</span>, <span class='terminalapp'>source</span> it to make the changes take effect:

<span class='terminal'>$ source ~/.bashrc</span>

Your system should now be ready to receive the Ruby "gems" and put them in the <span class='file'>/gems/</span> folder under your user (separated from any system folder).

<span class='terminal'>$ gem install bundler</span>

<span class='terminal'>$ gem install jekyll</span>

Your <span class='app'>Jekyll</span> installation should now work.

#### Create site and start Jekyll

Create a new site from the terminal using Jekyll by typing:

<span class='terminal'>$ jekyll new \"new-site-name\"</span>

To get to the new site you have to change directory (cd) to it in the terminal:

<span class='terminal'>$ cd \"new-site-name\"</span>

Jekyll’s built-in lightweight web server will serve content on port 4000. If you have firewall service enabled, allow access to this port:

<span class='terminal'>$ sudo ufw allow 4000</span>

Then start the Jekyll server:

<span class='terminal'>$ bundle exec jekyll serve</span>

With this command, Jekyll takes the setup files and the markdown files it can find, and generates the entire blog, including all blogposts. At the same time Jekyll creates a local server on your computer allowing you to browse the blog. The local url of your blog is written at the prompt (default is http://127.0.0.1:4000/). Copy the url and paste it into your web-browser.

From this point my blog/post on [Set up blog tools: Jekyll and Atom](https://karttur.github.io/setup-blog/2017/12/21/setup-blog-tools.html) details how to use Jekyll. My blog on [Setup Jekyll Theme Blog](https://karttur.github.io/setup-theme-blog/) contains a dozen posts on how to use and customise Jekyll themes, add more bling and process images and videos for publication.

### Git

To setup <span class='terminalapp'>git</span> just open a terminal and execute the command:

<span class='terminal'>$ sudo apt install git</span>

My instructions on [setting up SPIDE for Ubuntu 18.04](../ubuntu-setup-spide) has a lengthy section on how to get [GitHub desktop](https://desktop.github.com) to work un Ubuntu 18.04.

How to actually use <span class='terminalapp'>git</span> is covered in my blog on [git command line](https://karttur.github.io/git-vcs/).

## Part IV: Media tools

### ImageMagick

[ImageMagick](https://www.imagemagick.org) is a command-line (<span class='app'>Terminal</span>) tool for creating, editing, composing, and converting images. The [ImageMagic site](https://www.imagemagick.org) lists other options than the command-line for accessing the image manipulation functions. This post only covers installing ImageMagick in Ubuntu. Using ImageMagick is covered in several other of my blogs, starting [with this post](https://karttur.github.io/setup-theme-blog/blog/install-imagemagick/).

For Ubuntu 20.04 you can install ImageMagick with sudo apt:

<span class='terminal'>$ sudo apt install imagemagick</span>

### FFmpeg

[FFmpeg](https://www.FFmpeg.org/)  is a <span class='app'>Terminal</span> command-line driven "cross-platform solution to record, convert and stream audio and video".

The installation uses the default <span class='terminal'>apt install</span> and is elaborated on, for instance, the page [How to Install and Use FFmpeg on Ubuntu 18.04](https://linuxize.com/post/how-to-install-ffmpeg-on-ubuntu-18-04/). It only requires three steps:

1. Update <span class='terminal'>$ sudo apt update</span>
2. Install <span class='terminal'>$ sudo apt install ffmpeg</span>
3. Check <span class='terminal'>$ ffmpeg -version</span>

My blog on [Setup Jekyll Theme Blog](https://karttur.github.io/setup-theme-blog/blog/) contains several posts on how to use <span class='terminalapp'>FFmpeg</span>, including for time-series animations of satellite images.
