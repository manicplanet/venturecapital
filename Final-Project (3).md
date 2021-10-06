<center>
    <img src="https://s3-api.us-geo.objectstorage.softlayer.net/cf-courses-data/CognitiveClass/Logos/organization_logo/organization_logo.png" width="300" alt="cognitiveclass.ai logo"  />
</center>

# Assignment: Notebook for Peer Assignment

Estimated time needed: 45 minutes


# Assignment Scenario

Congratulations! You have just been hired by a US Venture Capital firm as a data analyst.

The company is considering foreign grain markets to help meet its supply chain requirements for its recent investments in the microbrewery and microdistillery industry, which is involved with the production and distribution of craft beers and spirits.

Your first task is to provide a high level analysis of crop production in Canada. Your stakeholders want to understand the current and historical performance of certain crop types in terms of supply and price volatility. For now they are mainly interested in a macro-view of Canada's crop farming industry, and how it relates to the relative value of the Canadian and US dollars.


# Introduction

Using this R notebook you will:

1.  Understand four datasets
2.  Load the datasets into four separate tables in a Db2 database
3.  Execute SQL queries unsing the RODBC R package to answer assignment questions

You have already encountered two of these datasets in the previous practice lab. You will be able to reuse much of the work you did there to prepare your database tables for executing SQL queries.


# Understand the datasets

To complete the assignment problems in this notebook you will be using subsetted snapshots of two datasets from Statistics Canada, and one from the Bank of Canada. The links to the prepared datasets are provided in the next section; the interested student can explore the landing pages for the source datasets as follows:

1.  <a href="https://www150.statcan.gc.ca/t1/tbl1/en/tv.action?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMRP0203ENSkillsNetwork23863830-2021-01-01&pid=3210035901">Canadian Principal Crops (Data & Metadata)</a>
2.  <a href="https://www150.statcan.gc.ca/t1/tbl1/en/tv.action?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMRP0203ENSkillsNetwork23863830-2021-01-01&pid=3210007701">Farm product prices (Data & Metadata)</a>
3.  <a href="https://www.bankofcanada.ca/rates/exchange/daily-exchange-rates?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMRP0203ENSkillsNetwork23863830-2021-01-01">Bank of Canada daily average exchange rates</a>

### 1. Canadian Principal Crops Data \*

This dataset contains agricultural production measures for the principle crops grown in Canada, including a breakdown by province and teritory, for each year from 1908 to 2020.

For this assignment you will use a preprocessed snapshot of this dataset (see below).

