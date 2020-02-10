---
layout: article
title: Install Anaconda
categories: setup-ide
previousurl: setup-ide/install-gis
nexturl: setup-ide/conda-environ
excerpt: Install Anaconda Python package on macOS
tags:
  - Anaconda
  - Python
  - macOS
image: avg-trmm-3b43v7-precip_3B43_trmm_2001-2016_A
date: '2017-12-27 19:35'
modified: 2020-02-10
comments: true
share: true
---

**Contents**
	- [Anaconda Python Environment](#anaconda-python-environment)
		- [Download and install Anaconda](#download-and-install-anaconda)
		- [Checking, updating and extending Anaconda](#checking-updating-and-extending-anaconda)
	- [Resources](#resources)

## Anaconda Python Environment
Python is a general purpose high-level programming language, and I use Python to do most of my Geo Imagine. Most modern operating systems include one or more Python installations, and it can be difficult to keep track of Python installations and versions on your system. To bypass that problem, and also to get more control, as well as a complete and extendable Python installation, [Anaconda](https://anaconda.org) is the solution.

### Download and install Anaconda

Anaconda is a free platform for scientific Python. To download Anaconda, visit the [Anaconda homepage](https://www.anaconda.com), and look for the download link. In the download page, find the distribution (e.g. Windows, Linux, macOS) and the Python version (e.g. 2.X or 3.X) that you need. Since I wrote the original post (December 2017) I have switched from Python 2.7 to 3.7. Download the graphical installer, unless you feel comfortable with the command line version.

Note, the default location of the Anaconda installation for Karttur's GeoImagine Framework is directly under the <span class='file'>Applications</span> folder.

Follow the official instructions for [installing Anaconda on Mac OS X](https://docs.anaconda.com/anaconda/install/mac-os/), or the shorthand instructions below.

Open the Anaconda installation package (<span class='file'>.pkg</span>) by double clicking. If you are denied installing, you need to bypass the macOS security settings. The [previous post](http://localhost:4000/setup-ide/install-gis/#gdal) contains a more detailed manual, with figures, on how to manoeuvre to get the installation past the security system. Go via the mac main menu, click the apple (absolute top left of the computer screen), select <span class='finder'>System Preferences...</span>. In the top row of the <span class='tab'>System Preferences</span> window, click the Security & Privacy icon. In the window that opens, under the <span class='tab'>General</span> tab (usually in front by default), you should see a paragraph towards the bottom:
```
"Anaconda'version'.pkg" was blocked from opening...
```
Click the button <span class='button'>Open Anyway</span>, and try to install again. The installation should come one step further, but you still have to confirm that you want to to go ahead with the installation in a pop-up window. The installer will guide you through the installation. Remember that if you want to streamline the Anaconda installation for Karttur's GeoImagine Framework you must change the installation destination to the <span class='file'>Applications</span> folder.

When the installation is finished, it asks if you want to install PyCharm IDE. I have not tested PyCharm IDE, but it seems worthwhile. At present PyCharm is not required for Kartturs´s GeoImagine Framework.

### Checking, updating and extending Anaconda

Anaconda comes with a Navigator that assists you in keeping Anaconda up to date. The <span class='app'>Anaconda-Navigator.app</span> is found in the Anaconda main folder (that you defined during the installation). You can also check the versions of the different Python packages installed by Anaconda from the command line.

Open a <span class='app'>Terminal</span> window, and confirm that Anaconda is installed by typing at the prompt:

<span class='terminal'>$ conda -V</span>

To update your Anaconda distribution, type:

<span class='terminal'>$ conda update conda</span>

<span class='terminal'>$ conda update anaconda</span>

Anaconda comes with coherent packages, that can be used together. You can also install packages on your own, or install other versions of existing packages if needed. There are two different commands for installing packages, <span class='terminal'>conda install</span> and <span class='terminal'>pip</span>. Which to use depends on how the package you want to install is distributed. But you should have 'pip' installed. Check that pip is installed by writing at the Terminal prompt:

<span class='terminal'>$ pip -V</span>

If you installed Anaconda, you should have a pip version included in the Anaconda package. If you do not have pip installed, either user the Terminal and write:

<span class='terminal'>$ sudo easy_install pip</span>

Or go to the official [pip homepage](https://pip.pypa.io), that includes installation instruction.

The next step is either [creating a virtual Python environment](../conda-environ/) or to install and setup [<span class='app'>Eclipse</span>](../install-eclipse) IDE (Integrated Development Environment) for Python Developing (PyDev). There are many advantages with setting up PyDev using a virtual Python environment, so I recommend that you start with the post on [Conda virtual environments](../conda-environ/).

## Resources

[Anaconda](https://www.anaconda.com)

[Setup python with Anaconda](https://machinelearningmastery.com/setup-python-environment-machine-learning-deep-learning-anaconda/) by Jason Brownlee (Machine Learning Mastery).
