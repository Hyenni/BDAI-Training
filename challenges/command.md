
## 1 - a

### 1)

groupadd seoul \
groupadd gyeonnggi

useradd bdai -g seoul -u 3900 \
useradd isaac -g gyeonggi -u 3800

gpasswd -a bdai seoul \
gpasswd -a isaac gyeonggi

![test1](https://user-images.githubusercontent.com/33708512/57597131-17191580-7589-11e9-8ccb-ea3cc3885faa.PNG)

![test1-1](https://user-images.githubusercontent.com/33708512/57597132-17b1ac00-7589-11e9-8026-8d082c6e827b.PNG)


### 2)


![test2-0](https://user-images.githubusercontent.com/33708512/57597134-17b1ac00-7589-11e9-91bd-281fa60ffe11.PNG)



### 3)

cat /etc/centos-release

### 4)

df -h

![test2](https://user-images.githubusercontent.com/33708512/57597133-17b1ac00-7589-11e9-9a05-836fae2fa30e.PNG)

### 5)

yum repolist enabled


![test3](https://user-images.githubusercontent.com/33708512/57597135-17b1ac00-7589-11e9-9aaa-2876c0f5e425.PNG)

### 6)

cat /etc/passwd

![test4](https://user-images.githubusercontent.com/33708512/57597136-184a4280-7589-11e9-9d43-c846e3d3f80a.PNG)

### 7) 

cat /etc/group


![test5](https://user-images.githubusercontent.com/33708512/57597138-184a4280-7589-11e9-874a-3bdb1a3feb99.PNG)

### 8)

getent group seoul \
getent passwd bdai


![test6](https://user-images.githubusercontent.com/33708512/57597139-184a4280-7589-11e9-8520-36e46a3d5f75.PNG)

------------

## 1 - b

### 2)

cat /etc/hostname

mysql -V

show databases;


![test7](https://user-images.githubusercontent.com/33708512/57597140-184a4280-7589-11e9-92de-7423d065f3c6.PNG)

------------


## 1 - c

### 1)

wget https://archive.cloudera.com/cm5/installer/5.15.0/cloudera-manager-installer.bin

### 3)

![test14](https://user-images.githubusercontent.com/33708512/57598229-606b6400-758d-11e9-8bb7-e5984f5f7632.PNG)

![test15](https://user-images.githubusercontent.com/33708512/57599210-eb9a2900-7590-11e9-87a6-d44873f65be0.PNG)



-------------

## 2

### 1)

create database test;


![test9](https://user-images.githubusercontent.com/33708512/57597141-18e2d900-7589-11e9-9771-69c458bac507.PNG)

### 2)

![test12](https://user-images.githubusercontent.com/33708512/57598227-5fd2cd80-758d-11e9-9c68-7410bc0ccd9f.PNG)

![test13](https://user-images.githubusercontent.com/33708512/57598228-606b6400-758d-11e9-9eba-41ff00992ffb.PNG)

### 3)

create user 'Cert'@'%' identified by 'Cert';

grant all privileges on test.* to 'Cert'@'%';


![test11](https://user-images.githubusercontent.com/33708512/57597142-18e2d900-7589-11e9-85e5-8d6968207448.PNG)

![test11-1](https://user-images.githubusercontent.com/33708512/57597143-18e2d900-7589-11e9-968b-9ac2da26c26f.PNG)

-------------

## 3 

### 1 - 3)

~~~ sql

sqoop import --connect jdbc:mysql://localhost/test --table authors  --target-dir /user/cert/authors --as-parquetfile --username cert --password cert

sqoop import --connect jdbc:mysql://localhost/test --table posts --target-dir /user/cert/posts --as-parquetfile --username cert --password cert

~~~

### 4 - 7)

~~~ sql

CREATE EXTERNAL TABLE authors 
(
    id int,
    first_name string,
    last_name string,
    email string,
    birthdate date,
    added timestamp
) 
STORED AS PARQUET LOCATION '/user/cert/authors';

CREATE MANAGED TABLE posts 
(
    id int,
    author_id int,
    title string,
    description string,
    content string,
    date date 
) 
STORED AS PARQUET LOCATION '/user/cert/posts';

~~~ 
-------------

## 4

### 1 - 2)

~~~ sql

select a.id, a.first_name fname, a.last_name lname, count(a.id) num_posts 
from authors a join posts p on a.id = p.author_id group by a.id;

~~~ 

### 3)

~~~ sql

INSERT OVERWRITE LOCAL DIRECTORY '/user/cert/result/'
ROW FORMAT DELIMITED
FIELDS TERMINATED BY '\t'
STORED AS TEXTFILE
SELECT a.id, a.first_name fname, a.last_name lname, count(a.id) num_posts from authors a join posts p on a.id = p.author_id group by a.id;

~~~

-------------

## 5

~~~ sql

create table result
(
id int,
fname string,
Lname string,
num_posts int
);


sqoop export -connect:mysql://localhost/test --username cert --password cert --table results --export-dir /user/cert/result

~~~
-------------