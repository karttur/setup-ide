---
layout: article
title: Conda virtual environments I
categories: setup-ide
previousurl: setup-ide/install-anaconda
nexturl: setup-ide/install-eclipse
excerpt: "Create Conda environment for Karttur's GeoImagaine project"
tags:
  - Conda environment
  - Python
image: avg-trmm-3b43v7-precip_3B43_trmm_2001-2016_A
date: '2019-08-18'
modified: '2022-01-17'
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

To avoid having your complete system corrupted, it is recommended that you build the Python system and packages using a "virtual" environment. In essence this means that you build a system command for creating a role is is working as a stand-alone solution not affecting the core (or 'base') system. This is easily done in conda. This tutorial will take you through the steps of creating a virtual python environment in conda. Then you can proceed to install [<span class='app'>Eclipse</span>](../install-eclipse) and setup PyDev using the virtual environment created in this post.

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

The manual for setting default packages to install with every new environment is described [here](https://docs.conda.io/projects/conda/en/latest/user-guide/configuration/use-condarc.html#config-add-default-pkgs). Lists of available default packages for different conda distributions are found [here](https://docs.anaconda.com/anaconda/packages/pkg-docs/). The advantage with installing the core components in a single command is that conda will solve conflicts among dependencies. In other words, it is best to install all packages at once, so that all of the dependencies are installed at the same time. The above list will also install several other packages that are required by the Framework.

#### Default packages only

For creating virtual conda python environments only using [conda default packages](https://docs.anaconda.com/anaconda/packages/pkg-docs/) for Karttur's GeoImagine Framework, add the following lines to your <span class='file'>.condarc</span> file:

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

#### Default and conda-forge packages

[conda-forge](https://conda-forge.org) is a community-led collection of recipes, build infrastructure and distributions for the conda package manager. Some of the conda packages required to run the complete Framework are not available as default packages, but with conda-forge. You can extent the <span class='file'>.condarc</span> configuration to also include packages available at [conda-forge](https://conda-forge.org). You can also install the [conda-forge](https://conda-forge.org) packages after you have set up the default packages - the commands for the latter are given further down in this post. If you want to ty out using extending the configuration, update your <span class='file'>.condarc</span> file:

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
  - plotnine
  - pypng
  - sentinelsat
  - svgwrite
  - tqdm
channels:
  - conda-forge
  - defaults
```

### Default packages only

The example below is the result from creating a new environment only using conda default packages.

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

### Default and conda-forge packages

<button id= "togglecondaforgecreate" onclick="hiddencode('condaforgecreate')">Hide/Show conda-forge create command and response</button>

<div id="condaforgecreate" style="display:none">

{% capture text-capture %}
{% raw %}

```
$ conda create --name geoimagine_202109
Collecting package metadata (current_repodata.json): done
Solving environment: done

## Package Plan ##

  environment location: /Applications/anaconda3/envs/geoimagine_202109

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
    - plotnine
    - psycopg2
    - rasterio
    - scipy
    - sentinelsat
    - statsmodels
    - svgwrite
    - tqdm
    - xmltodict


The following packages will be downloaded:

    package                    |            build
    ---------------------------|-----------------
    affine-2.3.0               |             py_0          16 KB  conda-forge
    attrs-21.4.0               |     pyhd8ed1ab_0          49 KB  conda-forge
    blosc-1.21.0               |       he49afe7_0         920 KB  conda-forge
    boost-cpp-1.74.0           |       hf3dc895_5        15.6 MB  conda-forge
    branca-0.4.2               |     pyhd8ed1ab_0          26 KB  conda-forge
    brotli-1.0.9               |       h0d85af4_6          18 KB  conda-forge
    brotli-bin-1.0.9           |       h0d85af4_6          17 KB  conda-forge
    brotlipy-0.7.0             |py310he24745e_1003         363 KB  conda-forge
    bzip2-1.0.8                |       h0d85af4_4         155 KB  conda-forge
    c-ares-1.18.1              |       h0d85af4_0         100 KB  conda-forge
    ca-certificates-2021.10.8  |       h033912b_0         139 KB  conda-forge
    cached-property-1.5.2      |       hd8ed1ab_1           4 KB  conda-forge
    cached_property-1.5.2      |     pyha770c72_1          11 KB  conda-forge
    cairo-1.16.0               |    he01c77b_1009         1.3 MB  conda-forge
    cartopy-0.20.2             |  py310hebf2dc4_0         1.7 MB  conda-forge
    certifi-2021.10.8          |  py310h2ec42d9_1         145 KB  conda-forge
    cffi-1.15.0                |  py310hcc37b68_0         226 KB  conda-forge
    cfitsio-4.0.0              |       hb20e66c_0         1.3 MB  conda-forge
    cftime-1.5.1.1             |  py310h81f86ea_1         206 KB  conda-forge
    charset-normalizer-2.0.10  |     pyhd8ed1ab_0          34 KB  conda-forge
    click-8.0.3                |  py310h2ec42d9_1         149 KB  conda-forge
    click-plugins-1.1.1        |             py_0           9 KB  conda-forge
    cligj-0.7.2                |     pyhd8ed1ab_1          10 KB  conda-forge
    colorama-0.4.4             |     pyh9f0ad1d_0          18 KB  conda-forge
    cryptography-36.0.1        |  py310ha82f1d4_0         1.4 MB  conda-forge
    curl-7.81.0                |       hf45b732_0         145 KB  conda-forge
    cycler-0.11.0              |     pyhd8ed1ab_0          10 KB  conda-forge
    descartes-1.1.0            |             py_4           7 KB  conda-forge
    expat-2.4.2                |       he49afe7_0         143 KB  conda-forge
    fiona-1.8.20               |  py310h56b70e1_4         966 KB  conda-forge
    folium-0.12.1.post1        |     pyhd8ed1ab_1          64 KB  conda-forge
    font-ttf-dejavu-sans-mono-2.37|       hab24e00_0         388 KB  conda-forge
    font-ttf-inconsolata-3.000 |       h77eed37_0          94 KB  conda-forge
    font-ttf-source-code-pro-2.038|       h77eed37_0         684 KB  conda-forge
    font-ttf-ubuntu-0.83       |       hab24e00_0         1.9 MB  conda-forge
    fontconfig-2.13.1          |    h10f422b_1005         271 KB  conda-forge
    fonts-conda-ecosystem-1    |                0           4 KB  conda-forge
    fonts-conda-forge-1        |                0           4 KB  conda-forge
    fonttools-4.28.5           |  py310he24745e_0         1.6 MB  conda-forge
    freetype-2.10.4            |       h4cff582_1         890 KB  conda-forge
    freexl-1.0.6               |       h0d85af4_0          42 KB  conda-forge
    gdal-3.4.0                 | py310h17500b9_12         1.9 MB  conda-forge
    geojson-2.5.0              |             py_0          15 KB  conda-forge
    geomet-0.3.0               |     pyhd3deb0d_0          26 KB  conda-forge
    geopandas-0.10.2           |     pyhd8ed1ab_1           5 KB  conda-forge
    geopandas-base-0.10.2      |     pyha770c72_1         982 KB  conda-forge
    geos-3.10.1                |       he49afe7_1         1.2 MB  conda-forge
    geotiff-1.7.0              |       h9e3163f_5         127 KB  conda-forge
    gettext-0.19.8.1           |    hd1a6beb_1008         3.3 MB  conda-forge
    giflib-5.2.1               |       hbcb3906_2          71 KB  conda-forge
    h5py-3.6.0                 |nompi_py310h47026f8_100         1.2 MB  conda-forge
    hdf4-4.2.15                |       hefd3b78_3         885 KB  conda-forge
    hdf5-1.12.1                |nompi_h2f0ef1a_103         3.7 MB  conda-forge
    html2text-2020.1.16        |             py_0          33 KB  conda-forge
    icu-69.1                   |       he49afe7_0        12.9 MB  conda-forge
    idna-3.1                   |     pyhd3deb0d_0          52 KB  conda-forge
    jbig-2.1                   |    h0d85af4_2003          42 KB  conda-forge
    jinja2-3.0.3               |     pyhd8ed1ab_0          99 KB  conda-forge
    joblib-1.1.0               |     pyhd8ed1ab_0         210 KB  conda-forge
    jpeg-9d                    |       hbcb3906_0         250 KB  conda-forge
    json-c-0.15                |       hcb556a6_0          70 KB  conda-forge
    kealib-1.4.14              |       ha22a8b1_3         156 KB  conda-forge
    kiwisolver-1.3.2           |  py310h2fea185_1          64 KB  conda-forge
    krb5-1.19.2                |       hcfbf3a7_3         1.2 MB  conda-forge
    lcms2-2.12                 |       h577c468_0         404 KB  conda-forge
    lerc-3.0                   |       he49afe7_0         170 KB  conda-forge
    libblas-3.9.0              |12_osx64_openblas          12 KB  conda-forge
    libbrotlicommon-1.0.9      |       h0d85af4_6          63 KB  conda-forge
    libbrotlidec-1.0.9         |       h0d85af4_6          33 KB  conda-forge
    libbrotlienc-1.0.9         |       h0d85af4_6         324 KB  conda-forge
    libcblas-3.9.0             |12_osx64_openblas          12 KB  conda-forge
    libcurl-7.81.0             |       hf45b732_0         319 KB  conda-forge
    libcxx-12.0.1              |       habf9029_1         1.1 MB  conda-forge
    libdap4-3.20.6             |       h3e144a0_2         2.2 MB  conda-forge
    libdeflate-1.8             |       h0d85af4_0          67 KB  conda-forge
    libedit-3.1.20191231       |       h0678c8f_2         103 KB  conda-forge
    libev-4.33                 |       haf1e3a3_1          99 KB  conda-forge
    libffi-3.4.2               |       h0d85af4_5          50 KB  conda-forge
    libgdal-3.4.0              |      h3ab75b0_12         9.4 MB  conda-forge
    libgfortran-5.0.0          |9_3_0_h6c81a4c_23          19 KB  conda-forge
    libgfortran5-9.3.0         |      h6c81a4c_23         1.7 MB  conda-forge
    libglib-2.70.2             |       hf1fb8c0_1         2.8 MB  conda-forge
    libiconv-1.16              |       haf1e3a3_0         1.3 MB  conda-forge
    libkml-1.3.0               |    h8fd9edb_1014         483 KB  conda-forge
    liblapack-3.9.0            |12_osx64_openblas          12 KB  conda-forge
    libllvm11-11.1.0           |       hd011deb_2        22.5 MB  conda-forge
    libnetcdf-4.8.1            |nompi_h6609ca0_101         1.3 MB  conda-forge
    libnghttp2-1.43.0          |       h6f36284_1         867 KB  conda-forge
    libopenblas-0.3.18         |openmp_h3351f45_0         9.0 MB  conda-forge
    libpng-1.6.37              |       h7cec526_2         313 KB  conda-forge
    libpq-14.1                 |       hea3049e_1         2.9 MB  conda-forge
    librttopo-1.1.0            |       h0d74fd6_8         219 KB  conda-forge
    libspatialindex-1.9.3      |       he49afe7_4         400 KB  conda-forge
    libspatialite-5.0.1        |      h24415ee_12         4.2 MB  conda-forge
    libssh2-1.10.0             |       h52ee1ee_2         221 KB  conda-forge
    libtiff-4.3.0              |       hd146c10_2         622 KB  conda-forge
    libwebp-base-1.2.1         |       h0d85af4_0         692 KB  conda-forge
    libxml2-2.9.12             |       h7e28ab6_1         684 KB  conda-forge
    libzip-1.8.0               |       h8b0c345_1         117 KB  conda-forge
    libzlib-1.2.11             |    h9173be1_1013          58 KB  conda-forge
    llvm-openmp-12.0.1         |       hda6cdc1_1         287 KB  conda-forge
    llvmlite-0.38.0            |  py310h003a20c_0         240 KB  conda-forge
    lz4-c-1.9.3                |       he49afe7_1         155 KB  conda-forge
    mapclassify-2.4.3          |     pyhd8ed1ab_0          36 KB  conda-forge
    markupsafe-2.0.1           |  py310he24745e_1          21 KB  conda-forge
    matplotlib-3.5.1           |  py310h2ec42d9_0           6 KB  conda-forge
    matplotlib-base-3.5.1      |  py310hfc59cd6_0         7.5 MB  conda-forge
    mizani-0.7.3               |     pyhd8ed1ab_0          53 KB  conda-forge
    munch-2.5.0                |             py_0          12 KB  conda-forge
    munkres-1.1.4              |     pyh9f0ad1d_0          12 KB  conda-forge
    ncurses-6.2                |       h2e338ed_4         881 KB  conda-forge
    netcdf4-1.5.8              |nompi_py310h1579a17_101         447 KB  conda-forge
    networkx-2.6.3             |     pyhd8ed1ab_1         1.5 MB  conda-forge
    nspr-4.32                  |       hcd9eead_1         247 KB  conda-forge
    nss-3.74                   |       h31e2bf1_0         2.0 MB  conda-forge
    numba-0.55.0               |  py310h3ca88a5_0         3.8 MB  conda-forge
    numpy-1.21.5               |  py310ha69e199_0         6.1 MB  conda-forge
    olefile-0.46               |     pyh9f0ad1d_1          32 KB  conda-forge
    openjpeg-2.4.0             |       h6e7aa92_1         374 KB  conda-forge
    openssl-1.1.1l             |       h0d85af4_0         1.9 MB  conda-forge
    packaging-21.3             |     pyhd8ed1ab_0          36 KB  conda-forge
    pandas-1.3.5               |  py310hdd25497_0        12.1 MB  conda-forge
    patsy-0.5.2                |     pyhd8ed1ab_0         188 KB  conda-forge
    pcre-8.45                  |       he49afe7_0         220 KB  conda-forge
    pillow-8.4.0               |  py310h7a4d1b7_0         688 KB  conda-forge
    pip-21.3.1                 |     pyhd8ed1ab_0         1.2 MB  conda-forge
    pixman-0.40.0              |       hbcb3906_0         615 KB  conda-forge
    plotnine-0.8.0             |     pyhd8ed1ab_0         3.8 MB  conda-forge
    poppler-21.11.0            |       h9573804_0         1.7 MB  conda-forge
    poppler-data-0.4.11        |       hd8ed1ab_0         3.6 MB  conda-forge
    postgresql-14.1            |       he8fe76e_1         5.0 MB  conda-forge
    proj-8.2.0                 |       h1512c50_0         2.7 MB  conda-forge
    psycopg2-2.9.3             |  py310hff56b6c_0         164 KB  conda-forge
    pycparser-2.21             |     pyhd8ed1ab_0         100 KB  conda-forge
    pyopenssl-21.0.0           |     pyhd8ed1ab_0          48 KB  conda-forge
    pyparsing-3.0.6            |     pyhd8ed1ab_0          79 KB  conda-forge
    pyproj-3.3.0               |  py310hc1e3477_0         469 KB  conda-forge
    pyshp-2.1.3                |     pyh44b312d_0          36 KB  conda-forge
    pysocks-1.7.1              |  py310h2ec42d9_4          28 KB  conda-forge
    python-3.10.2              |h1248fe1_0_cpython        12.9 MB  conda-forge
    python-dateutil-2.8.2      |     pyhd8ed1ab_0         240 KB  conda-forge
    python_abi-3.10            |          2_cp310           4 KB  conda-forge
    pytz-2021.3                |     pyhd8ed1ab_0         242 KB  conda-forge
    rasterio-1.2.10            |  py310h18fb0d0_3         8.2 MB  conda-forge
    readline-8.1               |       h05e3726_0         266 KB  conda-forge
    requests-2.27.1            |     pyhd8ed1ab_0          53 KB  conda-forge
    rtree-0.9.7                |  py310had9ce37_3          46 KB  conda-forge
    scikit-learn-1.0.2         |  py310h598de7d_0         7.3 MB  conda-forge
    scipy-1.7.3                |  py310h47774c9_0        20.6 MB  conda-forge
    sentinelsat-1.1.1          |     pyhd8ed1ab_0          47 KB  conda-forge
    setuptools-59.8.0          |  py310h2ec42d9_0         1.0 MB  conda-forge
    shapely-1.8.0              |  py310h8c76d38_4         352 KB  conda-forge
    six-1.16.0                 |     pyh6c4a22f_0          14 KB  conda-forge
    snuggs-1.4.7               |             py_0           8 KB  conda-forge
    sqlite-3.37.0              |       h23a322b_0         1.8 MB  conda-forge
    statsmodels-0.13.1         |  py310h81f86ea_0        10.9 MB  conda-forge
    svgwrite-1.4.1             |     pyhd8ed1ab_0          51 KB  conda-forge
    threadpoolctl-3.0.0        |     pyh8a188c0_0          17 KB  conda-forge
    tiledb-2.5.3               |       hca4410a_0         3.1 MB  conda-forge
    tk-8.6.11                  |       h5dbffcc_1         3.3 MB  conda-forge
    tornado-6.1                |  py310he24745e_2         657 KB  conda-forge
    tqdm-4.62.3                |     pyhd8ed1ab_0          80 KB  conda-forge
    tzcode-2021e               |       h0d85af4_0          64 KB  conda-forge
    tzdata-2021e               |       he74cb21_0         121 KB  conda-forge
    urllib3-1.26.8             |     pyhd8ed1ab_1         100 KB  conda-forge
    wheel-0.37.1               |     pyhd8ed1ab_0          31 KB  conda-forge
    xerces-c-3.2.3             |       h6564042_4         1.5 MB  conda-forge
    xmltodict-0.12.0           |             py_0          11 KB  conda-forge
    xyzservices-2021.11.0      |     pyhd8ed1ab_1          26 KB  conda-forge
    xz-5.2.5                   |       haf1e3a3_1         228 KB  conda-forge
    zlib-1.2.11                |    h9173be1_1013          87 KB  conda-forge
    zstd-1.5.1                 |       h582d3a0_0         454 KB  conda-forge
    ------------------------------------------------------------
                                           Total:       255.8 MB

The following NEW packages will be INSTALLED:

  affine             conda-forge/noarch::affine-2.3.0-py_0
  attrs              conda-forge/noarch::attrs-21.4.0-pyhd8ed1ab_0
  blosc              conda-forge/osx-64::blosc-1.21.0-he49afe7_0
  boost-cpp          conda-forge/osx-64::boost-cpp-1.74.0-hf3dc895_5
  branca             conda-forge/noarch::branca-0.4.2-pyhd8ed1ab_0
  brotli             conda-forge/osx-64::brotli-1.0.9-h0d85af4_6
  brotli-bin         conda-forge/osx-64::brotli-bin-1.0.9-h0d85af4_6
  brotlipy           conda-forge/osx-64::brotlipy-0.7.0-py310he24745e_1003
  bzip2              conda-forge/osx-64::bzip2-1.0.8-h0d85af4_4
  c-ares             conda-forge/osx-64::c-ares-1.18.1-h0d85af4_0
  ca-certificates    conda-forge/osx-64::ca-certificates-2021.10.8-h033912b_0
  cached-property    conda-forge/noarch::cached-property-1.5.2-hd8ed1ab_1
  cached_property    conda-forge/noarch::cached_property-1.5.2-pyha770c72_1
  cairo              conda-forge/osx-64::cairo-1.16.0-he01c77b_1009
  cartopy            conda-forge/osx-64::cartopy-0.20.2-py310hebf2dc4_0
  certifi            conda-forge/osx-64::certifi-2021.10.8-py310h2ec42d9_1
  cffi               conda-forge/osx-64::cffi-1.15.0-py310hcc37b68_0
  cfitsio            conda-forge/osx-64::cfitsio-4.0.0-hb20e66c_0
  cftime             conda-forge/osx-64::cftime-1.5.1.1-py310h81f86ea_1
  charset-normalizer conda-forge/noarch::charset-normalizer-2.0.10-pyhd8ed1ab_0
  click              conda-forge/osx-64::click-8.0.3-py310h2ec42d9_1
  click-plugins      conda-forge/noarch::click-plugins-1.1.1-py_0
  cligj              conda-forge/noarch::cligj-0.7.2-pyhd8ed1ab_1
  colorama           conda-forge/noarch::colorama-0.4.4-pyh9f0ad1d_0
  cryptography       conda-forge/osx-64::cryptography-36.0.1-py310ha82f1d4_0
  curl               conda-forge/osx-64::curl-7.81.0-hf45b732_0
  cycler             conda-forge/noarch::cycler-0.11.0-pyhd8ed1ab_0
  descartes          conda-forge/noarch::descartes-1.1.0-py_4
  expat              conda-forge/osx-64::expat-2.4.2-he49afe7_0
  fiona              conda-forge/osx-64::fiona-1.8.20-py310h56b70e1_4
  folium             conda-forge/noarch::folium-0.12.1.post1-pyhd8ed1ab_1
  font-ttf-dejavu-s~ conda-forge/noarch::font-ttf-dejavu-sans-mono-2.37-hab24e00_0
  font-ttf-inconsol~ conda-forge/noarch::font-ttf-inconsolata-3.000-h77eed37_0
  font-ttf-source-c~ conda-forge/noarch::font-ttf-source-code-pro-2.038-h77eed37_0
  font-ttf-ubuntu    conda-forge/noarch::font-ttf-ubuntu-0.83-hab24e00_0
  fontconfig         conda-forge/osx-64::fontconfig-2.13.1-h10f422b_1005
  fonts-conda-ecosy~ conda-forge/noarch::fonts-conda-ecosystem-1-0
  fonts-conda-forge  conda-forge/noarch::fonts-conda-forge-1-0
  fonttools          conda-forge/osx-64::fonttools-4.28.5-py310he24745e_0
  freetype           conda-forge/osx-64::freetype-2.10.4-h4cff582_1
  freexl             conda-forge/osx-64::freexl-1.0.6-h0d85af4_0
  gdal               conda-forge/osx-64::gdal-3.4.0-py310h17500b9_12
  geojson            conda-forge/noarch::geojson-2.5.0-py_0
  geomet             conda-forge/noarch::geomet-0.3.0-pyhd3deb0d_0
  geopandas          conda-forge/noarch::geopandas-0.10.2-pyhd8ed1ab_1
  geopandas-base     conda-forge/noarch::geopandas-base-0.10.2-pyha770c72_1
  geos               conda-forge/osx-64::geos-3.10.1-he49afe7_1
  geotiff            conda-forge/osx-64::geotiff-1.7.0-h9e3163f_5
  gettext            conda-forge/osx-64::gettext-0.19.8.1-hd1a6beb_1008
  giflib             conda-forge/osx-64::giflib-5.2.1-hbcb3906_2
  h5py               conda-forge/osx-64::h5py-3.6.0-nompi_py310h47026f8_100
  hdf4               conda-forge/osx-64::hdf4-4.2.15-hefd3b78_3
  hdf5               conda-forge/osx-64::hdf5-1.12.1-nompi_h2f0ef1a_103
  html2text          conda-forge/noarch::html2text-2020.1.16-py_0
  icu                conda-forge/osx-64::icu-69.1-he49afe7_0
  idna               conda-forge/noarch::idna-3.1-pyhd3deb0d_0
  jbig               conda-forge/osx-64::jbig-2.1-h0d85af4_2003
  jinja2             conda-forge/noarch::jinja2-3.0.3-pyhd8ed1ab_0
  joblib             conda-forge/noarch::joblib-1.1.0-pyhd8ed1ab_0
  jpeg               conda-forge/osx-64::jpeg-9d-hbcb3906_0
  json-c             conda-forge/osx-64::json-c-0.15-hcb556a6_0
  kealib             conda-forge/osx-64::kealib-1.4.14-ha22a8b1_3
  kiwisolver         conda-forge/osx-64::kiwisolver-1.3.2-py310h2fea185_1
  krb5               conda-forge/osx-64::krb5-1.19.2-hcfbf3a7_3
  lcms2              conda-forge/osx-64::lcms2-2.12-h577c468_0
  lerc               conda-forge/osx-64::lerc-3.0-he49afe7_0
  libblas            conda-forge/osx-64::libblas-3.9.0-12_osx64_openblas
  libbrotlicommon    conda-forge/osx-64::libbrotlicommon-1.0.9-h0d85af4_6
  libbrotlidec       conda-forge/osx-64::libbrotlidec-1.0.9-h0d85af4_6
  libbrotlienc       conda-forge/osx-64::libbrotlienc-1.0.9-h0d85af4_6
  libcblas           conda-forge/osx-64::libcblas-3.9.0-12_osx64_openblas
  libcurl            conda-forge/osx-64::libcurl-7.81.0-hf45b732_0
  libcxx             conda-forge/osx-64::libcxx-12.0.1-habf9029_1
  libdap4            conda-forge/osx-64::libdap4-3.20.6-h3e144a0_2
  libdeflate         conda-forge/osx-64::libdeflate-1.8-h0d85af4_0
  libedit            conda-forge/osx-64::libedit-3.1.20191231-h0678c8f_2
  libev              conda-forge/osx-64::libev-4.33-haf1e3a3_1
  libffi             conda-forge/osx-64::libffi-3.4.2-h0d85af4_5
  libgdal            conda-forge/osx-64::libgdal-3.4.0-h3ab75b0_12
  libgfortran        conda-forge/osx-64::libgfortran-5.0.0-9_3_0_h6c81a4c_23
  libgfortran5       conda-forge/osx-64::libgfortran5-9.3.0-h6c81a4c_23
  libglib            conda-forge/osx-64::libglib-2.70.2-hf1fb8c0_1
  libiconv           conda-forge/osx-64::libiconv-1.16-haf1e3a3_0
  libkml             conda-forge/osx-64::libkml-1.3.0-h8fd9edb_1014
  liblapack          conda-forge/osx-64::liblapack-3.9.0-12_osx64_openblas
  libllvm11          conda-forge/osx-64::libllvm11-11.1.0-hd011deb_2
  libnetcdf          conda-forge/osx-64::libnetcdf-4.8.1-nompi_h6609ca0_101
  libnghttp2         conda-forge/osx-64::libnghttp2-1.43.0-h6f36284_1
  libopenblas        conda-forge/osx-64::libopenblas-0.3.18-openmp_h3351f45_0
  libpng             conda-forge/osx-64::libpng-1.6.37-h7cec526_2
  libpq              conda-forge/osx-64::libpq-14.1-hea3049e_1
  librttopo          conda-forge/osx-64::librttopo-1.1.0-h0d74fd6_8
  libspatialindex    conda-forge/osx-64::libspatialindex-1.9.3-he49afe7_4
  libspatialite      conda-forge/osx-64::libspatialite-5.0.1-h24415ee_12
  libssh2            conda-forge/osx-64::libssh2-1.10.0-h52ee1ee_2
  libtiff            conda-forge/osx-64::libtiff-4.3.0-hd146c10_2
  libwebp-base       conda-forge/osx-64::libwebp-base-1.2.1-h0d85af4_0
  libxml2            conda-forge/osx-64::libxml2-2.9.12-h7e28ab6_1
  libzip             conda-forge/osx-64::libzip-1.8.0-h8b0c345_1
  libzlib            conda-forge/osx-64::libzlib-1.2.11-h9173be1_1013
  llvm-openmp        conda-forge/osx-64::llvm-openmp-12.0.1-hda6cdc1_1
  llvmlite           conda-forge/osx-64::llvmlite-0.38.0-py310h003a20c_0
  lz4-c              conda-forge/osx-64::lz4-c-1.9.3-he49afe7_1
  mapclassify        conda-forge/noarch::mapclassify-2.4.3-pyhd8ed1ab_0
  markupsafe         conda-forge/osx-64::markupsafe-2.0.1-py310he24745e_1
  matplotlib         conda-forge/osx-64::matplotlib-3.5.1-py310h2ec42d9_0
  matplotlib-base    conda-forge/osx-64::matplotlib-base-3.5.1-py310hfc59cd6_0
  mizani             conda-forge/noarch::mizani-0.7.3-pyhd8ed1ab_0
  munch              conda-forge/noarch::munch-2.5.0-py_0
  munkres            conda-forge/noarch::munkres-1.1.4-pyh9f0ad1d_0
  ncurses            conda-forge/osx-64::ncurses-6.2-h2e338ed_4
  netcdf4            conda-forge/osx-64::netcdf4-1.5.8-nompi_py310h1579a17_101
  networkx           conda-forge/noarch::networkx-2.6.3-pyhd8ed1ab_1
  nspr               conda-forge/osx-64::nspr-4.32-hcd9eead_1
  nss                conda-forge/osx-64::nss-3.74-h31e2bf1_0
  numba              conda-forge/osx-64::numba-0.55.0-py310h3ca88a5_0
  numpy              conda-forge/osx-64::numpy-1.21.5-py310ha69e199_0
  olefile            conda-forge/noarch::olefile-0.46-pyh9f0ad1d_1
  openjpeg           conda-forge/osx-64::openjpeg-2.4.0-h6e7aa92_1
  openssl            conda-forge/osx-64::openssl-1.1.1l-h0d85af4_0
  packaging          conda-forge/noarch::packaging-21.3-pyhd8ed1ab_0
  palettable         conda-forge/noarch::palettable-3.3.0-py_0
  pandas             conda-forge/osx-64::pandas-1.3.5-py310hdd25497_0
  patsy              conda-forge/noarch::patsy-0.5.2-pyhd8ed1ab_0
  pcre               conda-forge/osx-64::pcre-8.45-he49afe7_0
  pillow             conda-forge/osx-64::pillow-8.4.0-py310h7a4d1b7_0
  pip                conda-forge/noarch::pip-21.3.1-pyhd8ed1ab_0
  pixman             conda-forge/osx-64::pixman-0.40.0-hbcb3906_0
  plotnine           conda-forge/noarch::plotnine-0.8.0-pyhd8ed1ab_0
  poppler            conda-forge/osx-64::poppler-21.11.0-h9573804_0
  poppler-data       conda-forge/noarch::poppler-data-0.4.11-hd8ed1ab_0
  postgresql         conda-forge/osx-64::postgresql-14.1-he8fe76e_1
  proj               conda-forge/osx-64::proj-8.2.0-h1512c50_0
  psycopg2           conda-forge/osx-64::psycopg2-2.9.3-py310hff56b6c_0
  pycparser          conda-forge/noarch::pycparser-2.21-pyhd8ed1ab_0
  pyopenssl          conda-forge/noarch::pyopenssl-21.0.0-pyhd8ed1ab_0
  pyparsing          conda-forge/noarch::pyparsing-3.0.6-pyhd8ed1ab_0
  pyproj             conda-forge/osx-64::pyproj-3.3.0-py310hc1e3477_0
  pyshp              conda-forge/noarch::pyshp-2.1.3-pyh44b312d_0
  pysocks            conda-forge/osx-64::pysocks-1.7.1-py310h2ec42d9_4
  python             conda-forge/osx-64::python-3.10.2-h1248fe1_0_cpython
  python-dateutil    conda-forge/noarch::python-dateutil-2.8.2-pyhd8ed1ab_0
  python_abi         conda-forge/osx-64::python_abi-3.10-2_cp310
  pytz               conda-forge/noarch::pytz-2021.3-pyhd8ed1ab_0
  rasterio           conda-forge/osx-64::rasterio-1.2.10-py310h18fb0d0_3
  readline           conda-forge/osx-64::readline-8.1-h05e3726_0
  requests           conda-forge/noarch::requests-2.27.1-pyhd8ed1ab_0
  rtree              conda-forge/osx-64::rtree-0.9.7-py310had9ce37_3
  scikit-learn       conda-forge/osx-64::scikit-learn-1.0.2-py310h598de7d_0
  scipy              conda-forge/osx-64::scipy-1.7.3-py310h47774c9_0
  sentinelsat        conda-forge/noarch::sentinelsat-1.1.1-pyhd8ed1ab_0
  setuptools         conda-forge/osx-64::setuptools-59.8.0-py310h2ec42d9_0
  shapely            conda-forge/osx-64::shapely-1.8.0-py310h8c76d38_4
  six                conda-forge/noarch::six-1.16.0-pyh6c4a22f_0
  snuggs             conda-forge/noarch::snuggs-1.4.7-py_0
  sqlite             conda-forge/osx-64::sqlite-3.37.0-h23a322b_0
  statsmodels        conda-forge/osx-64::statsmodels-0.13.1-py310h81f86ea_0
  svgwrite           conda-forge/noarch::svgwrite-1.4.1-pyhd8ed1ab_0
  threadpoolctl      conda-forge/noarch::threadpoolctl-3.0.0-pyh8a188c0_0
  tiledb             conda-forge/osx-64::tiledb-2.5.3-hca4410a_0
  tk                 conda-forge/osx-64::tk-8.6.11-h5dbffcc_1
  tornado            conda-forge/osx-64::tornado-6.1-py310he24745e_2
  tqdm               conda-forge/noarch::tqdm-4.62.3-pyhd8ed1ab_0
  tzcode             conda-forge/osx-64::tzcode-2021e-h0d85af4_0
  tzdata             conda-forge/noarch::tzdata-2021e-he74cb21_0
  urllib3            conda-forge/noarch::urllib3-1.26.8-pyhd8ed1ab_1
  wheel              conda-forge/noarch::wheel-0.37.1-pyhd8ed1ab_0
  xerces-c           conda-forge/osx-64::xerces-c-3.2.3-h6564042_4
  xmltodict          conda-forge/noarch::xmltodict-0.12.0-py_0
  xyzservices        conda-forge/noarch::xyzservices-2021.11.0-pyhd8ed1ab_1
  xz                 conda-forge/osx-64::xz-5.2.5-haf1e3a3_1
  zlib               conda-forge/osx-64::zlib-1.2.11-h9173be1_1013
  zstd               conda-forge/osx-64::zstd-1.5.1-h582d3a0_0


Proceed ([y]/n)? y


Downloading and Extracting Packages
libspatialite-5.0.1  | 4.2 MB    | #################################################################################################################################### | 100%
hdf4-4.2.15          | 885 KB    | #################################################################################################################################### | 100%
libgfortran-5.0.0    | 19 KB     | #################################################################################################################################### | 100%
wheel-0.37.1         | 31 KB     | #################################################################################################################################### | 100%
lerc-3.0             | 170 KB    | #################################################################################################################################### | 100%
libbrotlidec-1.0.9   | 33 KB     | #################################################################################################################################### | 100%
font-ttf-ubuntu-0.83 | 1.9 MB    | #################################################################################################################################### | 100%
pytz-2021.3          | 242 KB    | #################################################################################################################################### | 100%
gettext-0.19.8.1     | 3.3 MB    | #################################################################################################################################### | 100%
pixman-0.40.0        | 615 KB    | #################################################################################################################################### | 100%
zstd-1.5.1           | 454 KB    | #################################################################################################################################### | 100%
readline-8.1         | 266 KB    | #################################################################################################################################### | 100%
libblas-3.9.0        | 12 KB     | #################################################################################################################################### | 100%
ca-certificates-2021 | 139 KB    | #################################################################################################################################### | 100%
tzcode-2021e         | 64 KB     | #################################################################################################################################### | 100%
click-plugins-1.1.1  | 9 KB      | #################################################################################################################################### | 100%
packaging-21.3       | 36 KB     | #################################################################################################################################### | 100%
tzdata-2021e         | 121 KB    | #################################################################################################################################### | 100%
geopandas-base-0.10. | 982 KB    | #################################################################################################################################### | 100%
gdal-3.4.0           | 1.9 MB    | #################################################################################################################################### | 100%
requests-2.27.1      | 53 KB     | #################################################################################################################################### | 100%
python-3.10.2        | 12.9 MB   | #################################################################################################################################### | 100%
openssl-1.1.1l       | 1.9 MB    | #################################################################################################################################### | 100%
networkx-2.6.3       | 1.5 MB    | #################################################################################################################################### | 100%
statsmodels-0.13.1   | 10.9 MB   | #################################################################################################################################### | 100%
geomet-0.3.0         | 26 KB     | #################################################################################################################################### | 100%
fonttools-4.28.5     | 1.6 MB    | #################################################################################################################################### | 100%
kiwisolver-1.3.2     | 64 KB     | #################################################################################################################################### | 100%
libbrotlicommon-1.0. | 63 KB     | #################################################################################################################################### | 100%
libkml-1.3.0         | 483 KB    | #################################################################################################################################### | 100%
expat-2.4.2          | 143 KB    | #################################################################################################################################### | 100%
libpq-14.1           | 2.9 MB    | #################################################################################################################################### | 100%
markupsafe-2.0.1     | 21 KB     | #################################################################################################################################### | 100%
click-8.0.3          | 149 KB    | #################################################################################################################################### | 100%
tk-8.6.11            | 3.3 MB    | #################################################################################################################################### | 100%
brotli-1.0.9         | 18 KB     | #################################################################################################################################### | 100%
pyproj-3.3.0         | 469 KB    | #################################################################################################################################### | 100%
fonts-conda-ecosyste | 4 KB      | #################################################################################################################################### | 100%
libxml2-2.9.12       | 684 KB    | #################################################################################################################################### | 100%
libcxx-12.0.1        | 1.1 MB    | #################################################################################################################################### | 100%
pysocks-1.7.1        | 28 KB     | #################################################################################################################################### | 100%
svgwrite-1.4.1       | 51 KB     | #################################################################################################################################### | 100%
libev-4.33           | 99 KB     | #################################################################################################################################### | 100%
c-ares-1.18.1        | 100 KB    | #################################################################################################################################### | 100%
six-1.16.0           | 14 KB     | #################################################################################################################################### | 100%
html2text-2020.1.16  | 33 KB     | #################################################################################################################################### | 100%
liblapack-3.9.0      | 12 KB     | #################################################################################################################################### | 100%
kealib-1.4.14        | 156 KB    | #################################################################################################################################### | 100%
cycler-0.11.0        | 10 KB     | #################################################################################################################################### | 100%
sqlite-3.37.0        | 1.8 MB    | #################################################################################################################################### | 100%
freexl-1.0.6         | 42 KB     | #################################################################################################################################### | 100%
xyzservices-2021.11. | 26 KB     | #################################################################################################################################### | 100%
lz4-c-1.9.3          | 155 KB    | #################################################################################################################################### | 100%
scipy-1.7.3          | 20.6 MB   | #################################################################################################################################### | 100%
freetype-2.10.4      | 890 KB    | #################################################################################################################################### | 100%
rasterio-1.2.10      | 8.2 MB    | #################################################################################################################################### | 100%
nspr-4.32            | 247 KB    | #################################################################################################################################### | 100%
libedit-3.1.20191231 | 103 KB    | #################################################################################################################################### | 100%
brotli-bin-1.0.9     | 17 KB     | #################################################################################################################################### | 100%
geopandas-0.10.2     | 5 KB      | #################################################################################################################################### | 100%
idna-3.1             | 52 KB     | #################################################################################################################################### | 100%
llvm-openmp-12.0.1   | 287 KB    | #################################################################################################################################### | 100%
libglib-2.70.2       | 2.8 MB    | #################################################################################################################################### | 100%
libllvm11-11.1.0     | 22.5 MB   | #################################################################################################################################### | 100%
libtiff-4.3.0        | 622 KB    | #################################################################################################################################### | 100%
h5py-3.6.0           | 1.2 MB    | #################################################################################################################################### | 100%
libiconv-1.16        | 1.3 MB    | #################################################################################################################################### | 100%
libcurl-7.81.0       | 319 KB    | #################################################################################################################################### | 100%
poppler-data-0.4.11  | 3.6 MB    | #################################################################################################################################### | 100%
affine-2.3.0         | 16 KB     | #################################################################################################################################### | 100%
fontconfig-2.13.1    | 271 KB    | #################################################################################################################################### | 100%
libnghttp2-1.43.0    | 867 KB    | #################################################################################################################################### | 100%
pycparser-2.21       | 100 KB    | #################################################################################################################################### | 100%
librttopo-1.1.0      | 219 KB    | #################################################################################################################################### | 100%
libzip-1.8.0         | 117 KB    | #################################################################################################################################### | 100%
attrs-21.4.0         | 49 KB     | #################################################################################################################################### | 100%
colorama-0.4.4       | 18 KB     | #################################################################################################################################### | 100%
mizani-0.7.3         | 53 KB     | #################################################################################################################################### | 100%
tiledb-2.5.3         | 3.1 MB    | #################################################################################################################################### | 100%
bzip2-1.0.8          | 155 KB    | #################################################################################################################################### | 100%
pyshp-2.1.3          | 36 KB     | #################################################################################################################################### | 100%
cairo-1.16.0         | 1.3 MB    | #################################################################################################################################### | 100%
python_abi-3.10      | 4 KB      | #################################################################################################################################### | 100%
curl-7.81.0          | 145 KB    | #################################################################################################################################### | 100%
pandas-1.3.5         | 12.1 MB   | #################################################################################################################################### | 100%
psycopg2-2.9.3       | 164 KB    | #################################################################################################################################### | 100%
patsy-0.5.2          | 188 KB    | #################################################################################################################################### | 100%
lcms2-2.12           | 404 KB    | #################################################################################################################################### | 100%
setuptools-59.8.0    | 1.0 MB    | #################################################################################################################################### | 100%
font-ttf-dejavu-sans | 388 KB    | #################################################################################################################################### | 100%
munch-2.5.0          | 12 KB     | #################################################################################################################################### | 100%
geotiff-1.7.0        | 127 KB    | #################################################################################################################################### | 100%
descartes-1.1.0      | 7 KB      | #################################################################################################################################### | 100%
cached-property-1.5. | 4 KB      | #################################################################################################################################### | 100%
llvmlite-0.38.0      | 240 KB    | #################################################################################################################################### | 100%
libgfortran5-9.3.0   | 1.7 MB    | #################################################################################################################################### | 100%
postgresql-14.1      | 5.0 MB    | #################################################################################################################################### | 100%
proj-8.2.0           | 2.7 MB    | #################################################################################################################################### | 100%
json-c-0.15          | 70 KB     | #################################################################################################################################### | 100%
folium-0.12.1.post1  | 64 KB     | #################################################################################################################################### | 100%
xmltodict-0.12.0     | 11 KB     | #################################################################################################################################### | 100%
ncurses-6.2          | 881 KB    | #################################################################################################################################### | 100%
charset-normalizer-2 | 34 KB     | #################################################################################################################################### | 100%
libcblas-3.9.0       | 12 KB     | #################################################################################################################################### | 100%
threadpoolctl-3.0.0  | 17 KB     | #################################################################################################################################### | 100%
cffi-1.15.0          | 226 KB    | #################################################################################################################################### | 100%
matplotlib-base-3.5. | 7.5 MB    | #################################################################################################################################### | 100%
matplotlib-3.5.1     | 6 KB      | #################################################################################################################################### | 100%
pcre-8.45            | 220 KB    | #################################################################################################################################### | 100%
pyopenssl-21.0.0     | 48 KB     | #################################################################################################################################### | 100%
brotlipy-0.7.0       | 363 KB    | #################################################################################################################################### | 100%
geos-3.10.1          | 1.2 MB    | #################################################################################################################################### | 100%
libdap4-3.20.6       | 2.2 MB    | #################################################################################################################################### | 100%
libffi-3.4.2         | 50 KB     | #################################################################################################################################### | 100%
cligj-0.7.2          | 10 KB     | #################################################################################################################################### | 100%
cftime-1.5.1.1       | 206 KB    | #################################################################################################################################### | 100%
libopenblas-0.3.18   | 9.0 MB    | #################################################################################################################################### | 100%
fonts-conda-forge-1  | 4 KB      | #################################################################################################################################### | 100%
libbrotlienc-1.0.9   | 324 KB    | #################################################################################################################################### | 100%
libssh2-1.10.0       | 221 KB    | #################################################################################################################################### | 100%
cfitsio-4.0.0        | 1.3 MB    | #################################################################################################################################### | 100%
pip-21.3.1           | 1.2 MB    | #################################################################################################################################### | 100%
giflib-5.2.1         | 71 KB     | #################################################################################################################################### | 100%
libzlib-1.2.11       | 58 KB     | #################################################################################################################################### | 100%
fiona-1.8.20         | 966 KB    | #################################################################################################################################### | 100%
cached_property-1.5. | 11 KB     | #################################################################################################################################### | 100%
xerces-c-3.2.3       | 1.5 MB    | #################################################################################################################################### | 100%
pyparsing-3.0.6      | 79 KB     | #################################################################################################################################### | 100%
python-dateutil-2.8. | 240 KB    | #################################################################################################################################### | 100%
cartopy-0.20.2       | 1.7 MB    | #################################################################################################################################### | 100%
jpeg-9d              | 250 KB    | #################################################################################################################################### | 100%
olefile-0.46         | 32 KB     | #################################################################################################################################### | 100%
munkres-1.1.4        | 12 KB     | #################################################################################################################################### | 100%
joblib-1.1.0         | 210 KB    | #################################################################################################################################### | 100%
branca-0.4.2         | 26 KB     | #################################################################################################################################### | 100%
urllib3-1.26.8       | 100 KB    | #################################################################################################################################### | 100%
font-ttf-source-code | 684 KB    | #################################################################################################################################### | 100%
krb5-1.19.2          | 1.2 MB    | #################################################################################################################################### | 100%
snuggs-1.4.7         | 8 KB      | #################################################################################################################################### | 100%
nss-3.74             | 2.0 MB    | #################################################################################################################################### | 100%
libdeflate-1.8       | 67 KB     | #################################################################################################################################### | 100%
netcdf4-1.5.8        | 447 KB    | #################################################################################################################################### | 100%
sentinelsat-1.1.1    | 47 KB     | #################################################################################################################################### | 100%
font-ttf-inconsolata | 94 KB     | #################################################################################################################################### | 100%
pillow-8.4.0         | 688 KB    | #################################################################################################################################### | 100%
shapely-1.8.0        | 352 KB    | #################################################################################################################################### | 100%
tornado-6.1          | 657 KB    | #################################################################################################################################### | 100%
libwebp-base-1.2.1   | 692 KB    | #################################################################################################################################### | 100%
libgdal-3.4.0        | 9.4 MB    | #################################################################################################################################### | 100%
tqdm-4.62.3          | 80 KB     | #################################################################################################################################### | 100%
jinja2-3.0.3         | 99 KB     | #################################################################################################################################### | 100%
poppler-21.11.0      | 1.7 MB    | #################################################################################################################################### | 100%
zlib-1.2.11          | 87 KB     | #################################################################################################################################### | 100%
cryptography-36.0.1  | 1.4 MB    | #################################################################################################################################### | 100%
mapclassify-2.4.3    | 36 KB     | #################################################################################################################################### | 100%
libspatialindex-1.9. | 400 KB    | #################################################################################################################################### | 100%
geojson-2.5.0        | 15 KB     | #################################################################################################################################### | 100%
blosc-1.21.0         | 920 KB    | #################################################################################################################################### | 100%
libpng-1.6.37        | 313 KB    | #################################################################################################################################### | 100%
openjpeg-2.4.0       | 374 KB    | #################################################################################################################################### | 100%
icu-69.1             | 12.9 MB   | #################################################################################################################################### | 100%
numba-0.55.0         | 3.8 MB    | #################################################################################################################################### | 100%
jbig-2.1             | 42 KB     | #################################################################################################################################### | 100%
xz-5.2.5             | 228 KB    | #################################################################################################################################### | 100%
boost-cpp-1.74.0     | 15.6 MB   | #################################################################################################################################### | 100%
certifi-2021.10.8    | 145 KB    | #################################################################################################################################### | 100%
rtree-0.9.7          | 46 KB     | #################################################################################################################################### | 100%
libnetcdf-4.8.1      | 1.3 MB    | #################################################################################################################################### | 100%
scikit-learn-1.0.2   | 7.3 MB    | #################################################################################################################################### | 100%
plotnine-0.8.0       | 3.8 MB    | #################################################################################################################################### | 100%
numpy-1.21.5         | 6.1 MB    | #################################################################################################################################### | 100%
hdf5-1.12.1          | 3.7 MB    | #################################################################################################################################### | 100%
Preparing transaction: done
Verifying transaction: done
Executing transaction: done
#
# To activate this environment, use
#
#     $ conda activate geoimagine_202109
#
# To deactivate an active environment, use
#
#     $ conda deactivate
```
{% endraw %}
{% endcapture %}
{% include widgets/toggle-code.html  toggle-text=text-capture  %}
</div>

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

### Set your Python interpreter in Eclipse

With <span class='app'>Eclipse</span> workbench up and running, select from the top menu:

<span class='menu'>Eclipse : preferences</span>

In the <span class='tab'>Preferences</span> window that opens, click the PyDev expansion icon (\>) in the menu to the left. In the expanded sub-list click the expansion icon for <span class='button'>Interpreters</span> and click <span class='button'>Python interpreter</span>. In the window that opens, click the <span class='button'>Browse for python/pypy exe</span> button in the upper right corner. The dialog window <span class='tab'>Select Interpreters</span> opens.

Click the <span class='button'>Browse</span> button next to the textbox <span class='textbox'>Interpreter Executable</span>.

If you created a virtual environment, navigate to where you stored it and find the Python binary file (e.g. .../anaconda3/envs/geoimagine001/bin/python) and choose that file. Then edit the textbox <span class='textbox'>Interpreter Name</span> to something like 'Python3.x geoimagine001'.

If you did not setup a virtual Python environment you can use the Anaconda default (or 'base') environment as your Python interpreter. Click the <span class='button'>Browse</span> button and navigate to where you [installed Anaconda](https://karttur.github.io/setup-ide/setup-ide/install-anaconda), and drill down to the Python binary:

<span class='file'>.../anaconda3/bin/python</span>

Regardless of which interpreter you selected, click <span class='button'>Finish/OK</span>, and the dialog window <span class='tab'>Selection Needed</span> appears. Accept the default selection (all listed items), and click <span class='button'>Finish/OK</span> again. All the selected Libraries and their associated Packages will be linked to your project, and show up in the lower frame of the <span class='tab'>Preferences</span> window. When finished, click <span class='button'>Apply and Close</span>.

## Install additional packages in your environments

There are two main methods for installing additional Python packages to your conda environment, either using _conda install_ or _pip_. The general recommendation is to use just one of these for individual environments, with _conda install_ being the preferred method. But not all packages are available as default conda installations. All conda default available packages are listed [here](https://docs.anaconda.com/anaconda/packages/pkg-docs/). Some of the packages required by Karttur´s GeoImagaine Framework are only available via _pip_, and you thus have to mix _conda install_ and _pip_ as installations methods when setting up the complete library needed.

#### conda install

You can install new packages into your environment in the usual way that <span class='terminalapp'>conda</span> packages are installed. Just make sure that the terminal prompt points at your environment:

<span class='terminal'>(geoimagine???) ... $ conda install -c conda-forge svgwrite</span>

or tell <span class='terminalapp'>conda</span> under which environment to install the packages:

<span class='terminal'>$ conda install --name geoimagine??? -c conda-forge svgwrite/span>

Once the installation is finished you should see the installed packages under the <span class='file'>site-packages</span> path and with the <span class='terminal'>$ conda list</span>

#### Install non-listed conda packages

If you want to install a package that is not listed as an available package for your conda distribution (see above) you should first search for it as it might have been a recent addition.

<span class='terminal'>$ conda search plotnine</span>

If the search identifies the package just go ahead and install it. If the package is not found you might still find it by looking for it using your web-browser, as also suggested by a non-successful search:

```
To search for alternate channels that may provide the conda package you're
looking for, navigate to

    https://anaconda.org

and use the search bar at the top of the page.
```

For the example with _plotnine_, it is available for installation from the conda-forge channel, e.g.:

<span class='terminal'>$ conda install -c conda-forge plotnine</span>

If your package is available, it is likely that it is listed under a "-forge" command. This means that when <span class='terminal'>conda install</span> tries to solve the environment it will first report any conflicts with existing packages, and then forge conflicts. There are three alternative conflicts:

- SUPERSEED
- UPGRADE
- DOWNGRADE

**Superseed** means that the new package comes with a dependency that is already installed, but that the dependency will be replaced by the alternative found with the new package. It _should_ be exactly the same package with the same content as the existing dependency, but it will nevertheless be replaces. For all cases I have encountered it has worked out fine to accept the **superseed**. Otherwise this is the reason you are using a (virtual) environment. You just repeat the setup until the point where it went wrong, and then try another solution.

If the <span class='terminal'>-forge</span> installation reports that a package is to be **upgrade(d)** that _might_ work. If it reports that a package is in for a **downgrade** then proceeding with the installation is likely to cause problems with other packages. One solution is then to export your environment and try other versions or alternatives. Change the conflicting packages to use the same version of the common dependency (you have to look in the documentation to find versions that can go together).

#### Install using pip

If the package you want to install is neither available with <span class='terminal'>$ conda install</span>, nor available as a <span class='terminal'>$ conda -forge</span> installation, you need to use an alternative package manager (as described [here](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-pkgs.html#installing-non-conda-packages)). This more or less always boils down to <span class='terminalapp'>pip</span>.

The package _seasonal_, used for estimating and removing trend and periodicity in time-series is only available using <span class='terminalapp'>pip</span>:

<span class='terminal'>(geoimagine0) ... $ pip install seasonal</span>

Again you can check that the package was installed in the correct place by exploring the <span class='file'>site-packages</span> path.

### Additional installations

The complete GeoImagine Framework requires a handful of additional installations:

- landsatexplore
- plotnine*
- pypng*
- seasonal
- sentinelsat*
- svgis
- svgwrite*
- tqdm*

\* = installed with the extended channel conda-forge above.

##### landsatxplore

[Landsatxplore](https://pypi.org/project/landsatxplore/) is a package for searching and downloading Landsat satellite image scenes from [EarthExplorer](https://earthexplorer.usgs.gov). There are alternative packages that can be used for the same task, but Karttur's GeoImagine Framework is set up for using Landsatxplore. If you want to use Landsat data from EarthExplorer you need to register as an EarthExplorer user.

[Landsatxplore](https://pypi.org/project/landsatxplore/) is not available at any conda channel and you need to use the <span class='terminalapp'>pip</span> installation manager.

<span class='terminal'>$ pip install landsatxplore</span>

#### plotnine

[plotnine](https://plotnine.readthedocs.io/en/stable/) is a powerful graphics editor that you can use for composing maps and layouts in Python. It is like a Python version of the popular "Grammar of graphics" concept used by _ggplot_. The grammar allows users to compose plots by explicitly mapping data to the visual objects that make up the plot. plotnine is available on several conda channels, and can for instance be installed using the command:

<span class='terminal'>$ conda install -c conda-forge plotnine</span>

#### seasonal

The seasonal package estimate and remove trend and periodicity in time-series. In the Framework it is used for time-series decomposition and trend estimations.

**NOTE** that in the Framework __the seasonal package is edited to include more options and with altered default settings__.

The seasonal package is installed with [<span class='terminalapp'>pip install</span>](https://pypi.org/project/seasonal/)

<span class='terminal'>$ pip install seasonal</span>

#### sentinelsat

In the Framework Sentinelsat is used for searching, downloading and retrieving the metadata and the actual data of Sentinel satellite images from the Copernicus Open Access Hub.

Sentinelsat is preferably installed via [conda-forge](https://anaconda.org/conda-forge/sentinelsat)

<span class='terminal'>$ conda install -c conda-forge sentinelsat</span>.

Sentinelsat can also be installed with [<span class='terminalapp'>pip install</span>](https://pypi.org/project/sentinelsat/):

<span class='terminal'>$ pip install sentinelsat</span>.

#### svgis

SVGIS converts vector geodata to Scalable Vector Graphics (SVG). SVG can be styled using Cascaded Style Sheets (CSS) and also read and manipulated by drawing programs. In the Framework SVGIS is primarily used for exporting vector data to use as overlays in map layouts.

SVGIS is installed with [<span class='terminalapp'>pip install</span>](https://pypi.org/project/svgis/)

<span class='terminal'>$ pip install svgis</span>

#### svgwrite

SVGwrite is a more general library for writing SVG formated vector graphics. It is used for creating legends and other layout items for maps. The preferred installation is using [<span class='terminalapp'>conda</span>](https://anaconda.org/conda-forge/svgwrite):

<span class='terminal'>$ conda install -c conda-forge svgwrite</span>

svgwrite is also available as [<span class='terminalapp'>pip install</span>](https://pypi.org/project/svgwrite/)

<span class='terminal'>$ pip install svgwrite</span>

#### ggtools

[GRACE & GLDAS Tools (ggtools)](https://pypi.org/project/ggtools/) is a library for GRACE(Gravity Recovery and Climate Experiment) and GRACE-FO(Follow-on) GSM data(RL06 Level-2 monthly solutions) and GLDAS grid data. The package itself is installed using [<span class='terminalapp'>pip</span>](https://pypi.org/project/ggtools/). It is dependent on _cartopy_, _netcdf4_ and _h5py_; all included as default packages (listed in <span class='file'>.condarc</span>).

<span class='terminal'>$ pip install ggtools</span>

#### tqdm

tqdm is used for adding a progress line for large processes, it is preferably installed via [conda-forge](https://anaconda.org/conda-forge/tqdm):

<span class='terminal'>$ conda install -c conda-forge tqdm</span>

### Export and import environments

Once you have created a virtual environment that satisfies your needs, you can export it as <span class='file'>.yml</span> file. You can then use the <span class='file'>.yml</span> file to setup a new virtual environment, or share it to allow others to set up identical environments.

#### Export environment file (yml)

Activate the environment you want export
<span class ='terminal'>$ source activate geoimagine0</span>,

and then export

<span class='terminal'>(geoimagine) ... $ conda env export > geoimagine0.yml</span>.

The exported <span class='file'>.yml</span> file below shows the complete installation of all packages required for Karttur's GeoImagine Framework.

<button id= "toggleexport" onclick="hiddencode('export')">Hide/Show conda exported packages</button>

<div id="export" style="display:none">

{% capture text-capture %}
{% raw %}

```
name: geoimagine_202003_py37a
channels:
  - conda-forge
  - defaults
dependencies:
  - affine=2.3.0=py_0
  - attrs=20.3.0=pyhd3eb1b0_0
  - blas=1.0=mkl
  - brotlipy=0.7.0=py37hf967b71_1001
  - bzip2=1.0.8=h1de35cc_0
  - ca-certificates=2020.12.5=h033912b_0
  - cairo=1.14.12=hc4e6be7_4
  - cartopy=0.18.0=py37hf1ba7ce_1
  - certifi=2020.12.5=py37hf985489_1
  - cffi=1.14.4=py37hbddb872_0
  - cfitsio=3.470=hb33e7b4_2
  - cftime=1.4.1=py37he3068b8_0
  - chardet=4.0.0=py37hf985489_1
  - click=7.1.2=pyhd3eb1b0_0
  - click-plugins=1.1.1=py_0
  - cligj=0.7.1=py37hecd8cb5_0
  - cryptography=3.4.4=py37ha1e1f9f_0
  - curl=7.67.0=ha441bb4_0
  - cycler=0.10.0=py37_0
  - descartes=1.1.0=py_4
  - expat=2.2.10=hb1e8313_2
  - fiona=1.8.13.post1=py37h9c05f0f_0
  - fontconfig=2.13.1=ha9ee91d_0
  - freetype=2.10.4=ha233b18_0
  - freexl=1.0.6=h9ed2024_0
  - gdal=3.0.2=py37hbe65578_0
  - geojson=2.5.0=py_0
  - geomet=0.3.0=pyhd3deb0d_0
  - geopandas=0.8.1=py_0
  - geos=3.8.0=hb1e8313_0
  - geotiff=1.5.1=h0b0f252_0
  - gettext=0.19.8.1=h15daf44_3
  - giflib=5.1.4=h1de35cc_1
  - glib=2.63.1=hd977a24_0
  - h5py=2.10.0=py37h3134771_0
  - hdf4=4.2.13=h39711bb_2
  - hdf5=1.10.4=hfa1e0ec_0
  - html2text=2020.1.16=py_0
  - icu=58.2=h0a44026_3
  - idna=2.10=pyh9f0ad1d_0
  - intel-openmp=2019.4=233
  - jpeg=9b=he5867d9_2
  - json-c=0.13.1=h3efe00b_0
  - kealib=1.4.7=hf5ed860_6
  - kiwisolver=1.3.1=py37h23ab428_0
  - krb5=1.16.4=hddcf347_0
  - lcms2=2.11=h92f6f08_0
  - libboost=1.67.0=hebc422b_4
  - libcurl=7.67.0=h051b688_0
  - libcxx=10.0.0=1
  - libdap4=3.19.1=h3d3e54a_0
  - libedit=3.1.20191231=h1de35cc_1
  - libffi=3.2.1=h0a44026_1007
  - libgdal=3.0.2=h42cfeda_0
  - libgfortran=3.0.1=h93005f0_2
  - libiconv=1.16=h1de35cc_0
  - libkml=1.3.0=hbe12b63_4
  - libllvm10=10.0.1=h76017ad_5
  - libnetcdf=4.6.1=hd5207e6_2
  - libpng=1.6.37=ha441bb4_0
  - libpq=11.2=h051b688_0
  - libspatialindex=1.9.3=h0a44026_0
  - libspatialite=4.3.0a=h5142b36_0
  - libssh2=1.9.0=ha12b0ac_1
  - libtiff=4.1.0=hcb84e12_0
  - libxml2=2.9.10=h7cdb67c_3
  - llvm-openmp=10.0.0=h28b9765_0
  - llvmlite=0.34.0=py37h739e7dc_4
  - lz4-c=1.8.1.2=h1de35cc_0
  - matplotlib=3.3.2=hecd8cb5_0
  - matplotlib-base=3.3.2=py37h181983e_0
  - mizani=0.7.2=pyhd8ed1ab_0
  - mkl=2019.4=233
  - mkl-service=2.3.0=py37h9ed2024_0
  - mkl_fft=1.2.0=py37hc64f4ea_0
  - mkl_random=1.1.1=py37h959d312_0
  - munch=2.5.0=py_0
  - ncurses=6.2=h0a44026_1
  - netcdf4=1.4.2=py37h13743db_0
  - numba=0.51.2=py37h959d312_1
  - olefile=0.46=py37_0
  - openjpeg=2.3.0=hb95cd4c_1
  - openssl=1.1.1i=h35c211d_0
  - palettable=3.3.0=py_0
  - pandas=1.2.1=py37hb2f4e1b_0
  - patsy=0.5.1=py37_0
  - pcre=8.44=hb1e8313_0
  - pillow=8.1.0=py37h5270095_0
  - pip=20.3.3=py37hecd8cb5_0
  - pixman=0.40.0=haf1e3a3_0
  - plotnine=0.7.1=py_0
  - poppler=0.65.0=ha097c24_1
  - poppler-data=0.4.10=hecd8cb5_0
  - postgresql=11.2=h051b688_0
  - proj=6.2.1=hfd5b9e3_0
  - psycopg2=2.8.4=py37ha12b0ac_0
  - pycparser=2.20=pyh9f0ad1d_2
  - pyopenssl=20.0.1=pyhd8ed1ab_0
  - pyparsing=2.4.7=pyhd3eb1b0_0
  - pyproj=2.6.1.post1=py37h6d9c95c_1
  - pyshp=2.1.3=pyhd3eb1b0_0
  - pysocks=1.7.1=py37hf985489_3
  - python=3.7.7=hfe9666f_0_cpython
  - python-dateutil=2.8.1=pyhd3eb1b0_0
  - python_abi=3.7=1_cp37m
  - pytz=2021.1=pyhd3eb1b0_0
  - rasterio=1.1.0=py37heeaa653_0
  - readline=7.0=h1de35cc_5
  - requests=2.25.1=pyhd3deb0d_0
  - rtree=0.9.4=py37_1
  - scipy=1.6.0=py37h2515648_0
  - sentinelsat=0.14=pyh9f0ad1d_0
  - setuptools=52.0.0=py37hecd8cb5_0
  - shapely=1.7.1=py37hb435bde_0
  - six=1.15.0=py37hecd8cb5_0
  - snuggs=1.4.7=py_0
  - sqlite=3.33.0=hffcf06c_0
  - statsmodels=0.12.1=py37h9ed2024_0
  - svgwrite=1.4.1=pyhd8ed1ab_0
  - tbb=2018.0.5=h04f5b5a_0
  - tiledb=1.6.3=h29f752d_0
  - tk=8.6.10=hb0a8c7a_0
  - tornado=6.1=py37h9ed2024_0
  - tqdm=4.56.2=pyhd8ed1ab_0
  - urllib3=1.26.3=pyhd8ed1ab_0
  - wheel=0.36.2=pyhd3eb1b0_0
  - xerces-c=3.2.3=h48eee30_0
  - xmltodict=0.12.0=py_0
  - xz=5.2.5=h1de35cc_0
  - zlib=1.2.11=h1de35cc_3
  - zstd=1.3.7=h5bba6e5_0
  - pip:
    - appdirs==1.4.4
    - astropy==4.2
    - cssselect==1.1.0
    - datetime==4.3
    - decorator==4.4.2
    - ggtools==1.1.7
    - gpy==1.9.9
    - landsatxplore==0.9
    - lxml==4.6.2
    - numpy==1.20.1
    - packaging==20.9
    - paramz==0.9.5
    - pooch==1.3.0
    - pyerfa==1.7.2
    - pyshtools==4.8
    - seasonal==0.3.1
    - sphericalpolygon==1.2.0
    - svgis==0.5.1
    - tinycss2==1.1.0
    - utm==0.7.0
    - webencodings==0.5.1
    - xarray==0.16.2
    - zope-interface==5.2.0
prefix: /Applications/anaconda3/envs/geoimagine_202003_py37a
```
{% endraw %}
{% endcapture %}
{% include widgets/toggle-code.html  toggle-text=text-capture  %}
</div>

### Import

TO be completed

## Resources

[Using the .condarc conda configuration file](https://docs.conda.io/projects/conda/en/latest/user-guide/configuration/use-condarc.html)

[Anaconda package lists](https://docs.anaconda.com/anaconda/packages/pkg-docs/)

[Conda - Managing environments](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html)

[Using the .condarc conda configuration file](https://docs.conda.io/projects/conda/en/latest/user-guide/configuration/use-condarc.html)
