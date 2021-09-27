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
library(RODBC)
```

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
dfcrop<- sqlQuery(conn, "CREATE TABLE CROPDATA (
index INTEGER(6) NOT NULL,
YEAR VARCHAR(10) NOT NULL, 
CROPTYPE VARCHAR(14) NOT NULL,
GEO CHAR(14) NOT NULL,
seededArea INTEGER(10) NOT NULL,
harvestedArea INTEGER(10) NOT NULL,
production INTEGER(10) NOT NULL,
avgYield INTEGER(4) NOT NULL,
PRIMARY KEY (index))",
errors=FALSE)
```


```R
# FARM_PRICES:
dfprices <- sqlQuery(conn, "CREATE TABLE FARMPRICES (
index INTEGER(6) NOT NULL,
date VARCHAR(10) NOT NULL, 
CROPTYPE VARCHAR(14) NOT NULL,
GEO CHAR(14) NOT NULL,
pricePerMT FLOAT(6) NOT NULL,
PRIMARY KEY (index))",
errors=FALSE)
```


```R
# DAILY_FX:
dfdaily <- sqlQuery(conn, "CREATE TABLE DAILYFX (
index INTEGER(6) NOT NULL,
date VARCHAR(10) NOT NULL,
FXUSDCAD FLOAT(6) NOT NULL,
PRIMARY KEY (index))",
errors = FALSE)
```


```R
# MONTHLY_FX:
dfmonthly <- sqlQuery(conn, "CREATE TABLE MONTHLYFX(
index INTEGER(6) NOT NULL,
date VARCHAR(10) NOT NULL,
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
Annual_Crop_Data_df <- read.csv("https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-RP0203EN-SkillsNetwork/labs/Final%20Project/Annual_Crop_Data.csv")

#Farm product prices: 
Farm_Product_Prices_df <- read.csv("https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-RP0203EN-SkillsNetwork/labs/Final%20Project/Monthly_Farm_Prices.csv")

#Daily FX Data: 
Daily_FX_Data_df <- read.csv("https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-RP0203EN-SkillsNetwork/labs/Final%20Project/Daily_FX.csv")

#Monthly FX Data: 
Monthly_FX_Data_df <- read.csv("https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-RP0203EN-SkillsNetwork/labs/Final%20Project/Monthly_FX.csv")




head(Annual_Crop_Data_df)
sqlSave(conn, Annual_Crop_Data_df, "CROPDATA", append=TRUE, fast=FALSE, rownames=FALSE,colnames=FALSE, verbose=FALSE)

head(Farm_Product_Prices_df)
sqlSave(conn, Farm_Product_Prices_df, "FARMPRICES", append=TRUE, fast=FALSE, rownames=FALSE,colnames=FALSE, verbose=FALSE)

head(Daily_FX_Data_df)
sqlSave(conn, Daily_FX_Data_df, "DAILYFX", append=TRUE, fast=FALSE, rownames=FALSE,colnames=FALSE, verbose=FALSE)

head(Monthly_FX_Data_df)
sqlSave(conn, Monthly_FX_Data_df, "MONTHLYFX", append=TRUE, fast=FALSE, rownames=FALSE,colnames=FALSE, verbose=FALSE)
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
record_count_query <- query <- paste("select count(*) AS count from FARMPRICES")
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
	<tr><th scope=row>1</th><td>18746</td></tr>
</tbody>
</table>



## Problem 4

#### Which geographies are included in the farm prices dataset?


### Solution 4



```R
unique_farm_prices_geographies_query <- query <- paste("select DISTINCT GEO FROM FARMPRICES")
unique_farm_prices_geographies_df <-sqlQuery(conn,unique_farm_prices_geographies_query)
unique_farm_prices_geographies_df
#2
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


alter_query <- "ALTER TABLE CROPDATA ALTER COLUMN CROPTYPE VARCHAR(14)"
alter_df <-sqlQuery(conn, alter_query)

review_query <- "SELECT * FROM INFORMATION_SCHEMA.COLUMNS WHERE TABLE_NAME = N'CROPDATA'"
review_df<-sqlQuery(conn,review_query)
review_df

