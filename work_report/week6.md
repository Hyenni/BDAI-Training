work report (week6)
=============


<4.8 issue>
-------------
### **1. Zoomdata**
* Impala visualization \
-> Hive와 meta store 공유 \
-> Hive에 table이 있어야함 \
-> HDFS에 있는 train.csv 파일을 Hive table로 만들어서 생성한 상태


### **2. sqoop**
* split-by
* import 할 때 어떤 단위로 넘기는 것이 빠르고 효율적인지 알아보기 (table 단위, database 단위) \
-> sqoop import-all-tables \
-> sqoop import --table
* mapper 개수 지정\
-> sqoop --num-mappers N

1. import to HDFS
2. import to Hive
3. sql: oracle, Netezza, mysql
4. primary key 있을 때, 없을 때


http://hochul.net/blog/datacollector_apache_sqoop_from_rdbms2/

https://sqoop.apache.org/docs/1.4.1-incubating/SqoopUserGuide.html

http://develop.sunshiny.co.kr/941


### **3. StreamSets**
* StreamSet: 데이터를 수집하기 위한 통합 IDE로 [Any-to-Any 파이프라인] 설계, 테스트, 배포 및 관리 등을 용이하게 하기 위한 툴

* Pipeline: Streamsets의 기본 단위, 각 pipeline은 원본데이터(Origins)에서 목적지(Destinations)로 연결해주고 필요하다면 처리기(Processor)를 통해 데이터의 형태를 변환해서 목적지로 데이터를 보낸다

* open vmware (cloudera-quickstart-vm-5.13.0-0-vmware) \
-> cd /home/cloudera/streamsets-datacollector-3.1.1.0/bin \
-> ./streamsets dc \
-> url 

* 9시반, 정자역 SK-tower


### <twitter.json>

<U>local directory에 있는 twitter.json 파일을 가공하여 hdfs에 저장하려고 하는 pipeline</U>을 만들어보도록 하겠습니다

1. directory 
* file \
-> /home/cloudera/training_materials/devsh/data \
-> twitter.json

* data format \
-> JSON \
-> 500000

2. Field Flattener \
-> record들을 일렬로 늘어뜨리는 역할을 하는데 이것은 이후 preview를 통해 정확히 어떤 역할을 하는지 보여드리도록 하겠습니다

3. Field Remover \
-> option이 세가지 존재하는데 첫 번째 Keep은 입력한 field만 남기는 것이고 두 번째 Remove는 입력한 field만 삭제하는 것이고, 세 번째 Remove는 입력한 field를 삭제하긴 하는데 만약 null 값이 아니면 삭제하지 않습니다. 저희는 필요한 field만 추출하는 첫 번째 option을 사용할 것입니다.
* Keep Listed Fields (선택한 것만 남겨두는 option) \
-> /created_at, /timestamp_ms, /text, /id, /'user.name', /lang

4. Field Renamer
* /created_time, /created_unixtime, /msg, /twit_id, /displayname,

5. Field Order
* 순서대로 
* missing Fields \
-> Use default value \
-> NULL \
-> STRING

6. Field Type Converter \
-> conversion method가 두가지 존재하는데 첫 번째 By Field Name은 특정 Field의 type을 변경하는 것이고 두 번째 By Data Type은 어떤 type을 어떤 type으로 모두 변경해주는 option입니다. 저희는 첫 번째 option으로 /create_unixtime을 LONG 타입으로 변경하도록 하겠습니다
* /created_unixtime -> LONG


7. Hadoop FS
* hdfs://192.168.111.136
* data format: JSON

-> 이렇게 pipeline을 작성한 후 validate를 통해 구성한 pipeline이 정상적인지 검사해주며, 옆에 preview 버튼을 통해 실행하면 어떻게 데이터가 흘러가는지 미리 확인할 수 있습니다 (이 때, flattener 기능 확인시킬 것)

-> 그리고 실제로 start를 하면 전송되는 상황을 이렇게 chart형식으로 확인할 수 있습니다

---------------------------------------------------------------------------------------
 
### kafka producer
1. directory 
* /home/cloudera/training_materials/devsh/data/weblogs \
-> * \
-> 10000

* data format \
-> Text \
-> 10240


2. kafka producer 
* topic: weblogs 
* data format: Text

