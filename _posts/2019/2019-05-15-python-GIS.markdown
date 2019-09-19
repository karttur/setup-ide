---
layout: post
title: Python Geoinformatics
date: "2019-05-15 08:43"
modified: 2019-05-15 08:43
categories: blog
excerpt: "A short review of GIS processing packages in Python."
tags:
  - macOS
  - replace
  - ubuntu
  - target mode
image: rainfall-delta_3B43_trmm_2001-2016_mk-z-ts-model
comments: true
share: true
---

# Introduction to Python GIS

Python GIS
check [https://automating-gis-processes.github.io/2016/Lesson1-Intro-Python-GIS.html](https://automating-gis-processes.github.io/2016/Lesson1-Intro-Python-GIS.html)

The stuff you can do with Python GIS include:

- Read / write spatial data from/to different file formats
- Deal with different projections
- Do different geometric operations and geocoding
- Reclassify your data based on different criteria
- Do spatial queries
- Do simple spatial analyses
- Visualize data and create (interactive) maps, such as following


## Packages

Below we have listed most of the crucial modules (and links to their docs) that helps you get going when doing data analysis or GIS in Python. If you are interested or when you start using these modules in your own work, you should read the documentation from the web pages of the module that you need:

Data analysis & visualization:
- [Numpy](http://www.numpy.org) –> Fundamental package for scientific computing with Python
- [Pandas](http://pandas.pydata.org) –> High-performance, easy-to-use data structures and data analysis tools
[Scipy](https://www.scipy.org/about.html) –> A collection of numerical algorithms and domain-specific toolboxes, including signal processing, optimization and statistics
[Matplotlib](https://matplotlib.org) –> Basic plotting library for Python
[Bokeh](http://bokeh.pydata.org/en/latest/) –> Interactive visualizations for the web (also maps)
[Plotly](https://plot.ly/python/) –> Interactive visualizations (also maps) for the web (commercial - free for educational purposes)

### GIS:

[pyshp](https://pypi.org/project/pyshp/) -> Python Shapefile Library

[GDAL](https://www.gdal.org) –> Fundamental package for processing vector and raster data formats (many modules below depend on this). Used for raster processing.
[Geopandas](http://geopandas.org/#description) –> Working with geospatial data in Python made easier, combines the capabilities of pandas and shapely.
[Shapely](https://pypi.org/project/Shapely/) –> Python package for manipulation and analysis of planar geometric objects (based on widely deployed GEOS).
[Fiona](https://pypi.org/project/Fiona/) –> Reading and writing spatial data (alternative for geopandas).

[Pyproj](https://pypi.org/project/pyproj/) –> Performs cartographic transformations and geodetic computations (based on PROJ.4). For transformation of projections. Mostly unnecessary when using the more conveniant geopandas coordinate reference system (crs) functions.

[Pysal](https://pysal.readthedocs.io/en/latest/) –> Library of spatial analysis functions written in Python. The Python Spatial Analysis Library contains a multitude of functions for spatial analysis, statistical modeling and plotting.

[Geopy](https://geopy.readthedocs.io/en/latest/) –> Geocoding library: coordinates to address <-> address to coordinates.

[GeoViews](http://geo.holoviews.org/index.html) –> Interactive Maps for the web.
[Networkx](https://networkx.github.io/documentation/networkx-1.10/overview.html) –> Network analysis and routing in Python (e.g. Dijkstra and A* -algorithms), see this post.

[Cartopy](https://scitools.org.uk/cartopy/docs/latest/index.html) –> Make drawing maps for data analysis and visualisation as easy as possible.
[Scipy.spatial](https://docs.scipy.org/doc/scipy/reference/spatial.html) –> Spatial algorithms and data structures.
[Rtree](http://toblerity.org/rtree/) –> Spatial indexing for Python for quick spatial lookups.
[Rasterio](https://github.com/mapbox/rasterio) –> Clean and fast and geospatial raster I/O for Python.
[RSGISLib](https://www.rsgislib.org/index.html#python-documentation) –> Remote Sensing and GIS Software Library for Python.
[georasters](https://pypi.org/project/georasters/) -> Provides a fast and flexible tool to work with GIS raster files.

[rasterstats](https://pythonhosted.org/rasterstats/) -> For zonal statistics. Extracts statistics from rasters files or numpy arrays based on geometries.

[scikit-image](https://scikit-image.org) -> Library for image manipulation, e.g. histogram adjustments, filter, segmentation/edge detection operations, texture feature extraction etc.

[scikit-learn](https://scikit-learn.org/stable/) -> The best and at the same time easy-to-use Python machine learning library. Regression, classification, dimensionality reductions etc.

[folium](https://github.com/python-visualization/folium) -> Lets you visualize spatial data on interactive leaflet maps.

[descartes](https://pypi.org/project/descartes/) Enables plotting of shapely geometries as matplotlib paths/ patches. Also a dependency for the geometry plotting functions of geopandas.

[xarray](https://xarray.pydata.org/en/stable/) Great for handling extensive image time series stacks, imagine 5 vegetation indices x 24 dates x 256 pixel x 256 pixel. xarray lets you label the dimensions of the multidimensional numpy array and combines this with many functions and the syntax of the pandas library (e.g. groupby, rolling window, plotting). Not essential for beginners, but it is a great addition when working with extensive time series data.

[seaborn](https://seaborn.pydata.org) -> Statistical data visualization.

# Color ramps

[matplot libs colorramps](https://matplotlib.org/tutorials/colors/colormaps.html)

# Resources

[x](https://medium.com/@chrieke/essential-geospatial-python-libraries-5d82fcc38731)

[the “Awesome geospatial" list](https://github.com/sacridini/Awesome-Geospatial)

[Mapping Geograph Data in Python](https://towardsdatascience.com/mapping-geograph-data-in-python-610a963d2d7f)
