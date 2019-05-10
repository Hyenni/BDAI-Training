work report (week10)
=============

### **<U>test & zoomdata</U>**

<5.7 issue>
-------------

### **1. zoomdata**

### meeting

1. 스트림셋에서 하둡에 저장한 데이터에서 메시지를 토크나이저 하는 부분에서 워드클라우드 만들기 (진용씨 데이터로)

-> message를 워드로 쪼개서 모두 다 보여주는 것 (positive negative X)

2. 똑같은 센티멘트 분석된 워드클라우드  (positive negative O)

3. 똑같은 하둡데이터에서 geo정보 있는거를 워드맵 하는것

4. 스트림셋에서 메시지 토크나이저 시도

5. 줌데이타 만료 됨: 푸나한테 연장되는키 달라고 함, 안되면 코딩연습상 탑100 워드 워드클라우드, 탑20 트위터 사용자등를 엑셀 3DMap에 만들기

6. 푸나에게 시간 리프레쉬 기능 안되면 물어보기


### **2. test**

* aws cluster connect
* hiveql

-------------

<5.8 issue>
-------------

### **1. zoomdata**

* 진용씨 데이터로 전체 word cloud, positive word cloud, negative word cloud visualization


![cloud1](https://user-images.githubusercontent.com/33708512/57340131-8056ee00-716f-11e9-84a2-3f7f9ae45b68.PNG)

![cloud2](https://user-images.githubusercontent.com/33708512/57340132-8056ee00-716f-11e9-8e68-ec01e1fa79e5.PNG)


* geo 데이터 world map

좌표 값: 43/2958498 (0.0015%)

~~~ sql

create table geo_notnull as
select * from jylee.geotest3 where coordinates0 != 0.0 and coordinates1 != 0.0;

select * from geo_notnull limit 10;

~~~

![geo0](https://user-images.githubusercontent.com/33708512/57341643-332a4a80-7176-11e9-893d-74d299fbdefb.PNG)



![geo1](https://user-images.githubusercontent.com/33708512/57341220-7aafd700-7174-11e9-9060-70358bd6a3c6.PNG)

![geo2](https://user-images.githubusercontent.com/33708512/57341222-7b486d80-7174-11e9-8b6b-fe92db299a9a.PNG)


* streamset tokenizer

hive query를 직접 넣을 수 있음 -> 이용해서 해볼 것!



-------------

<5.9 issue>
-------------

### **1. test**

### 개인 클러스터 구축하기 

* virtual box에 centos 4개 올려서 구축

* environment setting
    * master01 - 192.168.56.101
    * datanode01 - 192.168.56.102
    * datanode02 - 192.168.56.103
    * datanode03 - 192.168.56.104
    * netmask - 255.255.255.0
    * gateway - 192.168.56.1
    * dns - 164.124.101.2

* ping 안날라 갈 때 
    * 호스트 전용 어탭터와 NAT 네트워크로 설정했는지 체크
    * systemctl restart network
    * ifdown enp0s3
    * ifup enp0s3


![install10](https://user-images.githubusercontent.com/33708512/57495175-e1192e80-7307-11e9-899b-7b392930fe7c.PNG)

### 실습 살펴보기


-------------

<5.10 issue>
-------------

### **1. flume, streamset demo**


-------------
-------------

NEXT WEEK)
-------------
1. test