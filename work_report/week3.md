work report (week3)
=============


<3.18 issue>
-------------

## <span style="color:green">Please review as blow </span>

### **1. Answers on Feedback**

Q. Data node에 block이 유실된다면? 

A. 같은 block이 있는 다른 Data node에게 job을 대신 시킨다 \
A. 만약 대신 시킬 Data node가 없다면 (다른 Data node에게 시킬 수 없는 상황이라면) 그 Data node에서 유실된 block을 복사해서 가져오게 된다 


Q. 만약에 장애가 발생한 block이 살아난다면? \
-> 살아난 것을 지운다 \
-> 복사한 것을 지운다 \
-> 무작위로 지운다 

A. 무작위로 지운다. Hadoop에서 중요한 것은 어떤 것이 original이냐가 아니라 항상 replication이 3이면 된다.

<br><br>

Q. jar 명령어 의미

A. 자바로 프로그래밍한 jar 파일을 실행할 때 사용하는 명령어

<br><br>

Q. Secondary Namenode 역할 확인

A. 다시 살아날 때를 대비하여 데이터를 저장
A. edit log file, fs image 

A. check point server, name node에서 fsimage와 edit를 가져와서 새로운 fsimage를 만들어 낸 후 name node에게 다시 fsimage를 가져다 줌 \
-> name node는 HDFS 내에서 발생하는 모든 파일 트랜잭션을 edit log 파일에 저장한다. 이 파일의 용량은 별도의 제한이 없어서, name node가 다운되지 않는 한 무한대로 저장이 되는데 문제는 HDFS가 restart 될 때, HDFS는 edit log와 fsimage(파일 시스템 이미지)를 병합해서 인메모리 형태로 메타 정보를 로딩합니다. 하지만 이떄, edit log가 지나치게 크다면 HDFS가 인메모리에 metadata를 로딩하지 못하거나, 로딩이 지연되어서 HDFS가 구동되지 못하는 상황이 발생할 수 있다. 이를 위해서 HDFS에는 secondary name node를 제공하여, name node의 edit log를 주기적으로 축약시켜주는 역할을 수행한다. 이러한 edit log의 축약 작업을 check point라고 한다. \
name node의 fsimage나 edit log가 깨졌을 때 secondary name node에 저장된 데이터를 이용해 복구도 가능하다. 물론 check pointing 만큼의 유실은 감안해야 하지만, 트랜잭션이 매우 빈번하지만 않다면 secondary name node만으로 일정한 백업 서버 역할을 커버할 수 있음.

