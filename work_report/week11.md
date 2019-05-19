work report (week11)
=============

### **<U>test & AD FS</U>**


<5.13 issue>
-------------

### **1. test**

* cluster 재구축 및 LAB

-> challenges/command.md  참고


-------------

<5.14 issue>
-------------

### **1. zoomdata**

https://streamsets.com/documentation/datacollector/2.6.0.1/help/#Executors/HiveQuery.html#concept_hqg_nzh_vx


https://developer.twitter.com/en/docs/tweets/search/api-reference/get-search-tweets.html

https://developer.twitter.com/en/docs/tweets/rules-and-filtering/overview/premium-operators

* place field


![place2](https://user-images.githubusercontent.com/33708512/57663219-1a1a1180-762e-11e9-8fee-9be4d8086951.PNG)


-------------

<5.14 issue>
-------------

### **1. AD FS**



-> Active Directory Federation Service

-> AD에서 SSO enable!


### <용어 정리>

* AD FS: Active Directory Federation Service, SSO를 지원하기 위해 Active directory에서 제공하는 IdP Server 서비스 \
-> Federation Server: 사용자 인증 및 클레임 발급 정책 등을 담당

* AD DS: Active Directory Domain Service, AD에서 제공하는 user, computer, domain 등을 관리하기 위한 도구. IdP 역할을 하는 Federation Server에서 로그인 과정을 진행할 때, ID/PW 확인과 SAML 토큰에 담길 정보를 가져오는데 사용

* Claims-Aware App: Claim 이라는 보안 토큰을 인식할 수 있는 어플리케이션. Claim을 구성하는 방법으로 SAML 토큰 등을 사용할 수 있음

* Relying Party Trust: SP(RP)와 Federation Server 간의 관계를 의미. Federation server의 metadata와 RP의 metadata 교환을 통해 형성할 수 있으며 신뢰 당사자 트러스트라고도 함

* Claim Provider Trust: 어떤 정보를 claim을 통해 넘겨줄 것인지, 어떤 암호화 방식을 사용할지 등 claim에 대한 정의를 담당함.

<br>

### <설치>

### 1. window server 2016 install

10.100.3.236 \
vm - Window Server 2016

-> https://ittutorials.net/microsoft/windows-server-2016/windows-server-2016-installation/

<br>

### 2. AD DS, DNS 설치

-> https://hope.pe.kr/261

-> https://winadminsblog.wordpress.com/2016/03/14/adds-%EC%84%A4%EC%B9%98%ED%95%98%EA%B8%B0-level-1/


domain: winadmins.local

<br>

### 3. AD CS 설치

https://winadminsblog.wordpress.com/2016/03/16/ad-cs-%EC%84%A4%EC%B9%98-%EB%B0%8F-%EA%B5%AC%EC%84%B1/

<br>


### 4. AD FS 설치


https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn280939(v%3dws.11) 

https://ossian.tistory.com/21 


    * IIS 클릭 - 오른쪽 버튼 클릭 - Internet Information Service (IIS) Manager 클릭 - Server Certificates 클릭 - RootCA, adfs.txt

<br>

![key](https://user-images.githubusercontent.com/33708512/57753622-92183280-7727-11e9-903c-d1fdde79fe1a.PNG)




<br>

-> 무료 인증서 받고 IIS 연결부터 시작하면 됨 (IP 충돌과 네트워크 때문에 멈춤)

https://www.gogetssl.com/

https://ossian.tistory.com/20

계정은 만들었고, CSR Configuration 부터 진행하기 
    

<br>

---------------

### Trouble Shooting

1. 라이센스 동의 후 Custom 선택에서 driver load가 안될 때 (We couldn't find any drives. To get a storage driver, click Load driver)

-> VM setting에서 Configuration 변경 
Microsoft Windows 선택 - Windows Server 2016 선택 후 다시 진행


![trouble1](https://user-images.githubusercontent.com/33708512/57743971-38514180-7702-11e9-93b9-2f47eba69dde.PNG)



-------------

<5.15 issue>
-------------

### **1. zoomdata**

https://developer.twitter.com

* twitter API token 받기 완료

-> hinkim_BDAI app


-------------

<5.16 - 5.17 issue>
-------------

### **이사 준비**


-------------
-------------

NEXT WEEK)
-------------
1. zoomdata