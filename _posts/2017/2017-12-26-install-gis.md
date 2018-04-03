---
layout: article
title: 'Install GDAL, QGIS and GRASS'
modified: 2017-12-26T18:17:25.000Z
categories: setup-ide
previousurl: null
nexturl: setup-ide/install-anaconda
excerpt: 'Installing Geospatial data processing engines on macOS; GDAL, QGIS and GRASS'
tags:
  - GDAL
  - QGIS
  - GRASS
  - macOS
image: rainfall-delta_3B43_trmm_2001-2016_mk-z-ts-model@p005
date: '2017-12-26 20:05'
comments: true
share: true
---

**Contents**
	\- [Geospatial data processing engines](#geospatial-data-processing-engines)
	\- [Installations](#installations)
		\- [GDAL](#gdal)
		\- [QGIS](#qgis)
		\- [GRASS](#grass)
	\- [Resources](#resources)

## Geospatial data processing engines
Working with geospatial data requires a Geographic Information System (GIS). There are several commercial packages available, but I only use Open Source (OS) GIS. The Open Source Geospatial Foundation [OSGeo](http://www.osgeo.org) site contains a lot of OS alternatives.

Most of my Geo Imagine processing I do using Python and the Geospatial Data Abstraction Library [(GDAL)](http://www.gdal.org). For specific tasks I use Geographic Resources Analysis Support System [(GRASS)](https://grass.osgeo.org) and for viewing maps, setting colors and creating map layouts I use Quantum GIS [(QGIS)](https://www.qgis.org/en/site/).

## Installations

### GDAL

If you intend to install both GDAL and QGIS on a mac, the QGIS convenience installer maintained by William Kyngesburye ([KyngChaos page](http://www.kyngchaos.com/software:frameworks)), also contains the complete GDAL package, and you can skip installing GDAL separately and jump directly to the QGIS installation.

[GDAL](http://www.gdal.org) is a powerful translator library for creating, modifying and managing geospatial data, and is also at the core of many commercial GIS software packages. GDAL can be downloaded via links on the [GDAL official homepage](http://www.gdal.org). Here I will only cover how to install GDAL on macOS.

GDAL is dependent on several frameworks, including:
* PROJ
* UnixImageIO
* GEOS
* SQLite3

The GDAL convenience installer offered on the [KyngChaos page](http://www.kyngchaos.com/software:frameworks) includes all the necessary frameworks (and is also the official installation site for GDAL on macOS). Go ahead and download the convenience installer for the latest version of GDAL (2.1 at the time of writing). The installer comes as a diskimage (.dmg). Open it by double clicking. The diskimage contains two package installer (<span class='file'>.pkg</span>) and several ReadMe files in Rich Textformat (<span class='file'>.rtf</span>). The installer <span class='file'>GDAL Complete.pkg</span>, installs both GDAL and the necessary dependencies.

Go ahead and install the complete GDAL package by double clicking <span class='file'>GDAL Complete.pkg</span>. You will most likely be denied installing GDAL due to the macOS security settings. To allow installation, go via the mac main menu, click the apple (absolute top left of the computer screen), select <span class='finder'>System Preferences...</span>. In the top row of the <span class='tab'>System Preferences</span> window, click the Security & Privacy icon. In the window that open, under the <span class='tab'>General</span> tab (usually in front by default), you should see a paragraph towards the bottom:
```
"GDAL Complete.pkg" was blocked from opening...
```
Click the button <span class='button'>Open Anyway</span>, and try to install again. The installation should come one step further, but you still have to confirm that you want to to go ahead with the installation in a pop-up window. The <span class='file'>.pkg</span> installer will guide you through the installation. Finally you have to give your user password, and the package will install.

You should also install the <span class='file'>NumPy.pkg</span> available on the GDAl Complete diskimage. You have to follow the same procedure with bypassing the macOS security settings.

If you intend to install GRASS as a stand alone package (GRASS is otherwise included in QGIS), also download and install GDAL 1.11. No need to re-install the <span class='file'>NumPy.pkg</span> if you did that while installing the later GDAL version.

On macOS, GDAL and its dependencies are installed in the Frameworks Library:

<span class="finder">/Library/Frameworks</span>

If you want to have access to GDAL without specifying the full path, add the path in the system file <span class='file'>.bash_profile</span> under your user. To do that, open a Terminal window and make sure you are in your home directory by printing pwd (print working directory) at the prompt:

<span class="terminal">$ pwd</span>

You should then get a tilde(~) followed by your username in return.

<span class="terminal">Computername:~ youruser$</span>

If you are not in your home directory, execute the following command at the Terminal prompt:

<span class="terminal">$ cd ~</span>

You can add the path directly by writing at the command line, or by editing the file <span class='file'>.bash_profile</span>. I prefer editing the file, as I can then comment what I have done. As you are about the edit a system file, you have to use the command sudo (superuser do or substitute user do) together with a text editor. I use the very basic <span class='app'>Terminal</span> text editor <span class='terminalapp'>pico</span>, and the command for opening <span class='file'>.bash_profile</span> in edit mode thus becomes:

<span class="terminal">$ sudo pico .bash_profile</span>

If you already installed Anaconda as described in [this blog post](../2017/2917-12-26-setting-uo-anaconda.html) you will see that the PATH to Anaconda is already given in <span class='file'>.bash_profile</span>. Now you need to add the path to GDAL, **below** any other PATHS already given.

<span class="terminal">PATH=/Library/Frameworks/GDAL.framework/Programs:$PATH</span>

The ':$PATH' at the end adds existing PATHS, and if you do not add it the PATHS above will be lost to the system. I also comment (by starting the line with #) what I have done, and when I did it. Close and save the edits by holding down ctrl-X at the same time, and then click Y when asked to save changes.

Alternative you can write the PATH command directly at the prompt:

id="terminal">$ echo 'export PATH=/Library/Frameworks/GDAL.framework/Programs:$PATH'</span>

GDAL is now ready for use, and if you gave the PATH, the GDAL commands are directly accessible from the command line.

### QGIS

[Quantum GIS](https://www.qgis.org/en/site/) is a popular, and powerful, Open Source GIS software. Download QGIS from the [official homepage](https://www.qgis.org/en/site/). If installing for macOS, you will be linked to the [KyngChaos site](http://www.kyngchaos.com/software/qgis) maintained by William Kyngesburye. Download the QGIS diskimage (<span class='file'>.dmg</span>) installation file. Open the <span class='file'>QGIS-'version'.dmg</span> file. Apart from the package installer (<span class='app'>.pkg</span>) for QGIS it also contains <span class='app'>.pkg's</span> for GDAL, NumPy and matplotlib. If you installed GDAL and NumPy above, no need to repeat that. But you need to install matplotlib before installing QGIS. If you are denied installing you have to bypass the macOS security settings as explained in the previous section on [GDAL](#gdal).

QGIS should now be available in your Applications folder:

<span class='finder'>/Applications/QGIS</span>

I am not a fluent user in QGIS, I mostly use it to view maps, test different color ramps, and create map layouts.

### GRASS

As mentioned above, [GRASS](https://grass.osgeo.org) is included in QGIS. The path to the QGIS internal GRASS package is:

<span class='finder'>/Applications/QGIS.app/Contents/MacOS/grass7</span>

I have not been able to get GRASS 7.x working in macOS so I still use GRASS 6.4 as a stand alone package.

GRASS can be downloaded via the [offical homepage](https://grass.osgeo.org), but for macOS it is easier to use the [KyngChaos](http://www.kyngchaos.com/software/grass) binaries.

To install GRASS on a macOS you must first install GDAL and the dependencies listed above, and then you must also install the following dependencies:

* Freetype
* Cairo

Also they are available via the [KyngChaos site](http://www.kyngchaos.com/software/grass). As with the other installations from KyngChaos, they come as diskimages (<span class='app'>.dmg</span>) and include package installers (<span class='app'>.pkg</span>), and you must bypass the security settings as explained above.

Install GRASS **after** all the required dependencies are installed. I use GRASS for complex processing, including hydrological modelling.

## Resources

[OSGeo](http://www.osgeo.org), the Open Source Geospatial Foundation

[GDAL](http://www.gdal.org/)

[QGIS](https://www.qgis.org)

[GRASS](https://grass.osgeo.org/)

[KyngChaos](http://www.kyngchaos.com/) packaged installers for macOS geospatial data processing engines by William Kyngesburye.

[Install GRASS 7 on macOS](http://schernthanner.de/how-to-get-grass-gis-7-0-5-working-on-mac-os-sierra) by Martin Trauth.