![secondarynamenode](https://user-images.githubusercontent.com/33708512/54577792-7e5e9180-4a40-11e9-8847-06dcd3e57d36.PNG)

<br><br>

Q. YARN 동작할 때, \
-> RM이 죽었을 때 \
-> NM이 죽었을 때 \
-> AM이 죽었을 때 \
각각 어떻게 일처리를 이어서 하게 되는지에 대해서 알아보기

A. 
* map task가 죽었을 때: AM이 인지할 수 있음. 다시 요청하여 다른 곳에 만들어서 실행함. \
-> 그런데 이 때 죽은 줄 알았던 map task가 살아났다면? \
-> 두 개가 동시에 있어도 상관없음. 다만 둘 중에 먼저 결과를 만든 map task를 받고 나머지 map task를 죽임

* AM이 죽었을 때: RM이 인지할 수 있음. 다시 AM을 어디에 만들고, 기존에 실행하고 있었던 task들을 없애고 새로 생긴 AM이 RM에게 다시 할당받아 재시작한다.
    
* NM이 죽었을 때: RM이 인지할 수 있음. 죽은 NM으로 자원을 주지 않고 나머지 NM으로 돌린다.

* RM이 죽었을 때: 새로운 job을 못받을 뿐이지 나머지 돌고 있는 애들은 수행한다. 

<br><br>

Q. Hbase를 대체할 수 있는 NoSQL이 무엇이 있는지 \
(Hbase는 HDFS가 있어야만 구동이 가능한데 다른 것도 그러한지 확인) \
-> cf. Hbase란, NoSQL database, column형 데이터를 저장할 때 사용. + HDFS와 연동  

A. Cassandra, HDFS 위에서 구동할 필요 없음

<br><br>

Q. wordcount 실행하였을 때 mapper가 몇개 돌았고 reducer가 몇개 돌았고의 여러 정보들을 확인할 것


![job](https://user-images.githubusercontent.com/33708512/54792516-cddfd000-4c81-11e9-9089-850e920427ed.PNG)

<br><br>

* Hadoop Architecture modify \
-> Ambari 맨 위 Layer (Management & Monitoring) \
-> Hbase -> Cassandra

<br>

-------------


### **2. Cloudera Admin Official Video Lecture**

### <"Introduction">

### Hadoop
* 데이터를 분산 저장하고 분산된 데이터를 병렬 처리하는 platform \
-> 분산 저장: HDFS \
-> 병렬 처리: YARN

### Big data
* Hadoop으로 처리하는 데이터 (85% 맞는 얘기)

### Cloudera Manager
* 시스템을 관리할 수 있게 해주는 cluster management tool \
-> Hadoop은 기본적으로 하나의 시스템으로 쓰는 것이 아니라, 여러 개의 server를 묶은 cluster를 사용하는데 이 cluster를 관리해야하고 전체적으로 운영할 수 있게 해주는 프로그램

### High Availability (HA) 고가용성
* 하나가 죽어도 또 쓸 수 있는 형태로 만들어야 하는데 그것을 어떻게 운영하고 그렇게 했을 때 seerver와 client가 각각 어떻게 작동하는 지 

### 디스크 가격 하락 대비해서 발전 속도는 제한적임

-------------

### <"Hadoop">

### Hadoop's deployment modes
* Pseudo-distributed \
-> Simulate a cluster on a single server \
-> 모든 daemon들은 하나의 로컬 서버에서 실행

* Fully distributed \
-> Running a Hadoop on cluster \
-> Hadoop daemon(프로세서)들이 클러스터의 각 노드에서 운영 \
-> NameNode, ResourceManager, JobhistoryServer daemons 등은 별도의 서버로 운영하는 것이 효율적 (slave가 응답을 줬을 때 master는 즉시 응답을 해야함)

### Hadoop 2.0
* Allows multiple applications to run on the same platform

* hadoop 1.0 \
-> 기본적인 자원 관리와 병렬 처리를 하는 framework

* hadoop 2.0 \
-> mapreduce는 computation, YARN은 여러 다른 형태의 platform (mapreducem, spark)를 병렬 처리할 수 있다 \
-> 클러스터의 자원을 효율적으로 사용할 수 있음

### MR1 vs MR2
* JobTracker - ResourceManager
* TaskTracker - NodeManager

* JobTracker: 자원에 대한 스케줄링, 리소스 관리, job에 대한 관리  -> 엄청 바쁨 \
-> 자원에 대한 스케줄링, 리소스 관리: ResourceManager \
-> job에 대한 관리: Application Manager (dynamic하게 생성됨)

### cluster
* 각각의 기계가 따로 따로 있지만 하나의 시스템인 것처럼, 하나의 컴퓨터인 것처럼 돌게해주는 것이 cluster이며, 그것을 돌리는 것이 hadoop이다

-------------

### <"Basic Topology of Cloudera Manager>

### Cloudera Manager Server
* Runs service monitor and activity monitor daemons
* Stores cluster confiuration information in a database
* Sends confiuration information and commands to the agents over HTTS(S)

### Cloudera Manager Agents
* Receive updated confiurations from server
* Start and stop Hadoop daemons, collect statistics
* Heartbeat status to the server

-------------


### <"HDFS, The Hadoop Distributed File System">

* HDFS is an application, written in JAVA, that simulates a filesystem \
-> 실질적으로 파일을 hdfs 명령어를 줘서 put을 하는 순간 나눠주고, ls 하는 순간 name node가서 위치를 가져다가 뿌려주는 역할

### "What feature HDFS provides"

* Fault tolerance \
-> data replication (3)

* Optimized for distributed processing \
-> 여러개의 데이터가 존재하다 보니까 Data locality가 높아짐. 데이터가 있는 곳에서 프로세스를 수행할 수 있는 권한이 높아지는 것 \
-> performance 높아짐

* Scalability (확장성) \
-> 디스크가 부족하면 더 많은 data node를 붙이면 된다

* "Modest" numbeer of large files \
-> Millions of large files, not hundreds of millions of small files 

    ex) 1MB의 파일 1000개와 1GB의 파일 1개가 있다고 하자. 두 가지의 경우 모두 local disk에는 1GB가 필요한 것이다. 이것을 HDFS으로 저장한다면? \
    먼저 1GB 파일은 블록 사이즈(128M)로 쪼갠다면 8개의 블록이 필요하고 3개의 복사본을 만들기 때문에 전체 24 블록이 필요하다. \
    1MB의 파일 1000개는 1000개의 블록이 필요하고 결론적으로 3배 하여 3000개의 블록이 필요한 셈이다. \
    여기서 각각의 블록에 대한 정보를 name node의 metadata로 저장을 해야하는데, 1M의 블록에 대한 정보를 저장하는 metadata의 크기나, 128M의 블록에 대한 정보를 저장하는 metadata의 크기는 동일함 \
    => 크기가 작으면서 많은 파일은 데이터의 블록 수와 metadata의 용량이 커지므로 비효율적임

* Files are write-once \
-> 수정 불가능 = 데이터 무결성 유지



### "Structure"

* When a file is added to HDFS, it is split into blocks (default = 128M)

* name node: meta data \
-> metadata: fsimage, edit \
-> fsimage: 그 때의 전체 상황, snapshot ex) 27분에서의 인구 수, 28분에서의 인구 수 \
-> edit: 그 사이에 있었던 transaction 저장 ex) 27분에서 28분으로 갈 때의 +출생신고수 -사망신고수