### kafka consumer
1. kafka consumer 
* topic: weblogs -> producer에서 설정한 topic과 같게 
* kafka configuration: auto.offset.reset   earliest \
-> consumer는 offset을 통해  어디까지 메시지를 가져왔는지 확인하는데 그것은 계속해서 메시지가 쌓일 경우지만 지금은 정적인 데이터를 사용해서 가져올 것이므로 offset을 제일 처음으로 reset 시킨 후 가져와야 한다
* data format \
-> Text

2. Hadoop FS
* hdfs://192.168.111.136
* data format: Text



--------------------
<4.9 issue>
-------------
### **1. SK CNC StreamSets DEMO**

### Flume
* sink group -> load balancing, fail over \
-> A sink group allows multiple sinks to be treated as one used for failover or load-balancing purposes

* fail over \
-> A failover sink processor will redirect an event to another sink within a sinkgroup if the current sink fails or throws an exception \
-> The selection of the sink is based on a priority parameter



(example)

agent1.sources = source1 \
agent1.sinks = sink1a sink1b \
agent1.sinkgroups = sinkgroup1 \
agent1.channels = channel1
 
  
agent1.sources.source1.channels = channel1 \
agent1.sinks.sink1a.channel = channel1 \
agent1.sinks.sink1b.channel = channel1 
 
 
agent1.sinkgroups.sinkgroup1.sinks = sink1a sink1b \
agent1.sinkgroups.sinkgroup1.processor.type = load_balance
agent1.sinkgroups.sinkgroup1.processor.selector = random
agent1.sinkgroups.sinkgroup1.processor.backoff = true 

or

agent1.sinkgroups.sinkgroup1.processor.type=failover
agent1.sinkgroups.sinkgroup1.processor.priority.sink1a=10
agent1.sinkgroups.sinkgroup1.processor.priority.sink1b=20

 
agent1.sources.source1.type = spooldir \
agent1.sources.source1.spoolDir = /tmp/spooldir 
 
 
agent1.sinks.sink1a.type = avro \
agent1.sinks.sink1a.hostname = localhost \
agent1.sinks.sink1a.port = 10000
 
  
agent1.sinks.sink1b.type = avro \
agent1.sinks.sink1b.hostname = localhost \
agent1.sinks.sink1b.port = 10001
 
 
agent1.channels.channel1.type = file

### telnet
* close : ctrl + ] , quit

### kafka
* 1 partition = 1 consumer

