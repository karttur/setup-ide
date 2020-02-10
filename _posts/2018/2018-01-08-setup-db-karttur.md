---
layout: article
title: Postgres setup with Python & xml
categories: blog
previousurl: blog/connect-with-psycopg2
nexturl: null
excerpt: XML governed setup of Postgres schemas and tables with Python and postgreSQL
tags:
  - PostgreSQL
  - Postgres
  - Python package
  - Eclipse
  - psycopg2
image: ols-sl-trmm-3b43v7-precip_3B43_trmm_2001-2016_A
date: '2018-01-08 19:38'
modified: 2020-01-30
comments: true
share: true
---
<script src="../../assets/js/karttur/togglediv.js"></script>
**Contents**
	\- [Introduction](#introduction)
	\- [Prerequisites](#prerequisites)
	\- [Python package for setting up schemas and tables](#python-code-fo-setting-up-schemas-and-tables)
		\- [Main module](#main-module)
			\- [xml files defining the schemas and tables](#xml-files-defining-the-schemas-and-tables)
		\- [Class module (db_setup_class.py)](#class-module-dbsetupclasspy)
			\- [Functions in db_setup_class.py](#functions-in-dbsetupclasspy)
				\- [ReadSqlXml](#ReadSqlXml)
				\- [BoolTag](#booltag)
				\- [CreateSchema](#createschema)
				\- [GetTableKeys](#gettablekeys)
				\- [CreateTable](#createtable)
				\- [TableInsert](#tableinsert)
				\- [TableUpdate](#tableupdate)
	\- [general_processes](#generalprocesses)
	\- [Using the setup-db-karttur package](#using-the-setup-db-karttur-package)

## Introduction

This post describes the automatic setup of schemas and tables for a PostgreSQL (or Postgres for short) database (db) from Python. Since it was published (in January 2018) it has become redundant if you clone or download Karttur´s GeoImagine Framework from the [GitHub.com](https://github.com/karttur/kt-gi-test01/).

However, the Python processes described are generic, and available as a Python package (<span class='package'>db_setup</span>) on [GitHub.com](https://github.com/karttur/geoimagine-setup_db/). Schemas and tables are defined in xml (eXtensible Markup Language) files, and by editing the xml contents, any schema and table architecture can be setup. The included xml files are for setting up Karttur's Geo Imagine processing framework.

The intention of this blog is that you can either use it for setting up your own architecture of schemas and tables, or just use it for setting up Karttur's Geo Imagine processing framework. In the former case, you must create your own xml files defining the architecture, in the latter case you can use the xml files on [GitHub.com](https://github.com/karttur/geoimagine-setup_db/tree/master/doc) as they are.

You can also choose to either follow the instructions to understand how the Python package <span class='package'>db_setup</span> works, or download the package from [GitHub.com](https://github.com/karttur/geoimagine-setup_db/). The package is, however, custom built for the Python and PostgreSQL setup as stated in the next section (Prerequisites).

## Prerequisites

To follow this post, and to run the Python package <span class='package'>db_setup</span>, you must have Python and Postgres server running on your machine. You must have a <span class='file'>.netrc</span> file in your home directory with your Postgres login credentials, and your Postgres server must be in the port 5432 with a database cluster called 'postgres'.
The instructions for installations and setup of the framework used in this post include:

* [Anaconda](/ide-setup/install-anaconda/) (Python Interpreter)
* [Eclipse](ide-setup/install-eclipse/) (Integrated Development Environment - IDE)
* [PostgreSQL](ide-setup/install-postgres/) (database server)
* [psycopg2](ide-setup/install-postgres/) (connection between Python and Postgres)

## Python package for setting up schemas and tables

If you want to follow this step by step instruction, start <span class='app'>Eclipse</span>.

### Main module (db_setup_main.py)

If you followed my [earlier post](../connect-with-psycopg2/index.html) and created the package <span class='package'>db_setup_karttur</span>, you must now add a new function <span class='dydef'>SetupSchemasTables</span> to the package main module <span class='package'>db_setup_main.py</span>, and then call it from <span class='pydef'>\_\_main\_\_</span>:

```
def SetupSchemasTables(projFPN,db,verbose):
    '''
    Setup schemas and tables
    '''
    #Read the textfile with links to the xml files defining schemas and tables
    dirPath = path.split(projFPN)[0]
    with open(projFPN) as f:
        xmlL = f.readlines()
    # Remove whitespace characters like `\n` at the end of each line
    xmlL = [path.join(dirPath,x.strip())  for x in xmlL if len(x) > 10 and x[0] != '#']
    # Define the database connection
    HOST = 'localhost1'
    secrets = netrc.netrc()
    username, account, password = secrets.authenticators( HOST )
    #encrypt the password
    pswd = b64encode(password)
    #create a query dictionary for connecting to the Postgres server
    query = {'db':db,'user':username,'pswd':pswd}
    #Connect to the Postgres Server
    session = PGsession(query)
    #Loop over all xml files and create Schemas and Tables
    for xml in xmlL:
        session.ReadSqlXml(xml,verbose)

if __name__ == '__main__':
    '''
    This module should only be run at the very startup of building the Karttur Geo Imagine framework.
    To run, remove the comment "#prodDB" and set the name of your production DB ("YourProdDB")
    '''
    #prodDB = 'YourProdDB'

    '''SetUpProdDb creates an empty Postgres database under the cluster postgres with the name "YourProdDB".'''
    SetUpProdDb(prodDB)
    '''
    SetupSchemasTables creates schemas and tables from xml files, with the relative path to the
    xml files given in the plain text file "projFPN".
    '''
    verbose = True
    projFPN = 'doc/db_karttur_setup_20180106_0.txt'
    SetupSchemasTables(projFPN,prodDB,verbose)
```

The function <span class='pydef'>SetupSchemasTables</span> expects 3 variables:
* projFPN - the path to a textfile that lists the xml files defining the schemas and tables to create
* db - the name of the database in the postgres cluster for which to create the schemas and tables
* verbose -  a boolean (True/False) variable defining if progress is to be shown at runtime or not

#### xml files defining the schemas and tables

In the [GitHub.com](https://github.com/karttur/geoimagine-setup_db/) repository the 'projFPN' is in a folder <span class='file'>doc</span> under the <span class='package'>setup_db</span> package itself. You can also create that folder from <span class='app'>Eclipse</span>. Make sure that the package <span class='package'>db_setup_karttur</span> is selected, and go via the menu:

<span class='menu'>File : New : Folder</span>

The text file linking to the xml files that defines the schemas and tables to create should be in the sub-folder <span class='file'>doc</span>. In the GitHub repository the text file is called <span class='file'>db_karttur_setup_YYYYMMDD_0.txt</span>, where YYYYMMDD is the date of the version ('20180106' when I write this post). The content of <span class='file'>db_karttur_setup_YYYYMMDD_0.txt</span> consists of comments and (relative) file paths:

```
# general_schema_vXX_sql.xml installs the default database schemas
xmlsql/general_schema_v80_sql.xml

# general_processes_vXX_sql.xml installs the tables for handling paths and processes and
# the core process handling all other process definitions,
xmlsql/general_processes_v80_sql.xml
```

The <span class='pydef'>SetupSchemasTables</span> function ignores rows with no text and rows starting with '#'. If you look at the paths to the xml files, they all start with <span class='file'>xmlsql/</span>. The function <span class='pydef'>SetupSchemasTables</span> interpretes this as a relative path, and thus expects the xml files to be in a sub-folder under the <span class='file'>doc</span> folder. If you cloned or downloaded the [setup_db repo on GitHub](https://github.com/karttur/geoimagine-setup_db/) the complete file and folder structure are already in place. If you want to copy and paste into your own structure, create a sub-folder <span class='file'>xmlsql</span> under the folder <span class='file'>doc</span>.

The first xml file linked in <span class='file'>db_karttur_setup_YYYYMMDD_0.txt</span> is <span class='file'>general_schema_v80_sql.xml</span> ([link to file on GitHub.com](https://github.com/karttur/geoimagine-setup_db/tree/master/doc)), and defines the schema to be created. It **must** be the first xml file in the list.

```
<?xml version='1.0' encoding='utf-8'?>
<sql>
	<!-- SQL xml read by geomimagine subpackage db_setup -->
	<process processid ='createschema'>
		<overwrite>N</overwrite>
		<delete>N</delete>
		<parameters db = 'karttur' schema = 'process'></parameters>
	</process>

  <process processid ='createschema'>
  ...
  </process>
  ...

  ...
</sql>
```

The key node for all Geo Imagine processes is \<process\>; the childtags \<overwrite\> and \<delete\> are universal for all processes, The childnode \<parameters\> contains process specific parameters. The xml for the \<process\> 'createschema' is simpler compared to almost all other processes, but more about that later.

### Class module (db_setup_class.py)

The final loop in the main module
```
    for xml in xmlL:
        session.ReadSqlXml(xml)
```
points to the function <span class ='pydef'>ReadSqlXml</span>. The version of <span class='package'>db_setup_class.py</span> that was created in the [previous post](../connect-with-psycopg2/index.html) does not contain that function, nor any of the other functions that are needed for creating schemas and tables.

You can download the complete version of <span class='file'>db_setup_class.py</span> from GitHub, or you can construct it by following the instructions below. The latter will help you understand a bit more about how Python function, but the end result will be the same as the module available on GitHub.

#### Functions in db_setup_class.py

The module <span class='package'>db_setup_class.py</span> can perform a range of different database tasks:

* Create schema
* Create table
* Delete table
* Replace table
* Insert table records
* Delete table records
* Replace table records
* Update table records

To achieve this, <span class='package'>db_setup_class.py</span> contains the object oriented Class <span class='package'>PGSession</span>. When calling the Class <span class='pydef'>PGSession</span>, the \_\_init\_\_ function expects a _dictionary_ with your login credentials and the name of the db to work with . <span class='pydef'>PGSession</span> responds by returning an _instance_ of itself (\_\_self\_\_). When you (later) call the <span class='package'>PGSession</span>, the functions defined in <span class='pydef'>PGSession</span> are available to the returned instance of the Class. In the main module this is utilized in the final loop, that calls the <span class='pydef'>PGSession</span> function <span class='pydef'>ReadSqlXml</span> and when doing so sends a single variable 'xmlfile' (the name of an <span class='file'>.xml</span> file). The function <span class='pydef'>ReadSqlXml</span> then reads and parses (disentangle) the xml code, and subsquently calls other functions for managing the Postgres db as defined in the xml code. As <span class='pydef'>PGSessions</span> was initiated with the login credentials for your user, the Postgres db connection is available to the returned instance also in each function defined under the <span class='pydef'>PGSession</span> Class.

The initiation (\_\_init\_\_) is the same as in the [post where you created <span class='pydef'>PGSession</span>](../connect-with-psycopg2/index.html).
```
class PGsession:
    """Connects the postgres database for processes."""   
    def __init__(self, query):
        query['pswd'] = b64decode(query['pswd'])
        conn_string = "host='localhost' dbname='%(db)s' user='%(user)s' password='%(pswd)s'" %query
        self.conn = psycopg2.connect(conn_string)
        self.cursor = self.conn.cursor()
```

But the complete <span class='pydef'>PGSession</span> Class in the module <span class='package'>db_setup_class.py</span> also contains these additional functions.

* <span class ='pydef'>ReadSqlXml</span>
* <span class ='pydef'>\_BoolTag</span>
* <span class ='pydef'>\_CreateSchema</span>
* <span class ='pydef'>\_GetTableKeys</span>
* <span class ='pydef'>\_CreateTable</span>
* <span class ='pydef'>\_TableInsert</span>
* <span class ='pydef'>\_TableUpdate</span>

All the functions, except <span class='pydef'>ReadSqlXml</span> starts with underscore (\_). This indicates that the function is _private_ and can only be accessed from within the Class <span class='pydef'>PGSession</span> itself, they can not be called from any other function.

##### ReadSqlXml

<span class ='pydef'>ReadSqlXml</span> reads and parses the XML file, and then calls the other (private) class functions dependent on the process(es) defined in the xml file it operates on.

<button id= "ReadSqlXmlbtn" onclick="hiddencode('ReadSqlXml')">Show/Hide code for <span class='pydef'>ReadSqlXml</span> </button>
<div id="ReadSqlXml" style="display:none">

{% capture text-capture %}
def ReadSqlXml(self,xmlFPN,verbose):
        ''' Read XML file '''
        self.verbose = verbose
        dom = minidom.parse(xmlFPN)  
        if self.verbose:
            print '    xmlfile', path.split(xmlFPN)[1]     
        processes = dom.getElementsByTagName('process')

        for process in processes:
            overwriteTag = process.getElementsByTagName('overwrite')
            if len(overwriteTag) == 0:
                self.overwrite = False
            else:
                self.overwrite = self.\_BoolTag(overwriteTag[0].firstChild.nodeValue )
            deleteTag = process.getElementsByTagName('delete')
            if len(deleteTag) == 0:
                self.delete = False
            else:
                self.delete = self.\_BoolTag(deleteTag[0].firstChild.nodeValue )
            processid = process.getAttribute('processid')
            #Get the parameters
            paramtag = process.getElementsByTagName('parameters')
            schema = paramtag[0].getAttribute('schema')

            if processid in ['createtable','tableinsert','tableupdate']:
                table = paramtag[0].getAttribute('table')
                if self.verbose:
                    print '        %s %s %s' %(processid, schema, table)
                command = process.getElementsByTagName('command')
                if not process in ['tableinsert','createschema']:
                    cmd = command[0].firstChild.nodeValue
                if processid in ['tableinsert','tableupdate']:
                    valueL = []
                    columns = command[0].getElementsByTagName('columns')
                    columnL = columns[0].firstChild.nodeValue.split(',')
                    values = command[0].getElementsByTagName('values')
                    for value in values:
                        valueL.append(value.firstChild.nodeValue.split(','))   
            if processid == 'createschema':
                self.\_CreateSchema(schema)   
            elif processid == 'createtable':
                self.\_CreateTable(schema,table,cmd)
            elif processid == 'tableinsert':
                self.\_TableInsert(schema,table,columnL,valueL)
            elif processid == 'tableupdate':
                self.\_TableUpdate(schema,table,columnL,valueL,cmd)
            else:
                exitstr =  'Initial command not found in initiate.initialize', processid
                exit(exitstr)
{% endcapture %}

{% include widgets/toggle-code2.html  toggle-text=text-capture  %}
</div>

##### \_BoolTag

<span class ='pydef'>\_BoolTag</span> converts the tags for \<overwrite\> and \<delete\> (and other parameters defining boolean variable) to either True or False.

<button id= "BoolTaglbtn" onclick="hiddencode('BoolTag')">Show/Hide code for <span class='pydef'>\_BoolTag</span> </button>
<div id="BoolTag" style="display:none">

{% capture text-capture %}
    def \_BoolTag(self,item):
        ''' Convert boolean variables from xml to python boolean (True or False)'''
        if item == '':
            return False #i.e. no item given, assume False
        if item[0].lower() == 'n' or item.lower() == 'false':
            return False
        elif item[0].lower() == 'y' or item.lower() == 'true':
            return True
        else:
            exitstr = 'Can not resolve boolean node %(s)s' %{'s':item}
            sys.exit(exitstr)
{% endcapture %}

{% include widgets/toggle-code2.html  toggle-text=text-capture  %}
</div>

##### \_CreateSchema

<span class ='pydef'>\_CreateSchema</span> creates the schema defined in the xml file. For security reasons I did not include the capacity to drop a schema in then module. If you want to drop a schema you have to do that manually.

<button id= "CreateSchemaBtn" onclick="hiddencode('CreateSchema')">Show/Hide code for <span class='pydef'>\_CreateSchema</span> </button>
<div id="CreateSchema" style="display:none">

{% capture text-capture %}
    def \_CreateSchema(self,schema):
       ''' Create schema if it does not exist '''
       query = {'schema':schema}
       self.cursor.execute("SELECT schema_name FROM information_schema.schemata WHERE schema_name = '%(schema)s';"%query)
       record = self.cursor.fetchone()
       if record == None:
           self.cursor.execute("CREATE SCHEMA %(schema)s;"  %query)
           self.conn.commit()
       self.conn.commit()
{% endcapture %}

{% include widgets/toggle-code2.html  toggle-text=text-capture  %}
</div>

##### \_GetTableKeys

<span class ='pydef'>\_GetTableKeys</span> is a support function that returns the primary keys (constraints) for a table - that is the keys that in combination must constitute a unique value.

<button id= "GetTableKeysBtn" onclick="hiddencode('CreateSchema')">Show/Hide code for <span class='pydef'>\_GetTableKeys</span> </button>
<div id="GetTableKeys" style="display:none">

{% capture text-capture %}
    def \_GetTableKeys(self,table):
        ''' Get table keys '''
        query = "SELECT \
                c.column_name, c.data_type \
                FROM \
                information_schema.table_constraints tc \
                JOIN information_schema.constraint_column_usage AS ccu USING (constraint_schema, constraint_name) \
                JOIN information_schema.columns AS c ON c.table_schema = tc.constraint_schema AND tc.table_name = c.table_name AND ccu.column_name = c.column_name \
                where constraint_type = 'PRIMARY KEY' and tc.table_name = '%s';" %(table)
        self.cursor.execute(query)
        records = self.cursor.fetchall()
        return records
{% endcapture %}

{% include widgets/toggle-code2.html  toggle-text=text-capture  %}
</div>

##### \_CreateTable

<span class ='pydef'>\_CreateTable</span> creates a new table if \<overwrite\> and \<delete\> are False (default), deletes a table if \<delete\> is set to True, and deletes and then recreates a table if \<overwrite\> is set to True. Both \<delete\> and \<overwrite\> are universal parameters for all processes in the Geo Imagine framework. If they are omitted from the xml they are by default set to False.

<button id= "CreateTableBtn" onclick="hiddencode('CreateTable')">Show/Hide code for <span class='pydef'>\_CreateTable</span> </button>
<div id="CreateTable" style="display:none">

{% capture text-capture %}
    def \_CreateTable(self,schema,table,command):  
        ''' Create, overwrite or drop table '''      
        query = {'schema':schema,'table':table,'cmd':command}
        self.cursor.execute("SELECT EXISTS (SELECT 1 FROM information_schema.tables WHERE table_schema = '%(schema)s' AND table_name = '%(table)s');"%query)
        record = self.cursor.fetchone()
        self.cursor.execute("SET search_path TO " + schema)
        if not record[0] and not self.delete:
            self.cursor.execute("CREATE TABLE %(table)s ( %(cmd)s );" %query)
            self.conn.commit()
        elif record[0] and (self.delete or self.overwrite):
            self.cursor.execute("DROP TABLE %(table)s;" %query)
            if self.overwrite:
                self.cursor.execute("CREATE TABLE %(table)s ( %(cmd)s );" %query)
                self.conn.commit()
{% endcapture %}

{% include widgets/toggle-code2.html  toggle-text=text-capture  %}
</div>

##### \_TableInsert

<span class ='pydef'>\_TableInsert</span> inserts, deletes or overwrites data to a table using the same logic as in the function <span class='pydef'>\_CreateTable</span>.

<button id= "TableInsertBtn" onclick="hiddencode('TableInsert')">Show/Hide code for <span class='pydef'>\_TableInsert</span> </button>
<div id="TableInsert" style="display:none">

{% capture text-capture %}
    def \_TableInsert(self,schema,table,columnL,valueL):
        ''' Insert, replace of delete records in schema table '''
        query = {'schema':schema,'table':table}
        self.cursor.execute("SELECT EXISTS (SELECT 1 FROM information_schema.tables WHERE table_schema = '%(schema)s' AND table_name = '%(table)s');"%query)
        record = self.cursor.fetchone()
        if not record[0]:
            exitstr = 'Schema %s Table %s does not exist' %(schema,table)
            sys.exit(exitstr)
        self.cursor.execute("SET search_path TO " + schema)

        tablekeys = self.\_GetTableKeys(table)
        keycols = []
        for item in tablekeys:
            keycols.append(str(item[0].lower()))    
        for values in valueL:
            askqueryL = []
            for x, item in enumerate(columnL):
                for key in keycols:
                    if str(item.lower().strip()) == str(key.lower().strip()):
                        askqueryL.append('%s = %s' %(item, values[x]))
            whereSql = " AND ".join(askqueryL)  
            query = {'table':table, 'where': whereSql}
            self.cursor.execute("SELECT * FROM %(table)s WHERE %(where)s;" %query)    
            record = self.cursor.fetchone()
            if record == None:
                queryinsert = {'table':table, 'cols': ",".join(columnL), 'vals':",".join(values)}
                self.cursor.execute("INSERT INTO %(table)s (%(cols)s) VALUES (%(vals)s);" %queryinsert)
                self.conn.commit()
            elif self.overwrite:
                self.cursor.execute("DELETE FROM %(table)s WHERE %(where)s;" %query)
                self.conn.commit()
                queryinsert = {'table':table, 'cols': ",".join(columnL), 'vals':",".join(values)}
                self.cursor.execute("INSERT INTO %(table)s (%(cols)s) VALUES (%(vals)s);" %queryinsert)
                self.conn.commit()
            elif self.delete:
                self.cursor.execute("DELETE FROM %(table)s WHERE %(where)s;" %query)
                self.conn.commit()
{% endcapture %}

{% include widgets/toggle-code2.html  toggle-text=text-capture  %}
</div>

##### \_TableUpdate

<span class ='pydef'>\_TableUpdate</span> updates existing records in a table.

<button id= "TableUpdateBtn" onclick="hiddencode('TableUpdate')">Show/Hide code for <span class='pydef'>\_TableUpdate</span> </button>
<div id="TableUpdate" style="display:none">

{% capture text-capture %}
    def \_TableUpdate(self,schema,table,columnL,valueL,cmd):
        ''' Update records in schema table '''
        query = {'schema':schema,'table':table}
        self.cursor.execute("SELECT EXISTS (SELECT 1 FROM information_schema.tables WHERE table_schema = '%(schema)s' AND table_name = '%(table)s');"%query)
        record = self.cursor.fetchone()
        if not record[0]:
            print 'Warning: Can not update; Schema %s Table %s does not exist' %(schema,table)
            return
        self.cursor.execute("SET search_path TO " + schema)
        for values in valueL:
            queryL = []
            for x, item in enumerate(columnL):
                queryL.append('%s = %s' %(item, values[x]))
            query = {'table':table, 'where': cmd}

            self.cursor.execute("SELECT * FROM %(table)s %(where)s;" %query)    
            record = self.cursor.fetchone()
            if record == None:
                errorstr = 'Can not update non-existing record'
                sys.exit(errorstr)
            query = {'table':table, 'cols': ",".join(columnL), 'vals':",".join(values), 'where':cmd}
            self.cursor.execute("UPDATE %(table)s SET (%(cols)s) = (%(vals)s) %(where)s;" %query)
            self.conn.commit()
{% endcapture %}

{% include widgets/toggle-code2.html  toggle-text=text-capture  %}
</div>

##### \_Close

def _Close(self):
        self.cursor.close()
        self.conn.close()

## General processes

The excerpt of <span class="file">general_processes_v80_sql.xml</span> shows the general structure of the xml files that are used for creating tables. Incidentally it is also the xml file that defines the process for later adding all other processes if you intend to setup Karttur's Geo Imagine processing framework.

```
<process processid ='createtable'>
		<parameters db = 'karttur' schema = 'process' table = 'rootprocesses'></parameters>
		<overwrite>N</overwrite>
		<delete>N</delete>
		<command>
			rootprocid TEXT,
			roottitle varchar(128),
			rootlabel varchar(255),
			creator varchar(32),
			PRIMARY KEY (rootprocid)
		</command>
	</process>

	<process processid ='createtable'>
		<parameters db = 'karttur' schema = 'process' table = 'subprocesses'></parameters>
		<overwrite>N</overwrite>
		<delete>N</delete>
		<command>
			rootprocid TEXT,
			subprocid TEXT,
			version varchar(16),
			minuserstratum integer,
			subtitle varchar(128),
			sublabel varchar(255),
			creator varchar(32),
			access varchar(8),
			createdate date,
			PRIMARY KEY (subprocid,version)
		</command>
	</process>
```

Both \<process\> nodes in the excerpt above contains the same tags and attributes. Only the attribute table (in the \<parameters\> tag) and the tag \<command\> text differ. The text in the \<command\> tag is an SQL statement, except that the SQL must also include the schema and table for which to execute the command. When converted to SQL by <span class='pydef'>PGsession</span> the SQL sent to Postgres for the top \<process\> tag will become:

```
CREATE TABLE process.rootprocesses (
  rootprocid TEXT,
  roottitle varchar(128),
  rootlabel varchar(255),
  creator varchar(32),
  PRIMARY KEY (rootprocid)
);
```

But only after <span class='pydef'>PGsession</span> made sure that table did not already exist. Further down in the xml file <span class='file'>general_processes_v80_sql.xml</span> the 'rootprocess' and 'subprocess' required to create all other processes is inserted.

```
<process processid ='tableinsert'>
		<parameters db = 'karttur' schema = 'process' table = 'rootprocesses'></parameters>
		<command>
			<columns>rootprocid,  roottitle,  rootlabel,  creator</columns>
			<values>'manageprocess', 'Manage database defined process', 'Mangaging a processes requires data on all parameters and their type and default values','thomasg'</values>
		</command>
	</process>
```

## Using the db_setup package

The package <span class='package'>setup-db</span> is rather a sub-package to Karttur's Geo Imagine processing framework, but it can be used for setting up any database schema and table architecture. I thus put it in a separate Github repository and made it publicly availabel. If you want to use the package for setting up your own db architecture you need to define your schemas and tables in your own xml files, and link them via a text file. If you want to setup Karttur's Geo Imagine processing framework, all you have to do is run the package.
