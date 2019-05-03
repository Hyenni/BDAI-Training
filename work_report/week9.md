work report (week9)
=============

### **<U>kibana & zoomdata</U>**

<4.29 issue>
-------------

### **1. zoomdata**

### meeting

* sentiment (긍정, 부정 word_cloud) -> kibana, zoomdata
* geo map -> kibana, zoomdata

<br>

### kibana LAB
http://10.100.3.247:5601

ELK index 보는 법: curl -XGET 10.100.3.247:9200/_cat/indices?v

### <U>1. StreamSets로 트위터 데이터 받기</U>
* pipeline: twitter api -> ELK, hdfs, hive (단, database는 미리 생성돼 있어야 함 - hinkim.db)

<br>

### trouble shooting 

### 1. /user/hinkim Permission denied

![permission_error](https://user-images.githubusercontent.com/33708512/56878080-2d08df80-6a8d-11e9-98fd-c9ce568affae.PNG)

-> Hue에서 해당 directory 권한을 변경 \
(우클릭 -> Change permissions -> other write check)

### 2. index read-only 

-> curl -XPUT -H "Content-Type: application/json" http://localhost:9200/_all/_settings -d '{"index.blocks.read_only_allow_delete": null}'

<br>

### <U>2. Hue에서 Hive Table 만들기</U>

* dictionary.tsv 파일 upload 후 생성
* 여러 데이터 가공 후 최종 table => tweetsbi_new

<br>

### <U>3. Hive Table을 Kibana에서 분석</U>

language 분포, 긍정 부정 중립의 sentiment 비율, map, country 분포, msg visualization


![kibana_1](https://user-images.githubusercontent.com/33708512/56886704-87fcff80-6aaa-11e9-8021-c9c1473678b8.PNG)

![kibana_2](https://user-images.githubusercontent.com/33708512/56886707-87fcff80-6aaa-11e9-8078-e0945b079326.PNG)


--------------

<4.30 issue>
-------------
### **1. zoomdata**

우고문님 지시 사항

1. Dashboard를 줌데이타, 키바나에 만들고 특히 워드클라우드 만들어서 어떤 의견들 단어가 많이 나오는지 구현하기

++ 시간과 공간으로 데이터 분석 시각화 중요! tempo-spatial analysis


2. 트위터 데이터를 센티멘트 분석하는것 연습 중요 (영어외 다른 언어 dictionary가 없으니 영어로만)

3. 트위터 데이타중 사용자 프로파일정보를 공개한 사용자가 전체 몇 %정도인지 알아내고 그 사용자 만으로 world map에 사용자 트윝 분포 차트 그리기

4. 줌데이타 자동 refresh 및 얼마나 빨리 대용량 데이타를 실시간으로 가져오는지 시간 측정


### kibana LAB

### <U>1. word cloud</U>
-> 이것을 하기 위해서는 사용자들이 쓴 메세지를 단어 별로 split하여 넣은 테이블을 생성해야 함 \
-> 그리고 더 나아가 긍정적인 단어 중 가장 많은 것 (), 부정적인 단어 중 가장 많은 것() 을 추출하기 위해서 sentiment field도 추가하여 테이블을 생성함

cf. \
view l3: \
각각의 user가 쓴 메세지를 word로 쪼개고, 각 word에 대해 긍정, 중립, 부정의 척도를 나타냄


~~~sql

CREATE TABLE word_cloud AS
    SELECT
        tweet_id,
        word,
        polarity
    FROM l3;

select * from word_cloud where polarity = 1 limit 10;

~~~

<center>view -> table 변환</center>

![word_cloud1](https://user-images.githubusercontent.com/33708512/56948052-e5a45100-6b69-11e9-9a03-1103d813b5c1.PNG)


~~~ sql

CREATE TABLE IF NOT EXISTS word_sentiment as SELECT 
word, case polarity 
WHEN 1 THEN 'positive'
WHEN -1 THEN 'negative'
else 'neutral' end as sentiment
FROM word_cloud;

select * from word_sentiment where sentiment = 'positive' limit 10;

~~~ 

<center>각각의 word에 대한 sentiment field 추가</center>

![word_cloud2](https://user-images.githubusercontent.com/33708512/56948360-deca0e00-6b6a-11e9-9aac-2c732200e07b.PNG)


~~~ sql

CREATE EXTERNAL TABLE word_hinkim ( 
word string, 
sentiment string 
) 
STORED BY 'org.elasticsearch.hadoop.hive.EsStorageHandler' 
TBLPROPERTIES('es.resource' = 'word_hinkim/doc','es.nodes'='10.100.3.247');
INSERT OVERWRITE TABLE word_hinkim SELECT * FROM word_sentiment;

~~~

<center>hive에서 만든 table kibana로 upload</center>

### < kibana word cloud 화면>

![kibana_2](https://user-images.githubusercontent.com/33708512/56949424-d8896100-6b6d-11e9-83ea-863370c7fbc5.PNG)

### **<center> positive word cloud vs negative word cloud </center>**

------------

### **2. distcp**

http://10.100.3.248:7180/cmf/localLogin

* dia -> topaz (CLI)

10.100.3.211 -> 10.100.3.246

hadoop distcp webhdfs://10.100.3.211:50070/tmp/distcptest hdfs://10.100.3.246:8020/tmp

(1) kerberos 

![distcp_error1](https://user-images.githubusercontent.com/33708512/56941326-c4823700-6b4e-11e9-9115-fde474154098.PNG)

-> hinkim@utility에서 

ssh master01 => kinit \
ssh master02 => kinit

(2) connection refused

![distcp_error2](https://user-images.githubusercontent.com/33708512/56941327-c4823700-6b4e-11e9-8b35-ea7afa016c3b.PNG)


--------------

<5.2 issue>
-------------
### **1. zoomdata**

1. 사용자 위치 정보 파악 : coordinates field, 얼만큼 위치 정보를 open했는지 확인할 것 \
-> /'coordinates.coordinates.0 => /coordinates0 \
-> 대략 0.001%


2. 시간 : live mode를 위해서 time bar open \
-> 시간 field 존재해야함 (timestamp)


cast( from_unixtime( cast ( substring ( created_unixtime,1,10) as bigint ) ) as timestamp) ts



### zoomdata LAB
http://10.100.3.212:8080

kibana에서 한 visualization과 동일한 내용

![zoomdata1](https://user-images.githubusercontent.com/33708512/57064688-04d5e680-6d02-11e9-9e07-aba5db1f75f8.PNG)

![zoomdata2](https://user-images.githubusercontent.com/33708512/57064689-056e7d00-6d02-11e9-9e2b-c875090bb7ee.PNG)



<br>


## <지금까지의 visualization 정리!!!!>

kibana -> ELK에 있는 데이터만을 시각화 할 수 있음

zoomdata -> ELK 뿐만 아니라 impala, hive 등과 connect할 수 있음 \
(but, 우리는 cloudera를 사용하고 있기 때문에 hive connect 불가능 - tez engine 필요)

ELK connect) http://10.100.3.247:9200 \
Impala connect) jdbc:hive2://10.100.3.212:21050/hinkim;auth=noSasl


## Point

twitter API 데이터를 ELK에 넣고 hive에 넣고 각각 kibana와 zoomdata에서 시각화 하였음 \
(데이터에서 map과 word cloud 중심으로 분석)


--------------

<5.3 issue>
-------------
### **1. zoomdata**

* live mode : ts (timestamp 형식인데도 enable live mode check X)

* presentation feedback

1. zoomdata kafka connector 가능한지   - OK
2. 병렬처리 - 각각의 daemon이 있어서 실행되는건지
3. memory limit configuration 가능한지
4. engine은 무엇을 사용하는지 (indexing)

<br>

### **2. 지금까지 한 일들 대략적으로 공유**

-----------
-----------

NEXT_WEEK)
----------------
1. distcp
2. test

