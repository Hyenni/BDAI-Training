work report (week7)
=============


<4.15 issue>
-------------
### **1. Spark**

* spark shell: The Spark shell provides interactive data exploration(REPL)
-> Read/Evaluate/Print Loop

* RDD (Resilient Distributed Dataset) \
-> Resilient: If data in memory is lost, it can be recreated \
-> Distributed: Processed across the cluster \
-> Dataset: Initial data can come from a source such as a file, or it can be
created programmatically

RDDs are the fundamental unit of data in spark \
RDDs are immutable \
-> Data in an RDD is never changed \
-> Transform in sequence to modify the data as needed

* Creating an RDD
1) A File-Based RDD \
-> sc.textFile(" ")

2) data in memory \
-> sc.parallelize( )

3) another RDD (transformation)

* RDD operations \
-> Action: return values
	1) count(): returns the number of elements
	2) take(n): returns an array of the first n elements
	3) collect(): returns an array of all elements \
	-> outofmemory 문제가 생길 수 있음
	4) saveAsTextFile(dir): saves to text file
	
	-> Transformation: defind a new RDD based on the current one
	1) map(function): creates a new RDD by performing a function on each record in the base RDD \
	-> 모든 record에 적용
	2) filter(function) : creates a new RDD by including or excluding each record in the base RDD according to a Boolean function
	
* Lazy Execution \
-> Data in RDDs is not processed until an action is performed \
-> action을 만날때까지 transformation을 쌓아둠 (optimization)

<br>

< exercise > 
* 처음에 pyspark를 열면 CLI command shell로 열림
* vi .bashrc에 끝에 두 줄 주석처리를 지움
* source .bashrc로 적용 후 pyspark를 실행하면
* jupyter notebook로 열림  

<br>

* Files are referenced by absolute or relative URL \
-> Absolute URL
	- file:/home/training/myfile.txt
	- hdfs://nnhost/loudacre/myfile.txt
-> Relative URL
	- default : HDFS 
	
* sc.textFile maps each line in a file to a separate RDD element \
-> what about files with a multi-line input format, such as XML or JSON? \
-> sc.wholeTextFiles(directory)
	- Maps entire contents of each file in a directory to a single RDD element
	

* Single-RDD Transformations \
-> flatMap \
-> distinct \
-> sortBy

cf) Map vs flatMap \
-> flatMap: maps one element in the base RDD to multiple elements

	ex) "the cat sat on the" 
	-> Map:  ["the","cat","sat","on","the"]
	-> flatMap: Map한 후 vectorize, 각각 element가 하나의 row를 차지할 수 있게 변환
		"the"
		"cat"
		"sat"
		"one"
		"the"
		
		
		
* Multi-RDD transformations \
-> intersection \
-> union \
-> zip \
-> subtract


* Other RDD operations \
-> first \
-> foreach \
-> top(n)

* Pair RDDs \
-> map \
-> keyby

-------------

<4.16 issue>
-------------
### **1. Spark**

* Hands-On Exercise: Process Data Files with Apache Spark \
-> activations부터 시작

* Spark Application Confiuration \
-> spark.app.name \
-> spark.master \
-> spark.executor.memory \
-> Log4j log level 등등

* parallel operations \
-> task가 다 끝나야 stage를 넘어갈 수 있다 (다들 비슷한 task를 실행하는 것이 중요)

* RDD Persistence \
-> Persisting an RDD saves the data (in memory, by default)

-------------

<4.17 issue>
-------------

### **1. zoomdata**
10.100.3.214:18630 

1. Data transfer

* twitter api token 받기

	- h3qRZVmNzdkRrfZyqpIiXmAsO (API key)

	- veb1nfTK7M1z7ef4p3SxO3jOg91Y3IkYJtJZBusfQniGjEi9ar (API secret key)

	- 1117643524923478016-U7dtqpAVR2T3Zmxdt1A6i8lSRlOrjq (Access token)

	- CGZh4nASAOrityb7OcE5qrOZfjVep8OKFHvQvpqIAAbrQ (Access token secret)


* twitter API data -> HDFS -> zoomdata
* twitter API data -> ELK -> zoomdata


2. Data visualization (zoomdata)

