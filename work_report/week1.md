work report
=============


<3.4 issue>
-------------

### **1.  Flink, 이진용 연구원님 발표 참관**

### flink 
* Streaming & batch processing platform
* real time 

### feedback

* Why? What? 이 중요 (기본에 충실)
* 슬라이드 action은 중요하지 않음 (why? 한눈에 슬라이드를 볼 수 없어서 내용을 이해하는데 독이 될 수 있음)
* 아는 것만 발표하고, 자신감 있게 하기
* technological하게 안되면 story telling으로 대신하자 
    * 세상에는 스트리밍 데이터를 다루는 툴이 많은데 그 중에서 Flink가 있고, 이것은 어떤 특징으로 각광받고 있으며 어떻게 사용하고 등등 배경 설명하기


### **2. 빅데이터의 전환점**

* hardware에서 software로!
* software가 hardware가 고장이 났는지에 대한 error 인식을 한 후 대응책을 제안하여 문제 해결

-------------

<3.5 issue>
-------------

### **1. virtual box + centos7 installing**

### understanding package

* net-tools: netstat, ifconfig 명령어를 사용하기 위해서는 net-tools package를 설치해야 한다.

    * 그렇다면 netstat, ifconfig의 용도는 무엇일까?

        * netstat: 네트워크의 연결 상태를 확인할 수 있다
        * ifconfig: 네트워크 인터페이스를 확인과 설정을 할 수 있다 (IP 주소, 넷마스크, 브로드 캐스트 주소)

    -> yum -y install net-tools

* telnet: 가장 기본적으로 쓰이는 원격접속 도구

    -> yum -y install telnet

* wget: world wide web get, 웹 서버로부터 원하는 웹 사이트의 정보를 가져오는 역할을 한다. http, https, ftp 프로토콜을 이용하여 다운로드 받을 수 있고 wget [URL]로 사용하면 된다

    -> yum install wget

* bind-utils: 도메인 주소를 IP 주소로 변경해준다

    -> yum install bind-utils

* NAT: Network Address Translation

* SELinux: Security-Enhanced Linux, 강제 접근 제어를 포함한 접근 제어 보안 정책을 지원하는 메커니즘을 제공하는 리눅스 커널 보안 모듈

    * cf. 강제 접근 제어 \
    응용 프로그램에서 불필요한 부분은 제외하고 오직 필요한 기능에 대해서만 사용 권한을 안전하게 부여하는 것. 따라서 사용자는 한 응용 프로그램에게 그 프로그램이 제대로 작동하는 데 필요한 권한만 안전하게 부여할 수 있다.

    -> sed -i 's/^SELINUX=enforcing/SELINUX=disabled/' /etc/
    selinux/config \
    -> sudo shutdown -r now    or     reboot (재부팅) \
    -> grep -P '^SELINUX=' /etc/selinux/config

* firewall : 방화벽 시스템

    -> systemctl stop firewalld \
    #리부팅 뒤에도 실행 안되게끔 \
    -> systemctl disable firewalld

* kdump : The kexec-based Crash Dumping Solution, kexec를 바탕으로 한 '커널 크러쉬 덤핑 메커니즘' 
    * 이는 커널 패닉이 발생하였을 때 시스템의 메모리 상태를 vmcore라는 파일 형태로 생성하는 작업이다. 
    * 장애가 발생하였을 때 그 원인을 찾아내서 같은 장애가 나타나지 않도록 하는 것이 중요한데, 그 원인을 찾을 수 있는 실마리를 제공하는 것이 
    * vmcore 라는 코어 파일이며, 코어 파일을 생성하는 것이 kdump이다

    -> systemctl stop kdump

* lsblk : 현재 디스크의 파티션 구조 확인

-------------

<3.6 issue>
-------------

### **1. putty ssh 접속**

* host <-> guest
* guest <-> guest 
Virtual guest ip 주소는 static으로 고정하였음 
(/etc/sysconfig/network-scripts/ifcfg-enp0s3 수정함)

BOOTPROTO=”none” \
IPADDR=” “ <- 호스트 네트워크 관리자에서 가능한 IP 주소의 범위를 확인한 후, 각각 다르게 할당해준다. \
GATEWAY=”192.168.56.1” <- host ip 주소 \
NETMASK=”255.255.255.0” <- 24비트를 사용하므로 \
DNS=”164.124.101.2” <- 회사 공인 ip 

cf) host IP : 192.168.56.1
guest IP
- worker03 (master) : 192.168.56.103
- worker01 : 192.168.56.101
- worker02 : 192.168.56.102

### **2. iptables**

* 시스템 관리자가 리눅스 커널 방화벽이 제공하는 테이블들과 그것을 저장하는 규칙들을 구성할 수 있게 해주는 사용자 공간 응용 프로그램이다.

