---
layout: article
title: Conda virtual environments I
categories: setup-ide
previousurl: setup-ide/install-anaconda
nexturl: setup-ide/install-eclipse
excerpt: "Create Conda environment for Karttur's GeoImagaine project"
tags:
  - Conda environment
image: avg-trmm-3b43v7-precip_3B43_trmm_2001-2016_A
date: '2019-08-18'
modified: '2020-01-30'
comments: true
share: true
figure1: eclipse_select_import
figure2: eclipse_import_project_from_file_system_or_archive
---
<script src="https://karttur.github.io/common/assets/js/karttur/togglediv.js"></script>

\- [Introduction](#introduction)
\- [Prerequisites](#prerequisites)
\- [Conda virtual environments](#conda-virtual-environments)
\- [Check your conda installation](#check-your-conda-installation)
\- [.condarc](#condarc)
\- [Default packages](#default-packages)
\- [Create a new environment](#create-a-new-environment)
\- [Activate your environment](#activate-your-environment)
\- [Locate your environment](#locate-your-environment)
\- [More later](#more-later)
\- [Resources](#resources)

# Introduction

With conda, you can create, update, export and import virtual Python environments that have different versions of Python and/or packages installed in them. If you use [<span class='app'>Eclipse</span>](../install-eclipse) as your Integrated Development Environment (IDE) you can easily reset your Python source to a virtual version created in conda. You can also share an environment by exporting and then importing it.

# Prerequisites

Conda must be installed as described in [this](../install-anaconda/) post.

# Conda virtual environments

Karttur's GeoImagine Framework requires a large set of Python packages to work. You have to install these packages and then link them to the Framework Spatial Data IDE (SPIDE). Many high level packages depend on other, more basic, packages. When installing many packages there is a large risk of forcing conflicting requirements regarding the versions of shared (i.e. more basic) packages. Sequentially installing (high level) packages can easily lead to a corrupt system due to conflicting requirements regarding shared packages.

To avoid having your complete system corrupted, it is recommended that you build the Python system and packages using a "virtual" environment. In essence this means that you build a system command for creating a role is is working as a stand-alone solution not affecting the core (or 'base') system. This is easily done in conda. This tutorial will take you through the steps of creating a virtual python environment in conda. You will not assemble the complete GeoImaigine Framework Python package library in this post, but rather just set the core packages. Then you can proceed to install [<span class='app'>Eclipse</span>](../install-eclipse) and setup PyDev using the virtual environment created in this post. The complete setup of Karttur's GeoImagine Framework is covered in my [GeoImagine blog](https://karttur.github.io/geoimagine/).

## Check your conda installation

Open a <span class='app'>Terminal</span> window, and confirm that Anaconda is installed by typing at the prompt:

<span class='terminal'>$ conda -V</span>

By default, the active environment---the one you are currently using---is shown in parentheses () or brackets [] at the beginning of your command prompt. If you have not installed any virtual environments, the _default_ environment is _base_:

<span class='terminal'>(base) $</span>,

if you have defined and activated a virtual environment it will be shown instead:

<span class='terminal'>(myenv) $</span>.

If you do not see this, run:

<span class='terminal'>conda info \-\-envs</span>

To update or manage your conda installation you need to deactivate any customized environment and return to the base environment. The best way to do that is to use the _activate_ command with no environment specified:

<span class='terminal'>$ conda activate</span>

Alternatively you can _deactivate_ the present environment, but if you do that while in _base_, it might crash your conda setup. Thus I do not write out the command for that.

When in the _base_ environment the terminal prompts should look like this:

<span class='terminal'>(base) $</span>

To update your Anaconda distribution, type:

<span class='terminal'>$ conda update conda</span>

<span class='terminal'>$ conda update anaconda</span>

## .condarc

To create a virtual environment from scratch you need to have a <span class='file'>.condarc</span> configuration file in you personal folder.

The <span class='file'>.condarc</span> was not included by default when you [installed conda](../install-anaconda/). To find out if you have a <span class='file'>.condarc</span> file open a <span class='app'>terminal</span> window and type:
 <span class='terminal'>$ conda info</span>

Look for the line <span class='terminal'>user config file:</span> in the results.

If you do not have a <span class='file'>.condarc</span> file, you can create it by using a text editor (e.g. [<span class='app'>Atom</span>](https://karttur.github.io/setup-blog/2017/12/21/setup-blog-tools.html)), directly from the command line ( <span class='terminal'>~$ pico .condarc</span>) or by running the command:

<span class='terminal'>$ conda config</span>

You can set a lot of parameters and functions in <span class='file'>.condarc</span> (as described [here](https://docs.conda.io/projects/conda/en/latest/user-guide/configuration/use-condarc.html)), but for now you will only use it for defining a set of default packages that will always be included when creating a new environment.

### Default packages

The manual for setting default packages to install with every new environment is described [here](https://docs.conda.io/projects/conda/en/latest/user-guide/configuration/use-condarc.html#config-add-default-pkgs). Lists of available packages for different conda distributions are found [here](https://docs.anaconda.com/anaconda/packages/pkg-docs/).

For creating virtual conda python environments for Karttur's GeoImagine Framework, add the following lines to your <span class='file'>.condarc</span> file:

```
create_default_packages:
  - cartopy
  - fiona
  - gdal
  - geopandas
  - h5py
  - matplotlib
  - netcdf4
  - numba
  - numpy
  - pandas
  - pip
  - psycopg2
  - rasterio
  - scipy
  - statsmodels
  - xmltodict
channels:
  - defaults
```

The advantage with installing the core components in a single command is that conda will solve conflicts among dependencies. In other words, it is best to install all packages at once, so that all of the dependencies are installed at the same time. The above list will also install several other packages that are required by the Framework.

## Create a new environment

If you now create a new environment:

<span class='terminal'>$ conda create ----name geoimagineXYZ</span>,

the rather short list of default packages will create a rather long list of package to install.

<button id= "togglecondacreate" onclick="hiddencode('condacreate')">Hide/Show conda create command and response</button>

<div id="condacreate" style="display:none">

{% capture text-capture %}
{% raw %}

```
$ conda create --name geoimagine001
CCollecting package metadata (current_repodata.json): done
Solving environment: done

## Package Plan ##

  environment location: /Applications/anaconda3/envs/geoimagine_202110

  added / updated specs:
    - cartopy
    - fiona
    - gdal
    - geopandas
    - h5py
    - matplotlib
    - netcdf4
    - numba
    - numpy
    - pandas
    - pip
    - psycopg2
    - rasterio
    - scipy
    - statsmodels
    - xmltodict


The following packages will be downloaded:

    package                    |            build
    ---------------------------|-----------------
    affine-2.3.0               |     pyhd3eb1b0_0          17 KB
    aws-c-common-0.4.57        |       hb1e8313_1         140 KB
    aws-c-event-stream-0.1.6   |       h23ab428_5          21 KB
    aws-checksums-0.1.9        |       hb1e8313_0          47 KB
    aws-sdk-cpp-1.8.185        |       he271ece_0         1.6 MB
    bottleneck-1.3.2           |   py37hf1fa96c_1         107 KB
    cached-property-1.5.2      |             py_0          11 KB
    cairo-1.16.0               |       h8023c5d_1         911 KB
    cartopy-0.18.0             |   py37hf1ba7ce_1         1.7 MB
    certifi-2021.10.8          |   py37hecd8cb5_0         151 KB
    cfitsio-3.470              |       hee0f690_6         782 KB
    cftime-1.5.0               |   py37he3068b8_0         168 KB
    click-plugins-1.1.1        |     pyhd3eb1b0_0          10 KB
    cligj-0.7.2                |   py37hecd8cb5_0          13 KB
    cycler-0.10.0              |           py37_0          14 KB
    fiona-1.8.13.post1         |   py37h9c05f0f_0         639 KB
    fontconfig-2.13.1          |       ha9ee91d_0         211 KB
    freexl-1.0.6               |       h9ed2024_0          38 KB
    gdal-3.0.2                 |   py37ha6ec53f_1         1.1 MB
    geopandas-0.9.0            |             py_1          10 KB
    geopandas-base-0.9.0       |             py_1         919 KB
    geos-3.8.0                 |       hb1e8313_0         815 KB
    geotiff-1.6.0              |       h02c1893_0         120 KB
    giflib-5.2.1               |       haf1e3a3_0          70 KB
    h5py-3.2.1                 |   py37h90fc2a2_0         894 KB
    hdf4-4.2.13                |       h39711bb_2         682 KB
    hdf5-1.10.6                |       hdbbcd12_0         3.0 MB
    json-c-0.13.1              |       h3efe00b_0          59 KB
    kealib-1.4.14              |       h7bbef82_0         139 KB
    kiwisolver-1.3.1           |   py37h23ab428_0          52 KB
    libboost-1.73.0            |      hd4c2dcd_11        12.7 MB
    libdap4-3.19.1             |       h3d3e54a_0         1.1 MB
    libgdal-3.0.2              |       ha1444b2_1        10.4 MB
    libkml-1.3.0               |       h952ee91_5         425 KB
    libnetcdf-4.6.1            |       hfd9a460_4         782 KB
    libpq-12.2                 |       h1b4eb34_1         2.0 MB
    libspatialite-4.3.0a       |       h5142b36_0         2.1 MB
    llvmlite-0.36.0            |   py37he4411ff_4         296 KB
    mapclassify-2.4.3          |     pyhd3eb1b0_0          36 KB
    matplotlib-3.4.2           |   py37hecd8cb5_0          26 KB
    matplotlib-base-3.4.2      |   py37h8b3ea08_0         5.5 MB
    mkl-service-2.4.0          |   py37h9ed2024_0          44 KB
    mkl_fft-1.3.0              |   py37h4a7008c_2         159 KB
    mkl_random-1.2.2           |   py37hb2f4e1b_0         263 KB
    munch-2.5.0                |     pyhd3eb1b0_0          13 KB
    netcdf4-1.5.7              |   py37h1695cb1_0         387 KB
    numba-0.53.1               |   py37hb2f4e1b_0         3.2 MB
    numexpr-2.7.3              |   py37h5873af2_1         124 KB
    numpy-1.21.2               |   py37h4b4dc7a_0          23 KB
    numpy-base-1.21.2          |   py37he0bd621_0         4.6 MB
    olefile-0.46               |           py37_0          49 KB
    pandas-1.3.3               |   py37h5008ddb_0         8.8 MB
    patsy-0.5.2                |   py37hecd8cb5_0         268 KB
    pillow-8.3.1               |   py37ha4cf6ea_0         607 KB
    pip-21.0.1                 |   py37hecd8cb5_0         1.8 MB
    pixman-0.40.0              |       h9ed2024_1         341 KB
    poppler-0.81.0             |       h031bf0f_2         1.5 MB
    poppler-data-0.4.10        |       hecd8cb5_0         2.1 MB
    postgresql-12.2            |       h1b4eb34_1         3.3 MB
    proj-6.2.1                 |       hfd5b9e3_0         8.8 MB
    psycopg2-2.8.6             |   py37hbcfaee0_1         139 KB
    pyproj-2.6.1.post1         |   py37hdfdfadc_1         330 KB
    pyshp-2.1.3                |     pyhd3eb1b0_0          37 KB
    python-3.7.11              |       h88f2d9e_0        18.1 MB
    rasterio-1.1.0             |   py37heeaa653_0         6.5 MB
    rtree-0.9.7                |   py37hecd8cb5_1          47 KB
    scikit-learn-0.24.2        |   py37hb2f4e1b_0         4.8 MB
    scipy-1.7.1                |   py37h88652d9_2        15.3 MB
    setuptools-58.0.4          |   py37hecd8cb5_0         787 KB
    shapely-1.7.1              |   py37h9250791_0         372 KB
    snuggs-1.4.7               |     pyhd3eb1b0_0           9 KB
    statsmodels-0.12.2         |   py37h9ed2024_0         8.2 MB
    tiledb-2.2.9               |       hdc16fd3_0         1.5 MB
    tornado-6.1                |   py37h9ed2024_0         588 KB
    xerces-c-3.2.3             |       h48eee30_0         1.9 MB
    ------------------------------------------------------------
                                           Total:       144.6 MB

The following NEW packages will be INSTALLED:

  affine             pkgs/main/noarch::affine-2.3.0-pyhd3eb1b0_0
  attrs              pkgs/main/noarch::attrs-21.2.0-pyhd3eb1b0_0
  aws-c-common       pkgs/main/osx-64::aws-c-common-0.4.57-hb1e8313_1
  aws-c-event-stream pkgs/main/osx-64::aws-c-event-stream-0.1.6-h23ab428_5
  aws-checksums      pkgs/main/osx-64::aws-checksums-0.1.9-hb1e8313_0
  aws-sdk-cpp        pkgs/main/osx-64::aws-sdk-cpp-1.8.185-he271ece_0
  blas               pkgs/main/osx-64::blas-1.0-mkl
  bottleneck         pkgs/main/osx-64::bottleneck-1.3.2-py37hf1fa96c_1
  brotli             pkgs/main/osx-64::brotli-1.0.9-hb1e8313_2
  bzip2              pkgs/main/osx-64::bzip2-1.0.8-h1de35cc_0
  c-ares             pkgs/main/osx-64::c-ares-1.17.1-h9ed2024_0
  ca-certificates    pkgs/main/osx-64::ca-certificates-2021.9.30-hecd8cb5_1
  cached-property    pkgs/main/noarch::cached-property-1.5.2-py_0
  cairo              pkgs/main/osx-64::cairo-1.16.0-h8023c5d_1
  cartopy            pkgs/main/osx-64::cartopy-0.18.0-py37hf1ba7ce_1
  certifi            pkgs/main/osx-64::certifi-2021.10.8-py37hecd8cb5_0
  cfitsio            pkgs/main/osx-64::cfitsio-3.470-hee0f690_6
  cftime             pkgs/main/osx-64::cftime-1.5.0-py37he3068b8_0
  click              pkgs/main/noarch::click-7.1.2-pyhd3eb1b0_0
  click-plugins      pkgs/main/noarch::click-plugins-1.1.1-pyhd3eb1b0_0
  cligj              pkgs/main/osx-64::cligj-0.7.2-py37hecd8cb5_0
  curl               pkgs/main/osx-64::curl-7.78.0-h7bc2e8c_0
  cycler             pkgs/main/osx-64::cycler-0.10.0-py37_0
  expat              pkgs/main/osx-64::expat-2.4.1-h23ab428_2
  fiona              pkgs/main/osx-64::fiona-1.8.13.post1-py37h9c05f0f_0
  fontconfig         pkgs/main/osx-64::fontconfig-2.13.1-ha9ee91d_0
  fonttools          pkgs/main/noarch::fonttools-4.25.0-pyhd3eb1b0_0
  freetype           pkgs/main/osx-64::freetype-2.10.4-ha233b18_0
  freexl             pkgs/main/osx-64::freexl-1.0.6-h9ed2024_0
  gdal               pkgs/main/osx-64::gdal-3.0.2-py37ha6ec53f_1
  geopandas          pkgs/main/noarch::geopandas-0.9.0-py_1
  geopandas-base     pkgs/main/noarch::geopandas-base-0.9.0-py_1
  geos               pkgs/main/osx-64::geos-3.8.0-hb1e8313_0
  geotiff            pkgs/main/osx-64::geotiff-1.6.0-h02c1893_0
  gettext            pkgs/main/osx-64::gettext-0.21.0-h7535e17_0
  giflib             pkgs/main/osx-64::giflib-5.2.1-haf1e3a3_0
  glib               pkgs/main/osx-64::glib-2.69.1-hdf23fa2_0
  h5py               pkgs/main/osx-64::h5py-3.2.1-py37h90fc2a2_0
  hdf4               pkgs/main/osx-64::hdf4-4.2.13-h39711bb_2
  hdf5               pkgs/main/osx-64::hdf5-1.10.6-hdbbcd12_0
  icu                pkgs/main/osx-64::icu-58.2-h0a44026_3
  intel-openmp       pkgs/main/osx-64::intel-openmp-2021.3.0-hecd8cb5_3375
  joblib             pkgs/main/noarch::joblib-1.0.1-pyhd3eb1b0_0
  jpeg               pkgs/main/osx-64::jpeg-9d-h9ed2024_0
  json-c             pkgs/main/osx-64::json-c-0.13.1-h3efe00b_0
  kealib             pkgs/main/osx-64::kealib-1.4.14-h7bbef82_0
  kiwisolver         pkgs/main/osx-64::kiwisolver-1.3.1-py37h23ab428_0
  krb5               pkgs/main/osx-64::krb5-1.19.2-hcd88c3b_0
  lcms2              pkgs/main/osx-64::lcms2-2.12-hf1fd2bf_0
  libboost           pkgs/main/osx-64::libboost-1.73.0-hd4c2dcd_11
  libcurl            pkgs/main/osx-64::libcurl-7.78.0-hb8e4fae_0
  libcxx             pkgs/main/osx-64::libcxx-12.0.0-h2f01273_0
  libdap4            pkgs/main/osx-64::libdap4-3.19.1-h3d3e54a_0
  libedit            pkgs/main/osx-64::libedit-3.1.20210714-h9ed2024_0
  libev              pkgs/main/osx-64::libev-4.33-h9ed2024_1
  libffi             pkgs/main/osx-64::libffi-3.3-hb1e8313_2
  libgdal            pkgs/main/osx-64::libgdal-3.0.2-ha1444b2_1
  libgfortran        pkgs/main/osx-64::libgfortran-3.0.1-h93005f0_2
  libiconv           pkgs/main/osx-64::libiconv-1.16-h1de35cc_0
  libkml             pkgs/main/osx-64::libkml-1.3.0-h952ee91_5
  libllvm10          pkgs/main/osx-64::libllvm10-10.0.1-h76017ad_5
  libnetcdf          pkgs/main/osx-64::libnetcdf-4.6.1-hfd9a460_4
  libnghttp2         pkgs/main/osx-64::libnghttp2-1.41.0-h7580e61_2
  libpng             pkgs/main/osx-64::libpng-1.6.37-ha441bb4_0
  libpq              pkgs/main/osx-64::libpq-12.2-h1b4eb34_1
  libspatialindex    pkgs/main/osx-64::libspatialindex-1.9.3-h23ab428_0
  libspatialite      pkgs/main/osx-64::libspatialite-4.3.0a-h5142b36_0
  libssh2            pkgs/main/osx-64::libssh2-1.9.0-ha12b0ac_1
  libtiff            pkgs/main/osx-64::libtiff-4.2.0-h87d7836_0
  libwebp-base       pkgs/main/osx-64::libwebp-base-1.2.0-h9ed2024_0
  libxml2            pkgs/main/osx-64::libxml2-2.9.12-hcdb78fc_0
  llvm-openmp        pkgs/main/osx-64::llvm-openmp-12.0.0-h0dcd299_1
  llvmlite           pkgs/main/osx-64::llvmlite-0.36.0-py37he4411ff_4
  lz4-c              pkgs/main/osx-64::lz4-c-1.9.3-h23ab428_1
  mapclassify        pkgs/main/noarch::mapclassify-2.4.3-pyhd3eb1b0_0
  matplotlib         pkgs/main/osx-64::matplotlib-3.4.2-py37hecd8cb5_0
  matplotlib-base    pkgs/main/osx-64::matplotlib-base-3.4.2-py37h8b3ea08_0
  mkl                pkgs/main/osx-64::mkl-2021.3.0-hecd8cb5_517
  mkl-service        pkgs/main/osx-64::mkl-service-2.4.0-py37h9ed2024_0
  mkl_fft            pkgs/main/osx-64::mkl_fft-1.3.0-py37h4a7008c_2
  mkl_random         pkgs/main/osx-64::mkl_random-1.2.2-py37hb2f4e1b_0
  munch              pkgs/main/noarch::munch-2.5.0-pyhd3eb1b0_0
  munkres            pkgs/main/noarch::munkres-1.1.4-py_0
  ncurses            pkgs/main/osx-64::ncurses-6.2-h0a44026_1
  netcdf4            pkgs/main/osx-64::netcdf4-1.5.7-py37h1695cb1_0
  networkx           pkgs/main/noarch::networkx-2.6.3-pyhd3eb1b0_0
  numba              pkgs/main/osx-64::numba-0.53.1-py37hb2f4e1b_0
  numexpr            pkgs/main/osx-64::numexpr-2.7.3-py37h5873af2_1
  numpy              pkgs/main/osx-64::numpy-1.21.2-py37h4b4dc7a_0
  numpy-base         pkgs/main/osx-64::numpy-base-1.21.2-py37he0bd621_0
  olefile            pkgs/main/osx-64::olefile-0.46-py37_0
  openjpeg           pkgs/main/osx-64::openjpeg-2.4.0-h66ea3da_0
  openssl            pkgs/main/osx-64::openssl-1.1.1l-h9ed2024_0
  pandas             pkgs/main/osx-64::pandas-1.3.3-py37h5008ddb_0
  patsy              pkgs/main/osx-64::patsy-0.5.2-py37hecd8cb5_0
  pcre               pkgs/main/osx-64::pcre-8.45-h23ab428_0
  pillow             pkgs/main/osx-64::pillow-8.3.1-py37ha4cf6ea_0
  pip                pkgs/main/osx-64::pip-21.0.1-py37hecd8cb5_0
  pixman             pkgs/main/osx-64::pixman-0.40.0-h9ed2024_1
  poppler            pkgs/main/osx-64::poppler-0.81.0-h031bf0f_2
  poppler-data       pkgs/main/osx-64::poppler-data-0.4.10-hecd8cb5_0
  postgresql         pkgs/main/osx-64::postgresql-12.2-h1b4eb34_1
  proj               pkgs/main/osx-64::proj-6.2.1-hfd5b9e3_0
  psycopg2           pkgs/main/osx-64::psycopg2-2.8.6-py37hbcfaee0_1
  pyparsing          pkgs/main/noarch::pyparsing-2.4.7-pyhd3eb1b0_0
  pyproj             pkgs/main/osx-64::pyproj-2.6.1.post1-py37hdfdfadc_1
  pyshp              pkgs/main/noarch::pyshp-2.1.3-pyhd3eb1b0_0
  python             pkgs/main/osx-64::python-3.7.11-h88f2d9e_0
  python-dateutil    pkgs/main/noarch::python-dateutil-2.8.2-pyhd3eb1b0_0
  pytz               pkgs/main/noarch::pytz-2021.3-pyhd3eb1b0_0
  rasterio           pkgs/main/osx-64::rasterio-1.1.0-py37heeaa653_0
  readline           pkgs/main/osx-64::readline-8.1-h9ed2024_0
  rtree              pkgs/main/osx-64::rtree-0.9.7-py37hecd8cb5_1
  scikit-learn       pkgs/main/osx-64::scikit-learn-0.24.2-py37hb2f4e1b_0
  scipy              pkgs/main/osx-64::scipy-1.7.1-py37h88652d9_2
  setuptools         pkgs/main/osx-64::setuptools-58.0.4-py37hecd8cb5_0
  shapely            pkgs/main/osx-64::shapely-1.7.1-py37h9250791_0
  six                pkgs/main/noarch::six-1.16.0-pyhd3eb1b0_0
  snuggs             pkgs/main/noarch::snuggs-1.4.7-pyhd3eb1b0_0
  sqlite             pkgs/main/osx-64::sqlite-3.36.0-hce871da_0
  statsmodels        pkgs/main/osx-64::statsmodels-0.12.2-py37h9ed2024_0
  tbb                pkgs/main/osx-64::tbb-2020.3-h879752b_0
  threadpoolctl      pkgs/main/noarch::threadpoolctl-2.2.0-pyh0d69192_0
  tiledb             pkgs/main/osx-64::tiledb-2.2.9-hdc16fd3_0
  tk                 pkgs/main/osx-64::tk-8.6.11-h7bc2e8c_0
  tornado            pkgs/main/osx-64::tornado-6.1-py37h9ed2024_0
  wheel              pkgs/main/noarch::wheel-0.37.0-pyhd3eb1b0_1
  xerces-c           pkgs/main/osx-64::xerces-c-3.2.3-h48eee30_0
  xmltodict          pkgs/main/noarch::xmltodict-0.12.0-pyhd3eb1b0_0
  xz                 pkgs/main/osx-64::xz-5.2.5-h1de35cc_0
  zlib               pkgs/main/osx-64::zlib-1.2.11-h1de35cc_3
  zstd               pkgs/main/osx-64::zstd-1.4.9-h322a384_0

  Proceed ([y]/n)?
```
{% endraw %}
{% endcapture %}
{% include widgets/toggle-code.html  toggle-text=text-capture  %}
</div>

Just press <span class='terminal'>y</span> when conda asks <span class='terminal'>Proceed ([y]/n)?</span> and let conda setup your environment. The terminal response should then be like this:

```
Proceed ([y]/n)? y

Downloading and Extracting Packages
affine-2.3.0         | 17 KB     | ##################################################################################################################### | 100%
mkl_random-1.2.2     | 263 KB    | ##################################################################################################################### | 100%
geotiff-1.6.0        | 120 KB    | ##################################################################################################################### | 100%
aws-checksums-0.1.9  | 47 KB     | ##################################################################################################################### | 100%
rtree-0.9.7          | 47 KB     | ##################################################################################################################### | 100%
mapclassify-2.4.3    | 36 KB     | ##################################################################################################################### | 100%
numba-0.53.1         | 3.2 MB    | ##################################################################################################################### | 100%
olefile-0.46         | 49 KB     | ##################################################################################################################### | 100%
psycopg2-2.8.6       | 139 KB    | ##################################################################################################################### | 100%
fiona-1.8.13.post1   | 639 KB    | ##################################################################################################################### | 100%
libboost-1.73.0      | 12.7 MB   | ##################################################################################################################### | 100%
scikit-learn-0.24.2  | 4.8 MB    | ##################################################################################################################### | 100%
python-3.7.11        | 18.1 MB   | ##################################################################################################################### | 100%
xerces-c-3.2.3       | 1.9 MB    | ##################################################################################################################### | 100%
rasterio-1.1.0       | 6.5 MB    | ##################################################################################################################### | 100%
pixman-0.40.0        | 341 KB    | ##################################################################################################################### | 100%
aws-c-common-0.4.57  | 140 KB    | ##################################################################################################################### | 100%
munch-2.5.0          | 13 KB     | ##################################################################################################################### | 100%
libpq-12.2           | 2.0 MB    | ##################################################################################################################### | 100%
poppler-data-0.4.10  | 2.1 MB    | ##################################################################################################################### | 100%
certifi-2021.10.8    | 151 KB    | ##################################################################################################################### | 100%
fontconfig-2.13.1    | 211 KB    | ##################################################################################################################### | 100%
cycler-0.10.0        | 14 KB     | ##################################################################################################################### | 100%
geopandas-base-0.9.0 | 919 KB    | ##################################################################################################################### | 100%
h5py-3.2.1           | 894 KB    | ##################################################################################################################### | 100%
cligj-0.7.2          | 13 KB     | ##################################################################################################################### | 100%
matplotlib-3.4.2     | 26 KB     | ##################################################################################################################### | 100%
matplotlib-base-3.4. | 5.5 MB    | ##################################################################################################################### | 100%
mkl_fft-1.3.0        | 159 KB    | ##################################################################################################################### | 100%
bottleneck-1.3.2     | 107 KB    | ##################################################################################################################### | 100%
aws-c-event-stream-0 | 21 KB     | ##################################################################################################################### | 100%
proj-6.2.1           | 8.8 MB    | ##################################################################################################################### | 100%
kealib-1.4.14        | 139 KB    | ##################################################################################################################### | 100%
postgresql-12.2      | 3.3 MB    | ##################################################################################################################### | 100%
libgdal-3.0.2        | 10.4 MB   | ##################################################################################################################### | 100%
poppler-0.81.0       | 1.5 MB    | ##################################################################################################################### | 100%
tornado-6.1          | 588 KB    | ##################################################################################################################### | 100%
libkml-1.3.0         | 425 KB    | ##################################################################################################################### | 100%
libspatialite-4.3.0a | 2.1 MB    | ##################################################################################################################### | 100%
numexpr-2.7.3        | 124 KB    | ##################################################################################################################### | 100%
giflib-5.2.1         | 70 KB     | ##################################################################################################################### | 100%
pip-21.0.1           | 1.8 MB    | ##################################################################################################################### | 100%
numpy-base-1.21.2    | 4.6 MB    | ##################################################################################################################### | 100%
hdf4-4.2.13          | 682 KB    | ##################################################################################################################### | 100%
cairo-1.16.0         | 911 KB    | ##################################################################################################################### | 100%
pandas-1.3.3         | 8.8 MB    | ##################################################################################################################### | 100%
hdf5-1.10.6          | 3.0 MB    | ##################################################################################################################### | 100%
patsy-0.5.2          | 268 KB    | ##################################################################################################################### | 100%
cached-property-1.5. | 11 KB     | ##################################################################################################################### | 100%
aws-sdk-cpp-1.8.185  | 1.6 MB    | ##################################################################################################################### | 100%
pyshp-2.1.3          | 37 KB     | ##################################################################################################################### | 100%
scipy-1.7.1          | 15.3 MB   | ##################################################################################################################### | 100%
snuggs-1.4.7         | 9 KB      | ##################################################################################################################### | 100%
libdap4-3.19.1       | 1.1 MB    | ##################################################################################################################### | 100%
pyproj-2.6.1.post1   | 330 KB    | ##################################################################################################################### | 100%
numpy-1.21.2         | 23 KB     | ##################################################################################################################### | 100%
pillow-8.3.1         | 607 KB    | ##################################################################################################################### | 100%
cftime-1.5.0         | 168 KB    | ##################################################################################################################### | 100%
libnetcdf-4.6.1      | 782 KB    | ##################################################################################################################### | 100%
setuptools-58.0.4    | 787 KB    | ##################################################################################################################### | 100%
freexl-1.0.6         | 38 KB     | ##################################################################################################################### | 100%
gdal-3.0.2           | 1.1 MB    | ##################################################################################################################### | 100%
geopandas-0.9.0      | 10 KB     | ##################################################################################################################### | 100%
shapely-1.7.1        | 372 KB    | ##################################################################################################################### | 100%
llvmlite-0.36.0      | 296 KB    | ##################################################################################################################### | 100%
json-c-0.13.1        | 59 KB     | ##################################################################################################################### | 100%
statsmodels-0.12.2   | 8.2 MB    | ##################################################################################################################### | 100%
tiledb-2.2.9         | 1.5 MB    | ##################################################################################################################### | 100%
geos-3.8.0           | 815 KB    | ##################################################################################################################### | 100%
cfitsio-3.470        | 782 KB    | ##################################################################################################################### | 100%
netcdf4-1.5.7        | 387 KB    | ##################################################################################################################### | 100%
cartopy-0.18.0       | 1.7 MB    | ##################################################################################################################### | 100%
click-plugins-1.1.1  | 10 KB     | ##################################################################################################################### | 100%
mkl-service-2.4.0    | 44 KB     | ##################################################################################################################### | 100%
kiwisolver-1.3.1     | 52 KB     | ##################################################################################################################### | 100%
Preparing transaction: done
Verifying transaction: done
Executing transaction: \

    Installed package of scikit-learn can be accelerated using scikit-learn-intelex.
    More details are available here: https://intel.github.io/scikit-learn-intelex

    For example:

        $ conda install scikit-learn-intelex
        $ python -m sklearnex my_application.py



done
#
# To activate this environment, use
#
#     $ conda activate geoimagine001
#
# To deactivate an active environment, use
#
#     $ conda deactivate

```

If something goes wrong you just simply delete the virtual environment:

<span class='terminal'>$ conda remove ----name geoimagine001 --all</span>

or

<span class='terminal'>$ conda env remove ----name geoimagine001</span>

The conda base setup is not affected by either installing or deleting a virtual environment.

## Create a new environment with specified python version

If you now create a new environment with a specified python environment:

<span class='terminal'>$ conda create --n geoimagine_py37_01 python=3.7</span>

or

<span class='terminal'>$ conda create --no-default-packages --n ortho_py38 python=3.8 numpy pandas scikit-learn</span>

## Activate your environment

To list available environments type:

<span class='terminal'>$ conda info --e</span>

If you want to activate a specific environment ('other than 'base' that is now the default) type at the terminal:

<span class='terminal'>$ conda activate geoimagine001</span>

The prompt should change to say

<span class='terminal'>(geoimagine001) ... $</span>

You can now make additional installations to your environment. But for now you only have to locate your virtual environment to learn its path. You need to know that in order to link it as the Python interpreter in <span class='app'>Eclipse</span> later.

### Locate your environment

You can tell conda to put your virtual environment under any path, but by default it is put under your <span class='app'>Anaconda</span> installation at:

<span class='file'>../anacondaX/envs</span>, which in my example (for Anaconda3) then becomes
<span class='file'>../anaconda3/envs/geoimagine001</span>.

If you explore that path you can find the packages installed under
<span class='file'>../anaconda3/envs/geoimagine001/lib/python3.7/site-packages</span>. (where python3.7 is the Python version I installed, but can differ if you installed another version).

## More later

If you reach here you should have a <span class='file'>.condarc</span> file listing some default Python packages that should be included in a virtual environment ('geoimagine001') residing on your local machine. If you are going to setup the complete GeoImagine Framework you will define more packages later. But for now it is enough that you have a virtual environment that can be used for the next post: [setting up your first PyDev project using <span class='app'>Eclipse</span>](../install-eclipse).

# Resources

[Using the .condarc conda configuration file](https://docs.conda.io/projects/conda/en/latest/user-guide/configuration/use-condarc.html)

[Anaconda package lists](https://docs.anaconda.com/anaconda/packages/pkg-docs/)

[Conda - Managing environments](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html)

[Using the .condarc conda configuration file](https://docs.conda.io/projects/conda/en/latest/user-guide/configuration/use-condarc.html)
