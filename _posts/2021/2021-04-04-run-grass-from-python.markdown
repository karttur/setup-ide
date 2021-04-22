---
layout: article
title: Run GRASS from Python
previousurl: null
nexturl: null
categories: blog
excerpt: "How to start and run GRASS from within an external Python script."
tags:
  - Python 3.7
  - GRASS 7.8
  - package
  - import
  - __init__.py
image: rainfall-delta_3B43_trmm_2001-2016_mk-z-ts-model@p005
date: '2021-04-03'
modified: '2021-04-03'
comments: true
share: true
---

## Introduction

[GRASS GIS](https://grass.osgeo.org) is very versatile and powerful Geographic Information System. And it is Open Source. In this post you learn how to call GRASS functions from a python module without actually starting GRASS.

## GRASSDATA, location and mapset

One of the defining features of GRASS GIS is how spatial data is organized at three hierarchihcal levels:

- GRASSDATA,
- Location,
- MapSet

_GRASSDATA_ is the root directory under which all GRASS related spatial data is stored. A _Location_ is defined by a projection and a region, and thus each regional dataset you work with must be defined as a _Location_. As a region can be global, the GRASSDATA Location used for external calls need only define the projection; the region (map extent) will be defined with each external call. All actual data belonging to a _Location_ is related to a _MapSet_, where the default _MapSet_ is called _PERMANENT_. While _GRASSDATA_ and _Location_ must exist beforehand when calling GRASS from an external Python module, the _MapSet_ can be created and changed on the fly.

### Setting up GRASS for external calls

Before calling GRASS you must setup _GRASSDATA_ and a _Location_. To use GRASS as part of Karttur's GeoImagine Framework you must setup a _Location_ for the projection system(s) you use (e.g. modis, ease2n, ease2t etc. This section illustrates how to setup GRASS for using the EASE-grid north (ease2n) projection system.

#### Start GRASS

Start GRASS and wait for the _Welcome_ window.

<figure>
<img src="../images/ ">
<figcaption>  </figcaption>
</figure>