* data node: 실제 data block 저장

* secondary name node: check point server, name node에서 fsimage와 edit를 가져와서 새로운 fsimage를 만들어 낸 후 name node에게 다시 fsimage를 가져다 줌, 주기적으로 metadata를 update함 (new snapshot) \
cf. 주기적으로: 일정 시간 duration을 configuration할 수 있음. 트랜잭션의 횟수, block의 수 등등


* name node, data node \
-> data node가 죽으면? : 복구 가능, 죽은 data node가 가지고 있었던 block을 가지고 있는 다른 data node에서 복사해오면 됨 \
-> name node가 죽으면? : 복구 불가능, data node에 있는 block들을 일일이 살펴볼 수 없고 어떤 것인지 알 수 없음. 이 데이터에 대한 meta 정보는 name node만이 가지고 있음. \
-> secondary name node가 죽으면? : 서비스는 가능, 하지만 check point 기능을 이용할 수 없기 떄문에 결국엔 망가질 수 밖에 없음 (edit 파일이 계속해서 커질 것이기 때문)



### "How HDFS reads and writes files"
* File Write
1. client connects to the NameNode
2. NameNode places an entry for the file in its metadata, returns the block name and list of DataNodes to the client
3. Client connects to the first DataNode and starts sending data
4. As data is received by the first DataNode, it connects to the second and starts sending data
5. Second DataNode similarly connects to the third
6. ACK packets from the pipeline are sent back to the client
7. Client reports to the NameNode when the block is written

* File Read
1. Client connects to the NameNode
2. NameNode returns the name and locations of the first few blocks of the file
3. Client connects to the first of the DataNodes, and reads the block

<br><br>



### **3. Zoomdata Visualization**

http://10.100.3.212:8080

-------------

<3.19 issue>
-------------

### **1. Cloudera Admin Official Video Lecture**

### <"kudu">
* Kudu is a storage engine for structured data
* Simplify applications that use hybrid HDFS/Hbase
* Accesses data through the local file system - not an HDFS client
* kudu + impala


### <"YARN">
* A platform for managing resources in a Hadoop cluster
* YARN allows you to run diverse workloads on the same Hadoop cluster (MapReduce, Spark 등등)
* container : job 실행에 필요한 cpu, 메모리의 집합을 의미

* Resource Manager (master)

    * Manages Node Managers
    * Runs a scheduler \
    cf. scheduler: 자원(cpu, 메모리)을 어떻게 할당할까 
    * Manages containers
    * Manages Application Masters 

* Node Manager

    * Communicate with the Resource Manager
    * container 안에 있는 자원들을 관리 (자원을 어느 정도 쓰고 있는지 monitoring)

* Application Master

    * One per YARN application execution
    * Runs in a container
    * Communicates with the Resource Manager \
    -> container를 받기 위해서
    * task가 어떻게 돌고 있는지 확인


### <"MapReduce">
* programming model, Record-oriented data (key-value) 형태로 분산 병렬로 처리할 수 있도록 여러 개의 node에 대해서 일을 나눠줄 수 있는 기능

* mapper가 만든 결과는 로컬 디스크에 쓰고, reducer가 만드는 결과는 HDFS에 쓰는 이유는? \
-> 중간 결과와 최종 결과의 차이 

* job = application
* task : an individual unit of work 
    - map task, reduce task 
    - runs in a countainer on a worker node

### <"spark">
* 스트림 데이터 처리
* 여러개의 job = application
* executor -> application이 끝날 때까지 없어지지 않기 때문에 빠름

### <"cluster configuration">
* service: A Hadoop cluster has Services, such as HDFS or YARN
* role: The individual components of a service. For example, the HDFS
service has the NameNode and DataNode roles
* role group: A way of creating logical groups of daemons across multiple
machines
* role instance: A member of a Role Group. For example, a DataNode
installed on a specific machine is a role instance

