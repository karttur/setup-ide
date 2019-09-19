---
layout: post
title: Install Anaconda3 and python3
modified: 2018-10-24T18:17:25.000Z
categories: blog
excerpt: "Install version 3 of Python, Anaconda and QGIS"
tags:
  - Anaconda3
  - Python3
  - QGIS3
  - mac osx
image: ols-sl-trmm-3b43v7-precip_3B43_trmm_2001-2016_A
date: '2018-10-23 19:38'
comments: true
share: true
---
<script src="../../assets/js/karttur/togglediv.js"></script>

## Introduction

Time has come for migrating to Python 3. This requires changing to Anaconda3, and if you want to also upgrade QGIS to version 3, the system Python version must also be upgraded to version 3.

## Changing mac os python version

QGIS 3 requires python 3. At time of writing QGIS 3.2 (Bonn) is the stable version and it requires Python 3.6. The QGIS package available at the [QGIS official homepage](https://www.qgis.org/en/site/) include the instructions and links for changing the system installation of Python.

As Anaconda and Eclipse are separated from the system version of Python, you do not need to change the system Python in order to change the Eclipse / Anaconda version of Python.

## Install GDAL2.2 and QGIS3

The details for installing both GDAL and QGIS are the same as in the article [Install GDAL, QGIS and GRASS](../../setup-ide/install-gis/).

If you want to have QGIS 3.2, QGIS now offers an installer that also include the instructions for upgrading the system python to 3.6 as well as the GDAL package installer for version 2.2. Just go to the [QGIS official homepage](https://www.qgis.org/en/site/) and download the package you want.

If you only want to upgrade GDAL you can instead go to the [KyngChaos page](http://www.kyngchaos.com/) maintained by William Kyngesburye. Search or browse for GDAL (towards the end of the front page). Click your way to the installer, and download and install GDAL.

## Upgrading to Anaconda3

The details for installing Anaconda3 are the same as in the article [Install Anaconda](../../setup-ide/install-anaconda/).

You can install Anaconda3 alongside Anaconda2, or remove Anaconda2 if you do not need it any longer.

To remove Anaconda2, simply delete the folder with your Anaconda2 installation, the shortcut to the Anaconda Navigator, and then remote the PATH in your system profile (.bash.profile). You should also remove the three hidden files in your home directory <span class='file'>.condarc</span>,  <span class='file'>.conda</span>  and <span class='file'>.continuum</span>. If you installed Anaconda in your home directory, here is what you need to do:

```
rm -rf ~/anaconda
rm -rf ~/.anaconda/navigator
rm -rf ~/.condarc ~/.conda ~/.continuum
pico ~/.bash_profile # delete the anaconda2 directory from your `PATH`
```

If you want to keep Anaconda2, just edit the proifle and the hidden files instead.

## Upgrading Eclipse

The details for installing and setting up Eclipse are the same as in the article [Setup Eclipse for PyDev](../../setup-ide/install-eclipse/).

You can upgrade Eclipse, or install a new version alongside your existing version.
