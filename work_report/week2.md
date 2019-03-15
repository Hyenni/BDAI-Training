work report (week2)
=============


<3.11 issue>
-------------

### **1. Cloudera unistall & reinstall complete**

### uninstall)

* master 외에 datanode에서도 cloudera agent를 remove 해야 함!! \
-> sudo service cloudera-scm-agent stop \
-> yum -y remove ‘cloudera-manager-*’ \
-> df -h 하면 cm_processes라는 이름의 프로세스가 cloudera 폴더에서 작동중인 것을 확인할 수 있음 \
-> cd / \
-> umount cm_processes 

* cloudera 관련 파일 모두 삭제 \
-> rm -rf /usr/share/cmf /var/lib/cloudera* \
-> rm -rf /var/cache/yum/cloudera* /var/log/cloudera* /var/run/cloudera* \
-> rm -rf /var/lib/flume-ng /var/lib/hadoop* \
-> rm -rf /var/lib/hue /var/lib/navigator /var/lib/oozie \
-> rm -rf /var/lib/solr /var/lib/sqoop* /var/lib/zookeeper \
-> rm -rf /var/lib/hadoop-* /var/lib/hive /var/lib/oozie /var/lib/hue \
-> rm -rf /var/lib/spark /var/lib/zookeeper \
-> rm -rf /var/lib/hbase /var/lib/kudu /var/lib/impala /var/lib/sentry \
-> rm -rf /var/log/hadoop-* /var/log/hive /var/log/oozie /var/log/hue \
-> rm -rf /var/log/spark /var/log/zookeeper /var/log/sqoop2 \
-> rm -rf /opt/cloudera \
-> rm -rf /opt/yarn \
-> rm -rf /etc/cloudera-scm-* \
-> rm -rf /etc/hadoop* /etc/hbase* /etc/hive* /etc/zookeeper \
-> rm -rf /etc/sqoop /etc/sqoop2 /etc/spark /etc/solr /etc/sentry \
-> rm -rf /var/lib/alternatives/* \
-> rm -rf /tmp/scm_* \
-> rm -rf /etc/default/cloudera-scm-agent

* mariadb 관련 파일 모두 삭제 \
-> yum list installed mariadb\* \
-> yum remove -y mariadb.x86_64 \
-> yum remove -y mariadb-libs.x86_64 \
-> yum remove -y mariadb-server.x86_64 \
-> rm -rf /opt/dbdata/mysql/ \
-> rm -rf /opt/dbdata

* process 확인 \
-> ps aux |awk '/cloudera-scm-agent/ {print $2}' \
-> ps -ef |grep supervisord
-> 만약 확인되는 것이 있다면 kill 명령어를 통해 프로세스 죽임
-> 7502 process 존재하는 것 확인 (밑에 화면은 다시 깔고 난 후에 확인한 것이라서 존재하는 것이 맞고, uninstall하고 나서 확인하였을 때에는 존재하지 않았음)

![process](https://user-images.githubusercontent.com/33708512/54176092-bb6ed500-44d0-11e9-8e3b-88995df3aa2b.png)

### reinstall)

* cluster install 할 때 잘 안되면 start node 해주기

![reinstall_finish](https://user-images.githubusercontent.com/33708512/54176109-d04b6880-44d0-11e9-81bc-dfe3edee3541.PNG)

### **2. prepare presentation**

### Big Data
### Hadoop

* HDFS
* YARN
* MapReduce

### PLM

-------------
<3.12 issue>
-------------

### **1. prepare presentation**

-------------
<3.13 issue>
-------------

### **1. prepare presentation**


### cf. linux leraning course

* https://www.edx.org/course/introduction-to-linux


### **2. 발표 준비 공유**


### **3. MapReduce LAB [wordcount]**

### find.jar file

![find_jar](https://user-images.githubusercontent.com/33708512/54264348-34942800-45b6-11e9-97d9-dd0f6ae8dca2.PNG)

### create & view test.txt

 ![test_txt](https://user-images.githubusercontent.com/33708512/54264361-41188080-45b6-11e9-8aec-4776982de175.PNG)

### hadoop upload permission denied

![denied](https://user-images.githubusercontent.com/33708512/54264425-745b0f80-45b6-11e9-9f36-2b3eb1d2d4ee.PNG)

### resolve error

![denied_resolve](https://user-images.githubusercontent.com/33708512/54264633-dfa4e180-45b6-11e9-9446-3d0bbe8868e6.PNG)

### wordcount success

![success](https://user-images.githubusercontent.com/33708512/54264655-ed5a6700-45b6-11e9-877d-e59b00c5ec66.PNG)

### check result

![result](https://user-images.githubusercontent.com/33708512/54264688-ffd4a080-45b6-11e9-8c93-31c778908f5a.PNG)

### check website

![website_output](https://user-images.githubusercontent.com/33708512/54265209-20512a80-45b8-11e9-963e-f180257a5e34.PNG)

![website_output2](https://user-images.githubusercontent.com/33708512/54265211-21825780-45b8-11e9-8ac8-36348265d6dd.PNG)

-------------
<3.14 issue>
-------------

### **1. prepare presentation**

------------- 
<3.15 issue>
-------------

### **1. presentation**

### Feedback (Big data)
* Data node에 block이 유실된다면? \
-> 같은 block이 있는 다른 Data node에게 job을 대신 시킨다 \
-> 만약 대신 시킬 Data node가 없다면 (다른 Data node에게 시킬 수 없는 상황이라면) 그 Data node에서 유실된 block을 복사해서 가져오게 된다 

* 만약에 장애가 발생한 block이 살아난다면? \
-> 살아난 것을 지운다 \
-> 복사한 것을 지운다 \
-> 무작위로 지운다 \
What is an answer?

* jar 명령어 의미

* Secondary Namenode 역할 확인
-> 다시 살아날 때를 대비하여 데이터를 저장
-> edit file, fs image 

* YARN 동작할 때, \
-> RM이 죽었을 때 \
-> NM이 죽었을 때 \
-> AM이 죽었을 때 \
각각 어떻게 일처리를 이어서 하게 되는지에 대해서 알아보기

* Hbase를 대체할 수 있는 NoSQL이 무엇이 있는지 \
-> Hbase는 HDFS가 있어야만 구동이 가능한데 다른 것도 그러한지 확인

* 하나의 job을 관리하는 것이 Application master, 그리고 Application master들을 관리하는 것이 Applications manager \
( job == Application)

* wordcount 실행하였을 때 mapper가 몇개 돌았고 reducer가 몇개 돌았고의 여러 정보들을 확인할 것

* MapReduce는 map, reduce를 하는 소프트웨어 (count, mean ...). job을 하려면 resource가 필요함. 이를 위해 YARN이 resource를 관리하고, 스케줄링을 해주는 것

### **2. ISAAC 구조**
* 공장 + IT \
-> Asset \
-> PLC \
-> HMI \
-> SCADA 

* 공장의 4 Levels \
-> ERP \
-> MES \
-> SCADA \
-> SPS (PLC) \
-> Asset 수집, Input/Output 

-------------
-------------

NEXT WEEK)
-------------
1. 발표 한 것 wiki에 문서로 정리하기 -> 위에 적은 Feedback 항목들 찾아보고 반영하기