![image](https://user-images.githubusercontent.com/33708512/55841960-6f18c280-5b6c-11e9-9ca5-e53663da582b.png)

Q. 만약 여기서 Consumer Group C가 있고, 그 안에 Consumer가 6명이 있는게 가능한가? 

A. NO, 1 partition = 1 consumer에 따라 partition은 4개이므로 Consumer가 4개보다 많을 수 없음 \
Consumer가 partition 개수보다 많다면 아무것도 일을 안하는 (데이터를 가져오지 않는) Consumer가 생기기 때문에

Q. 놀지 않게 한다면? 쉬지 않고 Consumer 모두 데이터를 가져오게 한다면?

A. 그래도 NO, 저 말 뜻은 결국엔 같은 partition을 둘 이상의 Consumer가 접근한다는 것인데 partition은 순서를 보장하는 장점을 가지고 있는데 이렇게 되면 순서를 믿을 수 없는 상황이 생기기 때문

* kafka의 단점 \
-> coding 

-> flafka

### Flafka
* Flume as Kafka producer

![image](https://user-images.githubusercontent.com/33708512/55842226-81dfc700-5b6d-11e9-8352-6d313b9ae03b.png)

* Flume as Kafka consumer

![image](https://user-images.githubusercontent.com/33708512/55842230-86a47b00-5b6d-11e9-8690-eb4e4a405901.png)

--------------------
<4.10 issue>
-------------

### **1. Zoomdata**
* 문제 해결 \
What: Hive에  table을 넣어 놨는데 impala에서 보이지 않음 \
How: Hive metastore가 refresh 되지 않는 문제
1. Hue에서 Hive 새로고침 버튼 click
2. impala shell에서 INVALIDATE METADATA; \
-> impala에서 이제 새로고침된 table이 보여지며 시각화 성공

* 어제 회의 내용 전달받은 것
1. zoomdata dashborad를 자동으로 refresh하는 option 찾아보기

2. tutorial 문서 작성 (zoomdata에서 impala로 table 가져와서 시각화하는 것)

3. StreamSets -> ELK -> Zoomdata 



### **2. StreamSet -> ELK** 

Y:\08. Work\인턴 김형근 인수인계 자료\edx_Study\Twitter_Data_Analyze

------------------

### **3. cj Sqoop**

### 1. split-by 
-> primary key가 지정되어 있지 않은 table을 import할 때 split의 기분이 될 column을 지정해주거나 mapper 개수를 1로 지정해야 함 \
-> 하지만 대용량 데이터를 다루기 때문에 split-by 사용 권장

* 데이터 가져 오기에 대한 분할 생성에 사용되는 테이블의 열을 지정하는 데 사용, 즉 클러스터로 데이터를 가져 오는 동안 분할에 사용할 컬럼을 지정. --split-by로 지정한 테이블의 특정 열에 있는 값을 기반으로 분할을 작성하는데 없는 경우 primary key로 분할 

* 하지만 primary key가 min 과 max 값 사이에 균일한 값 분포를 갖고 있지 않으면 비효율적임. 따라서 효율적으로 데이터를 가져오기 위해서는 데이터를 적절히 분배하는 column을 지정해야함.

* --boundary-query: 기본적으로 sqoop은 select min((split-by)), max((split-by)) from (table name) 사용하여 경계를 찾아내는데 경우에 따라 이 쿼리는 최적이 아니므로 경계 쿼리 인수를 사용하여 두 개의 숫자 열을 반환하는 임의의 쿼리를 지정할 수 있음 (--split-by가 최적의 성능을 제공하지 못할 때)

https://stackoverflow.com/questions/17923420/what-are-the-following-commands-in-sqoop


<br>

### 2. mapper 개수
* primary key가 있는 경우, 별도의 지정 없이도 primary key를 기준으로 4개의 mapper가 병렬 처리 함

* primary key가 없는 경우, --split-by option으로 병렬 처리를 위한 기준을 설정할 수 있음. 해당 column의 min과 max를 찾아 mapper의 개수만큼 균일한 간격으로 나누어 병렬 처리 수행

ex) mapper가 4개고, --split-by로 어떤 column을 지정했을 때, 그것을 4 구간으로 잘라서 진행 
min 값이 0이고 max 값이 1000이면 0~250, 250~500, 500~750, 750~1000
데이터의 분포가 균일하면 ok이지만 만약 가운데 몰려있다면 비효율적 \
-> 따라서 column의 데이터 분포 또한 고려하여 split-by와 mapper 개수를 지정할 수 있어야함

* --num-mappers or -m \
-> mapper를 늘린다고 무조건 속도가 빠른것이 아니기 때문에 8개, 16개 늘려가면서 확인하는 게 바람직함 \
-> 병렬 처리 수준을 mapreduce 클러스터에서 사용할 수 있는 정도보다 크게 늘리면 속도 저하

<br>

### 3. eval
-> 사용자가 데이터베이스에 대해 간단한 sql query를 빠르게 실행하며, 결과는 console에 출력. 이를 통해서 사용자는 예상한 데이터를 가져올 수 있는지 미리 확인할 수 있음

ex)

* Select ten records from the employees table: 

$ sqoop eval --connect jdbc:mysql://db.example.com/corp \
    --query "SELECT * FROM employees LIMIT 10"

* Insert a row into the foo table:

$ sqoop eval --connect jdbc:mysql://db.example.com/corp \
    -e "INSERT INTO foo VALUES(42, 'bar')"

<br>

### 4. sqoop import TroubleShooting 

* mysql root로 접근하여 import할 때 error 발생 \
-> mysql 들어가서 create user, grant all 한 후 새로 만든 user로 접근하여 import 할 것

![sqoop_error1](https://user-images.githubusercontent.com/33708512/55923991-fd5f7800-5c42-11e9-9757-e7616f79f06b.PNG)


code)

> mysql -u root -p \
> select host, user, password from user; \
> create user 'userid'@'%' identified by 'password'; -> %는 외부 접근 허용 \
> grant all privileges on DB명.* to 'userid'@'%' identified by 'password' ;

<br>

* 이후 import할 때 authenticate error 발생 

![KakaoTalk_20190411_083351556](https://user-images.githubusercontent.com/33708512/55920973-c6369a00-5c35-11e9-8a66-940d0c71160f.jpg)
(Client cannot authenticate via)

-> kinit 

* root 게정 이외 개인 계정으로 sqoop을 시도할 때 인증 error 발생

![kinit_error_sqoop](https://user-images.githubusercontent.com/33708512/56076146-30069d80-5e08-11e9-9412-6ba1341ec21a.PNG)

-> kinit을 해줘도 같은 error 발생


active name node에서 kinit 개인계정 후 klist 바뀐거 확인

<br>

* mariadb에서의 용량과 hdfs에서의 용량이 서로 다름 

    -> 단순히 용량 크기를 확인하는 것에서의 차이점이고 실제적으로 같은 크기가 들어간 것이다 

    1. mariadb에서 테이블 내용 끝과 hdfs에 들어간 파일 끝 내용이 같은지 확인 \
        -> 다름......... 

    2. 반대로 hdfs에 있는 것을 export해서 다시 db에 넣어서 용량 확인 \
    -> sqoop export --connect:mysql://localhost/test --username hinkim -P --table exporttable --export-dir /tmp/hinkim/2/part-m-00000 

-> <U>데이터 손실 없는 것 확인</U>

DB table: test6, 2458MB \
-> import: 1.5G, 204.75sec \
-> export: 2458MB, 1487.96sec

mysql에 있는 test6 table을 hdfs에 전송하고, 다시 그것을 mysql에 export6 table로 저장시킨 상황

![transfer1](https://user-images.githubusercontent.com/33708512/56007975-83e48a00-5d15-11e9-87bd-c3176413a755.PNG)

![transfer2](https://user-images.githubusercontent.com/33708512/56007994-9232a600-5d15-11e9-86ad-9e27e18404ce.PNG)


### 5. import option 실행**
* 약 4G의 데이터는 mapper 2개로 실행하는 것이 가장 빨랐음 \
-> 기본, mapper 1개: 24분 \
-> 기본, mapper 1개 + direct: 21분 \
-> split-by, mapper 4개: 12분 \
-> split-by, mapper 2개: 2분 30초

* 45G의 데이터



--------------------
<4.11 issue>
-------------

### **1. mysql troubleshooting**

*  ERROR 2002 (HY000): Can't connect to local MySQL server through socket '/tmp/mysql.sock' (2) 

my.cnf에서 socket 경로 바꿔주고, link 다시 연결

* mysql backup 해서 새로운 vm 올려서 500기가로 늘림
* 새로 설치 후 backup한 파일을 복구함

mysqldump -u root -p DB명 > 파일명.sql \
mysql -u root -p DB명 < 파일명.sql (단, 그 database가 존재해야한다)

* log 지우기 

mysql 접속 후 show binary logs; 치면 로그가 나옴 (어떤 쿼리를 날렸는지 보관하고 있음) \
이 로그 데이터의 사이즈가 상당히 커서 제일 최근 것만 남기고 지움

purge binary logs to 'mysql_binary_log.xxx';


### **2. cj 상황 파악**

https://www.cloudera.com/documentation/enterprise/5-12-x/topics/cdh_admin_distcp_data_cluster_migrate.html#concept_fx2_t1q_3x

* DBMS -> Hadoop (HDFS)  \
-> Sqoop import 진행 중 

* cluster kerberize \
-> kerberos는 MIT에서 개발한 authentication protocol. ticket을 발행해서 서로 누군지 인증하고 access할 수 있도록 한다. (Active Directory)

* kerberize 된 cluster 간에 DistCP test \
-> DistCP: Distributed copy, 거대한 클러스터 내부/클러스터 간의 복제를 할 때 사용하는 tool.

동일한 CDH 버전: \
hadoop distcp hdfs://(namenode):(port) hdfs://(namenode) \
cf. port 50070은 HDFS의 기본 namenode port

kbr5.conf -> realm (참가하고 있는 그룹, 도메인)

--------------------
<4.12 issue>
-------------

### **1. cj Sqoop <이어서 진행>**

### **2. DistCP**

-HA HDFS 

hadoop distcp hdfs://myclouster/path/to/src webhdfs://httpfs-svr:14000/path/to/dest

-non-HA HDFS

hadoop distcp hdfs://cluster1_nn:8020/path/to/src webhdfs://cluster2_nn:50070/path/to/dest

ruby name node: 10.100.3.217 \
new cluster name node: 10.100.3.245 


### trouble shooting

1.
![distcp_error1](https://user-images.githubusercontent.com/33708512/56027744-9bd9ff00-5d51-11e9-9651-6d20ae153c20.PNG)

-> active name node가 아니라 standby name node로 접근함으로써 발생하는 error. \
cm 가서 active name node ip 다시 확인

2. 

 hadoop distcp hdfs://10.100.3.217:8020/user/root/.staging/job_15549766           43879_0001/job.xml webhdfs://10.100.3.245:8020/user/root/distcp_test

![distcp_error2](https://user-images.githubusercontent.com/33708512/56027889-e6f41200-5d51-11e9-9c75-5c6c52e6e7cc.PNG)

-> http 404에러는 webpage not found 같아서 web 없애고 진행했더니 이문제는 뜨지 않음

3. 
 hadoop distcp hdfs://10.100.3.218/user/root/.staging/job_1554976643879               _0001/job.xml hdfs://10.100.3.245/user/root/distcp_test

![distcp_error3](https://user-images.githubusercontent.com/33708512/56033752-2e819a80-5d60-11e9-8064-fc5d3037b6d8.PNG)

-> uid 0번이니까 x, 500번 이상의 user로 해야함 \

-> 그 user에 파일을 임시로 하나 넣음

-> kinit jhnam 이후,

-> hadoop distcp hdfs://10.100.3.218/user/jhnam/aaa hdfs://10.100.3.246/user/jhnam

![distcp2](https://user-images.githubusercontent.com/33708512/56034204-abf9da80-5d61-11e9-8e62-fa5c21a79af9.PNG)


+ Configuration 바꿀 필요 없음 

(Locate the Hadoop RPC Protection property and select authentication )
-> privacy도 상관 X


--------------------
<4.13 issue>
-------------

### **1. DistCP**

4. distcp port test

(1) CLI 


https://www.cloudera.com/documentation/enterprise/5-14-x/topics/install_ports_distcp.html


    현재 active name node: 10.100.3.217 (RUBY), 10.100.3.246 (NEW)

    <new cluster - name node 246에서 진행>

    1. iptables -I INPUT -p tcp -s 10.100.3.217 --dport 8020 -j DROP 
    -> hadoop distcp hdfs://10.100.3.217/user/jhnam/ccc hdfs://10.100.3.246/user/jhnam 
    -> fail 
    
    -> 이상태에서 반대로 new cluster에서 ruby로 보내지는 것 가능 (당연히 ruby에서는 7080 port 안막았으니까)

![port8020](https://user-images.githubusercontent.com/33708512/56073465-4ba97e00-5ddf-11e9-9fe4-bd39eed7e043.PNG)


    1. 1004 port drop
    -> pssh로 해서 모든 src ip 막음
    -> 오래 걸리지만 보내짐 (5분 소요)  => ctrl+c로 종료하기 전까지는 임시파일 형태로 존재함 ( .distcp.tmp.attempt_1555140045954_0004_m_000000_0)
    -> log 파일 존재 (distcp_1004 포트 로그.txt)

    2. 그 외 50010, 9687, 14000 port는 막아도 상관 X

(2) oozie workflow

### oozie에서 statement error가 나면, 
/opt/cloudera/parcels/CDH-6.1.1-1.cdh6.1.1.p0.875250/lib/hue/desktop/libs/liboozie/src/liboozie 이 안에 있는 submission2.py 파일 아래와 같이 수정 (statement -> command)

action.data['type'] in ('sqoop', 'sqoop-document') and '--hive-import' in action.data['properties']['command']):

-> code 

 pssh -h /opt/app/dest_host -i "cp /opt/cloudera/parcels/CDH-6.1.1-1.cdh6.1.1.p0.875250/lib/hue/desktop/libs/liboozie/src/liboozie/submission2.py /opt/cloudera/parcels/CDH-6.1.1-1.cdh6.1.1.p0.875250/lib/hue/desktop/libs/liboozie/src/liboozie/submission2_backup.py"

 pscp.pssh -h /opt/app/dest_host /opt/cloudera/parcels/CDH-6.1.1-1.cdh6.1.1.p0.875250/lib/hue/desktop/libs/liboozie/src/liboozie/submission2.py /opt/cloudera/parcels/CDH-6.1.1-1.cdh6.1.1.p0.875250/lib/hue/desktop/libs/liboozie/src/liboozie/

1. DistCP workflow 생성 후 실행 

<'NoneType' object is not iterable> error 발생 

----------------
----------------

NEXT_WEEK)
----------------
1. Spark 교육 (월, 화 - SK C&C)