hectares_of_rye_query<- "SELECT * FROM CROPDATA"
hectares_of_rye_df<-sqlQuery(conn, hectares_of_rye_query)
hectares_of_rye_df




```


<style>
.list-inline {list-style: none; margin:0; padding: 0}
.list-inline>li {display: inline-block}
.list-inline>li:not(:last-child)::after {content: "\00b7"; padding: 0 .5ex}
</style>
<ol class=list-inline><li><span style=white-space:pre-wrap>'42S02 -204 [IBM][CLI Driver][DB2/LINUXX8664] SQL0204N  "INFORMATION_SCHEMA.COLUMNS" is an undefined name.  SQLSTATE=42704\n'</span></li><li>'[RODBC] ERROR: Could not SQLExecDirect \'SELECT * FROM INFORMATION_SCHEMA.COLUMNS WHERE TABLE_NAME = N\'CROPDATA\'\''</li></ol>




<table>
<caption>A data.frame: 4704 × 8</caption>
<thead>
	<tr><th></th><th scope=col>index</th><th scope=col>YEAR</th><th scope=col>cropType</th><th scope=col>GEO</th><th scope=col>seededArea</th><th scope=col>harvestedArea</th><th scope=col>production</th><th scope=col>avgYield</th></tr>
	<tr><th></th><th scope=col>&lt;int&gt;</th><th scope=col>&lt;fct&gt;</th><th scope=col>&lt;fct&gt;</th><th scope=col>&lt;fct&gt;</th><th scope=col>&lt;int&gt;</th><th scope=col>&lt;int&gt;</th><th scope=col>&lt;int&gt;</th><th scope=col>&lt;int&gt;</th></tr>
</thead>
<tbody>
	<tr><th scope=row>1</th><td> 0</td><td>1965-12-31</td><td>Barley</td><td>Alberta     </td><td> 1372000</td><td> 1372000</td><td> 2504000</td><td>1825</td></tr>
	<tr><th scope=row>2</th><td> 1</td><td>1965-12-31</td><td>Barley</td><td>Canada      </td><td> 2476800</td><td> 2476800</td><td> 4752900</td><td>1920</td></tr>
	<tr><th scope=row>3</th><td> 2</td><td>1965-12-31</td><td>Barley</td><td>Saskatchewan</td><td>  708000</td><td>  708000</td><td> 1415000</td><td>2000</td></tr>
	<tr><th scope=row>4</th><td> 3</td><td>1965-12-31</td><td>Canola</td><td>Alberta     </td><td>  297400</td><td>  297400</td><td>  215500</td><td> 725</td></tr>
	<tr><th scope=row>5</th><td> 4</td><td>1965-12-31</td><td>Canola</td><td>Canada      </td><td>  580700</td><td>  580700</td><td>  512600</td><td> 885</td></tr>
	<tr><th scope=row>6</th><td> 5</td><td>1965-12-31</td><td>Canola</td><td>Saskatchewan</td><td>  224600</td><td>  224600</td><td>  242700</td><td>1080</td></tr>
	<tr><th scope=row>7</th><td> 6</td><td>1965-12-31</td><td>Rye   </td><td>Alberta     </td><td>   81000</td><td>   81000</td><td>  116400</td><td>1435</td></tr>
	<tr><th scope=row>8</th><td> 7</td><td>1965-12-31</td><td>Rye   </td><td>Canada      </td><td>  323900</td><td>  323900</td><td>  453400</td><td>1400</td></tr>
	<tr><th scope=row>9</th><td> 8</td><td>1965-12-31</td><td>Rye   </td><td>Saskatchewan</td><td>  166000</td><td>  166000</td><td>  224000</td><td>1350</td></tr>
	<tr><th scope=row>10</th><td> 9</td><td>1965-12-31</td><td>Wheat </td><td>Alberta     </td><td> 2448000</td><td> 2448000</td><td> 4164000</td><td>1700</td></tr>
	<tr><th scope=row>11</th><td>10</td><td>1965-12-31</td><td>Wheat </td><td>Canada      </td><td>11451650</td><td>11451650</td><td>17673950</td><td>1545</td></tr>
	<tr><th scope=row>12</th><td>11</td><td>1965-12-31</td><td>Wheat </td><td>Saskatchewan</td><td> 7486000</td><td> 7486000</td><td>10886000</td><td>1455</td></tr>
	<tr><th scope=row>13</th><td>12</td><td>1966-12-31</td><td>Barley</td><td>Alberta     </td><td> 1570000</td><td> 1570000</td><td> 3462000</td><td>2205</td></tr>
	<tr><th scope=row>14</th><td>13</td><td>1966-12-31</td><td>Barley</td><td>Canada      </td><td> 3019600</td><td> 3019600</td><td> 6449950</td><td>2135</td></tr>
	<tr><th scope=row>15</th><td>14</td><td>1966-12-31</td><td>Barley</td><td>Saskatchewan</td><td>  913000</td><td>  913000</td><td> 1981000</td><td>2170</td></tr>
	<tr><th scope=row>16</th><td>15</td><td>1966-12-31</td><td>Canola</td><td>Alberta     </td><td>  252500</td><td>  252500</td><td>  249500</td><td> 990</td></tr>
	<tr><th scope=row>17</th><td>16</td><td>1966-12-31</td><td>Canola</td><td>Canada      </td><td>  617100</td><td>  617100</td><td>  585100</td><td> 950</td></tr>
	<tr><th scope=row>18</th><td>17</td><td>1966-12-31</td><td>Canola</td><td>Saskatchewan</td><td>  295800</td><td>  295800</td><td>  288000</td><td> 975</td></tr>
	<tr><th scope=row>19</th><td>18</td><td>1966-12-31</td><td>Rye   </td><td>Alberta     </td><td>   70000</td><td>   70000</td><td>  109000</td><td>1555</td></tr>
	<tr><th scope=row>20</th><td>19</td><td>1966-12-31</td><td>Rye   </td><td>Canada      </td><td>  293400</td><td>  293400</td><td>  437600</td><td>1490</td></tr>
	<tr><th scope=row>21</th><td>20</td><td>1966-12-31</td><td>Rye   </td><td>Saskatchewan</td><td>  161000</td><td>  161000</td><td>  228600</td><td>1420</td></tr>
	<tr><th scope=row>22</th><td>21</td><td>1966-12-31</td><td>Wheat </td><td>Alberta     </td><td> 2633000</td><td> 2633000</td><td> 5198000</td><td>1975</td></tr>
	<tr><th scope=row>23</th><td>22</td><td>1966-12-31</td><td>Wheat </td><td>Canada      </td><td>12016000</td><td>12016000</td><td>22516850</td><td>1875</td></tr>
	<tr><th scope=row>24</th><td>23</td><td>1966-12-31</td><td>Wheat </td><td>Saskatchewan</td><td> 7853000</td><td> 7853000</td><td>14615000</td><td>1860</td></tr>
	<tr><th scope=row>25</th><td>24</td><td>1967-12-31</td><td>Barley</td><td>Alberta     </td><td> 1732000</td><td> 1732000</td><td> 3005000</td><td>1735</td></tr>
	<tr><th scope=row>26</th><td>25</td><td>1967-12-31</td><td>Barley</td><td>Canada      </td><td> 3286850</td><td> 3286850</td><td> 5505800</td><td>1675</td></tr>
	<tr><th scope=row>27</th><td>26</td><td>1967-12-31</td><td>Barley</td><td>Saskatchewan</td><td>  951000</td><td>  951000</td><td> 1372000</td><td>1445</td></tr>
	<tr><th scope=row>28</th><td>27</td><td>1967-12-31</td><td>Canola</td><td>Alberta     </td><td>  354100</td><td>  354100</td><td>  276700</td><td> 780</td></tr>
	<tr><th scope=row>29</th><td>28</td><td>1967-12-31</td><td>Canola</td><td>Canada      </td><td>  661700</td><td>  661700</td><td>  563600</td><td> 850</td></tr>
	<tr><th scope=row>30</th><td>29</td><td>1967-12-31</td><td>Canola</td><td>Saskatchewan</td><td>  242800</td><td>  242800</td><td>  231300</td><td> 955</td></tr>
	<tr><th scope=row>⋮</th><td>⋮</td><td>⋮</td><td>⋮</td><td>⋮</td><td>⋮</td><td>⋮</td><td>⋮</td><td>⋮</td></tr>
	<tr><th scope=row>4675</th><td>642</td><td>2018-12-31</td><td>Rye   </td><td>Alberta     </td><td>   16000</td><td>    9100</td><td>   30000</td><td>3333</td></tr>
	<tr><th scope=row>4676</th><td>643</td><td>2018-12-31</td><td>Rye   </td><td>Canada      </td><td>  135400</td><td>   78900</td><td>  236400</td><td>2995</td></tr>
	<tr><th scope=row>4677</th><td>644</td><td>2018-12-31</td><td>Rye   </td><td>Saskatchewan</td><td>   30400</td><td>   21600</td><td>   47400</td><td>2194</td></tr>
	<tr><th scope=row>4678</th><td>645</td><td>2018-12-31</td><td>Wheat </td><td>Alberta     </td><td> 3053100</td><td> 2991300</td><td>10006100</td><td>3345</td></tr>
	<tr><th scope=row>4679</th><td>646</td><td>2018-12-31</td><td>Wheat </td><td>Canada      </td><td>10073100</td><td> 9880900</td><td>32351900</td><td>3274</td></tr>
	<tr><th scope=row>4680</th><td>647</td><td>2018-12-31</td><td>Wheat </td><td>Saskatchewan</td><td> 5255700</td><td> 5176200</td><td>14877100</td><td>2874</td></tr>
	<tr><th scope=row>4681</th><td>648</td><td>2019-12-31</td><td>Barley</td><td>Alberta     </td><td> 1441900</td><td> 1272700</td><td> 4955200</td><td>3890</td></tr>
	<tr><th scope=row>4682</th><td>649</td><td>2019-12-31</td><td>Barley</td><td>Canada      </td><td> 2995700</td><td> 2727500</td><td>10382600</td><td>3810</td></tr>
	<tr><th scope=row>4683</th><td>650</td><td>2019-12-31</td><td>Barley</td><td>Saskatchewan</td><td> 1274800</td><td> 1194000</td><td> 4449200</td><td>3730</td></tr>
	<tr><th scope=row>4684</th><td>651</td><td>2019-12-31</td><td>Canola</td><td>Alberta     </td><td> 2401200</td><td> 2355600</td><td> 5320100</td><td>2259</td></tr>
	<tr><th scope=row>4685</th><td>652</td><td>2019-12-31</td><td>Canola</td><td>Canada      </td><td> 8571700</td><td> 8456300</td><td>19607000</td><td>2319</td></tr>
	<tr><th scope=row>4686</th><td>653</td><td>2019-12-31</td><td>Canola</td><td>Saskatchewan</td><td> 4765200</td><td> 4741300</td><td>11089000</td><td>2339</td></tr>
	<tr><th scope=row>4687</th><td>654</td><td>2019-12-31</td><td>Rye   </td><td>Alberta     </td><td>   26600</td><td>    7800</td><td>   26800</td><td>3436</td></tr>
	<tr><th scope=row>4688</th><td>655</td><td>2019-12-31</td><td>Rye   </td><td>Canada      </td><td>  174700</td><td>  102600</td><td>  333400</td><td>3249</td></tr>
	<tr><th scope=row>4689</th><td>656</td><td>2019-12-31</td><td>Rye   </td><td>Saskatchewan</td><td>   47400</td><td>   35500</td><td>  102800</td><td>2895</td></tr>
	<tr><th scope=row>4690</th><td>657</td><td>2019-12-31</td><td>Wheat </td><td>Alberta     </td><td> 3020000</td><td> 2917300</td><td>10263200</td><td>3518</td></tr>
	<tr><th scope=row>4691</th><td>658</td><td>2019-12-31</td><td>Wheat </td><td>Canada      </td><td>10125700</td><td> 9655600</td><td>32669800</td><td>3384</td></tr>
	<tr><th scope=row>4692</th><td>659</td><td>2019-12-31</td><td>Wheat </td><td>Saskatchewan</td><td> 5226800</td><td> 5066400</td><td>15360800</td><td>3032</td></tr>
	<tr><th scope=row>4693</th><td>660</td><td>2020-12-31</td><td>Barley</td><td>Alberta     </td><td> 1481800</td><td> 1326200</td><td> 5283000</td><td>3980</td></tr>
	<tr><th scope=row>4694</th><td>661</td><td>2020-12-31</td><td>Barley</td><td>Canada      </td><td> 3059900</td><td> 2808700</td><td>10740600</td><td>3820</td></tr>
	<tr><th scope=row>4695</th><td>662</td><td>2020-12-31</td><td>Barley</td><td>Saskatchewan</td><td> 1264400</td><td> 1187300</td><td> 4385100</td><td>3690</td></tr>
	<tr><th scope=row>4696</th><td>663</td><td>2020-12-31</td><td>Canola</td><td>Alberta     </td><td> 2377900</td><td> 2313500</td><td> 5212100</td><td>2253</td></tr>
	<tr><th scope=row>4697</th><td>664</td><td>2020-12-31</td><td>Canola</td><td>Canada      </td><td> 8410400</td><td> 8319800</td><td>18719700</td><td>2250</td></tr>
	<tr><th scope=row>4698</th><td>665</td><td>2020-12-31</td><td>Canola</td><td>Saskatchewan</td><td> 4588800</td><td> 4574100</td><td>10202900</td><td>2231</td></tr>
	<tr><th scope=row>4699</th><td>666</td><td>2020-12-31</td><td>Rye   </td><td>Alberta     </td><td>   41600</td><td>   29200</td><td>  107400</td><td>3691</td></tr>
	<tr><th scope=row>4700</th><td>667</td><td>2020-12-31</td><td>Rye   </td><td>Canada      </td><td>  236700</td><td>  153000</td><td>  487800</td><td>3188</td></tr>
	<tr><th scope=row>4701</th><td>668</td><td>2020-12-31</td><td>Rye   </td><td>Saskatchewan</td><td>   58200</td><td>   47400</td><td>  122000</td><td>2572</td></tr>
	<tr><th scope=row>4702</th><td>669</td><td>2020-12-31</td><td>Wheat </td><td>Alberta     </td><td> 3014300</td><td> 2944700</td><td>11041000</td><td>3750</td></tr>
	<tr><th scope=row>4703</th><td>670</td><td>2020-12-31</td><td>Wheat </td><td>Canada      </td><td>10193600</td><td>10017800</td><td>35183000</td><td>3512</td></tr>
	<tr><th scope=row>4704</th><td>671</td><td>2020-12-31</td><td>Wheat </td><td>Saskatchewan</td><td> 5203700</td><td> 5175400</td><td>15984200</td><td>3088</td></tr>
</tbody>
</table>



## Problem 6

#### Query and display the first 6 rows of the farm prices table for Rye.


### Solution 6



```R
head_farm_prices_query <- query <- "SELECT * FROM FARMPRICES LIMIT 6"
head_farm_prices_df<-sqlQuery(conn,head_farm_prices_query)
head_farm_prices_df