![zoomdata_Test](https://user-images.githubusercontent.com/33708512/56339201-2a360100-61e8-11e9-8d72-5ff77277fe31.PNG)

* zoomdata dashboard refresh
* live mode

* 설정한 refresh 간격으로는 자동으로 refresh가 되지 않지만 zoomdata 자체적으로 default로 refresh하는 시간은 30분임을 파악함


https://www.zoomdata.com/docs/2.6/what-should-i-check-for-if-live-mode-is-configured-but-the-data-isn-t-updating-on-my-dashboard.html

https://thebipalace.com/2019/01/13/streaminganalyticsarchitecture/

<br>

### **2. DistCP** 
* trello에 error log 올리기 (Distcp oozie error log.txt, Distcp oozie action error log.txt)

* configuration 바꿔도 X (rpc protection : privacy -> authentication)
* Credentials 을 hcat 선택해도 X

계속해서 KERBEROS ERROR 발생

Invalid arguments: Failed on local exception: java.io.IOException: org.apache.hadoop.security.AccessControlException: Client cannot authenticate via:[TOKEN, KERBEROS]; Host Details : local host is: "rbworker2.bdai.com/10.100.3.220"; destination host is: "master02.bdai.com":8020; 

<br>

### **3. < 5.15.x port vs 6.1.x port >**
* trello 첨부

![port](https://user-images.githubusercontent.com/33708512/56328397-c4328500-61b9-11e9-8814-54c1b809a7b6.PNG)

-------------

<4.18 issue>
-------------

### **1. oozie share lib**
* Install Oozie ShareLib

cf) datanode 안될 때 systemctl restart cloudera-scm-restart 

1. path 변경

![oozie_path2](https://user-images.githubusercontent.com/33708512/56340070-b269d580-61eb-11e9-898a-dd50918c054d.png)

![oozie_path](https://user-images.githubusercontent.com/33708512/56340071-b3026c00-61eb-11e9-99b4-20416e2e7aa2.png)


-> path 경로를 설정해 줘야 하는데 바꾸면 자동으로 hdfs가 붙어서 변경됨 \
-> local directory를 바라볼 수 없음 \
-> 따라서 \
-> <U>**oozie 권한으로 새로 생성된 최근 lib 파일에 드라이버 put 하거나,**</U> \
-> symbolic link로 연결해서 hdfs 와 local을 연결해서 local을 바라보게 하는 방법 (=> impossbile) 등이 있음


2. move
-> 권한 없음 (oozie 권한을 획득해야 함)

![oozie_move](https://user-images.githubusercontent.com/33708512/56339740-6b2f1500-61ea-11e9-87d7-02b925a26fc8.PNG)

3. upload
-> Error 500, 

4. kadmin, oozie user로 하는 방법을 찾았으나 비밀번호 몰라서 fail
-> kadmin -p oozie/datanode01.bdai.com@BDAI.COM
-> kinit oozie/datanode01.bdai.com@BDAI.COM


5. 결국 비밀번호 없이 oozie의 ticket으로 job을 실행해야한다는 것

-> impossible!!! (다른 방법 생각해야 함)

-------------

<4.19 issue>
-------------

### **1. oozie share lib**

* install share lib을 할때 바라보는 path 를 알아내서 그 directory 안에 드라이버를 넣으면 직접 넣은 파일까지 다 가지고 새로운 lib_timestamp directory 생성 가능!

-> share lib directory path : /opt/cloudera/parcels/CDH-6.1.1-1.cdh6.1.1.p0.875250/lib/oozie/oozie-sharelib-yarn/lib

![sharelib_1](https://user-images.githubusercontent.com/33708512/56403541-bd307300-629c-11e9-8964-1a7d2d8e77b1.PNG)


-> 이 안에 각각에 서비스들이 존재하고, 각 서비스 directory 안에는 필요한 .jar 파일이 존재함

-> 그리고 모든 jar파일이 한곳으로 symbolic link가 걸려있음을 확인할 수 있음

-> 따라서 링크가 걸려있는 /opt/cloudera/parcels/CDH/jars directory안에 올릴 .jar 파일을 넣고 oozie-sharelib-yarn에 링크를 걸어줌

	ln -s /opt/cloudera/parcels/CDH/jars/mysql-connector-java.jar /opt/cloudera/parcels/CDH-6.1.1-1.cdh6.1.1.p0.875250/lib/oozie/oozie-sharelib-yarn/lib/sqoop

-> 그러면 아래와 같이 link가 잘 걸려있음을 확인할 수 있음 

![sharelib_2](https://user-images.githubusercontent.com/33708512/56403652-59f31080-629d-11e9-8125-515724866897.PNG)

-> 이후, oozie에서 install share lib을 하면 방금 넣은 드라이버까지 올라감을 확인할 수 있음

![sharelib_3](https://user-images.githubusercontent.com/33708512/56403735-bc4c1100-629d-11e9-95c9-9bcd27b5d48d.PNG)

<br>

### **2. SSO**

* C:\Program Files\WSO2\Identity Server\5.7.0 \
-> bin/wso2server.bat 실행하면 wso2 server 올라감 
* C:\Program Files\Java\jdk1.8.0_211
* localhost:9443

* SSO \
-> Single Sign On의 약어 \
-> 사용자가 단 한 번의 인증 절차만으로 다수의 애플리케이션에 접속할 수 있도록 해주는 인증 프로세스를 의미

* Terminology \
-> IdP: Identity Provider, 사용자 인증 대행 및 인증된 사용자의 이름, 역할 등을 담은 SAML 토큰을 발행하는 역할을 담당하는 서버 \
-> SP: Service Provider, 사용자가 접근하려는 서비스 또는 어플리케이션 \
-> SAML: Security Assertion Markup Language, 데이터 교환을 위해 만들어진 표준 프로토콜 \
-> CoT: Circle of Trust, 단일 IdP를 통해 SSO를 제공하는 영역을 의미하며 IdP-SP 간의 metadata 파일을 공유함으로써 형성됨 \
-> metadata: 신뢰 관계에 있는 IdP/SP 정보, encrypt/decrypt를 위해 사용되는 키의 위치 등의 정보를 가지는 xml 파일


https://docs.wso2.com/display/IS530/Configuring+Users

https://helpx.adobe.com/kr/enterprise/kb/configure-wso2-idp-adobe-sso.html

https://medium.com/@Pushpalanka/application-wise-authorization-wso2-identity-server-user-store-per-service-provider-dfea5f9ad758

<br>

----------------
----------------

NEXT_WEEK)
----------------
1. SSO (WSO2 identity server 사용법 익히기)
