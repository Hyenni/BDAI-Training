work report (week12)
=============

### **<U>trendminer & flume</U>**


<5.20 issue>
-------------

### **1. trendminer**

https://academy.trendminer.com


* 데이터를 받아와서 motor의 상태를 학습 후 고장을 예측하는 플랫폼  
(우리는 motor에 sensor를 달아서 데이터를 받아올 것) \
-> 고장 전에 일어나는 전조 증상을 파악하는 형태 (labeling 필요)

* 비정상적인 패턴이나 이벤트를 모니터링하며 프로세스의 상태를 파악할 수 있음


<br>

### **2. Flume, streamset demo 준비**

* streamset vm 올려서 한번 테스트 해보기 

-------------


<5.21 issue>
-------------

### **1. trendminer**

* 기본 과정 완료


### **2. Flume, streamset demo**

* flume - interceptor

* streamset - kafka


-------------


<5.22 issue>
-------------

### **1. flume**

* custom interceptor


/opt/cloudera/parcels/CDH/jars 에 custom interceptor 구현할 java 파일 생성

<br>

(HostInterceptor.java)

~~~ java

package org.apache.flume.interceptor;

import java.net.InetAddress;
import java.net.UnknownHostException;
import java.util.List;
import java.util.Map;
import org.apache.flume.Context;
import org.apache.flume.Event;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import static org.apache.flume.interceptor.HostInterceptor.Constants.*;


public class HostInterceptor implements Interceptor {

  private static final Logger logger = LoggerFactory
          .getLogger(HostInterceptor.class);

  private final boolean preserveExisting;
  private final String header;
  private String host = null;


  private HostInterceptor(boolean preserveExisting,
      boolean useIP, String header) {
    this.preserveExisting = preserveExisting;
    this.header = header;
    InetAddress addr;
    try {
      addr = InetAddress.getLocalHost();
      if (useIP) {
        host = addr.getHostAddress();
      } else {
        host = addr.getCanonicalHostName();
      }
    } catch (UnknownHostException e) {
      logger.warn("Could not get local host address. Exception follows.", e);
    }
  }


  public void initialize() {
  }


