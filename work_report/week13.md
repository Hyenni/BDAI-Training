work report (week13)
=============

### **<U>flume & CJ VC Data Handling</U>**


<5.27 issue>
-------------

### **1. trendminer**

* trendminer installation guide 보기


<br>

### **2. flume custom interceptor**

<br>

(TimestampInterceptor.java)

~~~ java


package org.apache.flume.interceptor;

import java.util.List;
import java.util.Map;
import org.apache.flume.Context;
import org.apache.flume.Event;

import static org.apache.flume.interceptor.TimestampInterceptor.Constants.*;


public class TimestampInterceptor implements Interceptor {

  private final boolean preserveExisting;
  private final String header;


  private TimestampInterceptor(boolean preserveExisting, String header) {
    this.preserveExisting = preserveExisting;
    this.header = header;
  }


  public void initialize() {

  }


  public Event intercept(Event event) {
    Map<String, String> headers = event.getHeaders();
    if (preserveExisting && headers.containsKey(header)) {

    } else {
      long now = System.currentTimeMillis();
      headers.put(header, Long.toString(now));
    }
    return event;
  }


  public List<Event> intercept(List<Event> events) {
    for (Event event : events) {
      intercept(event);
    }
    return events;
  }


  public void close() {

  }


  public static class Builder implements Interceptor.Builder {

    private boolean preserveExisting = DEFAULT_PRESERVE;
    private String header = DEFAULT_HEADER_NAME;


    public Interceptor build() {
      return new TimestampInterceptor(preserveExisting, header);
    }


    public void configure(Context context) {
      preserveExisting = context.getBoolean(CONFIG_PRESERVE, DEFAULT_PRESERVE);
      header = context.getString(CONFIG_HEADER_NAME, DEFAULT_HEADER_NAME);
    }

  }

  public static class Constants {
    public static final String CONFIG_PRESERVE = "preserveExisting";
    public static final boolean DEFAULT_PRESERVE = false;
    public static final String CONFIG_HEADER_NAME = "headerName";
    public static final String DEFAULT_HEADER_NAME = "timestamp";
  }

}


~~~

(custom.conf)

~~~ java 
... 


a1.sources.src1.interceptors = i1
a1.sources.src1.interceptors.i1.type=org.apache.flume.interceptor.TimestampInterceptor$Builder

...
~~~

