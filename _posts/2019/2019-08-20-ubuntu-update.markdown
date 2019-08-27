---
layout: "post"
title: Setup Karttur's GeoImagine Framework on Ubuntu 18.04
date: "2019-05-16 05:47"
modified: 2019-05-16 05:47
categories: setup-ide
previousurl: setup-ide/install-anaconda
nexturl: setup-ide/install-postgres
excerpt: "Python graphic packages."
tags:
  - Ubuntu
  - SPIDE
  - ubuntu
image: rainfall-delta_3B43_trmm_2001-2016_mk-z-ts-model
comments: true
share: true
---

This post goes through all the steps for setting up Karttur's GeoImagine Framework for the Linux Operating System (OS) Ubuntu 18.04 (bionic). The instructions are shorthand and refers extensively to online resources with more elaborate and detailed information. One source of a lot of hints and tricks related to linux is the [VITUX Linux Compendium](https://vitux.com), another is [Linuxhint](https://linuxhint.com).

This post assumes that you have already installed Ubuntu 18.04 on your machine.

## Check and update your Ubuntu Installation

To check the version of your Ubuntu installation, open the <span class='app'>Terminal</span> (pressing the keys ctrl+alt+T simultaneously will do that), and enter the command:

<span class='terminal'>$ sudo lsb_release -a</span>

To update and then upgrade your installation, use the two following commands:

<span class='terminal'>$ sudo apt update</span>,

and then

<span class='terminal'>$ sudo apt upgrade</span>.

## Installing packages on Ubuntu

The usual manner in which to add packages (applications, or apps) from the terminal is to use the command <span class='terminal'>sudo apt install</span>:

<span class='terminal'>$ sudo apt install "package"</span>

The install command searches repositories (online libraries) for the _package_ and then installs it if found. When you install Ubuntu, the default repository for your version is included - for version 18.04 it is something like "http://ppa.launchpad.net/ubuntugis/ppa/ubuntu". You can see which repositories are included by starting the app <span class='app'>Software & Updates</span> (click the [window] key or the [Show Application] icon in the screen, either will the <span class='textbox'>Type to search ...</span> box and start writing "Software & Updates"). In the main window of <span class='app'>Software & Updates</span> click the tab <span class='tab'>Other Software</span> and you will see the linked repositories. You can directly add, edit or delete repositories in the <span class='app'>Software & Updates</span>. it is, however, faster and more safe to use the <span class='app'>Terminal</span>.

The list of repositories in <span class='app'>Software & Updates</span> comes from a text file <span class='file'>~/etc/apt/sources.list</span>. If you want to inspect the file from the <span class='app'>Terminal</span>, open it using the command:

<span class='terminal'>$ pico /etc/app/souces.list</span>

You will not be able to edit the file unless you open it as the system administrator and giving the correct password:

<span class='terminal'>$ sudo pico /etc/app/souces.list</span>

### Ubuntu Personal Package Archives (PPA)

