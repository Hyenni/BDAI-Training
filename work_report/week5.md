work report (week5)
=============


<4.1 issue>
-------------
### **1. Flume LAB**
* bin/flume-ng agent --name agent01 --conf-file conf/test.conf 


### 1층 server 재구축 후 Flume LAB 환경 다시 Setting 

* 자바 설치 -> https://blog.hanumoka.net/2018/04/30/centOs-20180430-centos-install-jdk/

* cd /opt/cloudera/parcels/CDH/lib/flume-ng
* cp flume-env.sh.template flume-env.sh
* cp flume-conf.properties.template flume.conf

* flume-ng help (잘 깔렸나 check)

* vi /etc/profile
* export FLUME_HOME=/opt/cloudera/parcels/CDH/lib/flume-ng
* export PATH=$PATH:$FLUME_HOME/bin
* source /etc/profile

* vi flume-env.sh
* export JAVA_OPTS="-Xms100m -Xmx2000m -Dcom.sun.management.jmxremote"  # java 힙 메모리 설정

<br>

## morphlines interceptor
* Extracting, Transforming and Loading Data
* 일종의 pipeline 
* readline, grok, loadSolr
* bin/flume-ng agent --name agent01 --conf-file conf/mor.conf -Dflume.root.logger=INFO, console -Xms2048m -Xmx4096m


http://kitesdk.org/docs/1.1.0/morphlines/morphlines-reference-guide.html

https://blog.datatons.com/2017/06/15/morphlines-hadoop-etl-cloudera/


-> addCurrentTime 성공 