* /etc/hosts.allow : 특정 IP의 접근을 허용할 수 있다. 
* /etc/hosts.deny : 특정 IP의 접근을 차단할 수 있다. \
cf) hosts.deny에서는 차단되어 있어도, hosts.allow에서 접속허용이 되어 있다면 접속이 가능하다.

### **3. Cloudera installing**
* tail -f: log 보면서 설치 진행상황 지켜보기

### **4. sudo**
* superuser do, substitute user do (다른 사용자의 권한으로 명령을 이행하라)
* 루트 권한 대행, 다른 사용자의 보안권한과 관련된 프로그램을 구동할 수 있게 해주는 프로그램. 

-------------

<3.7 issue>
-------------

### **1. Cloudera 설치 완료**

* work03.isaac-eng.com wk3 -> master (centos7-1)
* /opt 안에 app directory 만들어서 설치 프로그램은 여기에 모두 설치하였음 (home directory 지저분하지 않게 관리)
* 클러스터 설정에서 all service 대신 custom service로 선택하여 HDFS, YARN, ZooKeeper만 선택 후 진행 \
-> 역할 할당 사용자 지정을 할 때 반드시 name node는 master!, data node는 data!, resource manager와 job history server는 master!, zookeeper는 모두 선택하여 setting

### error issue
* /opt/cloudera/cm/schema/scm_prepare_database.sh mysql scm scm \
-> database를 만들어주는 역할, mysql이 cm과 같은 호스트에 있을 때 \
-> 다른 호스트에 있을 때에 사용하는 명령어도 모르고 이어서 또 실행했더니 \
/etc/cloudera-scm-server/db.properties 파일에 com.cloudera.db.host=localhost 가 바뀌어 있고 netstat -antp | grep LISTEN으로 확인할 때 7180 port가 없는 것을 확인 \
-> 이 때문에 http://192.168.56.103:7180 페이지 refused to connect \
-> 그래서 cloudera-scm-server를 stop 시킨 후, 다시 맨 위에 같은 호스트에 있을 때 사용하는 명령어를 재실행시킴 \
-> 이후 All done, your SCM database is configured correctly! 문구가 나오고 종료되면 다시 server를 start 시킴 \
-> tail -f /var/log/cloudera-scm-server/cloudera-scm-server.log 명령어를 통해 잘 setting 이 됐는지 log를 보면서 확인 \
-> 오류 없이 완료되면 db.properties에 localhost로 알맞게 setting 돼있으며, 7180 port도 정상적으로 확인할 수 있음 \
-> cloudera manager 접속 성공 

### **2. putty ssh 접속할 때 빠르게 찾게 하는 방법**
* 접속할 때 제일 먼저 rc.local 파일을 찾기 때문에, 여기 안에 systemctl restart network 명령어를 넣어준다.
* chmod u+x /etc/rc.d/rc.local
* systemctl start rc-local

Q. 처음에 켰을 때 network restart가 되는 것이 아닌가? 껐다 키더라도 systemctl restart network를 하는 것과 같은 효과가 나타나지 않는 경우도 있기 때문에 무조건 네트워크를 재 작하는 명령어를 rc.local에 넣어주는 것으로 이해해도 되는지 확인하기

-------------

<3.8 issue>
-------------

### **1. cloudera uninstall**
* systemctl status cloudera-scm-server.service

### **2. cloudera reinstall < ~ing >**

### error issue 1.

![error1](https://user-images.githubusercontent.com/33708512/54018488-1f8d5280-41cc-11e9-9755-e76bc3421d04.PNG)

-> cause
* java jdk의 conflict

-> solution) 
* yum erase oracle-j2sdk1.8
* yum clean all

### error issue 2. << 해결중>>

![error2](https://user-images.githubusercontent.com/33708512/54066111-34222700-426e-11e9-8d10-750072870627.PNG)

-> cause
* datanode에서 cloudera를 지우지 않았기 때문

-> solution)
* sudo service cloudera-scm-agent stop
* yum -y remove 'cloudera-manager-*'
* cd / 
* umount cm_processes

### **3. 발표 참관**

### ansible, 남정훈 연구원님

* 서버를 관리하기 위한 환경구성 자동화 도구
* host grouping
* 모든 cluster에 일일이 설치하고 환경 구축을 해야하는 번거로움을 없앨 수 있는 tool

### hue, 송진호 연구원님

* hue server port: 8888
* hue load balancer port: 8889 \
-> heartbeat를 보내서 가장 반응이 빠른 서비스를 연결해줌 \
-> server가 죽거나, 일이 많으면 heartbeat가 느릴 것이고 \
-> 따라서 그 외에 server로 연결해줌으로써 fail over 

* load balancer
* fail over
* ha
* ha proxy


-------------
-------------
NEXT WEEK)
-------------
1. cloudera reinstall complete
2. hadoop 기본 이해  (HDFS, hue ...)

