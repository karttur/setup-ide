---
layout: article
title: 'Connect Python and PostgreSQL using psycopg2'
modified: 2019-08-20T17:17:25.000Z
categories: setup-ide
previousurl: setup-ide/install-postgres
nexturl: setup-ide/setup-db-karttur
excerpt: 'Install psycopg2, create connection to Postgres and createdb'
tags:
  - Python
  - Anaconda
  - psycopg2
  - macOS
image: rainfall-delta_3B43_trmm_2001-2016_mk-z-ts-model@p005
date: '2018-01-06 17:53'
comments: true
share: true
---
<script src="https://karttur.github.io/common/assets/js/karttur/togglediv.js"></script>

## Introduction

In earlier posts I described how to [install Eclipse IDE](../install-eclipse/) for Python development after [installing Anaconda Python](../install-anaconda/) as the Python interpreter, and then I [installed PostgreSQL and PostGIS](../install-postgres/). This post describes how to connect Python with the Postgres server and create a new database in Postgres using Python.

## Environments and packages

The Anaconda Python distribution contains a lot of Python packages (a package is a self contained collection of python modules [<span class='file'>.py</span> files] that performs given tasks). When working with Python you can find packages for almost all your needs, either that can be used out of the box, or after some modification. There is of course a package for connecting Python to Postgres server - <span class='package'>psycopg2</span>.

### Psycopg2 as a package in a virtual environments

If you followed the post on [Conda virtual environments](../conda-environ) you should have a virtual environment for Python (e.g. geoimagine001) installed on your local machine. And it should include psycopg2 as this package was added to the list of default packages to install with any new virtual environment.

### Add psycopg2 to a virtual environment

You can install new packages into your environment in the usual way that <span class='terminalapp'>conda</span> packages are installed. Make sure that the environment into which you want to install a package (psycopg2 in this case) is the active environment:

<span class='terminal'>$ conda activate geoimagine001</span>

The terminal prompt should now point at your environment. and you can enter the install command:

<span class='terminal'>(geoimagine001) ... $ conda install -c anaconda psycopg2</span>

or tell <span class='terminalapp'>conda</span> under which environment to install the packages:

<span class='terminal'>(base) ...$ conda install --name geoimagine001 -c anaconda psycopg2</span>

Once the installation is finished you should see the installed packages under the <span class='file'>site-packages</span> path.

### Add psycopg2 to the Anaconda base

If you installed Anaconda and set up <span class='app'>Eclipse</span> as described in the earlier posts, the Python distribution that <span class='app'>Eclipse</span> uses is under:

<span class='file'>'path'/anaconda3/lib/python3.7</span>

where 'path' is the path you choose for installing Anaconda (if you use another python version then 3.7, the version number is different). Your Python path contains a folder called <span class='file'>site-packages</span>. The packages in that folder are available for direct use in the <span class='app'>Eclipse</span> IDE.

In my Anaconda installation, <span class='package'>psycopg2</span> was not installed under <span class='file'>site-packages</span>, but included as an <span class='file'>.egg</span> file - a kind of package installer. To install <span class='package'>psycopg2</span>, with Anaconda set up as described in the [earlier post](../install-anaconda), all you have to do to add <span class='package'>psycopg2</span> to your <span class='file'>site-packages</span> is to execute the <span class='app'>Terminal</span> command:

<span class='terminal'>$ conda install psycopg2</span>

To check if <span class='package'>psycopg2</span> is in place, list the package content in the <span class='app'>Terminal</span>:

<span class='terminal'>$ ls 'path'/anaconda2/lib/python2.7/site-packages/psycopg2</span>

You can also open the folder in <span class='app'>Finder</span>. Copy the full path to the <span class='file'>site-packages</span> folder, and when in <span class='app'>Finder</span>, simultaneously press the keys for 'command(cmd)'+'Shift'+'G', in the <span class='tab'>Go to the Folder</span> that opens, just paste the full path and click <span class='button'>Go</span>.

### Security

If you are only going to use your Postgres database as localhost (on your own machine), security is less important. But if you want to protect your data you must set some level of security. The solution I use is primarily for macOS and UNIX/Linux systems, and is not very advanced. I use a combination of storing my password in my home directory (~) combined with a simple encryption.

