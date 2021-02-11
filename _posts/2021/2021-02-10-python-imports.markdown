---
layout: article
title: Python 3.7 imports
previousurl: null
nexturl: null
categories: blog
excerpt: "Python 3.7 imports and import structures within a PyDev project"
tags:
  - Python 3.7
  - package
  - import
  - sibling
  - relative
  - absolute
  - __init__.py
image: rainfall-delta_3B43_trmm_2001-2016_mk-z-ts-model@p005
date: '2021-02-10'
modified: '2021-02-10'
comments: true
share: true
---

## Introduction

Correctly referencing functions (_def_), classes (_class_), modules (<span class='file'>.py</span> files) and packages (directories/folders, with one or more [linked] <span class='file'>.py</span> files) in Python can be done in different ways and is not that easy to understand. This post tries to disentangle the different ways in which it can be done. After going over different alternatives the final part summarizes the import structure I use for Karttur's GeoImagine Framework. This manual is done using Python 3.7.

## Absolute and relative paths

The article [Absolute vs Relative Imports in Python](https://realpython.com/absolute-vs-relative-python-imports/) is a good summary on that never ending topic. In most cases I prefer an absolute path. But building access import via *\_\_init\_\_.py* the inclusion of modules, functions, classes etc in *\_\_init\_\_.py* itself could be relative (start with dot [.]). This is logical as the content of a package has a given hierarchy and structure that is fixed vis-a-vis *\_\_init\_\_.py*.

## Importing siblings within the same package

Import of a sibling can be done with or without using *\_\_init\_\_.py*.

### Without using *\_\_init\_\_.py*

Importing without  *\_\_init\_\_.py* is done using absolute paths. Just leave out the <span class='file'>.py</span> ending in the import clause. You can then call any function (_def_) or class (_class_) etc. within the imported module using the standard Python dot notation:

```
# Example 1: import of sibling module

import parent_package.sibling_module1
import parent_package.sibling_module2

result = parent_package.sibling_module1.def_function1(param1, param2)
result = parent_package.sibling_module2.def_function2(param1, param2)
```

or

```
# Example 2: import of sibling module

from parent_package.sibling_module1 import def_function1
from parent_package.sibling_module2 import def_function2

result1 = def_function1(param1, param2)
result2 = def_function2(param1, param2)
```

or

```
# Example 3: import of sibling module

from parent_package.sibling_module1 import *
from parent_package.sibling_module2 import *

result1 = def_function1(param1, param2)
result2 = def_function2(param1, param2)
```

The two first alternatives can be imported using an alias.

```
# Example 1a: import of sibling module as alias

import parent_package.sibling_module1 as sm1
import parent_package.sibling_module2 as sm2

result = sm1.def_function1(param1, param2)
result = sm2.def_function2(param1, param2)
```

or

```
# Example 2b: import of sibling module as alias

from parent_package.sibling_module1 import def_function1 as df1
from parent_package.sibling_module2 import def_function2 as df2

result1 = df1(param1, param2)
result2 = df2(param1, param2)
```

You _can_ skip the full path by omitting the parent package name (_parent_package_). It still works, but in
my <span class='app'>Eclipse</span> PyDev environment and Python 3.7, the import functions all become indicated as erroneous.

### With *\_\_init\_\_.py*

Siblings can also be imported via definitions in *\_\_init\_\_.py*. The syntax for that is identical for import of siblings or any external Python script.  

## Import definitions via *\_\_init\_\_.py*

Using *\_\_init\_\_.py* to expose functions, classes etc, not only to siblings but to any other module in your project structure. The syntax is the same for siblings as for a call from an external package.

Importing through *\_\_init\_\_.py* differs between different versions of Python. In general the definition of imports has become more strict over time. As noted in the introduction, this manual is done using Python 3.7.

The three different alternatives for import structuring, as well as the labelling, are taken from the article [What’s \_\_init\_\_ for me?](https://towardsdatascience.com/whats-init-for-me-d70a312da583) by Jacob Deppen.

### Import complete modules ("Grocery store")

One alternative is to import complete modules within a package (rather than individual functions, classes etc). Sometimes referred to as an online Grocery store. A lot of choices for the user, but also a lot of steps before you reach the product you are looking for.

As far as I can figure out, the complete package can only be imported by using a full and absolute path in *\_\_init\_\_.py*:

```
# __init__.py

import package.module1
import package.module2
```

#### User implementation

On the implementation side, the user has to define the full path for the import. This can be done in at least four different ways:

```
import package.module1
import package.module2

result1 = package.module1.def_function1(param1, param2)
result2 = package.module2.def_function2(param1, param2)
```

or

```
from package import module1, module2

result1 = module1.def_function1(param1, param2)
result2 = module2.def_function2(param1, param2)
```

or

```
from package.module1 import def_function1
from package.module2 import def_function2

result1 = def_function1(param1, param2)
result2 = def_function2(param1, param2)
```

or

```
from package.module1 import *
from package.module2 import *

result1 = def_function1(param1, param2)
result2 = def_function2(param1, param2)
```

For the first and third alternatives alias can be used.

```
from package import module1 as md1
from package import module2 as md2

result1 = md1.def_function1(param1, param2)
result2 = md2.def_function2(param1, param2)
```

or

```
from package.module1 import def_function1 as df1
from package.module2 import def_function2 as df2

result1 = df1(param1, param2)
result2 = df2(param1, param2)
```

### Import functions and classes ("Convenience" store)

A second alternative is to bypass the module level and link functions, classes etc directly to the package in *\_\_init\_\_.py*. This is sometimes called the Convenience store - because it offers the readily packed products on display with no detour. It can be defined using both relative and absolute paths.

The paths, whether abslute or relative, must be given using the  _from .module import_ (or _from package.module import_) reference style. By explicitly defining the import items in *\_\_init\_\_.py*, internal functions are omitted and not directly available from the outside.

```
# __init__.py

from .module1 import def_function1
from .module2 import def_function2
```

#### User implementation

In the module that then uses the package (including siblings to module1 and module2), you import the _package_ (not the individual modules). And then you call the function direct from under the package name (the module step in between is omitted when calling).

```
import package

result1 = package.def_function1(param1, param2)
result2 = package.def_function2(param1, param2)
```

or

```
from package import def_function1, def_function2

result1 = def_function1(param1, param2)
result2 = def_function2(param1, param2)
```

Again you can use an alias in the first example of this type.

```
import package as pck

result1 = pck.def_function1(param1, param2)
result2 = pck.def_function2(param1, param2)
```

In my setup (python 3.7 running under <span class='app'>Eclipse</span>) there are no error warning with these solutions.

### Wildcard (*) imports ("General" store)

With a wildcard all functions of a module are imported in one go. This is an easy solution for the developer, giving the user access to everything in the module. It is thus sometimes called the General store.

```
#__init__.py

from .module1 import *
from .module2 import *
```

#### User implementation

The import functions on the user side remain the same as for explicitly defined content ("Convenience" store).

### Clarifying and combining with the *\_\_all\_\_* clause

An extension (alternative or complement) to the import definitions in *\_\_init\_\_.py* above, is to list the key functions (classes etc) under *\_\_all\_\_*. This has two effects, only items listed under *\_\_all\_\_* will be available when importing using a wildcard (*), and the list will also reveal how the package is intended to be used. Here is an example:

```
# __init__.py

from .module1 import def_function1
from .module2 import def_function2

__all__ = ['def_function1', 'def_function2']
```

## Karttur´s solution

In Karttur´s GeoImagine Framework the imports are defined using the "Convenience" store approach, complemented with an *\_\_all\_\_* list. The *\_\_all\_\_* syntax allows import of all the key features using a wildcard on the calling side. The path stated in *\_\_init\_\_.py* can be either relative or absolute with this solution. But in Karttur's implementation a relative path is used. Thus a typical package *\_\_init\_\_.py* looks like this:


```
# __init__.py

from .module1 import def_function1
from .module2 import def_function2
```

The user implemented call separates imports into three groups: _Standard library imports_, _Third party imports_ and _Package application imports_. Thus a typical module calling other parts of Karttur´s GeoImagine Framework looks like this:

```
# Standard library imports

from os import path

# Third party imports

from base64 import b64encode

import netrc

# Package application imports

from setup_db import PGsession
```

## On \_\_init\_\_.py

The official [Python 3 document on \_\_init\_\_.py](https://docs.python.org/3/reference/import.html#regular-packages)

When a regular package is imported, this *\_\_init\_\_.py* file is implicitly executed, and the objects it defines are bound to names in the package’s namespace. The *\_\_init\_\_.py* file can contain the same Python code that any other module can contain, and Python will add some additional attributes to the module when it is imported.

## Resources

[Absolute vs Relative Imports in Python](https://realpython.com/absolute-vs-relative-python-imports/) - perhaps the most clear description on how to use the import function in python coding.

[What’s \_\_init\_\_ for me?](https://towardsdatascience.com/whats-init-for-me-d70a312da583) compares three different alternatives for how to open up package contents for importing in other modules. It does, however, not cover the \_\_all\_\_ alternative.

[How to create a Python Package with \_\_init\_\_.py](https://timothybramlett.com/How_to_create_a_Python_Package_with___init__py.html)