![custom3](https://user-images.githubusercontent.com/33708512/58389217-62402780-8063-11e9-899b-0895e86a720a.PNG)


<br>



### **3. mysql full disk 문제 해결**

## TOPAZ 접속 안되는 문제 발생 

https://10.100.3.248:7183/cmf/localLogin 으로 접속은 되지만 로그인을 할 때 계속 로딩되며 들어가지지 않는 문제 

$ systemctl restart cloudera-scm-server \
$ tail -f /var/log/cloudera-scm-server/cloudera-scm-server.log

![error1](https://user-images.githubusercontent.com/33708512/58391542-480d4600-8071-11e9-8159-c430166cd5a1.PNG)

-> mysql 문제일 수도 있겠다 싶어 mariadb 로그 확인

$ tail -f /var/log/mariadb/mariadb.log
![error2](https://user-images.githubusercontent.com/33708512/58391544-480d4600-8071-11e9-8866-1b5697971892.PNG)

-> 디스크가 꽉 찼다는 에러 로그 발견


![error3](https://user-images.githubusercontent.com/33708512/58391545-48a5dc80-8071-11e9-981e-49ef43cc6166.PNG)


![error4](https://user-images.githubusercontent.com/33708512/58391546-48a5dc80-8071-11e9-9265-0e3a99c13237.PNG)

=> /var/lib/mysql에 활동 로그가 많이 쌓여있어서 Disk is full error가 발생한 것임

<br>

## 해결 방법

<U>binary_log 지우고 로그 디렉터리 /opt/dbdata/binlog/mysql_binary_log로 변경 및 expire day 2일로 설정한 뒤 재시작할 것!</U>

$ mysql -u root -p \
$ purge binary logs to 'mysql_binary_log.000088'; -> 제일 최근 로그만 남기고 모두 삭제

$ vi /etc/my.cnf

expire_logs_days = 2

log_bin=/opt/dbdata/binlog/mysql_binary_log


$ systemctl restart mariadb


-> 이후 $ systemctl restart cloudera-scm-server 후 정상 작동 확인!


(cf. disk가 100프로 차있으면 간단한 트랜잭션도 작동하지 않기 때문에 최소한의 용량은 확보할 것! )



<br>

-------------------

<br>

<5.28 issue>
-------------

### **1. trendminer**

* structure

TrendMiner (VM) -> 예측 등을 실제적으로 담당할 서버 \
Plant Intergrations (IIS on Windows Server) -> connector\
Historian -> HDFS와 같은 데이터베이스 

![trendminer](https://user-images.githubusercontent.com/33708512/58444981-9421be80-8135-11e9-8aad-a68b5ead6de7.png)


* AD : Active Directory 인증, 그룹 및 사용자 관리, 정책 관리 등과 같은 모든 종류의 기능을 제공하는 디렉터리 서비스
* LDAP : Lightweight Directory Access Protocol 

* SAML : Security Assertion Markup Language \
-> 인증 정보 제공자(identity provider)와 서비스 제공자(service provider) 간의 인증 데이터를 교환하기 위한 XML 기반의 데이터 포맷

<br>

### **2. CJ Vibration Controller Data handling**

* 진동 센서 이슈


* 파일이 local에 있을 땐 문제없이 읽어지는 것 DIA 서버에서 확인 완료 

    -> cf. CSV 파일은 실제 데이터가 아니라 임시 데이터가 들어있는 것을 사용함

10.100.3.214에 streamset이 돌고 있으며 그 안에 데이터 존재함 \
10.100.3.210 (active name node) HDFS에 데이터 저장

![csv_test2](https://user-images.githubusercontent.com/33708512/58526268-660dae80-8209-11e9-8285-e8ef6c54b8c3.PNG)


-------------------

<br>

<5.29 issue>
-------------


### **1.  CJ Vibration Controller Data handling**

* CSV 파일이 외부에 위치할 때


- csv 파일을 로컬에 가져올 수 있으면 ok
- Network File System (NFS) -> server에 configuration으로 공유를 해준다고 적고 client에서 mount로 그 폴더를 설정하면 local directory 처럼 사용 가능
- 실제 csv 파일이 있는 서버의 디렉터리를 읽을 수 있는지

<br>

## -> 다 필요없고, 실제 CJ Streamset이 돌고 있는 서버에 접속하여 데이터 parsing 후 Streamset 실행하여 데이터 전송하면 됨!



<br>

### -> 실제 test! CJ cluster

streamset http://223.171.48.52:18630/

streamset 가동 data node 223.171.48.52:20222


### 1. Streamset이 돌고 있는 data node1 (223.171.48.52:20222) 에 rTest 실행 파일 복사한 후 실행 
​
-> csv 파일이 만들어짐을 확인
​

### 2. Streamset pipeline 만들기 
​
​
-> directory : 위 csv 파일이 만들어진 경로 입력 (/home/csvtest) \
-> hadoop FS: 현재 CJ Active name node 입력 (hdfs://10.0.2.100) 
​

### 3. Hue port forwarding 후 전송된 파일 확인 
​
-> (http://223.171.48.52:8888) \
​
-> 파일 위치 : /tmp/out/sdc-0bb14b20-7d1e-11e9-a65d-6708dfe339b3_3710b16c-9064-4a3a-a255-fc049b9486ce

![csv_test3](https://user-images.githubusercontent.com/33708512/58541642-e8f92e00-8236-11e9-9778-fb059fd4a6d1.PNG)



-------------------

<br>

<5.30 issue>
-------------

### **1. trendminer**

추후에 실제 라이센스가 나오고 설치 후 테스트를 한다면,

* Hardware spec 
* 어떤 기능들이 있는지 정리 

파워포인트로 정리할 것

<br>

### **2.  CJ Vibration Controller Data handling**

### <U>실시간으로 데이터가 쌓일 때 append 된 이후의 데이터만 가져오는 솔루션이 필요함</U>

file tail \
https://streamsets.com/documentation/datacollector/latest/help/datacollector/UserGuide/Origins/FileTail.html


cf. 임의 csv 파일 만드는 python 코드 이용하여 테스트함

### 1) worker3 node (10.100.3.214) /home/hinkim에 make.py 실행


~~~ python 

import csv
import random

records=9000000
print("Making %d records\n" % records)

fieldnames=['id','name','age','city']
writer = csv.DictWriter(open("people.csv", "w"), fieldnames=fieldnames)

names=['Deepak', 'Sangeeta', 'Geetika', 'Anubhav', 'Sahil', 'Akshay']
cities=['Delhi', 'Kolkata', 'Chennai', 'Mumbai']

writer.writerow(dict(zip(fieldnames, fieldnames)))
for i in range(0, records):

  writer.writerow(dict([
    ('id', i),
    ('name', random.choice(names)),
    ('age', str(random.randint(24,26))),
    ('city', random.choice(cities))]))

~~~


### 2) people.csv 파일이 만들어짐 
( 9000000개의 레코드가 만들어질때까지 계속 실행되고 있음)
​
### 3) 위에 python 파일은 실행되고 있고, 동시에 streamset pipeline을 실행함​
( 참고 - http://10.100.3.214:18630 cjvibrationcontrollerrealtimetransfer )
​
 ​
### 4) pipeline이 돌고 있으면 옮겨진 파일이 hue에서 확인을 못하기 때문에 stop 시킨 후, hue에서 확인
​
output directory - /tmp/hinkim


![csv_test4](https://user-images.githubusercontent.com/33708512/58609013-04207800-82e1-11e9-8d13-2316e3710f0b.png)

### 5) python 파일은 계속 실행되고 있는 상태에서 다시 한번 streamset pipeline 실행

### 6) update된 파일을 확인하기 위해서 pipeline 중지시키고 hue에서 확인
​

![csv_test5](https://user-images.githubusercontent.com/33708512/58609015-04207800-82e1-11e9-8902-7d110435df90.png)

<br>
​
-> 파일을 이어서 읽어옴을 확인함
​
<br><br><br>

### +++ <U>추가로 streamset에서 특정 필드 계산하고 전송하는 것 필요</U>

https://streamsets.com/documentation/datacollector/latest/help/datacollector/UserGuide/Expression_Language/Functions.html#concept_s2c_q14_fs


### 1. 데이터가 하나의 string으로 받아와서 ','를 기준으로 split
### 2. 모든 데이터 타입이 string이여서 숫자 계산을 하기 위해 type converter 
### 3. Expression Evaluator

![csv_expression1](https://user-images.githubusercontent.com/33708512/58615820-ffff5500-82f6-11e9-8337-afb9c2f1ac1c.PNG)



### 4. 정렬 및 list map output type 적용

### 5. HDFS 전송

### pipeline 및 계산 적용 확인

![csv_expression2](https://user-images.githubusercontent.com/33708512/58615821-0097eb80-82f7-11e9-8d2f-aa07cbb412c8.PNG)

![csv_expression3](https://user-images.githubusercontent.com/33708512/58616015-8b78e600-82f7-11e9-8613-52e997fcbff6.png)


<br>


### ++ 계산된 값, 기존 값 따로따로 hdfs에 저장하는 final pipeline 완성

![csv_expression4](https://user-images.githubusercontent.com/33708512/58673363-025cc000-8386-11e9-92f4-3fe7f27efc2d.PNG)


-------------

<br>

<5.31 issue>
-------------


### **1.  CJ Vibration Controller Data handling**

* 데이터 가져오고 parsing하는 cpp 실행파일이 1시간 돌고 꺼짐을 확인

![transfer_error](https://user-images.githubusercontent.com/33708512/58679230-8e2e1680-839d-11e9-9b29-b41d6c547168.png)

-> 데이터를 받아오는 시간을 1초 간격에서 2초 간격으로 셋팅 후 다시 running


-> 2초로 셋팅해도 멈춤

-> 예외처리가 되어 있지 않은 기본 샘플 코드기 때문에, 연결이 끊어졌을 때 다시 retry 하는 등의 예외처리를 추가한 readVmm3.cpp 파일 running

-> 멈춤

-> 3초로 셋팅 후 running 


![transfer_error2](https://user-images.githubusercontent.com/33708512/58687984-20dead80-83be-11e9-9d86-74dfb5513d29.png)

-> request가 가지 않음 

-> 시간이 좀 지나고 나서는 다시 connect 되고 진행 됐음

-> 매번 disconnect - connect를 반복하는 코드로 바꿔 다시 running 

-> ing 상태

----------------
----------------

NEXT_WEEK)
----------------
1. trendminer 설치