<br><br>


### **2. wiki 문서 등록 (HDFS)**

<br><br>
### **3. prepare presentation**
 
### Flume
* What is a Flume? \
-> 분산 환경에서 대량의 로그 데이터를 효과적으로 수집하여 집계한 후, 다른 곳으로 전송할 수 있는 신뢰성을 가진 서비스이다. 스트리밍 데이터 흐름 기반.

* What feature Flume provides? 
1. Reliability (신뢰성) -> 장애가 나더라도 로그를 유실 없이 전송할 수 있는 능력

* data transfer between agents and channels is transactional \
-> A failed data transfer to a downstream agent rolls back and retries \
(데이터를 받거나 안받거나)

* Can configure multiple agents with the same task \
-> Two agents doing the job of one "collector" - if one agent fails then upstream agents would take over \
(collector 기능 -> multi agent에서 이것이 fail되면 다른 것을 쓰면 되는 것이 가능)

2. Scalability (확장성)  

* The abillity to increase system performance linearly - or better - by adding more resources to the system

* agent 추가



-----------------

<3.20 issue>
-------------

### **1. Cloudera Admin Official Video Lecture**

### <"Sqoop">
* the SQL-to-Hadoop database import tool
* RDBMS와 HDFS의 데이터를 주고 받을 수 있는 tool 
    * RDBS -> HDFS : import 
    * RDBS <- HDFS : export

ex) List all databases \
$ sqoop list-databases --username fred -P --connect jdbc:mysql://dbserver.example.com/

databases list를 보여주며, 필요한 파라미터로는 db에 접속할 username와 password, jdbc의 위치를 알려줘야 한다  

<br><br>

### **2. LAB**

### HDFS


* name node: 50070 port
* hdfs dfs -
* 현재 working directory 개념이 존재하지 않기때문에 절대 경로로 사용할 것
* hdfs dfs -put : copies local files to HDFS
* hdfs dfs -get : fetches a local copy of a file from HDFS

* -실습 환경 구축-
    * 10.100.1.115 원격 
    * 8888: hue, 계정 만들고 사용 (hinkim)
    * 7180: CM, 계정 만들고 사용 (hinkim)
    * 각자 directory 만들어서 사용 (hinkim)  


1. 원격 접속 후 각자의 directory 생성 후 training_materials.tar.gz 파일 압축 해제
    * $ mkdir ./hinkim
    * $ cp ./training_materials.tar.gz ./hinkim/
    * $ cd hinkim
    * $ tar -xvf training_materials.tar.gz
    * $ ls training_materials

