---
layout: article
title: Conda virtual environments I
modified: '2019-08-18T18:17:25.000Z'
categories: setup-ide
previousurl: setup-ide/install-anaconda
nexturl: setup-ide/install-eclipse
excerpt: "Create Conda environment for Karttur's GeoImagaine project"
tags:
  - Conda environment
image: avg-trmm-3b43v7-precip_3B43_trmm_2001-2016_A
date: '2019-08-18 T18:17:25.000Z'
comments: true
share: true
figure1: eclipse_select_import
figure2: eclipse_import_project_from_file_system_or_archive
---
# Introduction

With conda, you can create, update, export and import virtual Python environments that have different versions of Python and/or packages installed in them. If you use [<span class='app'>Eclipse</span>](../install-eclipse) as your Integrated Development Environment (IDE) you can easily reset your Python source to a virtual version created in conda. You can also share an environment by exporting and then importing it.

# Prerequisites

Conda must be installed as described in [this](../install-anaconda/) post.

# Conda virtual environments

Karttur's GeoImagine Framework requires a large set for Python packages to work. You have to install these packages and then link them to the Framework Spatial Data IDE (SPIDE). Many high level packages depend on other, more basic, packages. When installing many packages there is a large risk of forcing conflicting requirements regarding the versions of shared (i.e. more basic) packages. Sequentially installing (high level) packages can easily lead to a corrupt system due to conflicting requirements regarding shared (more basic) packages.

To avoid having your complete system corrupted, it is recommended that you build the Python system and packages using a "virtual" environment. In essence this means that you build a system that is working as a stand-alone solution not affecting the core (or 'base') system. This is easily done in conda. This tutorial will take your through the steps of creating a virtual python environment in conda. You will not assemble the complete GeoImaigine Framework Python package library in this post, but rather just set the core packages. Then you can proceed to install [<span class='app'>Eclipse</span>](../install-eclipse) and setup PyDev using the virtual environment created in this post.

## conda configuration with .condarc

To create a virtual environment from scratch you need to have a <span class='file'>.condarc</span> configuration file in you personal folder.

The <span class='file'>.condarc</span> is not included by default when you [installed conda](../install-anaconda/). To find out if you have a <span class='file'>.condarc</span> file open a <span class='app'>terminal</span> window and type:
 <span class='terminal'>$ conda info</span>

Look for the line <span class='terminal'>user config file:</span> in the results.