![mor1](https://user-images.githubusercontent.com/33708512/55315991-72df8180-54a8-11e9-8c22-f0b9cb5db55d.PNG)

![mor2](https://user-images.githubusercontent.com/33708512/55315992-73781800-54a8-11e9-913b-cb24641918ae.PNG)

![mor3](https://user-images.githubusercontent.com/33708512/55315994-73781800-54a8-11e9-8408-2f9fdf60a286.PNG)

![mor4](https://user-images.githubusercontent.com/33708512/55315996-73781800-54a8-11e9-9ac7-15380e6922d1.PNG)


-> addLocalHost 성공 

-> convertTimestamp 성공 (event_datetime field를 비교해보면 사용자가 보기 편한 format으로 변환된 것을 확인할 수 있음)

argument (field, inputTimezone, inputLocale, inputFormats, outputTimezone, outputLocale, outputFormat)

![mor2-1](https://user-images.githubusercontent.com/33708512/55367386-c3e28a80-5527-11e9-8822-95e56faee421.PNG)

![mor2-2](https://user-images.githubusercontent.com/33708512/55367384-c349f400-5527-11e9-8cf5-98af393a0b7f.PNG)

![mor2-3](https://user-images.githubusercontent.com/33708512/55367385-c3e28a80-5527-11e9-9e87-b10c7c976c49.PNG)


### **2. ThingWorx**
* 기업용 IoT 플랫폼
* IoT은 분석 대상이 될 수 있는 데이터의 양을 엄청난 속도로 증가시키고 있음
* 이럴 때에 ThingWork는 스마트 커넥티드로써 방대한 양의 귀중한 인사이트를 도출하고자 하고 있습니다. 
* 뿐만 아니라 AR을 사용하여 규모 있는 어플리케이션을 구축하는 것을 손쉽게 할 수 있는 솔루션을 제공하고 있습니다  
-> AR 기능은 기업에서 제품을 출시하기 전에 2D 이루어진 기계도면을 3D로 확인하여 대략적인 모습을 미리 알 수 있게 하는 등 다양하게 활용이 가능합니다. \
-> Vuforia Studio를 이용하여 프로그래밍 기술이 없어도 '드래그 앤 드롭' 인터페이스를 사용하여 확장 가능한 AR 경험을 익힐 수 있습니다

### **3. Name Node TroubleShooting**

* Namenode NTP 문제 발생 (시간 동기화 X) \
-> https://askubuntu.com/questions/429306/ntpdate-no-server-suitable-for-synchronization-found

<img width="335" alt="NTP" src="https://user-images.githubusercontent.com/33708512/55310853-70772a80-549c-11e9-9113-cc323b715289.png"> 

-> sudo date -s "$(wget -qSO- --max-redirect=0 google.com 2>&1 | grep Date: | cut -d' ' -f5-8)Z"


-------------

<4.2 issue>
-------------
### **1. Flume LAB** <위에 이어서 작성>

### **2. Synology NAS server backup**
* NAS server : 데이터 보관 및 안전하게 저장, 백업
* DS128+ : snapshot 기능
* synology NAS server의 관리자 페이지에 접속
* 대시보드 \
-> 언제 백업기능이 동작했는지에 알 수 있게 그래프로 표시되어 정상적으로 NAS가 동작하고 있는지 파악 가능 \
-> 패키지 센터에 snapshot Replication 존재, 스토지리의 형태를 캡처해두었다가 필요할 때에 거의 즉각적으로 복구가 가능한 백업 기능, 최소 5분마다 반복적으로 기록을 남겨두는 방식으로 안전하게 유지됨 \
-> 패키지 센터에 Cloud Station Server 

* NAS server에 로그인 해봤는데 패키지 센터라는 항목을 확인할 수 없음. 내가 관리자가 아니기 때문인 것 같음

http://gomdora.com/221264837369
https://www.synology.com/ko-kr/knowledgebase/DSM/tutorial/Backup/How_to_back_up_your_Synology_NAS

### **3. Cloudera Admin 교육 준비**

https://cloud.skytap.com/vms/a52c3d37ce4c084171b1cf9301b290ff/desktops 

1. install

![install4](https://user-images.githubusercontent.com/33708512/55386681-f792d400-556a-11e9-8701-df1c791e32d0.PNG)

2. CM server 작동 확인

![hdfs1](https://user-images.githubusercontent.com/33708512/55386738-17c29300-556b-11e9-80b2-0789bc2e8e6f.PNG)

![hdfs2](https://user-images.githubusercontent.com/33708512/55386740-17c29300-556b-11e9-9043-ff0d58b5e2cf.PNG)

3. HDFS 

![hdfs3](https://user-images.githubusercontent.com/33708512/55386734-1729fc80-556b-11e9-8030-fe6b2b3157b2.PNG)

![hdfs5](https://user-images.githubusercontent.com/33708512/55386894-66702d00-556b-11e9-8e56-7684e4797aa6.PNG)

![hdfs4](https://user-images.githubusercontent.com/33708512/55386736-17c29300-556b-11e9-91ca-c3359ae46e0c.PNG)

### **4. Flume report 작성**

-----------------

<4.3 ~ 4.5 issue>
--------------


### **1.Cloudera Admin Official 교육**
### HDFS 
* Name node, Data node, Secondary name node

### YARN
* resource (CPU, 메모리 -> container) 할당 관리
* Resource manager, Node manager, Job history server
* 다양한 application(MapReduce, spark, impala)을 돌릴 수 있음

### MapReduce
* MapReduce: programming model, Mapper와 Reducer의 개수를 각각 Configuration할 수 있음
-> programming, command line (-D), default 
-> mapper output: local disk저장 (중간 결과이기 때문), reducer output: hdfs 저장 (최종 결과)
* 1개의 job = 1개의 application, 1개 이상의 task = 1개의 job

### Spark
* engine, 큰 데이터를 처리
* job history를 따로 가지며, execute(≒container)는 task가 끝나도 application이 살아있는 한 반환하지 않아서 속도가 빠름

### Sqoop
* RDBMS <-> HDFS
* only mapper만! copy만 하면 되기 때문
Q. default로 HDFS에 어디에 저장되는지?

### Hive
* 주로 batch, SQL-like query
* meta store: schema + 데이터 위치 
* Hive		HDFS
-> table		directory
-> record 	file
-> partition 	sub directory
(어떤 특성에 따라 데이터를 나눠놓은 것) 


### Impala
* Impala daemon 존재 -> 빠름
* SQL-like (not turn queries into MR jobs)
* Hive Impala -> meta store 공유
* Impala daemon (data node와 같이 worker node에 존재), catalog server, impala state store 


### pig
* 스키마 몰라도 ok (default 값으로 넣음)
* map reduce job 
* meta store 공유 (Hcatalog 이용)
* A DUMP statement or STORE statement to generate OUTPUT

### oozie
* workflow, 여러 job들을 management
* action, coordinator, bundle

### MapReduce Speculative Execution 
* Job이 느릴 때 또 만들어서 수행시키는 것 

### Hadoop Security
* Term
-> Authentication 인증 (password, 지문)
-> Authorization 권한 부여 (access, control list)
-> Encryption 암호화

* Kerberos 
-> client, KDC (Key Distributed Center), Desired Network Service
-> KDC: ticket 발행 (TGS), 인증 확인(AS)

* Kerberos terminology
-> Realm: 참가하고 있는 그룹 (도메인)
-> Principal: unique identity
-> keytab file: key DB (key table)
* Sentry
-> 권한 세분화 (server, database, table, column, view)
-> Hiveserver2와 communication (meta store, 스키마 정보가 여기 있기 때문)

### Fair Scheduler
* Single Resource Fairness: memory만 보고 나눠줌 
-> 먼저 내가 얼만큼 줄 수 있는지 check

* Dominant Resource Fairness: CPU + Memory 고려

* Minimum Resource
-> 최소한 이 job을 시작할 때 이만큼은 줘야 한다고 정의하는 것 (allocate.xml)
-> 위에 두 개의 Scheduler를 하기 전에 줌

### **2. Flume LAB demo**

* Flume 정의
* Structure 간단하게 설명
* Demo \
-> data transfer (basic) \
-> use interceptor (timestamp, host, regex_filter, morphline)

----------------
----------------

NEXT_WEEK)
----------------
1. oozie를 이용하여 여러 ecosystem 서비스 동작하기
2. Zoomdata visualization 