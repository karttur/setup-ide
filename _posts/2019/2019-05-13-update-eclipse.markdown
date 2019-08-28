---
layout: "post"
title: Update or duplicate Eclipse installation
date: "2019-05-13 18:36"
modified: 2019-05-13 18:36
categories: setup-ide
previousurl: setup-ide/python3-upgrade
nexturl: null
excerpt: "Update Eclipse IDE and set up Eclipse for Python Development (PyDev) using
  Anaconda as interpreter"
tags:
  - eclipse
  - anaconda
  - python
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

## Eclipse Installer

In this post you will first download the [Eclipse Installer](https://www.eclipse.org/downloads/packages/) and then use the installer for setting up a specific Eclipse version. If you intend to use the same Eclipse project across several machines, having the same Eclipse version makes life easier.

### Download the Eclipse installer

Go to the [Eclipse download page](https://www.eclipse.org/downloads/packages/) and Download the installer for your operating system. For Mac OS X the installer is downloaded as a disk image (<span class='file'>.dmg</span>).

<figure>
<img src="{{ site.commonurl }}/images/{{ site.data.images[page.Fig1].file }}">
<figcaption> {{ site.data.images[page.Fig1].caption }} </figcaption>
</figure>

### Open the Eclipse Installer

Double click the <span class='file'>.dmg</span> and then start the <span class='app'>Eclipse Installer</span> by double clicking the icon.

<figure>
<img src="{{ site.commonurl }}/images/{{ site.data.images[page.Fig2].file }}">
<figcaption> {{ site.data.images[page.Fig2].caption }} </figcaption>
</figure>

### Select the product and version

The <span class='window'>Product</span> opens and lets you define the Product and Product version. For setting up Karttur's GeoImagine Framework you need the product <span class='button'>Eclipse IDE for Java Developers</span> (the top of the list in the figure below).

<figure>
<img src="{{ site.commonurl }}/images/{{ site.data.images[page.Fig3].file }}">
<figcaption> {{ site.data.images[page.Fig3].caption }} </figcaption>
</figure>

A new window opens with the selected product (<span class='tab'>Eclipse IDE for Java Developers</span>). By default the Installer will suggest the latest version and a corresponding path. If that is what you want, just click the  <span class='button'>Install</span> button.

<figure>
<img src="{{ site.commonurl }}/images/{{ site.data.images[page.Fig4].file }}">
<figcaption> {{ site.data.images[page.Fig4].caption }} </figcaption>
</figure>

#### Select version

If you want another version than the suggested default, click the icon for expanding the menu (in the upper right corner) and then click the <span class='button'>Advanced Mode...</span>.

In the <span class='tab'>Product</span> window that opens you can then select which version you want to install.

<figure>
<img src="{{ site.commonurl }}/images/{{ site.data.images[page.Fig5].file }}">
<figcaption> {{ site.data.images[page.Fig5].caption }} </figcaption>
</figure>

Different versions are by default installed under different paths (usually under <span class='file'>YourUser/ProductVersion</span>). Thus you can have several products and versions of <span class='app'>Eclipse</span> installed on the same machine.

### Install PyDev

When you start your new <span class='app'>Eclipse</span> product/version for the first time you need to add the PyDev package via the Eclipse Marketplace as described in [this](../install-eclipse/) post. You might also need to update your Java installation, described in [the same](../install-eclipse/) post.
