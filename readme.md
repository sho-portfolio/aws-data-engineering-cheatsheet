# Data Engineering cheatsheet for AWS

## create ec2 instance on aws
- goto the ec2 dashboard
- click 'launch instance'
- check 'free tier only'
- click 'select' on 'amazon linux ami' (preferably the one with AWS-CL, Python etc installed)
- check the 'free tier' instance type (usually t2.micro)
- click 'review and launch'
- click 'launch'
- select 'create a new key pair' from the drop-down
- name it: sho_keypair_YYYYMMDD
- click 'download key pair'
- click 'launch instance'
- click 'view instances'

## modify ec2 instance to enable access to s3
- goto the ec2 dashboard
- select the ec2 instance
- click 'actions' -> 'instance settings' -> 'attach/replace iam role'
- click 'create new iam role'
- click 'create role'
- click 'aws services'
- click 'ec2'
- click 'next: permissions'
- search for s3
- select the 'amazons3fullaccess' policy
- click 'next: tags'
- click 'next: review'
- name the role: 'sho_role_s3fullaccess'
- click 'create role'

## connect to ec2 instance via ssh
- goto the ec2 dashboard
- click 'instances'
- select the ec2 instance
- click 'connect'
- check 'a standard ssh client'
- on mac: launch termina
- type cd /Users/<user-name>/Downloads (or whereever you downloaded the 'key pair' to
- from aws site copy the command in instruction 3, e.g.: ```chmod 400 sho_keypair_20191213.pem```
- paste it into your terminal on your mac and hit enter
- from aws site copy the command in instruction 4 example, e.g.: 
    ```ssh -i "sho_keypair_20191213.pem" ec2-user@ec2-54-161-204-128.compute-1.amazonaws.com```
- paste it into your terminal on your mac and hit enter
- type 'yes' when prompted and hit enter
- you will now be connected to your ec2 instance via ssh
- type ```ls``` or ```pwd``` to check that you have connected successfully
  
## install python3 on ec2 instance
- sudo yum install python36

## install python libraries on ec2 instance
- ```pip install --upgrade --user pip```
- ```pip install --no-cache-dir --user tensorflow```
- ```pip install --user pandas```

## copy file(s) from s3 to ec2 instance
- to see s3 buckets: ```aws s3 ls```
- to copy entire bucket: 
    ```aws s3 sync s3://quantified-self-20191211 .``` (copy to current directory)
    ```aws s3 sync s3://remote_S3_bucket local_directory``` (copy to specified directory)
- to copy a specific file:
    ```aws s3 cp s3://my_bucket/my_folder/my_file.ext my_copied_file.ext```

## copy file(s) from ec2 instance to s3
- ```aws s3 cp my_copied_file.ext s3://my_bucket/my_folder/my_file.ext```

## run python code on ec2 instance
- python3 mypythonfile.py

## install mysql on ec2 instance
https://medium.com/@chamikakasun/installing-mysql-in-an-ec2-instance-55d6a3e19caf
- ```sudo su```
- ```yum update -y```
- ```yum install mysql-server```
- ```service mysqld start```

## create mysql instance on aws
- goto rds
- click 'create database'
- select 'standard create'
- select 'mysql'
- select the 'free tier' within 'templates'
- set the 'db instance identifier' to: sho_db_mysql_YYYYMMDD
- set username and password
- within the 'connectivity' section expand 'additional connectivity configuration'
- check 'yes' for 'public access' (this wil allow you to connect from outside the vpn i.e. from a client on your mac)
- uncheck 'enable automatic backups' (so you're not waiting for completion of this whilst you're working)
- click 'create database'

## modify security on mysql instance to allow for access from ec2 instance
- goto rds
- click on 'databases'
- select your database instance
- under 'connectivity and security' -> 'security' click on the 'vpc security groups' link
- in the inbound tab click 'edit' ensure the source is 'anywhere' (#todo how do I get the IP of my EC2 instance so that I can just provide custom access to that instead of to the whole internet?)


## connect to mysql instance from ec2 instance
- goto rds
- click on 'databases'
- select your database instance
- copy the 'endpoint' value and paste it into the <enndpoint> command below
    ```mysql -h <end-point> -P 3306 -u <user-name> -p```
    ```mysql -h database-1.c4lxnjkjrvz9.us-east-1.rds.amazonaws.com -P 3306 -u admin -p```
- paste the command into the ssh ec2 session on your mac terminal and enter the password when prompted
- once mysql has launched test it by typing ```show databases;``` and hitting enter
- type ```quit;``` to exit mysql

## modify security on mysql instance to allow for access from local-machine
- goto rds
- click on 'databases'
- select your database instance
- under 'connectivity and security' -> 'security' click on the 'vpc security groups' link
- in the inbound tab click 'edit' ensure the source is 'anywhere' or 'myip' 

## install a mysql client (mySqlWorkbench) on your local-machine (mac)
- download and install mysqlworkbench from https://www.mysql.com/products/workbench/
- launch it (mysqlworkbench)

## connect client (mySqlWorkbench) on local-machine to mysql instance
- goto 'database' -> 'manage connections'
- click 'new'
- connection name: <enter a name>
- connection method: 'standard (tcp/ip)
- hostname: <endpoint> (the endpoint of the database instance created on aws)
- port: 3306
- username: the username you created when setting up the mysql instance on aws
- click 'test connection'
- click 'close'
- goto 'database' -> 'connect to database'
- select the connection you just created from 'stored connection' dropdown and click ok
- goto the 'schemas' tab and you will see a list of al the databases
- type 'show databases;' and execute to check that you can execute sql

## install a mysql engine and client (mySqlWorkbench) on your local-machine (mac)
[https://dev.mysql.com/doc/refman/5.7/en/osx-installation-pkg.html]
- download and install mySql community server from https://dev.mysql.com/downloads/mysql/
- follow the instructions in the link above to install (all straight forward)
- after the installation completes yo can goto apple -> system preferences (you will see a MySql logo here)
- goto terminal and type: ```/usr/local/mysql/bin/mysql -u root -p```
- note: the above doesnt work for ```LOAD DATA LOCAL INFILE```, use this instead ```/usr/local/mysql/bin/mysql -u root -p --local-infile quantified-self;```
- type: ```show databases;``` to see a list of databases


## upload csv file from local machine to mysql server (locally installed) [with example]
- via terminal connect to your mysql server (```/usr/local/mysql/bin/mysql -u root -p --local-infile quantified-self;```) (this bit is needed to upload files using LOAD DATA)
- create a database & table to which you can import your csv data
```
create database quantified-self;
use quantified-self;
create table AppleHealthData(Source VARCHAR(25), XMLType VARCHAR(250), type VARCHAR(250), unit VARCHAR(100), value  FLOAT, sourcename VARCHAR(250), sourceversion  VARCHAR(250), device  VARCHAR(250), creationDate VARCHAR(50), startDate VARCHAR(50), endDate VARCHAR(50), year INT, month INT, day INT, hour INT, wkday INT);
```
```
LOAD DATA LOCAL INFILE '/Users/shomunshi/Library/Mobile\ Documents/com\~apple\~CloudDocs/_PROJECTS/QuantifiedSelf/_TransformedData/Data_AppleHealth_Record_20191218-131754.csv' 
INTO TABLE AppleHealthData
FIELDS TERMINATED BY '|' 
ENCLOSED BY '"' 
LINES TERMINATED BY '\n'
IGNORE 1 LINES;
```
If you get this error: "The used command is not allowed with this MySQL version" you will need to follow the below instructions:
[source of instructions for knowing to create a my.cnf file: https://stackoverflow.com/questions/18437689/error-1148-the-used-command-is-not-allowed-with-this-mysql-version]
[source of instructions for knowing where to put the my.cnf file: https://dev.mysql.com/doc/refman/8.0/en/option-files.html]
- launch terminal
- type: ```cd /```
- type: ```cd etc```
- type: ```ls``` (check that my.cnf does does not already exist)
- type: ```sudo nano my.cnf```
- add the below 2 lines to the file:
	[mysqld]
	loose-local-infile = 1
- exit and save (control + X)
- stop and start your mysql server [http://osxdaily.com/2014/11/26/start-stop-mysql-commands-mac-os-x/]
- ```sudo /usr/local/mysql/support-files/mysql.server stop```
- ```sudo /usr/local/mysql/support-files/mysql.server start```
- (or you can stop and start from 'system preferences' 
- then rerun the LOAD DATA command (you may need to log into mySql again before you run it)

## connect mySqlWorkbench to mySql server (both on local machine)
- launch mySqlWorkbench
- goto 'database' > 'manage connections...'
- click 'new'
- provide a connection name (can be anything)
- leave everything else as defualt (i.e: hostname=127.0.0.1, port=3306)
- click 'test connection'
- when you are on the splash screen, right-click anywhere click 'rescan for local...' and your connection should appear


## upload csv file from ec2 instance to mysql instance (table)
- launch mysql on ec2 instance
- execute the following commands:
- ```create database quantified;```
- ```use quantified;```
- ```create table tbl01 (INPUT_FILENAME varchar(100), XMLType varchar(100), type varchar(100), unit varchar(100), value varchar(100), sourcename varchar(100), sourceversion varchar(100), device varchar(500), creationDate varchar(100), startDate varchar(100), endDate varchar(100), year varchar(100), month varchar(100), day varchar(100), hour varchar(100), wkday varchar(100))```
- ```LOAD DATA LOCAL INFILE '/home/ec2-user/Structured/Data_AppleHealth_Record_20191213-104755.csv' INTO TABLE tbl01;```

## create a glue job to import data from xml to csv

## create a glue job to import data from csv to mysql

## create sql server-instance on aws

## connect to sql-server instance from ec2 (windows server ssms) / local-machine client

## create and run a ssis job on sql-server instance

## copy file(s) from github / internet to s3 via local-machine / ec2 instance

## use athena to query a csv file on s3
- https://www.youtube.com/watch?v=Oar6zUD5ga8

## redshift

## use lambda to run sql

## lake formation

## open a windows ec2 instance from a mac (useful for sq server management studio & ssis)
- download and install/setup (follow the prompts) 'microsoft remote desktop 10' from the app store
- goto the ec2 instances dashboard
- select your windows ec2 instance
- click 'connect'
- follow the instructions

- install visual studio
- goto maange extensions
- search for integration services
- install it


## install a developer edition of sql server with ssis on an ec2 instance
- launch a windows ec2 (free tier)
- turn off the ie warnings and blockers
-   launch server manager
-   click 'configure this local server'
-   click link next to 'ie enhanced security configuration'
-   check off for both options and hit ok and (re)leanch ie
- goto www.microsoft.com/en-us/sql-server/developer-tools
- download 'sql server 2017 developer edition'
- run the download
- select 'custom' for the installation type
- click install (~5min to d/l and get to the next step)
- click on 'installation' on the left menu
- click 'next' | accept licence | click 'next'...
- at the 'feature selection' screen, check the following options:
-   Database Engine Services
-   Full-Text and Semantic Extractions for Search
-   Client Tools Connectivity
-   Integration Services (don't need the 'Scale Out...' options)
- click 'next' and follow prompts until you get to 'database engine configuration'
- at 'database engine configuration' click 'add current user' and hit next then install (~5min)

- now install the front end tools (ssms)
- select 'install sql server management tools' (from the already open 'sql server installation center' if not open goto start -> microsoft sql server 2017 -> sql server 201 installation center
- from the website 'download ssms' and install it
- you'll be forced to restart your ec2 instance (don't conect too quickly as the ec2 instance needs to restart fully before you can connect and it takes ~2 mins
- launch sql server management studio and connect!

- download 'visual studio 2019 community edition' and begin install
- when you get to the workloads screen check the 'data storage and processing' toolset
- click install (~2hrs)
- to launch goto start->visual studio 2019
- file -> new -> project
- open without code



## launching an ec2 instance using your own saved image
goto ec2 -> images -> ami
select your image
click 'launch'
follow the promopts (note it may take about 5 mins to initialize before you can connect)

## create an image of an ec2 instance (useful if you've installed things not available by default)

## create a connection from python to a mysql database table
install mysql connector for python: ```pip3 install mysql-connector-python```