Non-default Ubuntu packages (apps) are usually not available via the default repositories. To access additional packages (or more updated/nightly built etc versions) you need to app the Personal Package Archives (PPA) at which they reside. The general idea and access to PPA are described in the article [How to Add or Remove PPA in Ubuntu Using GUI and Terminal](https://www.tecmint.com/add-remove-purge-ppa-in-ubuntu/).  

Karttur's GeoImagine Framework rely on a set of packages for spatial processing, and, very conveniently, there is a PPA for Ubuntu dedicated to Geographical Information Systems (GIS): **ubuntugis**. Even if the default Ubuntu repository include some GIS packages, the **ubuntugis** is more up to date and also include additional packages.

To add the **ubuntugis** repositry to your system (i.e. to the file <span class='file'>sources.list</span>) run the following command in the terminal window:

<span class='terminal'>$ sudo add-apt-repository -y ppa:ubuntugis/ppa</span>


### PPA for Ubuntu GIS apps

The latest version of GDAL (and other GIS Packages) are available from UbuntuGIS-Stable PPA that needs to be added to the apt-repository:

<span class='terminal'>$ sudo add-apt-repository -y ppa:ubuntugis/ppa</span>

If you now check using Graphical User Interface (GUI) of <span class='app'>Software & Updates</span> under the tab <span class='tab'>Other Software</span> you should see the link to **ubuntugis/ppa**

Upgrade the whole system in two steps:

<span class='terminal'>$ sudo apt update</span>

<span class='terminal'>$ sudo apt -y upgrade</span>

All the GIS packages we are interested in for now (GDAL, QGIS and GRASS) are available via **ubuntugis/ppa**. Thus the installations of all three should be straight forward if you added the **ubuntugis/ppa**.

If you visit the [UbuntuGIS-Stable PPA](https://launchpad.net/~ubuntugis/+archive/ubuntu/ppa), you can see a all spatial data processing packages available via this PPA.

### Install GDAL

Then install the latest GDAL version (2.4.0 at time of writing in August 2019):

<span class='terminal'>$ sudo apt -y install gdal-bin python3-gdal</span>

If you are using Python 2, replace "python3" with "python2"

Confirm that the installation went thorugh, and the version of GDAL installed by typing
<span class='terminal'>$ gdalinfo --version</span>

### Install QGIS

How to install QGIS depends on how you intend to use it. I only use QGIS as a viewer, all actual processing is done using Python, GDAL and GRASS. Hence I do not bother too much about my QGIS version, and I can install it with the standard command as described in the [QGIS Installers page](https://www.qgis.org/en/site/forusers/alldownloads.html#debian-ubuntu):

<span class='terminal'>$ sudo apt-get install qgis qgis-plugin-grass</span>


At time of writing this (August 2019) this installs the latest Long-Term-Supported (LTS) version 3.4 (Madeira) from **ubuntugis/ppa**, which is what I wanted. If you want to get a particular version it gets rather more complicated, as described by Shahriar Shovon [How to Install and Get Started with QGIS 3 on Ubuntu 18.04](https://linuxhint.com/install-qgis3-geospatial-ubuntu/)

### Install GRASS

Also the latest GRASS version is available via **ubuntugis/ppa** and you can install it by typing (as outilned on the page [Download GRASS GIS for Linux](https://grass.osgeo.org/download/software/linux/)):

<span class='terminal'>$ sudo apt-get install grass</span>

## Install Anaconda

Anaconda is ....[Anaconda]()

The official tutorial for [Installing on Linux](https://docs.anaconda.com/anaconda/install/linux/#) is here. It starts with instrictions for how to setup you system to also include the GUI (Anaconda Navigator), which I do not use. You can aloso folow the [shprthand instuctions by Lisa Tagliaferri](https://www.digitalocean.com/community/tutorials/how-to-install-anaconda-on-ubuntu-18-04-quickstart). Here is an even shorter summary

Go to the [Anaconda official downland page](https://www.anaconda.com/distribution/), choose Linux and then the [64-Bit (x86) Installer (517 MB)](https://repo.anaconda.com/archive/Anaconda3-2019.07-Linux-x86_64.sh).

The download should end up in your <span class='file'>Downloads</span> folder. You can check the download (md5) and then run the installation shell script (the file you actually downloaded) by calling <span class='terminalapp'>bash</span> and giving the full path to the downloaded installer:

<span class='terminal'>$ bash ~/Downloads/Anaconda3-2019.03-Linux-x86_64.sh</span>

Answer <span class='termnal'>yes</span> to all questions and user the <enter> key to flip over the license agreement, and accept it (it you find the text agreeable).

if you answered <span class='termnal'>yes</span> to all questions, your installation will be complete and ready. But you need to stop and restart the terminal to let it take effect. Then you can test if it worked by writing:

span class='terminal'>$ conda -V</span>


To update your Anaconda distribution, type:

span class='terminal'>$ conda update conda</span>

Tolist all the packages available with your conda installation:

<span class='terminal'>$ conda list</span>

## Install Eclipse

Eclipse is a Java based Integrated Development Environment (IDE). It is flexible and exnesible and it is the IDE that I have used for developing Karttur's GeoImagine Framework. To install Eclipse you first need to install the correct version of Java Development Kit (JDK) - at time of writing this is either 8, 11 or 12. For our purposes it is usually better to have an older version as the Eclipse development is always behind the JDK development.

There are (at least) two ways to go about the installation: either using <span class='terminalapp'>snap</span> or download a package installer. <span class='terminalapp'>snap</span> (or "snappy") is an alternative software deployment to <span class='terminal'>apt-get</span> (page explaing [How to install and use Snap on Ubuntu 18.04](https://codeburst.io/how-to-install-and-use-snap-on-ubuntu-18-04-9fcb6e3b34f9)).


### Snap installation

Installing <span class='app'>Eclipse</span> with <span class='terminalapp'>snap</span> is outined the page [How to Install the Latest Eclipse IDE on Ubuntu 18.04](https://linuxize.com/post/how-to-install-the-latest-eclipse-ide-on-ubuntu-18-04/).

Install the latest version of JDK:


<span class='terminal'>$ sudo apt install default-jre</span>

the install the LTS version of Eclipse using snap

<span class='terminal'>$ sudo snap install --classic eclipse</span>

### Controlled installations

Follow the page on [How To Install Eclipse Oxygen IDE On Ubuntu 16.04 | 17.10 | 18.04](https://websiteforstudents.com/how-to-install-eclipse-oxygen-ide-on-ubuntu-167-04-17-10-18-04/) to get more control over your installation, including versions and packages to install.


## Install Atom

The instructions on  [How to install Atom editor in Ubuntu](https://codeforgeek.com/install-atom-editor-ubuntu-14-04/) that I followed, summarised here.

The PPA **webupd8team** contains goodies, including <span class='app'>Atom</span>. To link to the **webupd8team/atom** ppa enter the terminal command:

<span class='terminal'>$ sudo add-apt-repository ppa:webupd8team/atom</span>

update the repository:

<span class='terminal'>$ sudo apt-get update</span>

and then you can use <span class='terminal'>apt-get</span> to install <span class='app'>Atom</span>

<span class='terminal'>$ sudo apt-get install atom</span>

And yu should be ready to use <span class='app'>Atom</span>.


## Install and setup JEkyll

My [Mac osx installation of Jekyll]() was based on the Package manager _Homebrew_ and _Ruby Version Manager_ (RVM). This created a system inidde flexible environment, set aside from the machine version of Ruby. For Ubuntu it is a bit more complicated to to get the RVM installation. Actually, it turned out to be a bit complicated even without RVM.


To get Ruby and Jekyll up and running, I followed the [Jekyll official page installation instructions](https://jekyllrb.com/docs/installation/ubuntu/) complemented with the hints given by SvennD on [gem: Command not found](https://www.svennd.be/gem-command-not-found/). Alternatively you can follow this more [comprehensive instruction](https://computingforgeeks.com/how-to-install-jekyll-on-ubuntu-18-04/). Here is the sequence of terminal commands that worked for me.

<span class='terminal'>$ sudo apt-get install ruby-full build-essential zlib1g-dev</span>

<span class='terminal'>$ sudo apt-get install ruby ruby-dev</span>

manually add the following lines to ~/.bashrc, go to your home directory <span class='terminal'>$ cd ~</span>, followed by

<span class='terminal'>$ pico .bashrc</span>

then add the folowing lines at the end:

```
# Install Ruby Gems to ~/gems
export GEM_HOME="$HOME/gems"
export PATH="$HOME/gems/bin:$PATH"
```

Save and exit (<span class='terminal'>ctrl-x~</span>)

or use the terminal command <span class='terminal'>$ echo</span> to send the lines to the end of <span class='file'>.bashrc</span>

<span class='terminal'>echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc</span>
<span class='terminal'>echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc</span>
<span class='terminal'>echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc</span>

Once you have edited <span class='file'>.bashrc</span>, source it to make the changes take effect:

<span class='terminal'>$ source ~/.bashrc</span>

Your system should now be ready to receive the "gems" and put them in the <span class='file'>/gems/</span> folder under your user.

<span class='terminal'>$ gem install bundler</span>

<span class='terminal'>$ gem install jekyll</span>

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

From this point my blog/post on [Set up blog tools: Jekyll and Atom](https://karttur.github.io/setup-blog/2017/12/21/setup-blog-tools.html) details how to use Jekyll.

## GitHub Desktop

At time of setting this system up on Ubuntu 18.04, the default version of <span class='app'>GitHub Desktop</span> (version 63) is not fully compatible and will not start. (Otherwise it can be installed with <span class='terminal'>sudo snap install github-desktop --beta --classic</span>).

If you tried to install github-desktop using snap, but it does not start, remove the installation with the command:

<span class='terminal'>snap remove github-desktop</snap>

Then download GitHubDesktop-linux-2.0.4-linux1.snap directly from the [2.0.4 release assets](https://github.com/shiftkey/desktop/releases/tag/release-2.0.4-linux1), and run a local snap installation:

<span class='terminal'>snap install path/to/GitHubDesktop-linux-2.0.4-linux1.snap --classic<span class='terminal'>

where "path/to" should probably be replaced by "~/Downloads", giving the command:

<span class='terminal'>snap install ~/Downloads/GitHubDesktop-linux-2.0.4-linux1.snap --classic<span class='terminal'>

I then got the error:

<span class='terminal'>error: can not find signatures with metadata for snap ...</span>

But it then eports an error like:

<span class='terminal'>error: Snap install failure: error: cannot find signatures with metadata for snap</span>

This is because snap can not guarantee the security or source of a local file. To bypass the security setting, add the --dangerous flag to the installation:

<span class='terminal'>$ sudo snap install --dangerous GitHubDesktop-linux-2.0.4-linux1.snap --classic</span>

And then I got <span class='app'>GitHub Desktop</span> to work properly

## PostgreSQL

The following will install PostgreSQL 10 and PostGIS 2.4:

<span class='terminal'>$ sudo apt install postgresql-10</span>
<span class='terminal'>$ sudo apt install postgresql postgresql-10-contrib</span>
<span class='terminal'>$ sudo apt install postgresql-10-postgis-2.4</span>
<span class='terminal'>$ sudo apt install postgresql-10-postgis-scripts</span>
<span class='terminal'>$ sudo apt install postgresql-10-pgrouting</span>

Follow the page [How To Install and Use PostgreSQL on Ubuntu 18.04](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-18-04) to set up the initial user and database. Then follow the original

## ImageMAgick

 I followed the page on [How to Install ImageMagick 7 on Debian and Ubuntu](hhttps://www.tecmint.com/install-imagemagick-on-debian-ubuntu/) for installing ImageMagick on Ubuntu. It worked on the first try.

 Edit the <span class='file'>sources.list</span>:

<span class='terminal'>$ sudo pico /etc/apt/sources.list</span>

then remove the comment for the lines
```
deb-src ...bionic main restricted
```
S

Then Update
<span class='terminal'>$ sudo apt Update</span>
and installs

<span class='terminal'>$ sudo apt build-dep ImageMAgick</span>


## FFmpeg

Just follow the page [How to Install and Use FFmpeg on Ubuntu 18.04](https://linuxize.com/post/how-to-install-ffmpeg-on-ubuntu-18-04/)



# Resourcea

Official page on [Installing RVM](https://rvm.io/rvm/install) and
[GitHub page for RVM installation on Ubuntu](https://github.com/rvm/ubuntu_rvm).

First install Roby version Managet (RVM). You haveto add the PPA **rael-gc/rvm**

<span class='terminal'>$ sudo add-apt-repository ppa:rael-gc/rvm</span>

<span class='terminal'>$ sudo apt-get update</span>

<span class='terminal'>$ sudo apt-get install rvm</span>

# Resources

[How to Keep Ubuntu Up to Date](https://vitux.com/how-to-keep-ubuntu-up-to-date/) VITUX Linux Compendium

[Installing GDAL on Ubuntu](https://thegeoict.com/blog/2018/07/17/installing-gdal-on-ubuntu/) The Geo-ICT Blog

https://launchpad.net/~ubuntugis/+archive/ubuntu/ppa
https://launchpad.net/~ubuntugis/+archive/ubuntu/ppa
https://vitux.com


[Anaconda](https://www.anaconda.com)
