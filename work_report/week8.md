work report (week8)
=============


<4.22 issue>
-------------
### **1. SSO**
* API Manager install

https://docs.wso2.com/display/AM200/Installing+on+Windows

https://docs.wso2.com/display/AM210/Configuring+API+Manager+for+SSO#6f58b3b693be468d88e142469c2aa38d

https://docs.wso2.com/display/AM210/Configuring+API+Manager+for+SSO


< API manager + Identity server SSO >

cf. https://enmilocalfunciona.io/single-sign-on-con-wso2-identity-server/

https://docs.wso2.com/display/IS530/Configuring+Shibboleth+IdP+as+a+Trusted+Identity+Provider
-> 깔다가 중간에 변경해야할 xml이 존재하지 않아서 진행 불가. \
-> Configure a user store with Shibboleth. conf directory에 login.config X



<br>

### **2. zoomdata**
* 우고문님과 meeting

0. 먼저 튜토리얼로 연습 \
-> https://www.elastic.co/guide/en/kibana/current/tutorial-dashboard.html

1. Kibana에서 트위터 데이터 (twitter.json raw) visualization

2. zoomdata에서 impala에서 가져온 트위터 데이터 visualization \
-> kibana에서 한것을 토대로 좀 더 이쁘고 분석할만한 chart 생성

3. 위에 있는 하이브로 트위터 데이터 센티먼트 분석하는걸 연습해보고 줌데이터 그 결과를 시각화 \
-> https://hortonworks.com/tutorial/building-a-sentiment-analysis-application/ \
-> https://github.com/cloudera/cdh-twitter-example \
-> https://github.com/shubhamgosain/twitter-Sentiment-Analysis-using-hadoop


### Tutorial

kibana 스키마 생성 -> ELK data upload -> viusalization


-------------------------


<4.23 issue>
-------------
### **1. No-SQL (mongoDB)**

* mongoDB basic 강의 듣기 (edx)

* mongoDB LAB 하고 script 작성

1. university sancbox cluster로 먼저 mongoDB viusalization \
(schema, document, query, map 등등)

2. 개인 cluster sign up 후 shell과 compass로 connect

3. js 파일로 video database upload \
(안에 movieDetails collection 존재)

4. compass에서 새로운 collection 만들고 document 추가하면서 여러 query 실행
(Create, Read, Update, Delete)


---------------------


<4.24 issue>
-------------
### **1. mongoDB DEMO**

* tutorial에서 제공하는 university sandbox cluster와, 개인 sancbox cluster에서 진행
* 각각 compass와 shell로 connect하여 여러 operator 실습

* trouble shooting 
    * 환경변수 설정을 했는데도 cmd에서 mongo라는 명령어를 찾을 수 없다고 나올 때 \
    -> cmd 창을 재실행 후 다시 시도

    * 계정 권한 문제로 compass에서 connect가 안될 때 \
    -> admin 권한으로 사용자 다시 추가하기 (clusters -> Security -> Add New User -> Atlas admin)

<br>

| mysql                | mongoDB              |
| :-------------------:| :-------------------:|
| DB                   | DB                   |
| Table                | Collection           |
| Row                  | Document             |
| Column               | Field                |
---------------------


<4.25 issue>
-------------
### **1. SSO**

* Configuring Single Sign-On

https://docs.wso2.com/display/IS570/Configuring+Single+Sign-On

1. Tomcat install (version 7.x.)
2. Maven install (version 3.6.1) 

-> 환경 변수 필수 (path에 경로 추가)

3. host에 < 127.0.0.1   wso2is.local > 추가 \
-> C:\Windows\System32\drivers\etc\hosts (관리자 권한으로 메모장 실행해서 hosts 파일 열어서 추가해야 함)

4. building a sample (travelocity.com)

![configure1](https://user-images.githubusercontent.com/33708512/56703386-8dbdb280-6743-11e9-8c18-d1793aeb183f.PNG)

![configure2](https://user-images.githubusercontent.com/33708512/56704890-dbd5b480-6749-11e9-8503-f37461c80525.PNG)

### C:\Users\BDAIRND2\modules\samples\sso\sso-agent-sample\src\main\resources의 travelocity.properties 파일 수정 후 진행. 

    * #The URL of the SAML 2.0 Assertion Consumer
    * SAML2.AssertionConsumerURL=http://wso2is.local:8080/travelocity.com/home.jsp

![configure3](https://user-images.githubusercontent.com/33708512/56704891-dbd5b480-6749-11e9-9396-9c7fb2e5d807.PNG)


5. 인증서 받기

![configure4](https://user-images.githubusercontent.com/33708512/56706652-be581900-6750-11e9-9e70-776f3f79a58f.PNG)


6. service provider 추가
* travelocity.com

7. 실행
* http://wso2is.local:8080/travelocity.com

![configure5](https://user-images.githubusercontent.com/33708512/56707046-4e4a9280-6752-11e9-9c8a-f8e451b95031.PNG)


### **2. Flume HandsOn tutorial**

* Flume_HandsOn.docx 작성


----------------
----------------

NEXT_WEEK)
----------------
1. zoomdata (+ kibana)
2. SSO 