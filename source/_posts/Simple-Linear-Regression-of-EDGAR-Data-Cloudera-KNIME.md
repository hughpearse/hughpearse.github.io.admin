title: Simple Linear Regression of EDGAR Data (Cloudera & KNIME)
author: Hugh Pearse
tags: []
categories: []
date: 2018-06-24 11:50:00
---
## Equation of a Line
A line can be used to extrapolate predictions into the future. If you know two points that a line passes through you can define the equation of a line. The equation of a line is defined as

![line equation](/images/pasted-30.png)
- y is the dependent variable or result
- m is the slope of the line
- x is the independent variable or control
- c is the intercept where the line crosses the y axis

## Linear Regression
Sometimes a data set will have more than 2 points, and the points may be roughly arranged in a visible direction but not precisely. In cases like this a simple line equation will not work. For example when provided with a scatterplot of data, there is no specific slope or intercept and it must be estimated.

![scatterplot](/images/pasted-31.png)

When there are several points in a dataset a modified version of the standard line equation can be used, where the slope and intercept are estimated using a calculation. The equation for simple linear regression is as follows:

![linear regression equation](/images/pasted-32.png)
- y is the dependent variable or predicted result
- β is the estimated slope of the line
- x is the independent variable or control
- α is the estimated intercept where the line crosses the y axis

The value for the slope can be estimated using the following equation:

![slope equation](/images/pasted-33.png)
- x̄ is the mean of x
- ȳ is the mean of y
- i is the instance of data
- x_i is the independent variable for a given instance of data
- y_i is the dependent variable for a given instance of data

The value for the intercept can be estimated using the following equation:

![intercept equation](/images/pasted-34.png)

## Performance Considerations
Performing a simple linear regression on data stored in a CSV file will usually involve typical operations such as reading the file from disk, copying it across the network, storing it temporarily in an object in memory, performing a few calculations and displaying a result. The slowest parts of this task are usually reading the file from the hard drive and transferring it across the network.

## Database functions and procedures
Most database management systems come with some primitive functions for performing common mathematical operations such as max(), average() and round(). These functions allow us to perform operations on data inside the database without having to export it first to the filesystem or across the network. Performing the mathematical operations using database functions can save a significant amount of time. Some databases [such as DB2][1] have functions already built in to perform operations such as simple linear regression.

It is possible to [calculate Linear Regression Coefficients in SQL][0] by using window functions:
``` sql
select slope, 
y_bar - x_bar * slope as intercept
from(
    select sum((x - x_bar) * (y - y_bar)) / sum((x - x_bar) * (x - x_bar)) as slope,
    max(x_bar) as x_bar,
    max(y_bar) as y_bar
    from(
            select
            x as x, avg(x) over () as x_bar,
            y as y, avg(y) over () as y_bar
            from(
                    select value as y, ddate as x from sub
                    right join num on sub.adsh = num.adsh
                    right join pre on num.adsh = pre.adsh
                    and num.tag = pre.tag and num.version = pre.version
                    where cik=1081316 and coreg=""
                    and stmt="BS" and num.tag="Assets" and form = "10-K"
            ) dataset
    ) s
) i;
```

![linear regression impala sql](/images/pasted-35.png)


## Impala functions and procedures
Apache Impala is an open source parallel processing SQL query engine for data stored in a computer cluster running Apache Hadoop. Stored procedures as database objects are not supported by Impala yet. [Impala does however support][2] User Defined Functions (UDFs) written in C++, and Hive UDFs written in Java.

## Apache MADlib
[Apache MADlib][4] is an open-source library for scalable in-database analytics. It provides data-parallel implementations of mathematical, statistical and machine learning methods for structured and unstructured data. MADlib operates on the data locally in-database and does not move data between multiple runtime environments unnecessarily.

## MADlib Port
[MADlib Port][3] brings in-DBMS data analytics to Impala. MADlib Port is a fork of a project called Apache MADlib which is an open source library for scalable in-database analytics. It provides data-parallel implementations of machine learning, mathematical, statistical, and graph methods on the PostgreSQL family of databases. The port of Apache MADlib to Impala is for MADlib v1.0 and requires boost v1.54 and is not being actively maintained.

