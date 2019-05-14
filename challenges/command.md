
### environment settings

  * master01 - 192.168.56.101
    * datanode01 - 192.168.56.102
    * datanode02 - 192.168.56.103
    * datanode03 - 192.168.56.104
    * netmask - 255.255.255.0
    * gateway - 192.168.56.1
    * dns - 164.124.101.2

----------------

## 1. Create a CDH Cluster

## a. Linux setup

### 1) Add the following linux accounts to all nodes.


groupadd seoul \
groupadd gyeonnggi

useradd bdai -g seoul -u 3900 \
useradd isaac -g gyeonggi -u 3800

gpasswd -a bdai seoul \
gpasswd -a isaac gyeonggi

![test1](https://user-images.githubusercontent.com/33708512/57597131-17191580-7589-11e9-8ccb-ea3cc3885faa.PNG)

![test1-1](https://user-images.githubusercontent.com/33708512/57597132-17b1ac00-7589-11e9-8026-8d082c6e827b.PNG)


### 2) List the your instances by IP address and DNS name


![test2-0](https://user-images.githubusercontent.com/33708512/57597134-17b1ac00-7589-11e9-91bd-281fa60ffe11.PNG)


### 3) List the Linux release you are using

cat /etc/centos-release

### 4) List the file system capacity for the master node

df -h

![test2](https://user-images.githubusercontent.com/33708512/57597133-17b1ac00-7589-11e9-9a05-836fae2fa30e.PNG)

### 5) List the command and output for yum repolist enabled

yum repolist enabled


![test3](https://user-images.githubusercontent.com/33708512/57597135-17b1ac00-7589-11e9-9aaa-2876c0f5e425.PNG)

### 6) List the /etc/passwd entries for isaac and bdai 

cat /etc/passwd

![test4](https://user-images.githubusercontent.com/33708512/57597136-184a4280-7589-11e9-9d43-c846e3d3f80a.PNG)

### 7) List the /etc/group entries for seoul and gyeonggi

cat /etc/group


![test5](https://user-images.githubusercontent.com/33708512/57597138-184a4280-7589-11e9-874a-3bdb1a3feb99.PNG)

### 8) List output of the following commands

getent group seoul \
getent passwd bdai


![test6](https://user-images.githubusercontent.com/33708512/57597139-184a4280-7589-11e9-8520-36e46a3d5f75.PNG)

------------

## b. Install a MySQL server

### 1) Use MariaDB as the database. 


### 2) A command and output that shows the hostname of your database server, database server version, lists all the databases

cat /etc/hostname

mysql -V

show databases;


![test7](https://user-images.githubusercontent.com/33708512/57597140-184a4280-7589-11e9-92de-7423d065f3c6.PNG)

------------


## c. Install Cloudera Manager

### 1) CDH version 5.15

wget https://archive.cloudera.com/cm5/installer/5.15.0/cloudera-manager-installer.bin

### 3) Make sure that the following services are installed (HDFS, YARN, Sqoop, Hive, Impala, HUE)

![test14](https://user-images.githubusercontent.com/33708512/57598229-606b6400-758d-11e9-8bb7-e5984f5f7632.PNG)

![test15](https://user-images.githubusercontent.com/33708512/57599210-eb9a2900-7590-11e9-87a6-d44873f65be0.PNG)

![finish_install](https://user-images.githubusercontent.com/33708512/57668228-98cc7a00-7641-11e9-9208-07500077bd23.PNG)


### 4) Hue에서 Cert user 생성

HDFS home directory 만들어짐

-------------

## 2. In MySQL create the sample tables that will be used for the rest of the test

### 1) Create database and name it "test"

create database test;


![test9](https://user-images.githubusercontent.com/33708512/57597141-18e2d900-7589-11e9-9771-69c458bac507.PNG)

### 2) Create 2 tables in the test databases: authors and posts

.sql 파일이 있는 곳에 가서 mysql 접속 후 source 명령어 실행

![test12](https://user-images.githubusercontent.com/33708512/57598227-5fd2cd80-758d-11e9-9c68-7410bc0ccd9f.PNG)

![test13](https://user-images.githubusercontent.com/33708512/57598228-606b6400-758d-11e9-9eba-41ff00992ffb.PNG)

### 3) Create and grant user "Cert"

create user 'Cert'@'%' identified by 'Cert';

grant all privileges on test.* to 'Cert'@'%';


![test11](https://user-images.githubusercontent.com/33708512/57597142-18e2d900-7589-11e9-85e5-8d6968207448.PNG)

![test11-1](https://user-images.githubusercontent.com/33708512/57597143-18e2d900-7589-11e9-968b-9ac2da26c26f.PNG)

-------------

## 3. Extract tables authors and posts from the database and create Hive tables

### 1 - 3) Use sqoop to import the data from authors and posts + parquet format

실행하기 전에 hue에서 Cert user 생성하여 hdfs home directory 만들어줘야함 \
++ linux, mysql에 Cert 계정 있어야 함!!!!

~~~ sql

sqoop import --connect jdbc:mysql://192.168.56.101:3306/test --table authors  --target-dir /user/Cert/authors --as-parquetfile --username Cert --password Cert

~~~

![import1](https://user-images.githubusercontent.com/33708512/57668231-9a963d80-7641-11e9-882e-3643b63538df.PNG)


~~~ sql

sqoop import --connect jdbc:mysql://192.168.56.101:3306/test --table posts  --target-dir /user/Cert/posts --as-parquetfile --username Cert --password Cert

~~~

![import2](https://user-images.githubusercontent.com/33708512/57668481-725b0e80-7642-11e9-8dc8-748e826985f0.PNG)


### 4 - 7) In Hive, create 2 tables: authors and posts

~~~ sql

CREATE EXTERNAL TABLE authors 
(
    id int,
    first_name string,
    last_name string,
    email string,
    birthdate string,
    added timestamp
)
ROW FORMAT SERDE 'parquet.hive.serde.ParquetHiveSerDe'
STORED AS PARQUET LOCATION '/user/Cert/authors';

CREATE TABLE posts 
(
    id int,
    author_id int,
    title string,
    description string,
    content string,
    date string 
)
ROW FORMAT SERDE 'parquet.hive.serde.ParquetHiveSerDe'
STORED AS PARQUET
LOCATION '/user/Cert/posts';

~~~ 

![create1](https://user-images.githubusercontent.com/33708512/57670952-7b50dd80-764c-11e9-94d5-45eb28dcd3d2.PNG)

~~~ sql

select id, first_name, last_name from authors limit 10;

~~~

![create2](https://user-images.githubusercontent.com/33708512/57670950-7b50dd80-764c-11e9-82e2-f87ce14d57dc.PNG)


~~~ sql

select id, author_id, title from posts limit 10;

~~~

![create3](https://user-images.githubusercontent.com/33708512/57670951-7b50dd80-764c-11e9-8385-340c966d309e.PNG)

-------------

## 4. Create and run a Hive query

### 1 - 2) Create a query that counts the number of posts each author has created

~~~ sql

select a.id, count(a.id) num_posts 
from authors a join posts p on a.id = p.author_id group by a.id;

~~~ 

![join1](https://user-images.githubusercontent.com/33708512/57673705-e3f18780-7657-11e9-9fce-ebdb89df2c22.PNG)



### 3) The output of the query should be saved in Cert home directory

results directory 먼저 생성해야함

~~~ sql

INSERT OVERWRITE DIRECTORY '/user/Cert/results/'
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
STORED AS TEXTFILE
SELECT a.id, count(a.id) num_posts 
from authors a join posts p on a.id = p.author_id group by a.id;

~~~

![join2](https://user-images.githubusercontent.com/33708512/57673706-e3f18780-7657-11e9-8511-a6dce1be88ad.PNG)

-------------

## 5. Export the data from above query to MySQL

### 1) Create a MySQL table and name it "results"

test database에 스키마 생성

~~~ sql

reate table results
(
    Id int,
    fname varchar(15),
    Lname varchar(15),
    num_posts int
);

~~~

### 2) Export into MySQL the result of your query 

~~~ sql

sqoop export -connect jdbc:mysql://192.168.56.101:3306/test --input-fields-terminated-by ',' --username Cert --password Cert --table result --export-dir /user/Cert/result/000000_0

~~~

![export1](https://user-images.githubusercontent.com/33708512/57673966-c83ab100-7658-11e9-8d75-a6df43a5ef4f.PNG)

![export2](https://user-images.githubusercontent.com/33708512/57673967-c83ab100-7658-11e9-9f3d-574ae882740d.PNG)

-------------