You can create the <span class='file'>.condarc</span> file using a text editor (e.g. [<span class='atom'>Atom</span>](https://karttur.github.io/setup-blog/2017/12/21/setup-blog-tools.html)), directly from the command line ( <span class='terminal'>~$ pico .condarc</span>) or by running the command:

<span class='terminal'>$ conda config</span>

You can set a lot of parameters and functions in <span class='file'>.condarc</span> (as described [here](https://docs.conda.io/projects/conda/en/latest/user-guide/configuration/use-condarc.html)), but for now you will use it for defining a set of default packages that will always be included when creating a new environment.

### Default packages

The manual for setting default packages to install with every new environment is described [here](https://docs.conda.io/projects/conda/en/latest/user-guide/configuration/use-condarc.html#config-add-default-pkgs). Lists of available packages for different conda distributions are found [here](https://docs.anaconda.com/anaconda/packages/pkg-docs/).

For creating virtual conda python environments for Karttur's GeoImagine Framework, add the following lines to your <span class='file'>.condarc</span> file:

```
create_default_packages:
  - pip
  - numpy
  - scipy
  - pandas
  - geopandas
  - rasterio
  - psycopg2
  - statsmodels
  - numba
  - xmltodict
```

The above list will also install several other packages that are required by the Framework, including for instance <span class='package'>GDAL</span>, <span class='package'>fiona</span> and <span class='package'>shapely</span>.

The advantage with installing the core components in a single command is that conda will solve conflicts among dependencies. In other words, it is best to install all packages at once, so that all of the dependencies are installed at the same time.

## Create a new environment

If you now create a new environment:

<span class='terminal'>$ conda create --name geoimagineXYZ</span>,

the rather short list of default packages will create a rather long list of package to install:

```
$ conda create --name geoimagine001
Collecting package metadata: done
Solving environment: done

## Package Plan ##

  environment location: /Applications/anaconda3/envs/geoimagine001

  added / updated specs:
    - geopandas
    - numba
    - numpy
    - pandas
    - pip
    - psycopg2
    - scipy
    - statsmodels
    - xmltodict


The following packages will be downloaded:
...
...

The following NEW packages will be INSTALLED:

  attrs              pkgs/main/osx-64::attrs-19.1.0-py37_1
  blas               pkgs/main/osx-64::blas-1.0-mkl
  bzip2              pkgs/main/osx-64::bzip2-1.0.6-h1de35cc_5
  ca-certificates    pkgs/main/osx-64::ca-certificates-2019.5.15-0
  cairo              pkgs/main/osx-64::cairo-1.14.12-hc4e6be7_4
  certifi            pkgs/main/osx-64::certifi-2019.3.9-py37_0
  click              pkgs/main/osx-64::click-7.0-py37_0
  click-plugins      pkgs/main/noarch::click-plugins-1.1.1-py_0
  cligj              pkgs/main/osx-64::cligj-0.5.0-py37_0
  curl               pkgs/main/osx-64::curl-7.64.1-ha441bb4_0
  cycler             pkgs/main/osx-64::cycler-0.10.0-py37_0
  descartes          pkgs/main/osx-64::descartes-1.1.0-py37_0
  expat              pkgs/main/osx-64::expat-2.2.6-h0a44026_0
  fiona              pkgs/main/osx-64::fiona-1.8.4-py37h9a122fd_0
  fontconfig         pkgs/main/osx-64::fontconfig-2.13.0-h5d5b041_1
  freetype           pkgs/main/osx-64::freetype-2.9.1-hb4e5f40_0
  freexl             pkgs/main/osx-64::freexl-1.0.5-h1de35cc_0
  gdal               pkgs/main/osx-64::gdal-2.3.3-py37hbe65578_0
  geopandas          pkgs/main/noarch::geopandas-0.4.1-py_0
  geos               pkgs/main/osx-64::geos-3.7.1-h0a44026_0
  gettext            pkgs/main/osx-64::gettext-0.19.8.1-h15daf44_3
  giflib             pkgs/main/osx-64::giflib-5.1.4-h1de35cc_1
  glib               pkgs/main/osx-64::glib-2.56.2-hd9629dc_0
  hdf4               pkgs/main/osx-64::hdf4-4.2.13-h39711bb_2
  hdf5               pkgs/main/osx-64::hdf5-1.10.4-hfa1e0ec_0
  icu                pkgs/main/osx-64::icu-58.2-h4b95b61_1
  intel-openmp       pkgs/main/osx-64::intel-openmp-2019.4-233
  jpeg               pkgs/main/osx-64::jpeg-9b-he5867d9_2
  json-c             pkgs/main/osx-64::json-c-0.13.1-h3efe00b_0
  kealib             pkgs/main/osx-64::kealib-1.4.7-hf5ed860_6
  kiwisolver         pkgs/main/osx-64::kiwisolver-1.1.0-py37h0a44026_0
  krb5               pkgs/main/osx-64::krb5-1.16.1-hddcf347_7
  libboost           pkgs/main/osx-64::libboost-1.67.0-hebc422b_4
  libcurl            pkgs/main/osx-64::libcurl-7.64.1-h051b688_0
  libcxx             pkgs/main/osx-64::libcxx-4.0.1-hcfea43d_1
  libcxxabi          pkgs/main/osx-64::libcxxabi-4.0.1-hcfea43d_1
  libdap4            pkgs/main/osx-64::libdap4-3.19.1-h3d3e54a_0
  libedit            pkgs/main/osx-64::libedit-3.1.20181209-hb402a30_0
  libffi             pkgs/main/osx-64::libffi-3.2.1-h475c297_4
  libgdal            pkgs/main/osx-64::libgdal-2.3.3-h0950a36_0
  libgfortran        pkgs/main/osx-64::libgfortran-3.0.1-h93005f0_2
  libiconv           pkgs/main/osx-64::libiconv-1.15-hdd342a3_7
  libkml             pkgs/main/osx-64::libkml-1.3.0-hbe12b63_4
  libnetcdf          pkgs/main/osx-64::libnetcdf-4.6.1-hd5207e6_2
  libpng             pkgs/main/osx-64::libpng-1.6.37-ha441bb4_0
  libpq              pkgs/main/osx-64::libpq-11.2-h051b688_0
  libspatialindex    pkgs/main/osx-64::libspatialindex-1.8.5-h2c08c6b_2
  libspatialite      pkgs/main/osx-64::libspatialite-4.3.0a-h644ec7d_19
  libssh2            pkgs/main/osx-64::libssh2-1.8.2-ha12b0ac_0
  libtiff            pkgs/main/osx-64::libtiff-4.0.10-hcb84e12_2
  libxml2            pkgs/main/osx-64::libxml2-2.9.9-hab757c2_0
  llvmlite           pkgs/main/osx-64::llvmlite-0.28.0-py37h8c7ce04_0
  mapclassify        pkgs/main/noarch::mapclassify-2.0.1-py_0
  matplotlib         pkgs/main/osx-64::matplotlib-3.1.0-py37h54f8f79_0
  mkl                pkgs/main/osx-64::mkl-2019.4-233
  mkl_fft            pkgs/main/osx-64::mkl_fft-1.0.12-py37h5e564d8_0
  mkl_random         pkgs/main/osx-64::mkl_random-1.0.2-py37h27c97d8_0
  munch              pkgs/main/osx-64::munch-2.3.2-py37_0
  ncurses            pkgs/main/osx-64::ncurses-6.1-h0a44026_1
  numba              pkgs/main/osx-64::numba-0.43.1-py37h6440ff4_0
  numpy              pkgs/main/osx-64::numpy-1.16.4-py37hacdab7b_0
  numpy-base         pkgs/main/osx-64::numpy-base-1.16.4-py37h6575580_0
  openjpeg           pkgs/main/osx-64::openjpeg-2.3.0-hb95cd4c_1
  openssl            pkgs/main/osx-64::openssl-1.1.1c-h1de35cc_1
  pandas             pkgs/main/osx-64::pandas-0.24.2-py37h0a44026_0
  patsy              pkgs/main/osx-64::patsy-0.5.1-py37_0
  pcre               pkgs/main/osx-64::pcre-8.43-h0a44026_0
  pip                pkgs/main/osx-64::pip-19.1.1-py37_0
  pixman             pkgs/main/osx-64::pixman-0.38.0-h1de35cc_0
  poppler            pkgs/main/osx-64::poppler-0.65.0-ha097c24_1
  poppler-data       pkgs/main/osx-64::poppler-data-0.4.9-0
  proj4              pkgs/main/osx-64::proj4-5.2.0-h0a44026_1
  psycopg2           pkgs/main/osx-64::psycopg2-2.7.6.1-py37ha12b0ac_0
  pyparsing          pkgs/main/noarch::pyparsing-2.4.0-py_0
  pyproj             pkgs/main/osx-64::pyproj-1.9.6-py37h9c430a6_0
  python             pkgs/main/osx-64::python-3.7.3-h359304d_0
  python-dateutil    pkgs/main/osx-64::python-dateutil-2.8.0-py37_0
  pytz               pkgs/main/noarch::pytz-2019.1-py_0
  readline           pkgs/main/osx-64::readline-7.0-h1de35cc_5
  rtree              pkgs/main/osx-64::rtree-0.8.3-py37_0
  scipy              pkgs/main/osx-64::scipy-1.2.1-py37h1410ff5_0
  setuptools         pkgs/main/osx-64::setuptools-41.0.1-py37_0
  shapely            pkgs/main/osx-64::shapely-1.6.4-py37he8793f5_0
  six                pkgs/main/osx-64::six-1.12.0-py37_0
  sqlalchemy         pkgs/main/osx-64::sqlalchemy-1.3.3-py37h1de35cc_0
  sqlite             pkgs/main/osx-64::sqlite-3.28.0-ha441bb4_0
  statsmodels        pkgs/main/osx-64::statsmodels-0.9.0-py37h1d22016_0
  tk                 pkgs/main/osx-64::tk-8.6.8-ha441bb4_0
  tornado            pkgs/main/osx-64::tornado-6.0.2-py37h1de35cc_0
  wheel              pkgs/main/osx-64::wheel-0.33.4-py37_0
  xerces-c           pkgs/main/osx-64::xerces-c-3.2.2-h44e365a_0
  xmltodict          pkgs/main/noarch::xmltodict-0.12.0-py_0
  xz                 pkgs/main/osx-64::xz-5.2.4-h1de35cc_4
  zlib               pkgs/main/osx-64::zlib-1.2.11-h1de35cc_3
  zstd               pkgs/main/osx-64::zstd-1.3.7-h5bba6e5_0


Proceed ([y]/n)?
```

Just press <span class='terminal'>y</span> and let conda setup your environment. The terminal response should then be like this:

```
Proceed ([y]/n)? y

Preparing transaction: done
Verifying transaction: done
Executing transaction: done
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

<span class='terminal'>$ conda remove --name geoimagine001 --all</span>

or

<span class='terminal'>$ conda env remove --name geoimagine001</span>

The conda base setup is not affected by either installing or deleting a virtual environment.

## Activate your environment

To list available environments type:

<span class='terminal'>$ conda info -e</span>

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

[Managing environments](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html)