#head(Farm_Product_Prices_df, 6)
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



## Problem 7

#### Which provinces grew Barley?


### Solution 7



```R
barley_query<-"SELECT GEO FROM CROPDATA WHERE CROPTYPE = 'Barley'"
barley_df<-sqlQuery(conn,barley_query)
barley_df
```


<style>
.list-inline {list-style: none; margin:0; padding: 0}
.list-inline>li {display: inline-block}
.list-inline>li:not(:last-child)::after {content: "\00b7"; padding: 0 .5ex}
</style>
<ol class=list-inline><li><span style=white-space:pre-wrap>'42S22 -206 [IBM][CLI Driver][DB2/LINUXX8664] SQL0206N  "CROPTYPE" is not valid in the context where it is used.  SQLSTATE=42703\n'</span></li><li>'[RODBC] ERROR: Could not SQLExecDirect \'SELECT GEO FROM CROPDATA WHERE CROPTYPE = \'Barley\'\''</li></ol>



## Problem 8

#### Find the first and last dates for the farm prices data.


### Solution 8



```R
max_query<-"SELECT MAX(date) AS MAX FROM FARMPRICES"
max_df<-sqlQuery(conn,max_query)
max_df
```


<style>
.list-inline {list-style: none; margin:0; padding: 0}
.list-inline>li {display: inline-block}
.list-inline>li:not(:last-child)::after {content: "\00b7"; padding: 0 .5ex}
</style>
<ol class=list-inline><li><span style=white-space:pre-wrap>'42S22 -206 [IBM][CLI Driver][DB2/LINUXX8664] SQL0206N  "DATE" is not valid in the context where it is used.  SQLSTATE=42703\n'</span></li><li>'[RODBC] ERROR: Could not SQLExecDirect \'SELECT MAX(date) AS MAX FROM FARMPRICES\''</li></ol>



