---
layout: article
title: Install Anaconda
modified: 2017-12-27T20:17:25.000Z
categories: setup-ide
previousurl: setup-ide/install-gis
nexturl: setup-ide/install-eclipse
excerpt: Install Anaconda Python package on macOS
tags:
  - Anaconda
  - Python
  - macOS
image: avg-trmm-3b43v7-precip_3B43_trmm_2001-2016_A
date: '2017-12-27 19:35'
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

Anaconda is a free platform for scientific Python. To download Anaconda, visit the [Anaconda homepage](https://www.anaconda.com), and look for the download link. In the download page, find the distribution (e.g. Windows, Linux, macOS) and the Python version (e.g. 2.X or 3.X) that you need. I use Python 2.7, but the day I find the strength I will change to 3.x. Download the graphical installer, unless you feel comfortable with the command line version.

Open Anaconda installation package (<span class='file'>.pkg</span>) by double clicking. If you are denied installing, you need to bypass the macOS security settings. Go via the mac main menu, click the apple (absolute top left of the computer screen), select <span class='finder'>System Preferences...</span>. In the top row of the <span class='tab'>System Preferences</span> window, click the Security & Privacy icon. In the window that opens, under the <span class='tab'>General</span> tab (usually in front by default), you should see a paragraph towards the bottom:
```
"Anaconda'version'.pkg" was blocked from opening...
```
Click the button <span class='button'>Open Anyway</span>, and try to install again. The installation should come one step further, but you still have to confirm that you want to to go ahead with the installation in a pop-up window. The <span class='file'>.pkg</span> installer will guide you through the installation. Finally you have to give your user password, and the package will install.

### Checking, updating and extending Anaconda

Anaconda comes with a Navigator that assists you in keeping Anaconda up to date. The <span class='app'>Anaconda-Navigator.app</span> is found in the Anaconda main folder (that you defined during the installation). You can also check the versions of the different Python packages installed by Anaconda from the command line.

Open a <span class='app'>Terminal</span> window, and confirm that Anaconda is installed by typing at the prompt:

<span class='terminal'>$ conda -V</span>

To update your Anaconda distribution, type:

<span class='terminal'>$ conda update conda</span>

<span class='terminal'>$ conda update anaconda</span>

Anaconda comes with coherent packages, that can be used together. You can also install packages on your own, or install other versions of existing packages if needed. There are two different commands for installing packages, 'conda install' and 'pip'. Which to use depends on how the package you want to install is distributed. But you should have 'pip' installed. Check that pip is installed by writing at the Terminal prompt:

<span class='terminal'>$ pip -V</span>

If you installed Anaconda, you should have a pip version included in the Anaconda package. If you do not have pip installed, either user the Terminal and write:

<span class='terminal'>$ sudo easy_install pip</span>

Or go to the official [pip homepage](https://pip.pypa.io), that includes installation instruction.

In the [next blog post](../install-eclipse/index.html) I will go through installing Eclipse IDE (Integrated Development Environment), and setting Eclipse up for Python Developing (PyDev) using Anaconda Python as the interpreter.

## Resources

[Anaconda](https://www.anaconda.com)

[Setup python with Anaconda](https://machinelearningmastery.com/setup-python-environment-machine-learning-deep-learning-anaconda/) by Jason Brownlee (Machine Learning Mastery).
