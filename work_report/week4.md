work report (week4)
=============


<3.25 issue>
-------------

### **1. Flume LAB**


1. flume install
* cd /home/hinkim
* wget http://apache.mirror.cdnetworks.com/flume/1.8.0/apache-flume-1.8.0-bin.tar.gz
* tar -xvf apache-flume-1.8.0-bin.tar.gz

* cd apache-flume-1.8.0-bin/conf
* cp flume-env.sh.template flume-env.sh
* cp flume-conf.properties.template flume.conf

* flume-ng help

* vi /etc/profile
* export FLUME_HOME=/home/hinkim/apache-flume-1.8.0-bin
* export PATH=$PATH:$FLUME_HOME/bin
* source /etc/profile
* vi flume-env.sh
* export JAVA_OPTS="-Xms100m -Xmx2000m -Dcom.sun.management.jmxremote"  # java 힙 메모리 설정

-------------

2. cm에서 flume install
* add service (host: master, slave1)
* agent configuration \
-> /home/hinkim/training_materials/admin/scripts/flume-tail1.txt \
-> /home/hinkim/training_materials/admin/scripts/flume-collector1.txt \
-> master는 horse 부분을 slave1.isaac-eng.com 으로 변경 \
-> slave1은 마찬가지로 horse 부분 위와 같이 변경 + elephant 부분을 192.168.159.136으로 변경

-------------

3. test.conf 파일 생성

agent01.sources = dirSrc
agent01.channels = memChannel
agent01.sinks = fileSink


agent01.sources.dirSrc.channels = memChannel
agent01.sinks.fileSink.channel = memChannel


agent01.sources.dirSrc.type = spoolDir
agent01.sources.dirSrc.spoolDir = /home/hinkim/data/tmp


agent01.sinks.fileSink.type = file_roll
agent01.sinks.fileSink.sink.directory = /home/hinkim/data/output
agent01.sinks.fileSink.sink.rollInterval = 0


agent01.channels.memChannel.type = memory
agent01.channels.memChannel.capacity = 100

-------------

4. test.conf flume agent 실행 \
(같은 host : master tmp directory -> master output directory)

$ bin/flume-ng agent -n agent01 -c conf -f conf/test.conf


-> 실행이 돼서 output 폴더에 파일이 생기긴 생겼는데 내용이 없음