Create a file in your home directory (~) called <span class='file'>.netrc</span> that defines your credentials. An [earlier post](https://karttur.github.io/setup-blog/2017/12/21/setup-blog-tools.html#opening-and-understanding-the-terminal) describes how to use the <span class='app'>Terminal</span> for creating and editing files in detail. In the <span class='app'>Terminal</span> go to your home directory:

<span class='terminal'>$ cd ~</span>

Then start the <span class='app'>Terminal</span> text editor <span class='terminalapp'>pico</span> for editing/creating the file:

<span class='terminal'>$ pico .netrc</span>

Enter the two lines below (but with your role/user and password), one for the default user (if you [installed Postgres with Homebrew](../install-postgres/) the default user is the same as your user on the local machine - 'yourUser'), and one for the production user ('prodUser') if you followed my suggestions in the [previous post](../install-postgres/). If you only have the default user, enter the same login and password in both lines.
```
machine localhost0   login yourUser   password yourPassword
machine localhost1   login prodUser   password prodPassword
```
Exit <span class='terminalapp'>pico</span> (ctrl-X) and save the file (click Y when asked to save). You probably have to change the read and write permissions for <span class='file'>.netrc</span>, which you do by executing the following Terminal command:

<span class='terminal'>$ chmod og-rw .netrc</span>

With this solution your credentials will only be explicitly written out in a hidden file.

### Set Postgres connection in Python (Eclipse)

Start <span class='app'>Eclipse</span>, and you should come back to the workbench that you created in a [previous post](../install-eclipse/). Repeat the steps outlined in that post to create a new PyDev project, with a PyDev package and a sub-package.

Create a project (call it what you want):

<span class='menu'>File : New : Project : PyDev project</span>

Create a new PyDev Package and call it 'geoimagine':

<span class='menu'>File : New : PyDev Package</span>

Create a new sub-package and call it 'db_setup':

<span class='menu'>File : New : PyDev Package</span>

In the dialog window you should see the main package ('geoimagine') already filled (<span class='textbox'>geoimagine</span>). Use Python syntax and add a dot (.) followed by the name of the sub-package ('db_setup') (<span class='textbox'>geoimagine.db_setup</span>).

#### PyDev Class module

In the sub-package create a Python module called 'db_setup_class' (set type to Class in the <span class='tab'>Template</span>) window that appears after you click click <span class='button'>Finish</span>:

<span class='menu'>File : New : PyDev Module</span>

In the <span class='module'>db_setup_class.py</span> module enter (or copy and paste) the following code (replace the default Class that was created with the module):

```
import psycopg2
from base64 import b64decode

class PGsession:
    """Connect to postgres server"""   
    def __init__(self, query):
        """Connect to selected database"""
        query['pswd'] = b64decode(query['pswd'])
        conn_string = "host='localhost' dbname='%(db)s' user='%(user)s' password='%(pswd)s'" %query
        self.conn = psycopg2.connect(conn_string)
        self.cursor = self.conn.cursor()
```
The import <span class='package'>psycopg2</span> you installed above, whereas the package <span class='package'>base64</span> is a Python core package. You will use it to send your password in encrypted form. When you call the module class <span class='pydef'>PGsession</span> it expects a variabe called 'query'. This variable should be in the form of a _dictionary_, with pairs of keys and values:
```
query = {key0:value0, key1:value1 ...}
```
You are going to use the query dictionary for sending the user and (encrypted) password to <span class='pydef'>PGsession</span>. But you must first create the Main PyDev module from which you will do that.

### Connect and create a new DB

Create a second PyDev module, called <span class='module'>db_setup_main</span> in the <span class='package'>db_setup</span> package. Below are two versions, but the second version is just in case your system can not handle the default <span class='file'>.netrc</span> file using the <span class='package'>netrc</span> package (the second version instead includes an explicit file reader, but you should not need to worry about that).

```
from db_setup_class import PGsession
from base64 import b64encode
import netrc

def SetUpProdDb(prodDB):
    '''
    Create production database(db)
    '''
    HOST = 'localhost1'
    secrets = netrc.netrc()
    username, account, password = secrets.authenticators( HOST )
    #encrypt the password
    pswd = b64encode(password)
    #create a query dictionary for connecting to the Postgres server
    query = {'db':'postgres','user':username,'pswd':pswd}
    #Connect to the Postgres Server
    iniSession = PGsession(query)
    #Set the name of your production database(db)
    prodDbD = {'dbname':prodDB}
    #Select the current (cluster) db
    iniSession.cursor.execute("SELECT current_database()")
    #Get the results from the SELECT statement
    record = iniSession.cursor.fetchone()
    #Print Current (cluster) db
    print 'Current database',record[0]
    #Select the logged in user
    iniSession.cursor.execute("SELECT user")
    #Get the results from the SELECT statement
    record = iniSession.cursor.fetchone()
    #Print Current user
    print 'User',record[0]
    #Select all databases in the cluster db
    iniSession.cursor.execute("SELECT datname FROM pg_database;")
    #Get the results from the SELECT statement
    records = iniSession.cursor.fetchall()
    #Convert the retrieved records to a simple list
    dbL = [item[0] for item in records]
    #Print the list of all databases in the cluster
    print 'Databases', dbL
    #Check if your required production db exists
    if not prodDbD['dbname'] in dbL:
        #Your production db does not exists
        printstr = 'Creating database: %s' %( prodDbD['dbname'])
        print printstr
        #Import the psycopg extension that allows you to create a new db
        from psycopg2.extensions import ISOLATION_LEVEL_AUTOCOMMIT
        #Invoke the connection with the extension
        iniSession.conn.set_isolation_level(ISOLATION_LEVEL_AUTOCOMMIT)
        #Create your production db
        iniSession.cursor.execute("CREATE DATABASE %(dbname)s;" %prodDbD)

if __name__ == "__main__":
    prodDB = 'YourProdDB'
    SetUpProdDb(prodDB)
```
The alternative version (with an explicit reader for the <span class='file'>.netrc</span>) is only a backup if the above version does not work properly. Click the button to see the code.

<button id= "mainclassbtn" onclick="hiddencode('inimain2')">Show/Hide alternative code</button>
<div id="inimain2" style="display:none">
{% capture text-capture %}
from db_setup_class import PGsession
from base64 import b64encode
from os import path


def SetUpProdDb(prodDB):
    '''
    Create production database(db)
    '''
    HOST = 'localhost1'
    home = path.expanduser("~")
    netrcFPN =path.join(home,'.netrc')
    with open(netrcFPN) as f:
        lines = f.readlines()
        for line in lines:
            if HOST in line:
                username, password = line.split()[3], line.split()[5]
    #encrypt the password
    pswd = b64encode(password)
    #create a query dictionary for connecting to the Postgres server
    query = {'db':'postgres','user':username,'pswd':pswd}
    #Connect to the Postgres Server
    iniSession = PGsession(query)
    #Set the name of your production database(db)
    prodDbD = {'dbname':prodDB}
    #Select the current (cluster) db
    iniSession.cursor.execute("SELECT current_database()")
    #Get the results from the SELECT statement
    record = iniSession.cursor.fetchone()
    #Print Current (cluster) db
    print 'Current database',record[0]
    #Select the logged in user
    iniSession.cursor.execute("SELECT user")
    #Get the results from the SELECT statement
    record = iniSession.cursor.fetchone()
    #Print Current user
    print 'User',record[0]
    #Select all databases in the cluster db
    iniSession.cursor.execute("SELECT datname FROM pg_database;")
    #Get the results from the SELECT statement
    records = iniSession.cursor.fetchall()
    #Convert the retrieved records to a simple list
    dbL = [item[0] for item in records]
    #Print the list of all databases in the cluster
    print 'Databases', dbL
    #Check if your required production db exists
    if not prodDbD['dbname'] in dbL:
        #Your production db does not exists
        printstr = 'Creating database: %s' %( prodDbD['dbname'])
        print printstr
        #Import the psycopg extension that allows you to create a new db
        from psycopg2.extensions import ISOLATION_LEVEL_AUTOCOMMIT
        #Invoke the connection with the extension
        iniSession.conn.set_isolation_level(ISOLATION_LEVEL_AUTOCOMMIT)
        #Create your production db
        iniSession.cursor.execute("CREATE DATABASE %(dbname)s;" %prodDbD)

if \_\_name\_\_ == "\_\_main\_\_":
    prodDB = 'YourProdDB'
    SetUpProdDb(prodDB)
{% endcapture %}

{% include widgets/toggle-code.html  toggle-text=text-capture  %}
</div>

### Run your package

With the main module (<span class='module'>db_setup_main.py</span>) open in the <span class='app'>Eclipse</span> main pane, kick off your code from the <span class='app'>Eclipse</span> menu:

<span class='menu'> Run : Run</span>

If everything worked out, the <span class='tab'>Console</span> pane should return:
```
Current database postgres
User YourUser
Databases ['postgres', 'template1', 'template0']
Creating database: 'prodDB'
```

If you run it again, the database 'prodDB' will be listed in 'Databases []' in the <span class='tab'>Console</span>, and not be created again.

You can look at the details for 'prodDB' in <span class ='app'>pgAdmin</span> (installing and setting up pgAdmin is described in [this post](#)). Connect <span class ='app'>pgAdmin</span> to your Postgres server and expand the <span class ='tab'>Browser</span> menu by clicking on the small '+' signs:

<span class='menu'>Servers : postgres : Databases : 'prodDB'</span>

If you click 'prodDB' (you might also need to click the tab <span class = 'tab'>SQL</span> in the right pane of pgAdmin) you will see how 'prodDB' was defined:

```
CREATE DATABASE 'prodDB'
    WITH
    OWNER = 'youruser'
    ENCODING = 'UTF8'
    LC_COLLATE = 'en_US.UTF-8'
    LC_CTYPE = 'en_US.UTF-8'
    TABLESPACE = pg_default
    CONNECTION LIMIT = -1;
```

Encoding, collation, and Ctype are set to UTF8 (the most universal set of characters), and tablespace is the to pg_default. The '-1' for connection limit means unlimited. You can set all this parameters in the [SQL for CREATEDB](https://www.postgresql.org/docs/10/static/sql-createdatabase.html) if you want, but I prefer the most universal, which is also the postgres default.

## Resources

[PostgreSQL](https://www.postgresql.org/docs/10)

[psycopg2](http://initd.org/psycopg/docs/)