2. cloudera와 hue server에 계정 만든 후 확인

    ![hdfs1](https://user-images.githubusercontent.com/33708512/54652264-a1994780-4af9-11e9-893f-27379873ea4c.PNG)


받은 파일이 exercise에서 사용하는 파일과 다르고 여러 오류로 인해 진행하지 더이상 진행하지 못함. 

aws를 이용하여 cluster 구축 및 elephant server로 접속 후 그 안에 있는 training_materials파일을 tar.gz로 압축함

cloudera_training_get2ec2 vmware에서 elephant 접속한 후 그 안에 있는 파일을 local로 scp 명령어를 통해 파일 복사 

web browser로 파일 보내고 1층 컴퓨터로 네트워크 접속 후 메일로 전송한 파일 다운

filezilla 이용하여 그 파일을 1층 cloudera master 서버로 파일 전송 

1층 cloudera master 서버에 파일이 잘 들어왔는지 확인 후 압축 풀어서 각자의 계정에서 실습 시작

------------


<3.21 issue>
-------------

### **1. LAB** 

### HDFS 이어서

3. chmod 

![hdfs2](https://user-images.githubusercontent.com/33708512/54735038-f797e900-4be6-11e9-91d1-ef0b59be98b1.PNG)

4. access_log upload to HDFS & review

![hdfs3](https://user-images.githubusercontent.com/33708512/54735039-f797e900-4be6-11e9-8b7c-e3a82d2720ba.PNG)

5. review the files in the NameNode Web UI

![hdfs4](https://user-images.githubusercontent.com/33708512/54735040-f797e900-4be6-11e9-821a-cb02bf25ebaf.PNG)

6. Block 개수 확인 및 Block ID 복사

![hdfs5](https://user-images.githubusercontent.com/33708512/54735041-f797e900-4be6-11e9-91ce-1d21601ea356.PNG)

![hdfs6](https://user-images.githubusercontent.com/33708512/54735042-f8307f80-4be6-11e9-8ed6-faff277c2a93.PNG)

7. 찾은 block의 내용과 업로드한 파일의 내용이 같은지 비교하기 (파일이 블록으로 나눠져 저장됐기 때문에 앞의 내용은 같아야 한다)

![hdfs7](https://user-images.githubusercontent.com/33708512/54735900-e0a7c580-4beb-11e9-8233-e7035081c9df.PNG)


![hdfs8](https://user-images.githubusercontent.com/33708512/54735898-e00f2f00-4beb-11e9-9bea-a19ebee6cfd0.PNG)


<br><br>

### **2. prepare presentation**

<br>

---------------------

<3.22 issue>
-------------

### **1. LAB**
### YARN
* history: 19888 port


1. 파일 확인 후 wordcount 실행 

![yarn1](https://user-images.githubusercontent.com/33708512/54796324-22d81200-4c93-11e9-974b-dc8b5bd86d1f.PNG)

2. 결과 확인

![yarn2](https://user-images.githubusercontent.com/33708512/54796325-2370a880-4c93-11e9-9353-728080adde41.PNG)

$ sudo -u hinkim hdfs dfs -cat /user/hinkim/counts/part-r-00000 \
아래 이미지는 위의 파일에 대한 내용이 너무 길어서 일부분만 캡처한 것입니다

![yarn3](https://user-images.githubusercontent.com/33708512/54796409-7ba7aa80-4c93-11e9-89bc-4a095a752f16.PNG)


3. history server (19888) 확인 

![yarn4](https://user-images.githubusercontent.com/33708512/54796691-8ca4eb80-4c94-11e9-8691-b1023bf0c0c5.PNG)

![yarn5](https://user-images.githubusercontent.com/33708512/54805957-fc7a9c80-4cbb-11e9-959f-fd30c3ff50d3.PNG)

aplication master: slave2 \
map task : 1개 (slave2) \
reduce task : 4개 (slave1, slave2 두개씩)

4. log level = INFO 확인

![yarn6](https://user-images.githubusercontent.com/33708512/54805958-fc7a9c80-4cbb-11e9-9198-8be6c5acd369.PNG)


5. log level = DEBUG

![yarn7](https://user-images.githubusercontent.com/33708512/54806415-41eb9980-4cbd-11e9-919f-4fdc7fa2307e.PNG)

![yarn8](https://user-images.githubusercontent.com/33708512/54806416-41eb9980-4cbd-11e9-928e-b5d8913d791c.PNG)

-> log파일이 더 자세히 기록된 것을 확인

### spark

1. 접속

![spark1](https://user-images.githubusercontent.com/33708512/54807361-5a10e800-4cc0-11e9-8efa-09fcf4f99f83.PNG)

2. word count 실행

![spark2](https://user-images.githubusercontent.com/33708512/54809967-25089380-4cc8-11e9-930d-c3731ffb3d28.PNG)


3. 결과 확인

$ sudo -u hinkim hdfs dfs -cat /user/hinkim/sparkcount/part-00000 | less

![spark3](https://user-images.githubusercontent.com/33708512/54809968-25089380-4cc8-11e9-99ab-01acfdd881e1.PNG)

4. spark history server UI (18088)

![spark4](https://user-images.githubusercontent.com/33708512/54810920-a2350800-4cca-11e9-93ba-39c04350e9c2.PNG)

![spark5](https://user-images.githubusercontent.com/33708512/54810921-a2350800-4cca-11e9-8f9e-e7fd1d7f23e2.PNG)

5. review spark application log from command line

![spark6](https://user-images.githubusercontent.com/33708512/54810922-a2350800-4cca-11e9-81ff-c28978d7a3c5.PNG)

$ sudo -u hinkim yarn logs -applicationId application_1553242157386_0004 | less

![spark7](https://user-images.githubusercontent.com/33708512/54810923-a2350800-4cca-11e9-831d-092565620a40.PNG)


<br><br>

### **2. presentation**

### Feedback

* flume interceptor 실습
* topic, broker에 데이터가 저장될 때 유효기간이 어느정도인지
* offset
* 하나의 topic에 parition이 나눠지는데 그럼 읽을 때 파일이 뒤죽박죽이 되는 것인지, 어떠한 형태로 나눠져서 저장이 되는건지 실습

<br>

-------------
-------------

NEXT WEEK)
-------------
1. flume 실습 및 feedback 정리
2. 센터장님 강의 마저 듣기 (3일차 후반 ~ 4일차)