![flume3](https://user-images.githubusercontent.com/33708512/54961462-1827c080-4fa4-11e9-944d-28b48dda6e4a.PNG)

-> why? : 이유는 당연함. tmp 폴더를 spoolDir로 설정을 하였는데 거기서 아무런 action을 안했기 때문에 로그 파일이 안생김. 
(cf. spooldir : Spooling Directory 디렉토리에 새롭게 추가되는 파일을 데이터로 사용, \
file_roll : 로컬 파일에 저장)

-> master 서버 여러개 띄워 놓고 하나는 agent 실행, 하나는 tmp 디렉터리 가서 임시 파일을 생성해보고 하나는 output 디렉터리 가서 생기는 로그 파일을 확인한 결과 

![flume4](https://user-images.githubusercontent.com/33708512/54962231-61c5da80-4fa7-11e9-8637-b7b7bb4b30f9.PNG)

![flume5](https://user-images.githubusercontent.com/33708512/54962232-61c5da80-4fa7-11e9-85c8-e5ea67655e2a.PNG)

![flume6](https://user-images.githubusercontent.com/33708512/54962230-61c5da80-4fa7-11e9-9618-7ca50eb61f95.PNG)

-> 로그에 파일 내용이 잘 반영돼 있음을 확인!!!!!

-------------

5. 잘 되다가 갑자기 error 발생

![flume7_error](https://user-images.githubusercontent.com/33708512/54974458-73bf7180-4fd7-11e9-9a34-8d1c483c3a12.PNG)

-> 위에꺼 지웠더니 agent 종료됨

![flume7_error2](https://user-images.githubusercontent.com/33708512/54978928-0dd9e680-4fe5-11e9-8ebb-ae9947edbda3.PNG)

-> 홈페이지에서 다운받아서 원래 있던 폴더에 넣어놨음에도 없다고 에러뜸

-> 처음 flume 깐 tar.gz파일을 다시 압축 해제해서 그 안에 있는 slf4j-log4j12-1.6.1.jar파일을 cp해서 원래대로 복구함

-> 다시 위와 같이 path 중복 현상으로 돌아옴

-> why? 이유는 당연함. CM에서도 flume을 설치하고, terminal command에서도 설치했으니까 2중으로 flume이 설치된 것!

-> CM에서 flume을 끄기

-> 해결

![flume8](https://user-images.githubusercontent.com/33708512/55046035-724b8300-5083-11e9-9c43-47dd8ec1587f.PNG)

![flume8-1](https://user-images.githubusercontent.com/33708512/55046036-724b8300-5083-11e9-8155-827af127c252.PNG)


-------------

6. timestamp interceptor

* 1 ) source :  spoolDir \
    sink : logger

-> spool directory에 파일을 미리 만들고 agent 실행 후 결과 확인

![flume11-1](https://user-images.githubusercontent.com/33708512/55124162-a341bb80-5148-11e9-9f41-4a57bbe0e299.PNG)

![flume11-2](https://user-images.githubusercontent.com/33708512/55124159-a341bb80-5148-11e9-829c-2ebf274453cd.PNG)

![flume11-3](https://user-images.githubusercontent.com/33708512/55124161-a341bb80-5148-11e9-9ec7-268c4437ed65.PNG)


* 2 )  source : netcat, 3333 port \
    sink : logger 

-> telnet을 이용해 실시간으로 데이터 전송을 확인

![flume10-1](https://user-images.githubusercontent.com/33708512/55124127-81483900-5148-11e9-8212-1a9989a4c89b.PNG)


![flume10-2](https://user-images.githubusercontent.com/33708512/55059042-3f1fe880-50b1-11e9-9bd2-fad61a93575d.PNG)


![flume10-3](https://user-images.githubusercontent.com/33708512/55059035-362f1700-50b1-11e9-9609-321ed3e35776.PNG)


-------------

7. host interceptor

![flume12-3](https://user-images.githubusercontent.com/33708512/55124355-4eeb0b80-5149-11e9-888c-00944b4df51f.PNG)

-------------

8. regex filter interceptor 

-> 특정 문자열을 fitering하여 전송 \
-> flume으로 시작하는 데이터는 제외하고 전송된 것을 확인

![flume13-1](https://user-images.githubusercontent.com/33708512/55124393-6aeead00-5149-11e9-859a-eb3fd39161b2.PNG)

![flume13-2](https://user-images.githubusercontent.com/33708512/55124391-6a561680-5149-11e9-8e30-459f2aba21c6.PNG)

![flume13-3](https://user-images.githubusercontent.com/33708512/55124392-6aeead00-5149-11e9-9843-e1325ecafdb5.PNG)

-------------

9. regex extractor interceptor 

-> 들어오는 데이터에서 특정 문자열 추출해서 추출된 문자열을 가공하여 새롭게 헤더에 추가 \
-> yyyy-mm-dd hh:mm 형태로 입력하면 이것을 timestamp 형식으로 가공하여 헤더에 추가됨을 확인

![flume14-2](https://user-images.githubusercontent.com/33708512/55204736-760e0f80-5213-11e9-8b1b-f6fc7ffd9403.PNG)

![flume14-1](https://user-images.githubusercontent.com/33708512/55204734-760e0f80-5213-11e9-9773-85a4cc3c411d.PNG)

![flume14-3](https://user-images.githubusercontent.com/33708512/55204733-75757900-5213-11e9-80ea-8bf83e0c495a.PNG)


--------------------------

### **2. Cloudera Admin Official Lecture**


### exercise: Explore Hadoop Configurations and Daemon Logs

1. core-site.xml 파일 찾기

![configuration1](https://user-images.githubusercontent.com/33708512/54893387-df7edd00-4ef8-11e9-874d-929f0b8ba938.PNG)

![configuration2](https://user-images.githubusercontent.com/33708512/54893388-e0177380-4ef8-11e9-974e-eee702f69e23.PNG)

2. fs.trash.interval 확인 (1일)

![configuration3](https://user-images.githubusercontent.com/33708512/54893422-1c4ad400-4ef9-11e9-84c0-97385cde1154.PNG)

3. diff check (1일 -> 2일)

![configuration5](https://user-images.githubusercontent.com/33708512/54893389-e0177380-4ef8-11e9-9377-4586db9181a3.PNG)


### exercise: Using Flume to Put Data into HDFS

1. create directory
$ sudo -u hinkim hdfs dfs -mkdir /user/hinkim/flume/collector1

2. run flume
$ /home/hinkim/training_materials/admin/scripts/accesslog-gen.sh /tmp/access_log

![flume1](https://user-images.githubusercontent.com/33708512/54898095-3ba02c00-4f0e-11e9-98e0-1bea45cc8315.PNG)


### exercise: Importing Data with Sqoop

1. find movielens.sql 

$ find / -name '*movielens*' 

-> /home/hinkim/training_materials/admin/data/movielens.sql 

2. run movielens.sql

![sqoop1](https://user-images.githubusercontent.com/33708512/54901411-b589e280-4f19-11e9-8580-79fa527d0aee.PNG)

3. show databases & table

![sqoop2](https://user-images.githubusercontent.com/33708512/54901407-b4f14c00-4f19-11e9-880e-d30ea88d4231.PNG)

4. symlink 

![sqoop3](https://user-images.githubusercontent.com/33708512/54901408-b4f14c00-4f19-11e9-82c1-d9a241dcd838.PNG)

5. sqoop list-databases

![sqoop4](https://user-images.githubusercontent.com/33708512/54901409-b4f14c00-4f19-11e9-8b93-bf2c00821bc8.PNG)

![sqoop5](https://user-images.githubusercontent.com/33708512/54901410-b589e280-4f19-11e9-8265-d7b3612d7911.PNG)

6. import table

-> error (CommunicationsException)

![sqoop_error](https://user-images.githubusercontent.com/33708512/55204984-5c20fc80-5214-11e9-9a38-b4849efb04af.png)


---------------------------

<3.26 issue>
-------------

### **1. server uninstall & reinstall** 
* 코엑스로 보낸다하여 분해해서 포장했는데 그럴필요 없다고 해서 다시 서버 구축


1. 벽 쪽에 콘센트와 랜 꼽기
2. 앞 쪽에 전체 파워와 본체, 라즈베리파이 담당하는 파워 올림
3. 본체 킴
4. 모니터 설치 \
-> ip주소, dns 주소, gateway 주소 입력


### **2. Flume LAB**   <위에 이어서 작성>

-------------

### **3. Cloudera Admin Official Lecture**

### Hive
* HDFS에서 저장 돼 있는 데이터들을 가장 high-level에서 분석할 수 있는 tool
* like SQL

* Hive table = HDFS directory (A hive table represents an HDFS directory)
* Hive field 정보 = HDFS file

* Metadata (table structure and path to data) is stored - MetaStore

* Hive Table \
-> Managed : if the table is dropped, the schema and HDFS data is deleted \
-> External : if the table is dropped, only the table schema is deleted

* Updata and Delete are not supported

### Impala
* Like Hive, Impala allow users to query data in HDFS using an SQL-like language
* Unllike Hive, Impala does not turn queries into MapReduce jobs
* Impala shares the Hive Metastore

* Impala - Hive 호환 가능

* Impala Coordinator : 작업을 나눠줌, 작업을 받은 노드가 혼자 다하는 것이 아니라 그 작업을 여유로운 노드에게 넘겨줌 (like AM) 
* Impala State Store : impala daemon (worker node) 들의 상태를 보내줌, 누가 바쁜지에 대해서 알고 있음 \
-> Impala Coordinator와 communication을 통해 어디에 작업을 보내야하는지 알 수 있음 (like RM) 
* Impala Catalog Server : metadata의 변동 사항을 Impala daemon 들에게 알려줌
 
### Pig
* high-level에서 분석할 수 있는 tool, Hive와 쌍두마차
* Like Hive, executes as MapReduce job
* provides a scripting language known as Pig Latin for processing data \
-> procedure, 점진적으로 처리함 \
-> default로 채워넣어서 어떻게든 결과가 나옴 (스키마를 몰라도 사용 가능!)

### Hue
* 기본적인 hadoop user UI (모든 hadoop acivity 가능)

* Hadoop client : Hadoop 서비스를 쓰기 위한 API를 가지고 서비스에 접근함

### Oozie
* workflow and coordination service for managing Hadoop jobs \
-> action nodes (task를 수행하는 노드), control-flow nodes (그 뒤에 어떻게 할 것인가, 죽일 것인지 살릴 것인지 에러가 났을 때 어떻게 할 것인지) 지정할 수 있다

------------

* mariadb 안될 떄는 my.cnf 파일 확인!

* log - /var/log/cloudera-scm-server/~ 

---------------------------

<3.27 issue>
-------------

### **1. Flume LAB**   <위에 이어서 작성>

### **2. kafka LAB**
* CM에 kafka가 존재하기 때문에 별도의 install X (혹시 모르니 kafka 관련 shell 파일이 있는지 확인함)

* /opt/cloudera/parcels/CDH-6.1.1-1.cdh6.1.1.p0.875250/lib/kafka


1. topic 생성 및 확인

![kafka1](https://user-images.githubusercontent.com/33708512/55045210-61e5d900-5080-11e9-8275-08408d9b0aa4.PNG)



### **3. Cloudera Admin Official Lecture**

### exercise: Using Hue to Control Hadoop User Access

1. Adding an HttpFS Role Instance to the hdfs Service

* HttpFS: HDFS에 HTTP access를 제공하는 서비스. HttpFS에는 모든 HDFS File system 작업의 읽고 쓰는 기능을 모두 지원하는 API가 존재한다

* 기본적으로 HttpFS 서버는 포트 14000번에서 실행되며, 기본 url은 http:// <httpfs_hostname>:14000/webhdfs/v1 이다

* slave2에 Role instance를 추가하고, port 확인 

![httpFS1](https://user-images.githubusercontent.com/33708512/55051364-1c350a80-5098-11e9-9e98-7b559fc15be6.PNG)

* httpFS (user:hinkim 확인)

![httpFS2](https://user-images.githubusercontent.com/33708512/55051511-c745c400-5098-11e9-857f-522ebf36512f.PNG)

+++ 사용자의 홈 디렉토리를 얻기

![httpFS3](https://user-images.githubusercontent.com/33708512/55052327-db3ef500-509b-11e9-95a4-c38b41fcd73a.PNG)


2. Hue UI에서 HDFS file system 확인

![hue1](https://user-images.githubusercontent.com/33708512/55053283-037c2300-509f-11e9-99b8-1414e3869f42.PNG)


---------------------------

<3.28 issue>
-------------

### **1. Flume LAB**   <위에 이어서 작성>

### **2. prepare presentation**

### **3. Install MongoDB**

* Install Compass

* Install shell \
-> mongodb 사이트 들어가서 (https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/#install-mdb-edition) mongodb zip 파일로 다운로드 \
-> path 설정하기 편하게 D 드라이브에 저장 \
-> /bin까지의 경로를 환경변수에 path 추가 \
-> cmd 창에서 mongo 치면 일단 없는 명령이라고 뜨진 않음 \
-> mongodb cluster username password 등등을 연결해주면 접속 성공


* create my cluster \
-> mongo "mongodb+srv://cluster0-4gdfd.mongodb.net/test" --username hyenni (shell) 

 ![login](https://user-images.githubusercontent.com/33708512/55144763-dd30b300-5184-11e9-984d-3e895a1eb35e.PNG)

* load loadMovieDetailDataset.js

![load_js](https://user-images.githubusercontent.com/33708512/55144840-fafe1800-5184-11e9-9542-b46ef85ff5bb.PNG)

* create document MoviesStratch from compass

![create](https://user-images.githubusercontent.com/33708512/55201652-4b1dbe80-5207-11e9-8e6e-0bf586667d47.PNG)

* find query 

-> pretty() : 좀 더 깔끔하게 list 형식으로 보여줌

![find](https://user-images.githubusercontent.com/33708512/55201556-d77bb180-5206-11e9-976b-49f7b2caa70f.PNG)

-> second argument 

    1 : 그 필드를 포함하여 return
    0 : 그 필드를 제외하고 return

![find2](https://user-images.githubusercontent.com/33708512/55201557-d77bb180-5206-11e9-8b92-65e951cdef7a.PNG)

* update query

![update1](https://user-images.githubusercontent.com/33708512/55201560-d8144800-5206-11e9-98b7-88ec33bf9e5c.PNG)

![update2](https://user-images.githubusercontent.com/33708512/55201561-d8144800-5206-11e9-958e-bf070cc5ea70.PNG)


---------------------------

<3.29 issue>
-------------

### **1. presentation**

### Feedback

* morphlin 실습하기

### **2. 1층 server 다시 구축** 

---------------------------
---------------------------

NEXT WEEK)
-------------
1. Flume morphline interceptor 실습



