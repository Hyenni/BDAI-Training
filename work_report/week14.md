work report (week14)
=============

### **<U>CJ VC Data Handling</U>**


<6.3 issue>
-------------


### **1. HDFS disk space**

https://www.cloudera.com/documentation/enterprise/5-14-x/topics/admin_hdfs_quotas.html

1. HDFS - File browser - Quota Management 

/user/hinkim directory의 disk space를 500M 로 제한

2. 229M realtime.csv 파일 (실제 크기: 229 * 3) upload 실패

![disk_limit2](https://user-images.githubusercontent.com/33708512/58783357-af9c3600-861b-11e9-8860-1d7c529c2aca.PNG)



3. HDFS - File browser - Quota Management
disk space 1G 로 늘려 줌

4. 2번과 같은 명령어 실행 
$ hdfs dfs -put /home/hinkim/realtime.csv /user/hinkim

5. success
 
![disk_limit3](https://user-images.githubusercontent.com/33708512/58783358-af9c3600-861b-11e9-8f59-8f31eaa92023.png)

![disk_limit4](https://user-images.githubusercontent.com/33708512/58783359-af9c3600-861b-11e9-90be-22cee08a06aa.png)

<br>


### **2. Impala Dynamic Resource Pool**

------ topaz ------
----


* 그룹 혹은 사용자에 따라 resource pool을 다르게 할당하는 것 (일종의 partitioning 인데, 하지 않으면 어떤 한 쿼리가 대부분의 resource를 사용하여 다른 쿼리들이 작동하지 않을 수 있음)

1. 제한을 걸기 전 상황

![topaz_pool1](https://user-images.githubusercontent.com/33708512/58843891-85945380-86af-11e9-8456-d962656cbe26.PNG)

2. impala dynamic resource pool 설정 

hinkim user로, max memory 1M 제한

![topaz_pool2](https://user-images.githubusercontent.com/33708512/58843892-85945380-86af-11e9-83b0-dc28a2574789.PNG)

![topaz_pool3](https://user-images.githubusercontent.com/33708512/58843893-85945380-86af-11e9-913a-a402ebb44d8d.PNG)


3. 그 후 같은 쿼리를 날렸을 때, 할당된 pool size보다 크다고 뜸

![topaz_pool4](https://user-images.githubusercontent.com/33708512/58843894-85945380-86af-11e9-8941-9fdc5f57c13f.PNG)


4. minimum query memory 1M ~ maximum query memory 15M

![topaz_pool5](https://user-images.githubusercontent.com/33708512/58844553-a611dd00-86b2-11e9-8a3b-524695a7702f.PNG)

5. 그 후 같은 쿼리를 날렸을 때, 적어도 40M의 query memory가 필요하다고 뜸

![topaz_pool6](https://user-images.githubusercontent.com/33708512/58844554-a611dd00-86b2-11e9-91e7-4c26061947da.PNG)


6. max running queries 2로 설정

![topaz_pool7](https://user-images.githubusercontent.com/33708512/58846711-26d4d700-86bb-11e9-9be2-185dfc44ffd0.PNG)

-> 2개까지는 잘 실행이 되는데 
-> 1개를 더 돌리면 query_id까지 출력되며 어떤 한 쿼리가 끝날때까지 waiting 
-> 나머지 한 쿼리가 끝난 후에야 실행됨


<br>
<br>

------------

<6.4 issue>
-------------

### **1. Impala Dynamic Resource Pool**


------ ruby --------
---


1) Max Memory

최대 메모리를 1MB로 설정 후 진행

![ruby_pool5](https://user-images.githubusercontent.com/33708512/58855694-a6729e00-86db-11e9-8f68-976e83027458.PNG)


<br>

2) Minimum (Maximum) Query Memory Limit 

Max Memory와 비교해서 설명하자면,

ex) 

Cluster : 100G
Host: 5개
Minimum: 2G
Maximum: 10G

-> 이 상태에서 다양한 수의 쿼리를 동시에 실행할 수 있다.  이 때, 한 host에서 2GB인 작은 query를 최대 10개까지 실행하거나 메모리 제한이 10GB인 큰 query를 2개까지 실행할 수 있다. 

-> 전체 cluster에서 사용 가능한 메모리가 있어도  하나의 host에서 10G가 넘는 query는 실행할 수 없게 된다.

![ruby_pool7](https://user-images.githubusercontent.com/33708512/58855710-ae324280-86db-11e9-83a2-1df8f55dd38e.PNG)


<br>

3) Max Running Queries

동시에 실행할 수 있는 query를 2로 설정 후 진행

-> Max Running Queries를 2로 설정한 이후, 
실행 시간이 다소 걸리는 query를 날렸을 때의 상황

![ruby_query](https://user-images.githubusercontent.com/33708512/58855717-b1c5c980-86db-11e9-8349-d2a7a144453a.PNG)

putty를 세 개 띄운 후 진행하였는데 2개까지는 문제 없이 동작하며
3번 째 query 실행은 위와 같이 대기중인 것을 확인

또한, 이전에 동작하던 query가 끝나는 순간 대기 중인 아래의 query문이 
동작하는 것을 확인


<br>
<br>

------------

<6.5 issue>
-------------

### **1. trendminer**

## hive table

beeline \
beeline> !connect jdbc:hive2://10.0.2.101:10000 admin admin 

<br>

* hive table (vibration controller)

~~~ sql

set hive.supports.subdirectories=true;
set mapred.input.dir.recursive=true;

create external table if not exists test_vmm2 (
 xtime timestamp,
 ch1 double, 
 ch2 double
)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
STORED AS textfile 
LOCATION '/tmp/out/hinkim_vmmtest/';

~~~


![hive1](https://user-images.githubusercontent.com/33708512/58932064-19901900-879d-11e9-98f8-bf92194742cc.PNG)

![hive2](https://user-images.githubusercontent.com/33708512/58932065-19901900-879d-11e9-8927-ae9d81f21703.PNG)

![hive3](https://user-images.githubusercontent.com/33708512/58932066-19901900-879d-11e9-8d9a-097dfa2b98d1.PNG)

~~~ sql

cf. 정렬해서 마지막 10개 확인 (총 record 개수를 알고 있다면 위와 같이 command를 사용하는 게 빠름)

select * from test_vmm2 order by xtime desc limit 10;

~~~

![hive4](https://user-images.githubusercontent.com/33708512/58932104-42181300-879d-11e9-96a2-a6cff0bdb0c1.PNG)




<br>

----------------


### **2. 시계열 데이터**

* vibration controller data의 일부를 timestamp 기준으로 시계열 차트 모습 \
(일단, 약 10분정도의 데이터로 test 진행함) 


![시계열](https://user-images.githubusercontent.com/33708512/58943784-74386d80-87bb-11e9-941f-a85029aa5a02.PNG)



## Vibration controller 
## 6/5 오후 3시 4분부터 약 10분간 컴퓨터 자체의 오류로 인해 다운돼 데이터 수집 X

<br>

## 6/7 오전 10시 5분부터 약 15분간 서버 연결이 안되는 오류로 인해 데이터 수집 X - 기다리니까 다시 서버와 연결이 돼서 다시 프로그램을 돌려 데이터 수집 시작함


## -> 이와 관련해서 opc-ua pipeline log 수집과, wireshark에서 전체 packet 저장하였음. (20190607_PM1552.pcapng) 


<br>

------------

<6.7 issue>
-------------

### **1. trendminer**

## hive table

### 1) vmmfactor - ch1 & ch2 string vs double (정확도 차이)

~~~ sql

set hive.supports.subdirectories=true;
set mapred.input.dir.recursive=true;

create external table if not exists test_vmm (
 xtime string,
 ch1 string, 
 ch2 string
)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
STORED AS textfile 
LOCATION '/tmp/out/hinkim_vmmtest/';


create external table if not exists test_vmm2 (
 xtime timestamp,
 ch1 double, 
 ch2 double
)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
STORED AS textfile 
LOCATION '/tmp/out/hinkim_vmmtest/';

~~~

![hive6](https://user-images.githubusercontent.com/33708512/59074334-efa63600-8905-11e9-85e4-b77042158cfa.PNG)

![hive5](https://user-images.githubusercontent.com/33708512/59074336-efa63600-8905-11e9-8722-9a6a286d5296.PNG)


![hive7](https://user-images.githubusercontent.com/33708512/59074873-98559500-8908-11e9-9f3f-c95889dbd65d.PNG)

![hive8](https://user-images.githubusercontent.com/33708512/59074886-b8855400-8908-11e9-817c-431bec07382b.PNG)

-> 정확도 문제 없음

<br>
<br>

### 2) vmmfactor - time timestamp vs string (어디부터 어디까지 where 옵션을 줬을 때 동작하는 지)

~~~ sql

create external table if not exists test_vmm3 (
 xtime string,
 ch1 string, 
 ch2 string
)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
STORED AS textfile 
LOCATION '/tmp/out/hinkim_vmmtest/timestamp/';


create external table if not exists test_vmm4 (
 xtime timestamp,
 ch1 double, 
 ch2 double
)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
STORED AS textfile 
LOCATION '/tmp/out/hinkim_vmmtest/timestamp/';

~~~

string 일 때

![hive11](https://user-images.githubusercontent.com/33708512/59076567-b7a4f000-8911-11e9-9b2a-88875a3d9c04.PNG)

timestamp 일 때

![hive12](https://user-images.githubusercontent.com/33708512/59076569-b7a4f000-8911-11e9-996e-79d87f0ba00e.PNG)

-> 둘 다 문제 없이 query 동작 


<br>
<br>

### **2. git**

* private으로 isaac-bdai/trendminer join 완료

* private repository 이지만, key를 공유할 필요 없이 clone path를 그대로 따라서 치면 정상 동작함


~~~ sql

$ git clone <path>

$ ls
trendminer/

$ cd trendminer/

$ vi GIT_SETUP.md

$ git add GIT_SETUP.md

$ git commit -m 'update GIT_SETUP.md'

$ git push origin master

~~~

![git2](https://user-images.githubusercontent.com/51468250/59091793-0cb12800-894b-11e9-8f65-8227d1fcb131.PNG)



<br>

----------------
----------------

NEXT_WEEK)
----------------
1. trendminer 설치