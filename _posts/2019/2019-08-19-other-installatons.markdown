---
layout: article
title: Other installations
categories: setup-ide
previousurl: setup-ide/conda-environ
nexturl: setup-ide/other-installations
excerpt: "Additional resources to install for the complete functions of Karttur´s GeoImagine Framework"
tags:
  - wget
  - AWSCLIV2
  - aria2
  - inkscape
image: avg-trmm-3b43v7-precip_3B43_trmm_2001-2016_A
date: '2019-08-19'
modified: '2022-01-17'
comments: true
share: true
figure1: eclipse_select_import
figure2: eclipse_import_project_from_file_system_or_archive
---
<script src="https://karttur.github.io/common/assets/js/karttur/togglediv.js"></script>

## Introduction

In addition to installing the [virtual python environment](../conda-environ), there are some additional applications required for running all the processes of Karttur's GeoImagine Framework. This post lists the additioanl resources required and explains how to install them.

## Prerequisites

The installations outlined in this post does not require any prior installations or setups, all apps listed in this post are stand-alone.

## Additional resources

The following additional resources are require for running all of the processes of Karttur's GeoImagine Framework:

- wget,
- AWSCLIV2,
- aria2 and
- inkscape

### wget

Wget is a command-line tool for retrieving files using HTTP, HTTPS, FTP and FTPS (the most widely-used Internet protocols). In Karttur´s GeoImagine Framework, wget is used for accessing online available data from e.g.[https://earthdata.nasa.gov](https://earthdata.nasa.gov), including MODIS and SMAP. To install Wget on MacOS you can use <span class='app'>Homebrew</span>.

<span class='terminal'>$ brew install wget</span>

The installation of <span class='app'>Homebrew</span> itself is covered in the blog post on [ImageMagick](https://karttur.github.io/setup-theme-blog/blog/install-imagemagick/).

### Amazon Web Services Command Line Interface 2 (AWSCLIV2)

Some daa, including Digital Elevation Models (DEMs) are available as Amazon Web Services (AWS) Open Data. To access registries on AWS Open Data you can use the free [Amazon Web Services Command Line Interface 2 (AWSCLIV2)](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-welcome.html). To install AWSCLIV2 on your machine, check out [Installing, updating, and uninstalling the AWS CLI version 2](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html). Here is a summary of the steps needed for installation on MacOS:

1. start a <span class='app'>Terminal</span> session.

2. <span class='terminal'>$ curl \"https://awscli.amazonaws.com/AWSCLIV2.pkg\" -o \"AWSCLIV2.pkg\"</span>

3. <span class='terminal'>$ sudo installer -pkg AWSCLIV2.pkg -target /</span>

Give your machine password and the AWSCLIV2.pkg package should install and report back:

```
installer: Package name is AWS Command Line Interface
installer: Installing at base path /
installer: The install was successful.
```

### aria2

[aria2](https://aria2.github.io) is a lightweight multi-protocol & multi-source command-line download utility. It supports HTTP/HTTPS, FTP, SFTP, BitTorrent and Metalink. You can use [<span class='terminalapp'>brew</span>](https://formulae.brew.sh/formula/aria2) for installation on MacOS:

<span class='terminal'>$ brew install aria2</span>

### Inkscape

[Inkscape](https://inkscape.org) is a drawing app that can also be accessed using the [command line](https://wiki.inkscape.org/wiki/Using_the_Command_Line). You can install it as an ordinary binary app, and then access the command line features. it is used for transforming between vector and raster formats, for example for legends and maps.