  public Event intercept(Event event) {
    Map<String, String> headers = event.getHeaders();

    if (preserveExisting && headers.containsKey(header)) {
      return event;
    }
    if (host != null) {
      headers.put(header, host);
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

    private boolean preserveExisting = PRESERVE_DFLT;
    private boolean useIP = USE_IP_DFLT;
    private String header = HOST;


    public Interceptor build() {
      return new HostInterceptor(preserveExisting, useIP, header);
    }

    public void configure(Context context) {
      preserveExisting = context.getBoolean(PRESERVE, PRESERVE_DFLT);
      useIP = context.getBoolean(USE_IP, USE_IP_DFLT);
      header = context.getString(HOST_HEADER, HOST);
    }
  }

  public static class Constants {
    public static String HOST = "host";

    public static String PRESERVE = "preserveExisting";
    public static boolean PRESERVE_DFLT = false;

    public static String USE_IP = "useIP";
    public static boolean USE_IP_DFLT = true;

    public static String HOST_HEADER = "hostHeader";
  }

}


~~~

(custom.conf)

~~~ java 

... 

a1.sources.src1.interceptors = hostTimeInterceptor
a1.sources.src1.interceptors.hostTimeInterceptor.type=org.apache.flume.interceptor.HostInterceptor$Builder
a1.sources.src1.interceptors.hostTimeInterceptor.key=HostTime

...

~~~

<br>

![custom](https://user-images.githubusercontent.com/33708512/58314676-faaf8f80-7e4a-11e9-8af2-15637aa48607.PNG)

<br>

(참고 링크)


https://github.com/apache/flume/blob/trunk/flume-ng-core/src/main/java/org/apache/flume/interceptor/HostInterceptor.java

<br>

--------------


### **2. trendminer**

* 고급 과정 완료

-------------

<br>

<5.23 issue>
-------------


### **1. trendminer summary**


## <U>Basic - Module 1) Descriptive Analytics</U>

### 1. tag => data set

* Analysis of data in TrendMiner starts with loading in your time series data in the form of tags.

    -> 신호와 노이즈를 구별할 수 있으며 향후 분석을 위해 관심있는 데이터를 식별하는 데 도움이 됨

    -> 분석에 올바른 데이터 세트를 확보하기 위해 분류, 통합, 필터링 등 새로운 태그로 저장하며 활용

<br>

### 2. asset browser => 작업할 태그를 찾는 상황 별 방법 

* 자산 시스템 구조를 검색하여 특정 자산과 관련된 태그를 찾는 것이 더 쉬울 때 이용

<br>

### 3. time navigation => 데이터 탐색 기능

* 시간 탐색을 사용하면 문제를 정의하거나 가설을 확인하는데 도움이 되는 데이터 trend를 빠르게 찾을 수 있음

* the double slider 

    -> 탐색 및 검색 작업을 수행할 시간 동안 context bar의 시간 범위를 제어함

* the context bar

    -> 검토할 관련 데이터의 개요를 보여주며, 관심 기간을 편리하게 선택하는 데 사용할 수 있음

* the focus chart

### 4. data scooter => 설정된 시간대에 그래픽 데이터의 값을 포함하는 포커스 차트의 상자

![scooter](https://user-images.githubusercontent.com/33708512/58218168-e7b09880-7d40-11e9-81fe-59e0256d6bbe.PNG)


<br>

### 5. work organizer => view를 관리하고 저장

### 6. data export 

* Export raw data from the Focus-chart
* Export search results, start and end times, and the duration for each result

<br>

### 7. scatter plot => correlation

* 적어도 두 개의 태그를 표시해서 이용해야 하며 회귀선과 상관 계수 보여줌

<br>

## <U>Module 2) Discovery Analytics</U>

### 8. Search 

* Similarity search 

    -> 시각적으로 유사한 이벤트를 식별할 수 있음

* Value based search 

    -> 특정 임계 값 이하의 온도 등의 특정 조건을 정의하여 상세 검색을 할 수 있음

-> 검색 결과 저장도 가능, 나중에 검색을 재사용하거나 파생된 조건 기반 필터도 만들 수 있음

<br>

### 9. filter

* Time based filter 

    -> 특정 기간 필터링

* Criteria based filter 

    -> 저장된 검색에 연결되며 저장된 검색의 조건이 충족되면 업데이트


<br>

### 10. impact analysis

* 관심 있는 프로세스 이벤트의 빈도를 식별하기 위해 과거 프로세스 데이터를 평가하는 것 \
(과거에 비슷한 프로세스 동작이 얼마나 자주 있었는지)

-> 원치 않는 프로세스 이벤트의 빈도가 높으면 품질 및 생산성에 대한 영향이 높을 수 있음


<br>

### 11. hypothesis testing

-> 동영상 다시 볼 것!

<br>

## <U>Module 3) Root cause analysis in TrendMiner</U> 

-> 정상 및 비정상적인 동작 기간을 오버레이하고 오버레이 비교 기능을 사용하여 사용자는 이탈 동작을 나타내는 태그를 신속하게 식별할 수 있음


### 12. layer menu

* The layer menu displays all layers (time periods) which are added to a certain view. They can be shown in or hidden from the focus chart.

-> layering은 focus chart의 데이터를 시각적으로 비교하고, 다른 시간대 또는 운영 조건에 따라 유사한 이벤트를 비교하면서 문제를 해결하고 프로세스를 이해하는데 도움이 됨


### 13. Recommendation engine

* 비슷한 문제가 있는 항목을 검색하고, 그에 대한 원인을 파악하여 제공함 


<br>

## <U>Module 4) Process monitoring</U>

### 14. monitoring and alert

* The TrendMiner monitor uses your defined matches to act as a watchdog for defined process issues

    -> 사전 정의 된 조건을 만족하는 프로세스 값의 조합 \
    -> 기계 마모를 나타내는 특정 패턴 일치 \
    -> 서명 지문에 문제가 생겼을 때 \
    -> 검색이 일치할 때 등 사용

 -> inbox (받은편지함)으로 알람 수신 가능

<br>

 ### 15. live mode

* 도착한 데이터를 실시간으로 시각화할 수 있음

* 오른쪽 하단에 있는 재생 아이콘을 클릭 -> 10초마다 업데이트

<br>

## <U>Advanced - Module 1) Descriptive Analytics</U>

### 16. Tag builder => 태그 작성기

* Formula 

    -> 일반적인 수학 조건부 표현식을 사용하여 태그를 기준으로 계산하여 새 태그를 만들 수 있음

    -> ex) abs(A-B)

* Aggregation

    -> 특정 기간 동안 평균, 최소, 최대 등의 집계 기능을 사용하여 잡음이 많은 데이터를 부드럽게하거나 파생물을 만들 수 있음

    -> Delta: 델타 연산자는 지정된 시간 간격의 끝과 시작 값 사이의 차이를 반환함


* Data import 

    -> csv 파일 형식으로 데이터를 가져와 태그로 저장할 수 있음

    -> 하지만 trendminer가 데이터를 올바르게 읽을 수 있도록 특정 포맷으로 작성돼야 함


<br>

## <U>Module 2) Discovery Analytics</U>

### 17. digital step search

* 데이터의 단계 변화 인식 

* 어떤 상태에서 어떤 상태로 바뀌고, 그에 대한 지속시간을 설정해서 이 조건에 맞는 결과만 시간별로 정렬되서 나옴. context bar 아래에 파란색으로 표시 됨

ex) Aplha 1 day - Delta 3 day

<br>

### 18. operating area search

* scatter plot에 그려진 영역 내부 또는 외부에서 데이터를 검색할 수 있음

-> 예제 결과가 무엇을 의미하는지 잘 모르겠음


<br>

### 19. Calculations on search result

* 검색 결과에 대해서 평균값, 최소값, 최대값 등등의 집계하고 정렬할 수 있음

<br>

## <U>Module 3) Diagnostic Analytics</U> => 잘 모르겠음

### 20. Influence factor

* 특정 기간 동안 관심 태그와 하나 이상의 잠재적 영향인자 사이의 가장 유의미한 선형 관계를 식별할 수 있음


### 21. fingerprint deviations tool


<br>

## <U>Module 4) Prediction and Monitoring</U>

### 22. Fingerprint

* 일반적인 또는 이상적인 행동을 나타내는 오버레이의 집합


### 23. Predictive mode

* 이전에 관찰된 이전 동작을 기반으로 프로세스의 향후 발전을 예측할 수 있음




<br>

-------------


<5.24 issue>
-------------

### **1. trendminer presentation**

* trendminer

* function


### **2. flume custom interceptor**


-------------
-------------

NEXT WEEK)
-------------
1. kafka install test (topaz)






