A detailed description of this dataset can be obtained from the StatsCan Data Portal at:
[https://www150.statcan.gc.ca/t1/tbl1/en/tv.action?pid=3210035901](https://www150.statcan.gc.ca/t1/tbl1/en/tv.action?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMRP0203ENSkillsNetwork23863830-2021-01-01&pid=3210035901)\
Detailed information is included in the metadata file and as header text in the data file, which can be downloaded - look for the 'download options' link.

### 2. Farm product prices

This dataset contains monthly average farm product prices for Canadian crops and livestock by province and teritory, from 1980 to 2020 (or 'last year', whichever is greatest).

For this assignment you will use a preprocessed snapshot of this dataset (see below).

A description of this dataset can be obtained from the StatsCan Data Portal at:
[https://www150.statcan.gc.ca/t1/tbl1/en/tv.action?pid=3210007701](https://www150.statcan.gc.ca/t1/tbl1/en/tv.action?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMRP0203ENSkillsNetwork23863830-2021-01-01&pid=3210007701)
The information is included in the metadata file, which can be downloaded - look for the 'download options' link.

### 3. Bank of Canada daily average exchange rates \*

This dataset contains the daily average exchange rates for multiple foreign currencies. Exchange rates are expressed as 1 unit of the foreign currency converted into Canadian dollars. It includes only the latest four years of data, and the rates are published once each business day by 16:30 ET.

For this assignment you will use a snapshot of this dataset with only the USD-CAD exchange rates included (see next section). We have also prepared a monthly averaged version which you will be using below.

A brief description of this dataset and the original dataset can be obtained from the Bank of Canada Data Portal at:
[https://www.bankofcanada.ca/rates/exchange/daily-exchange-rates/](https://www.bankofcanada.ca/rates/exchange/daily-exchange-rates/?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMRP0203ENSkillsNetwork23863830-2021-01-01)

( \* these datasets are the same as the ones you used in the practice lab)


### Dataset URLs

1.  Annual Crop Data: <https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-RP0203EN-SkillsNetwork/labs/Final%20Project/Annual_Crop_Data.csv>

2.  Farm product prices: <https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-RP0203EN-SkillsNetwork/labs/Final%20Project/Monthly_Farm_Prices.csv>

3.  Daily FX Data: <https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-RP0203EN-SkillsNetwork/labs/Final%20Project/Daily_FX.csv>

4.  Monthly FX Data: <https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-RP0203EN-SkillsNetwork/labs/Final%20Project/Monthly_FX.csv>

<span style="color:red">**IMPORTANT:**</span> You will be loading these datasets directly into R data frames from these URLs instead of from the StatsCan and Bank of Canada portals. The versions provided at these URLs are simplified and subsetted versions of the original datasets.


#### Now let's load these datasets into four separate Db2 tables.

Let's first load the RODBC package:



```R
install.packages("RODBC")
library(RODBC)
```

    Updating HTML index of packages in '.Library'
    Making 'packages.html' ... done


## Problem 1

#### Create tables

Establish a connection to the Db2 database, and create the following four tables using the RODBC package in R.
Use the separate cells provided below to create each of your tables.

1.  **CROP_DATA**
2.  **FARM_PRICES**
3.  **DAILY_FX**
4.  **MONTHLY_FX**

The previous practice lab will help you accomplish this.


### Solution 1



```R
# Establish database connection
dsn_driver <- "{IBM DB2 ODBC Driver}"
dsn_database <- "bludb" # e.g. "bludb"
dsn_hostname <- "dashdb-txn-sbox-yp-dal09-08.services.dal.bluemix.net" # e.g→"54a2f15b-5c0f-46df-8954-.databases.appdomain.cloud"
dsn_port <- "50001" # e.g. "32733"
dsn_protocol <- "TCPIP" # i.e. "TCPIP"
dsn_uid <- "stm14075" # e.g. "zjh17769"
dsn_pwd <- "nxmfkzmdsc+n2kmr"  # e.g. "zcwd4+8gbq9bm5k4"
dsn_security <- "ssl"

conn_path <- paste("DRIVER=",dsn_driver,
";DATABASE=",dsn_database,
";HOSTNAME=",dsn_hostname,
";PORT=",dsn_port,
";PROTOCOL=",dsn_protocol,
";UID=",dsn_uid,
";PWD=",dsn_pwd,
";SECURITY=",dsn_security,
sep="")
conn <- odbcDriverConnect(conn_path, believeNRows=FALSE)
conn

```


    RODBC Connection 1
    Details:
      case=nochange
      DRIVER={IBM DB2 ODBC DRIVER}
      UID=stm14075
      PWD=******
      DATABASE=bludb
      HOSTNAME=dashdb-txn-sbox-yp-dal09-08.services.dal.bluemix.net
      PORT=50001
      PROTOCOL=TCPIP
      SECURITY=SSL



```R
# CROP_DATA:
dfcrop_drop<-sqlQuery(conn,"DROP TABLE CROP_DATA", errors=FALSE)

dfcrop<- sqlQuery(conn, "CREATE TABLE CROP_DATA (
INDEX INTEGER(6) NOT NULL,
YEAR DATE(10) NOT NULL, 
croptype CHAR(14) NOT NULL,
GEO CHAR(14) NOT NULL,
SEEDEDAREA INTEGER(10) NOT NULL,
HARVESTEDAREA INTEGER(10) NOT NULL,
PRODUCTION INTEGER(10) NOT NULL,
AVGYIELD INTEGER(4) NOT NULL,
PRIMARY KEY (index))",
errors=FALSE)
```


```R
# FARM_PRICES:

dfprices_drop<-sqlQuery(conn,"DROP TABLE FARM_PRICES", errors=FALSE)

dfprices <- sqlQuery(conn, "CREATE TABLE FARM_PRICES (
INDEX INTEGER(6) NOT NULL,
DATE DATE(10) NOT NULL, 
CROP_TYPE CHAR(14) NOT NULL,
GEO CHAR(14) NOT NULL,
PRICEPERMT FLOAT(6) NOT NULL,
PRIMARY KEY (index))",
errors=FALSE)
```


```R
# DAILY_FX:
dfdaily_drop<-sqlQuery(conn,"DROP TABLE DAILY_FX", errors=FALSE)

dfdaily <- sqlQuery(conn, "CREATE TABLE DAILY_FX (
INDEX INTEGER(6) NOT NULL,
DATE DATE(10) NOT NULL,
FXUSDCAD FLOAT(6) NOT NULL,
PRIMARY KEY (index))",
errors = FALSE)
```


```R
# MONTHLY_FX:
dfmonthly_drop<-sqlQuery(conn,"DROP TABLE MONTHLY_FX",errors=FALSE)

dfmonthly <- sqlQuery(conn, "CREATE TABLE MONTHLY_FX(
INDEX INTEGER(6) NOT NULL,
DATE DATE(10) NOT NULL,
FXUSDCAD FLOAT(6) NOT NULL,
PRIMARY KEY (index))",
errors = FALSE)

```

## Problem 2

#### Read Datasets and Load Tables

Read the datasets into R dataframes using the urls provided above. Then load your tables.


### Solution 2



```R
#Annual Crop Data: 
Annual_Crop_Data_df <- ''
Annual_Crop_Data_df <- read.csv("https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-RP0203EN-SkillsNetwork/labs/Final%20Project/Annual_Crop_Data.csv")

#Farm product prices: 
Farm_Product_Prices_df <-''
Farm_Product_Prices_df <- read.csv("https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-RP0203EN-SkillsNetwork/labs/Final%20Project/Monthly_Farm_Prices.csv")

#Daily FX Data: 
Daily_FX_Data_df <- ''
Daily_FX_Data_df <- read.csv("https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-RP0203EN-SkillsNetwork/labs/Final%20Project/Daily_FX.csv")

#Monthly FX Data: 
Monthly_FX_Data_df <-''
Monthly_FX_Data_df <- read.csv("https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-RP0203EN-SkillsNetwork/labs/Final%20Project/Monthly_FX.csv")

```


```R
sqlSave(conn, Annual_Crop_Data_df, "CROP_DATA", append=TRUE, fast=FALSE, colnames=FALSE, rownames=FALSE, verbose=FALSE)

sqlSave(conn, Farm_Product_Prices_df, "FARM_PRICES", append=TRUE, fast=FALSE, colnames=FALSE, rownames=FALSE, verbose=FALSE)

sqlSave(conn, Daily_FX_Data_df, "DAILY_FX", append=TRUE, fast=FALSE, rownames=FALSE,colnames=FALSE, verbose=FALSE)

sqlSave(conn, Monthly_FX_Data_df, "MONTHLY_FX", append=TRUE, fast=FALSE, rownames=FALSE,colnames=FALSE, verbose=FALSE)
```


```R
query <- "SELECT * FROM CROP_DATA;" 
view <- sqlQuery(conn,query) 
head(view)
```


<table>
<caption>A data.frame: 6 × 8</caption>
<thead>
	<tr><th></th><th scope=col>index</th><th scope=col>YEAR</th><th scope=col>cropType</th><th scope=col>GEO</th><th scope=col>seededArea</th><th scope=col>harvestedArea</th><th scope=col>production</th><th scope=col>avgYield</th></tr>
	<tr><th></th><th scope=col>&lt;int&gt;</th><th scope=col>&lt;fct&gt;</th><th scope=col>&lt;fct&gt;</th><th scope=col>&lt;fct&gt;</th><th scope=col>&lt;int&gt;</th><th scope=col>&lt;int&gt;</th><th scope=col>&lt;int&gt;</th><th scope=col>&lt;int&gt;</th></tr>
</thead>
<tbody>
	<tr><th scope=row>1</th><td>0</td><td>1965-12-31</td><td>Barley</td><td>Alberta     </td><td>1372000</td><td>1372000</td><td>2504000</td><td>1825</td></tr>
	<tr><th scope=row>2</th><td>1</td><td>1965-12-31</td><td>Barley</td><td>Canada      </td><td>2476800</td><td>2476800</td><td>4752900</td><td>1920</td></tr>
	<tr><th scope=row>3</th><td>2</td><td>1965-12-31</td><td>Barley</td><td>Saskatchewan</td><td> 708000</td><td> 708000</td><td>1415000</td><td>2000</td></tr>
	<tr><th scope=row>4</th><td>3</td><td>1965-12-31</td><td>Canola</td><td>Alberta     </td><td> 297400</td><td> 297400</td><td> 215500</td><td> 725</td></tr>
	<tr><th scope=row>5</th><td>4</td><td>1965-12-31</td><td>Canola</td><td>Canada      </td><td> 580700</td><td> 580700</td><td> 512600</td><td> 885</td></tr>
	<tr><th scope=row>6</th><td>5</td><td>1965-12-31</td><td>Canola</td><td>Saskatchewan</td><td> 224600</td><td> 224600</td><td> 242700</td><td>1080</td></tr>
</tbody>
</table>




```R
query <- "SELECT * FROM FARM_PRICES;" 
view <- sqlQuery(conn,query) 
head(view)
```


<table>
<caption>A data.frame: 6 × 5</caption>
<thead>
	<tr><th></th><th scope=col>index</th><th scope=col>date</th><th scope=col>cropType</th><th scope=col>GEO</th><th scope=col>pricePerMT</th></tr>
	<tr><th></th><th scope=col>&lt;int&gt;</th><th scope=col>&lt;fct&gt;</th><th scope=col>&lt;fct&gt;</th><th scope=col>&lt;fct&gt;</th><th scope=col>&lt;dbl&gt;</th></tr>
</thead>
<tbody>
	<tr><th scope=row>1</th><td>0</td><td>1985-01-01</td><td>Barley</td><td>Alberta     </td><td>127.39</td></tr>
	<tr><th scope=row>2</th><td>1</td><td>1985-01-01</td><td>Barley</td><td>Saskatchewan</td><td>121.38</td></tr>
	<tr><th scope=row>3</th><td>2</td><td>1985-01-01</td><td>Canola</td><td>Alberta     </td><td>342.00</td></tr>
	<tr><th scope=row>4</th><td>3</td><td>1985-01-01</td><td>Canola</td><td>Saskatchewan</td><td>339.82</td></tr>
	<tr><th scope=row>5</th><td>4</td><td>1985-01-01</td><td>Rye   </td><td>Alberta     </td><td>100.77</td></tr>
	<tr><th scope=row>6</th><td>5</td><td>1985-01-01</td><td>Rye   </td><td>Saskatchewan</td><td>109.75</td></tr>
</tbody>
</table>




```R
query <- "SELECT * FROM DAILY_FX;" 
view <- sqlQuery(conn,query) 
head(view)
```


<table>
<caption>A data.frame: 6 × 3</caption>
<thead>
	<tr><th></th><th scope=col>index</th><th scope=col>date</th><th scope=col>FXUSDCAD</th></tr>
	<tr><th></th><th scope=col>&lt;int&gt;</th><th scope=col>&lt;fct&gt;</th><th scope=col>&lt;dbl&gt;</th></tr>
</thead>
<tbody>
	<tr><th scope=row>1</th><td>0</td><td>2017-01-03</td><td>1.3435</td></tr>
	<tr><th scope=row>2</th><td>1</td><td>2017-01-04</td><td>1.3315</td></tr>
	<tr><th scope=row>3</th><td>2</td><td>2017-01-05</td><td>1.3244</td></tr>
	<tr><th scope=row>4</th><td>3</td><td>2017-01-06</td><td>1.3214</td></tr>
	<tr><th scope=row>5</th><td>4</td><td>2017-01-09</td><td>1.3240</td></tr>
	<tr><th scope=row>6</th><td>5</td><td>2017-01-10</td><td>1.3213</td></tr>
</tbody>
</table>




```R
query <- "SELECT * FROM MONTHLY_FX;" 
view <- sqlQuery(conn,query) 
head(view)
```


<table>
<caption>A data.frame: 6 × 3</caption>
<thead>
	<tr><th></th><th scope=col>index</th><th scope=col>date</th><th scope=col>FXUSDCAD</th></tr>
	<tr><th></th><th scope=col>&lt;int&gt;</th><th scope=col>&lt;fct&gt;</th><th scope=col>&lt;dbl&gt;</th></tr>
</thead>
<tbody>
	<tr><th scope=row>1</th><td>0</td><td>2017-01-01</td><td>1.319276</td></tr>
	<tr><th scope=row>2</th><td>1</td><td>2017-02-01</td><td>1.310726</td></tr>
	<tr><th scope=row>3</th><td>2</td><td>2017-03-01</td><td>1.338643</td></tr>
	<tr><th scope=row>4</th><td>3</td><td>2017-04-01</td><td>1.344021</td></tr>
	<tr><th scope=row>5</th><td>4</td><td>2017-05-01</td><td>1.360705</td></tr>
	<tr><th scope=row>6</th><td>5</td><td>2017-06-01</td><td>1.329805</td></tr>
</tbody>
</table>



## Now execute SQL queries using the RODBC R package to solve the assignment problems.

## Problem 3

#### How many records are in the farm prices dataset?


### Solution 3



```R
record_count_query <- query <- paste("select count(*) AS count from FARM_PRICES;")
record_count_df<- sqlQuery(conn, record_count_query)
record_count_df


# Non-SQL solution: data.frame(Farm_Product_Prices_df)
#2678 records are in the farm prices dataset
```


<table>
<caption>A data.frame: 1 × 1</caption>
<thead>
	<tr><th></th><th scope=col>COUNT</th></tr>
	<tr><th></th><th scope=col>&lt;int&gt;</th></tr>
</thead>
<tbody>
	<tr><th scope=row>1</th><td>2678</td></tr>
</tbody>
</table>



## Problem 4

#### Which geographies are included in the farm prices dataset?


### Solution 4



```R
unique_farm_prices_geographies_query <- query <- paste("select DISTINCT (GEO) FROM FARM_PRICES;")
unique_farm_prices_geographies_df <-sqlQuery(conn,unique_farm_prices_geographies_query)
unique_farm_prices_geographies_df
#The geographies included in the farm prices dataset are Alberta and Saskatchewan.
```


<table>
<caption>A data.frame: 2 × 1</caption>
<thead>
	<tr><th></th><th scope=col>GEO</th></tr>
	<tr><th></th><th scope=col>&lt;fct&gt;</th></tr>
</thead>
<tbody>
	<tr><th scope=row>1</th><td>Alberta     </td></tr>
	<tr><th scope=row>2</th><td>Saskatchewan</td></tr>
</tbody>
</table>



## Problem 5

#### How many hectares of Rye were harvested in Canada in 1968?


### Solution 5



```R
d <-"SELECT sum(harvestedArea) FROM CROP_DATA WHERE YEAR LIKE '1968%';"
v<-sqlQuery(conn,d)
head(v)
```


<style>
.list-inline {list-style: none; margin:0; padding: 0}
.list-inline>li {display: inline-block}
.list-inline>li:not(:last-child)::after {content: "\00b7"; padding: 0 .5ex}
</style>
<ol class=list-inline><li><span style=white-space:pre-wrap>'42S22 -206 [IBM][CLI Driver][DB2/LINUXX8664] SQL0206N  "HARVESTEDAREA" is not valid in the context where it is used.  SQLSTATE=42703\n'</span></li><li>'[RODBC] ERROR: Could not SQLExecDirect \'SELECT sum(harvestedArea) FROM CROP_DATA WHERE YEAR LIKE \'1968%\';\''</li></ol>



## Problem 6

#### Query and display the first 6 rows of the farm prices table for Rye.


### Solution 6



```R
farm_prices_df<-''
farm_prices_query <- query <- "SELECT * FROM FARM_PRICES WHERE CROP_TYPE = 'Rye';"
farm_prices_df<-sqlQuery(conn,farm_prices_query)
head(farm_prices_df)
```


<style>
.list-inline {list-style: none; margin:0; padding: 0}
.list-inline>li {display: inline-block}
.list-inline>li:not(:last-child)::after {content: "\00b7"; padding: 0 .5ex}
</style>
<ol class=list-inline><li><span style=white-space:pre-wrap>'42S22 -206 [IBM][CLI Driver][DB2/LINUXX8664] SQL0206N  "CROP_TYPE" is not valid in the context where it is used.  SQLSTATE=42703\n'</span></li><li>'[RODBC] ERROR: Could not SQLExecDirect \'SELECT * FROM FARM_PRICES WHERE CROP_TYPE = \'Rye\';\''</li></ol>



## Problem 7

#### Which provinces grew Barley?


### Solution 7



```R
barley_query<-"SELECT DISTINCT(GEO) FROM CROP_DATA WHERE croptype = 'BARLEY';"
barley_df<-sqlQuery(conn,barley_query)
barley_df

#Alberta, Saskatchewan
```


<style>
.list-inline {list-style: none; margin:0; padding: 0}
.list-inline>li {display: inline-block}
.list-inline>li:not(:last-child)::after {content: "\00b7"; padding: 0 .5ex}
</style>
<ol class=list-inline><li><span style=white-space:pre-wrap>'42S22 -206 [IBM][CLI Driver][DB2/LINUXX8664] SQL0206N  "CROPTYPE" is not valid in the context where it is used.  SQLSTATE=42703\n'</span></li><li>'[RODBC] ERROR: Could not SQLExecDirect \'SELECT DISTINCT(GEO) FROM CROP_DATA WHERE croptype = \'BARLEY\';\''</li></ol>



## Problem 8

#### Find the first and last dates for the farm prices data.


### Solution 8



```R
query <-
"SELECT min(DATE) FIRST_DATE, max(DATE) LAST_DATE FROM FARM_PRICES;"
view <- sqlQuery(conn,query)
view


#1985-01-01 / 2020-12-01
```


<style>
.list-inline {list-style: none; margin:0; padding: 0}
.list-inline>li {display: inline-block}
.list-inline>li:not(:last-child)::after {content: "\00b7"; padding: 0 .5ex}
</style>
<ol class=list-inline><li><span style=white-space:pre-wrap>'42S22 -206 [IBM][CLI Driver][DB2/LINUXX8664] SQL0206N  "DATE" is not valid in the context where it is used.  SQLSTATE=42703\n'</span></li><li>'[RODBC] ERROR: Could not SQLExecDirect \'SELECT min(DATE) FIRST_DATE, max(DATE) LAST_DATE FROM FARM_PRICES;\''</li></ol>



## Problem 9

#### Which crops have ever reached a farm price greater than or equal to $350 per metric tonne?


### Solution 9



```R
high_price_query<-"SELECT * FROM FARM_PRICES WHERE PricePerMT >350;"
high_price_df<-sqlQuery(conn,high_price_query)
high_price_df

#Canola
```


<style>
.list-inline {list-style: none; margin:0; padding: 0}
.list-inline>li {display: inline-block}
.list-inline>li:not(:last-child)::after {content: "\00b7"; padding: 0 .5ex}
</style>
<ol class=list-inline><li><span style=white-space:pre-wrap>'42S22 -206 [IBM][CLI Driver][DB2/LINUXX8664] SQL0206N  "PRICEPERMT" is not valid in the context where it is used.  SQLSTATE=42703\n'</span></li><li>'[RODBC] ERROR: Could not SQLExecDirect \'SELECT * FROM FARM_PRICES WHERE PricePerMT &gt;350;\''</li></ol>



## Problem 10

#### Rank the crop types harvested in Saskatchewan in the year 2000 by their average yield. Which crop performed best?


### Solution 10



```R
avg_yield_query<-"SELECT *FROM CROP_DATA WHERE GEO = 'Saskatchewan' AND YEAR LIKE '2000%';"
avg_yield_df<-sqlQuery(conn,avg_yield_query)
avg_yield_df

#Barley
```


<table>
<caption>A data.frame: 4 × 8</caption>
<thead>
	<tr><th></th><th scope=col>index</th><th scope=col>YEAR</th><th scope=col>cropType</th><th scope=col>GEO</th><th scope=col>seededArea</th><th scope=col>harvestedArea</th><th scope=col>production</th><th scope=col>avgYield</th></tr>
	<tr><th></th><th scope=col>&lt;int&gt;</th><th scope=col>&lt;fct&gt;</th><th scope=col>&lt;fct&gt;</th><th scope=col>&lt;fct&gt;</th><th scope=col>&lt;int&gt;</th><th scope=col>&lt;int&gt;</th><th scope=col>&lt;int&gt;</th><th scope=col>&lt;int&gt;</th></tr>
</thead>
<tbody>
	<tr><th scope=row>1</th><td>422</td><td>2000-12-31</td><td>Barley</td><td>Saskatchewan</td><td>2063900</td><td>1922300</td><td> 5301600</td><td>2800</td></tr>
	<tr><th scope=row>2</th><td>425</td><td>2000-12-31</td><td>Canola</td><td>Saskatchewan</td><td>2387600</td><td>2371500</td><td> 3424600</td><td>1400</td></tr>
	<tr><th scope=row>3</th><td>428</td><td>2000-12-31</td><td>Rye   </td><td>Saskatchewan</td><td>  66800</td><td>  46500</td><td>   97800</td><td>2100</td></tr>
	<tr><th scope=row>4</th><td>431</td><td>2000-12-31</td><td>Wheat </td><td>Saskatchewan</td><td>6145100</td><td>6080300</td><td>13411800</td><td>2200</td></tr>
</tbody>
</table>



## Problem 11

#### Rank the crops and geographies by their average yield (KG per hectare) since the year 2000. Which crop and province had the highest average yield since the year 2000?


### Solution 11



```R
highest_yield_query<-"SELECT croptype, GEO FROM CROP_DATA WHERE YEAR >= '2000-1-1';"
highest_yield_df<-sqlQuery(conn,highest_yield_query)
highest_yield_df

#Barley, Alberta, 2020-12-31, 3980
```


<style>
.list-inline {list-style: none; margin:0; padding: 0}
.list-inline>li {display: inline-block}
.list-inline>li:not(:last-child)::after {content: "\00b7"; padding: 0 .5ex}
</style>
<ol class=list-inline><li><span style=white-space:pre-wrap>'42S22 -206 [IBM][CLI Driver][DB2/LINUXX8664] SQL0206N  "CROPTYPE" is not valid in the context where it is used.  SQLSTATE=42703\n'</span></li><li>'[RODBC] ERROR: Could not SQLExecDirect \'SELECT croptype, GEO FROM CROP_DATA WHERE YEAR &gt;= \'2000-1-1\';\''</li></ol>



## Problem 12

#### Use a subquery to determine how much wheat was harvested in Canada in the most recent year of the data.


### Solution 12



```R
harvestquery <- "SELECT * FROM CROP_DATA WHERE AND CROP_TYPE = 'WHEAT' AND 
YEAR in (SELECT * FROM CROP_DATA WHERE YEAR LIKE '2000%');"
view<-sqlQuery(conn, harvestquery) 
view
#35183000
```


<style>
.list-inline {list-style: none; margin:0; padding: 0}
.list-inline>li {display: inline-block}
.list-inline>li:not(:last-child)::after {content: "\00b7"; padding: 0 .5ex}
</style>
<ol class=list-inline><li><span style=white-space:pre-wrap>'42601 -104 [IBM][CLI Driver][DB2/LINUXX8664] SQL0104N  An unexpected token "CROP_TYPE" was found following "CROP_DATA WHERE AND".  Expected tokens may include:  "&lt;space&gt;".  SQLSTATE=42601\n'</span></li><li>'[RODBC] ERROR: Could not SQLExecDirect \'SELECT * FROM CROP_DATA WHERE AND CROP_TYPE = \'WHEAT\' AND \nYEAR in (SELECT * FROM CROP_DATA WHERE YEAR LIKE \'2000%\');\''</li></ol>



## Problem 13

#### Use an implicit inner join to calculate the monthly price per metric tonne of Canola grown in Saskatchewan in both Canadian and US dollars. Display the most recent 6 months of the data.


### Solution 13



```R
query<-"SELECT * FROM MONTHLY_FX, FARM_PRICES;"
view<-sqlQuery(conn,query)
head(view)
```


<table>
<caption>A data.frame: 6 × 8</caption>
<thead>
	<tr><th></th><th scope=col>index</th><th scope=col>date</th><th scope=col>FXUSDCAD</th><th scope=col>index.1</th><th scope=col>date.1</th><th scope=col>cropType</th><th scope=col>GEO</th><th scope=col>pricePerMT</th></tr>
	<tr><th></th><th scope=col>&lt;int&gt;</th><th scope=col>&lt;fct&gt;</th><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;int&gt;</th><th scope=col>&lt;fct&gt;</th><th scope=col>&lt;fct&gt;</th><th scope=col>&lt;fct&gt;</th><th scope=col>&lt;dbl&gt;</th></tr>
</thead>
<tbody>
	<tr><th scope=row>1</th><td>0</td><td>2017-01-01</td><td>1.319276</td><td>0</td><td>1985-01-01</td><td>Barley</td><td>Alberta     </td><td>127.39</td></tr>
	<tr><th scope=row>2</th><td>0</td><td>2017-01-01</td><td>1.319276</td><td>1</td><td>1985-01-01</td><td>Barley</td><td>Saskatchewan</td><td>121.38</td></tr>
	<tr><th scope=row>3</th><td>0</td><td>2017-01-01</td><td>1.319276</td><td>2</td><td>1985-01-01</td><td>Canola</td><td>Alberta     </td><td>342.00</td></tr>
	<tr><th scope=row>4</th><td>0</td><td>2017-01-01</td><td>1.319276</td><td>3</td><td>1985-01-01</td><td>Canola</td><td>Saskatchewan</td><td>339.82</td></tr>
	<tr><th scope=row>5</th><td>0</td><td>2017-01-01</td><td>1.319276</td><td>4</td><td>1985-01-01</td><td>Rye   </td><td>Alberta     </td><td>100.77</td></tr>
	<tr><th scope=row>6</th><td>0</td><td>2017-01-01</td><td>1.319276</td><td>5</td><td>1985-01-01</td><td>Rye   </td><td>Saskatchewan</td><td>109.75</td></tr>
</tbody>
</table>



## Author(s)

<h4> Jeff Grossman </h4>

## Contributor(s)

<h4> Rav Ahuja </h4>

## Change log

| Date       | Version | Changed by    | Change Description                                                                                         |
| ---------- | ------- | ------------- | ---------------------------------------------------------------------------------------------------------- |
| 2021-04-01 | 0.7     | Jeff Grossman | Split Problem 1 solution cell into multiple cells, fixed minor bugs                                        |
| 2021-03-12 | 0.6     | Jeff Grossman | Cleaned up content for production                                                                          |
| 2021-03-11 | 0.5     | Jeff Grossman | Moved more advanced problems to optional honours module                                                    |
| 2021-03-10 | 0.4     | Jeff Grossman | Added introductory and intermediate level problems and removed some advanced problems                      |
| 2021-03-04 | 0.3     | Jeff Grossman | Moved some problems to a new practice lab as prep for this assignment                                      |
| 2021-03-04 | 0.2     | Jeff Grossman | Sorted problems roughly by level of difficulty and relegated more advanced ones to ungraded bonus problems |
| 2021-02-20 | 0.1     | Jeff Grossman | Started content creation                                                                                   |

## <h3 align="center"> © IBM Corporation 2021. All rights reserved. <h3/>



```R

```