## Knime
KNIME is a free and open-source data analytics, reporting and integration platform. KNIME integrates various components for machine learning and data mining through its modular data pipelining concept. [The KNIME Big Data Connectors][5] extension allow easy access to Apache Hadoop data from within KNIME Analytics Platform and KNIME Server. This extension offers a set of KNIME nodes for accessing Hadoop/HDFS via Hive or Impala and ships with all required libraries.

## Installing KNIME on Cloudera
To install and configure KNIME on Cloudera Quickstart VM run the following commands in bash:
``` bash
$ sudo yum install webkitgtk
$ cd ~/Downloads
$ wget https://download.knime.org/analytics-platform/linux/knime_3.5.3.linux.gtk.x86_64.tar.gz
$ tar -xzvf knime_3.5.3.linux.gtk.x86_64.tar.gz
$ cd *knime*
$ ./knime
```
Then install the Impala KNIME database connector extension: Press File -> Install KNIME Extensions -> Search "Big Data Connectors" -> Tick Checkbox -> Next -> Next -> Select "I accept the terms of the license agreements" -> Finish -> Restart Knime

## Perform a Basic Impala Query
Create a new workflow: Press File -> New -> New KNIME Workflow -> Next -> Finish.

To perform a simple SQL query to Impala drag an "Impala Connector", a "Database Reader" and an "Interactive Table" on to the user interface.

![KNIME Impala SQL Query setup](/images/pasted-36.png)

Configure the Impala Connector: Under the "Node Repository" view drill down to -> Database -> Connector -> Impala Connector -> Drag it on to the workflow -> Right click -> Configure.

Use the following settings:
- Hostname: 127.0.0.1
- Database Name: default
- Use username and password
- username:impala
- password: <EMPTY\>

![KNIME Database Connection](/images/pasted-37.png)

Edit the database reader node and enter an SQL query:
``` sql
select * from tag
```

![SQL Query Example](/images/pasted-38.png)

Execute the workflow by pressing the green arrow on top.

![Execute workflow](/images/pasted-39.png)

View the results stored in the interactive table node by right clicking on it and pressing "View Table View".

![View table view](/images/pasted-40.png)

The SQL query results should be displayed in a table.

![SQL Table Results](/images/pasted-41.png)

## KNIME Linear Regression
Next add a node for Linear Regression.

![KNIME Linear Regression Workflow](/images/pasted-42.png)

Modify the Database Reader node and add the following SQL query:

``` sql
select value as y, ddate as x from sub
right join num on sub.adsh = num.adsh
right join pre on num.adsh = pre.adsh
and num.tag = pre.tag and num.version = pre.version
where cik=1081316 and coreg=""
and stmt="BS" and num.tag="Assets" and form = "10-K"
```

![Linear Regression SQL KNIME](/images/pasted-43.png)

Configure the Linear Regression node. (You may not be able to see which variables to use as parameters. To populate the variables on the user interface execute all the nodes before the Linear Regression node.) In the top section called "Target" there is a drop down to select the dependent variable y. In the "Values" § "Include" section add the independent variable x.

![Configure Linear Regression](/images/pasted-44.png)

Run all the nodes and when the execution is finished view the results in the interactive table.

![KNIME Linear Regression Results](/images/pasted-45.png)

[0]: http://www.silota.com/docs/recipes/sql-linear-regression.html "SQL Linear Regression"
[1]: https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.sql.ref.doc/doc/r0002321.html "DB2 Functions"
[2]: https://www.cloudera.com/documentation/enterprise/latest/topics/impala_udf.html "Impala UDFs"
[3]: https://github.com/cloudera/madlibport "MADlib Port"
[4]: http://madlib.apache.org/ "MADlib"
[5]: https://www.knime.com/knime-big-data-connectors "KNIME Connectors"