## Problem 9

#### Which crops have ever reached a farm price greater than or equal to $350 per metric tonne?


### Solution 9



```R
high_price_query<-"SELECT CROPTYPEFROM FARMPRICES WHERE pricePerMT >=350"
high_price_df<-sqlQuery(conn,high_price_query)
high_price_df
```


<style>
.list-inline {list-style: none; margin:0; padding: 0}
.list-inline>li {display: inline-block}
.list-inline>li:not(:last-child)::after {content: "\00b7"; padding: 0 .5ex}
</style>
<ol class=list-inline><li><span style=white-space:pre-wrap>'42601 -104 [IBM][CLI Driver][DB2/LINUXX8664] SQL0104N  An unexpected token "FARMPRICES" was found following "SELECT CROPTYPEFROM".  Expected tokens may include:  "FROM".  SQLSTATE=42601\n'</span></li><li>'[RODBC] ERROR: Could not SQLExecDirect \'SELECT CROPTYPEFROM FARMPRICES WHERE pricePerMT &gt;=350\''</li></ol>



## Problem 10

#### Rank the crop types harvested in Saskatchewan in the year 2000 by their average yield. Which crop performed best?


### Solution 10



```R
alter_query2 <- "ALTER TABLE CROPDATA ALTER COLUMN DATE DATE"
alter_df <-sqlQuery(conn, alter_query2)


avg_yield_query<-"SELECT CROPTYPE FROM CROPDATA WHERE DATE BETWEEN 1999 AND 2001 ORDER BY avgYield"
avg_yield_df<-sqlQuery(conn,avg_yield_query)
avg_yield_df
```


<style>
.list-inline {list-style: none; margin:0; padding: 0}
.list-inline>li {display: inline-block}
.list-inline>li:not(:last-child)::after {content: "\00b7"; padding: 0 .5ex}
</style>
<ol class=list-inline><li><span style=white-space:pre-wrap>'42S22 -206 [IBM][CLI Driver][DB2/LINUXX8664] SQL0206N  "DATE" is not valid in the context where it is used.  SQLSTATE=42703\n'</span></li><li>'[RODBC] ERROR: Could not SQLExecDirect \'SELECT CROPTYPE FROM CROPDATA WHERE DATE BETWEEN 1999 AND 2001 ORDER BY avgYield\''</li></ol>



## Problem 11

#### Rank the crops and geographies by their average yield (KG per hectare) since the year 2000. Which crop and province had the highest average yield since the year 2000?


### Solution 11



```R

```

## Problem 12

#### Use a subquery to determine how much wheat was harvested in Canada in the most recent year of the data.


### Solution 12



```R

```

## Problem 13

#### Use an implicit inner join to calculate the monthly price per metric tonne of Canola grown in Saskatchewan in both Canadian and US dollars. Display the most recent 6 months of the data.


### Solution 13



```R

```

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
