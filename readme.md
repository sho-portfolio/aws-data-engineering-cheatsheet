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

## run python code on ec2 instance

## install mysql on ec2 instance

## create mysql instance on aws

## connect to mysql instance from ec2 instance

## modify security on mysql instance to allow for access from local-machine

## connect client on local-machine to mysql instance (mySqlWorkbench)

## modify security on mysql instance to allow for access from ec2 instance

## copy file(s) from s3 to ec2 instance

## copy file(s) from ec2 instance to s3

## upload csv file from ec2 instance to mysql instance (table)

## create a glue job to import data from xml to csv

## create a glue job to import data from csv to mysql

## create sql server-instance on aws

## connect to sql-server instance from ec2 (windows server ssms) / local-machine client

## create and run a ssis job on sql-server instance

## copy file(s) from github / internet to s3 via local-machine / ec2 instance

## use athena to query xml file on s3

## redshift

## use lambda to run sql

## lake formation

