---
title: "하둡에서 YARN 명령어 실행하기"
date: 2022-07-18T16:03:22+09:00
categories:
- dataengineering
tags:
- dataengineering
keywords:
- data engineering, python, development, mapreduce
image: knit-g8e2df2961_1920.jpg
---
![yarn](https://github.com/shcDE/pictures/blob/main/images_for_blog/knit-g8e2df2961_1920.jpg?raw=true)
_________________________________________________________________________________________________________________________________________________________________________
# YARN 명령어는 어떻게 사용할까요?
_________________________________________________________________________________________________________________________________________________________________________
안녕하세요. shcDE입니다. 오늘은 하둡 v2부터 적용된 YARN에 대해 알아보겠습니다. YARN(Yet Another Resource Negotiator)은 하둡에서 도입한 클러스터 리소스 관리 및 애플리케이션 라이프 사이클 관리를 위한 아키텍처를 의미하며, 이번 포스팅에서는 스케줄러, 메모리 설정, 명령어, 노드 레이블, 타임라인 서비스 v2에 대해 알아보겠습니다. 시작 전에, 해당 포스팅은 ['빅데이터 - 하둡, 하이브로 시작하기'(위키독스)](https://wikidocs.net/book/2203)를 바탕으로 작성하였음을 알려드립니다.
_________________________________________________________________________________________________________________________________________________________________________
우선 YARN 스케줄러에 대해 알려드리겠습니다. 스케줄러는 자원 할당을 위한 정책을 의미하며, 하둡에서 제공하는 기본 스케줄러는 다음과 같습니다.
|스케줄러|클래스명|
|---|------------------|
|FIFO 스케줄러|org.apache.hadoop.yarn.server.resourcemanager.scheduler.fifo.FifoScheduler|
|Fair 스케줄러|org.apache.hadoop.yarn.server.resourcemanager.scheduler.fair.FairScheduler|
|Capacity 스케줄러|org.apache.hadoop.yarn.server.resourcemanager.scheduler.capacity.CapacityScheduler|

_________________________________________________________________________________________________________________________________________________________________________
스케줄러 설정 방법은 다음과 같습니다.
_________________________________________________________________________________________________________________________________________________________________________
```
<property>
 <name>yarn.resourcemanager.scheduler.class</name>
 <value>org.apache.hadoop.yarn.server.resourcemanager.scheduler.capacity.CapacityScheduler</value>
</property>
```
_________________________________________________________________________________________________________________________________________________________________________
각 스케줄러에 대한 문서의 설명은 다음과 같으며, 추가적인 예시는 문서를 참고해서 확인할 수 있습니다.
_________________________________________________________________________________________________________________________________________________________________________
1. FIFO Scheduler : FIFO 스케줄러는 이름과 같이 먼저 들어온 작업이 먼저 처리됩니다. 작업의 제출 순서대로 처리되고, 먼저 들어온 작업이 종료될 때까지 다음작업은 대기합니다. 이로 인해 자원을 효율적으로 사용할 수 없기 때문에 FIFO 스케줄러는 테스트 목적으로만 사용하는 것이 좋습니다. 
2. Fair Scheduler : Fair 스케줄러는 제출 된 작업이 동등하게 리소스를 점유합니다. 작업 큐에 작업이 제출되면 클러스터는 자원을 조절하여 작업에 균등하게 자원을 할당 하여 줍니다. 
3. Capacity Scheduler : 하둡2의 기본 스케줄러 입니다. 트리 형태로 큐를 선언하고 각 큐 별로 이용할 수 있는 자원의 용량을 정하여 주면 그 용량에 맞게 자원을 할당 하여 줍니다.
_________________________________________________________________________________________________________________________________________________________________________
다음으로 메모리 설정에 대해 설명드리겠습니다. YARN의 메모리 설정은 yarn-site.xml 파일을 수정하여 변경할 수 있으며, 노드매니저의 메모리, CPU 개수와 컨테이너에 할당 할 수 있는 최대, 최소 메모리등을 설정할 수 있습니다. 사용 예시는 다음과 같습니다.
_________________________________________________________________________________________________________________________________________________________________________
```
  <property>
    <name>yarn.nodemanager.resource.memory-mb</name>
    <value>28672</value>
  </property>
  <property>
    <name>yarn.nodemanager.resource.cpu-vcores</name>
    <value>8</value>
  </property>
  <property>
    <name>yarn.scheduler.maximum-allocation-mb</name>
    <value>8192</value>
  </property>
  <property>
    <name>yarn.scheduler.minimum-allocation-mb</name>
    <value>1024</value>
  </property>
  <property>
    <name>yarn.nodemanager.vmem-pmem-ratio</name>
    <value>2.1</value>
  </property>
  <property>
    <name>yarn.nodemanager.pmem-check-enabled</name>
    <value>true</value>
  </property>
  <property>
    <name>yarn.nodemanager.vmem-check-enabled</name>
    <value>false</value>
  </property>
```
_________________________________________________________________________________________________________________________________________________________________________
문서에 따른 리소스 매니저 설정 커맨드는 다음과 같습니다.

    ● yarn.nodemanager.resource.memory-mb
        ○ 클러스터의 각 노드에서 컨테이너 운영에 설정할 수 있는 메모리의 총량
        ○ 노드의 OS를 운영할 메모리를 제외하고 설정
        ○ 기본값은 장비에 설정된 메모리의 80% 정도를 설정
            ■ 노드의 메모리가 32G인경우 운영체제를 위한 4G를 제외하고 28G를 설정
    ● yarn.nodemanager.resource.cpu-vcores
        ○ 클러스터의 각 노드에서 컨테이너 운영에 설정할 수 있는 CPU의 개수
        ○ 기본값은 장비에 설치된 CPU의 80% 정도를 설정
            ■ 노드에 설치된 CPU가 40개일 경우 32를 설정
    ● yarn.scheduler.maximum-allocation-mb
        ○ 하나의 컨테이너에 할당할 수 있는 메모리의 최대값
        ○ 8G가 기본 값
    ● yarn.scheduler.minimum-allocation-mb
        ○ 하나의 컨테이너에 할당할 수 있는 메모리의 최소값
        ○ 1G가 기본값
    ● yarn.nodemanager.vmem-pmem-ratio
        ○ 실제 메모리 대비 가상 메모리 사용 비율
        ○ mapreduce.map.memory.mb * 설정값의 비율로 사용 가능
        ○ 메모리를 1G로 설정하고, 이 값을 10으로 설정하면 가상메모리를 10G 사용
    ● yarn.nodemanager.vmem-check-enabled
        ○ 가상 메모리에 대한 제한이 있는지 확인하여, true 일 경우 메모리 사용량을 넘어서면 컨테이너를 종료
        ○ false 로 설정하여 가상메모리는
    ● yarn.nodemanager.pmem-check-enabled
        ○ 물리 메모리에 대한 제한이 있는지 확인하여, true 일 경우 메모리 사용량을 넘어서면 컨테이너를 종료
_________________________________________________________________________________________________________________________________________________________________________
다음으로 사용자 커맨드에 대해 말씀드리겠습니다. 커맨드 리스트는 다음과 같습니다.
_________________________________________________________________________________________________________________________________________________________________________
|명령어|설명|
|------|----|
|application|애플리케이션 정보 확인, 작업 종료|
|applicationattempt|애플리케이션 Attempt 정보|
|classpath|필요한 클래스 정보|
|container|컨테이너 정보|
|jar|jar 파일 실행|
|logs|애플리케이션 로그 확인|
|node|노드 정보|
|queue|큐 정보|
|version|버전 정보|
|envvars|환경 변수 정보|
_________________________________________________________________________________________________________________________________________________________________________
application 커맨드는 애플리케이션의 정보를 확인하고, 작업을 종료할 수 있습니다. 주요 커맨드는 다음과 같습니다. 
_________________________________________________________________________________________________________________________________________________________________________
```
# 작업목록 확인
$ bin/yarn application -list(아무것도 없어서 이렇게 나옴)

2022-07-18 15:13:27,903 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable

2022-07-18 15:13:27,973 INFO client.DefaultNoHARMFailoverProxyProvider: Connecting to ResourceManager at /0.0.0.0:8032

Total number of applications (application-types: [], states: [SUBMITTED, ACCEPTED, RUNNING] and tags: []):0

                Application-Id	    Application-Name	    Application-Type	      User	    Queue	            State	      Final-State	      Progress	                      Tracking-URL
```
_________________________________________________________________________________________________________________________________________________________________________
작업상태 확인 명령어는 다음과 같습니다.
_________________________________________________________________________________________________________________________________________________________________________
```
# 작업 상태 확인(해당 애플리케이션 없으므로 다음과 같이 나옴)
$ bin/yarn application -status application_1234_1

2022-07-18 15:15:30,513 INFO client.DefaultNoHARMFailoverProxyProvider: Connecting to ResourceManager at /0.0.0.0:8032

Application with id 'application_1234_1' doesn't exist in RM or Timeline Server.
```
_________________________________________________________________________________________________________________________________________________________________________
작업 종료 커맨드는 다음과 같습니다.
_________________________________________________________________________________________________________________________________________________________________________
```
# 작업 종료

$ bin/yarn application -kill application_id
```
_________________________________________________________________________________________________________________________________________________________________________
어템트, 컨테이너, logs 커맨드는 다음과 같습니다.
_________________________________________________________________________________________________________________________________________________________________________
```
# 애플리케이션 어템트 커맨드
$ bin/yarn applicationattempt -list application_1234_1

# 컨테이너 커맨드

$ bin/yarn container -list appattempt_1234_1_000001

# logs 커맨드

$ bin/yarn logs -applicationId <application_id>
```
_________________________________________________________________________________________________________________________________________________________________________
운영자 커맨드 리스트는 다음과 같습니다. 이 중에서 리소스매니저에 동록된 큐와 노드 정보를 갱신할 수 있는 rmadmin 커맨드에 대해 알아보겠습니다.
_________________________________________________________________________________________________________________________________________________________________________
|명령어|설명|
|------|----|
|daemonlog|로그 레벨 설정|
|nodemanager|노드매니저 실행|
|proxyserver|프록시 서버 실행|
|resourcemanager|리소스매니저 실행|
|rmadmin|리소스매니저 어드민 클라이언트 명령|
|schedulerconf|스케쥴러 설정 업데이트|
|scmadmin|공유 캐쉬 매니저 어드민 클라이언트 명령|
|sharedcachemanager|공유 캐쉬 매니저 실행|
|timelineserver|타임라인 서버 실행|
_________________________________________________________________________________________________________________________________________________________________________
사용 예시는 다음과 같습니다.
_________________________________________________________________________________________________________________________________________________________________________
```
# 큐정보갱신

$ bin/yarn rmadmin -refreshQueues
```
_________________________________________________________________________________________________________________________________________________________________________
문서에는 REST API에 대한 내용도 있지만 파이썬3 내에서 urllib2 미지원으로 인하여 해당 파트는 스킵하겠습니다.
_________________________________________________________________________________________________________________________________________________________________________
다음으로 서버를 특성에 맞게 구분하여 작업을 처리하게 하는 기능을 제공하는 노드 레이블에 대해 알아보겠습니다. 설정은 다음과 같이 'yarn-site.xml'에서 하면 됩니다.
_________________________________________________________________________________________________________________________________________________________________________
```
  <property>
    <name>yarn.node-labels.enabled</name>
    <value>true</value>
  </property>
  <property>
    <name>yarn.node-labels.fs-store.root-dir</name>
    <value>hdfs://namenode:port/path/to/store/node-labels/</value>
  </property>
```
_________________________________________________________________________________________________________________________________________________________________________
노드 레이블 관련 명령어는 다음과 같습니다.
_________________________________________________________________________________________________________________________________________________________________________
```
# 클러스터의 레이블 정보 확인(여기엔 없으므로 빈 칸으로 나옴)
$ bin/yarn cluster --list-node-labels
2022-07-18 15:39:23,323 INFO client.DefaultNoHARMFailoverProxyProvider: Connecting to ResourceManager at /0.0.0.0:8032
Node Labels: 

# 클러스터 노드 레이블 추가, exclusive 기본값은 true
$ bin/yarn rmadmin -addToClusterNodeLabels "label_1(exclusive=true),label_2(exclusive=false)"
2022-07-18 15:40:45,333 INFO client.DefaultNoHARMFailoverProxyProvider: Connecting to ResourceManager at /0.0.0.0:8033
addToClusterNodeLabels: java.io.IOException: Node-label-based scheduling is disabled. Please check yarn.node-labels.enabled
	at org.apache.hadoop.yarn.ipc.RPCUtil.getRemoteException(RPCUtil.java:38)
	at org.apache.hadoop.yarn.server.resourcemanager.AdminService.logAndWrapException(AdminService.java:936)
...
	at org.apache.hadoop.yarn.server.resourcemanager.AdminService.addToClusterNodeLabels(AdminService.java:816)
	... 12 more



# 클러스터 노드 레이블 삭제 
$ bin/yarn rmadmin -removeFromClusterNodeLabels "label_1"
2022-07-18 15:42:54,828 INFO client.DefaultNoHARMFailoverProxyProvider: Connecting to ResourceManager at /0.0.0.0:8033
removeFromClusterNodeLabels: java.io.IOException: Node-label-based scheduling is disabled. Please check yarn.node-labels.enabled
	at org.apache.hadoop.yarn.ipc.RPCUtil.getRemoteException(RPCUtil.java:38)
...
	at org.apache.hadoop.yarn.server.resourcemanager.AdminService.logAndWrapException(AdminService.java:936)
	at org.apache.hadoop.yarn.server.resourcemanager.AdminService.removeFromClusterNodeLabels(AdminService.java:839)
	... 12 more


# 노드 레이블 추가(명령어 입력 방식 아래와 같이 바뀜)
$ bin/yarn rmadmin -replaceLabelsOnNode “node1[:port]=label1 node2=label2”
replaceLabelsOnNode: Invalid port: port]
Usage: yarn rmadmin [-replaceLabelsOnNode <"node1[:port]=label1,label2 node2[:port]=label1,label2"> [-failOnUnknownNodes] ]
...
```
_________________________________________________________________________________________________________________________________________________________________________
문서에서 다루는 고가용성은 주키퍼 범위를 다루고, 타임라인 서비스 v2는 HBase의 범위를 다루므로 마찬가지로 스킵하겠습니다.
_________________________________________________________________________________________________________________________________________________________________________
이상으로 YARN에 대해 가볍게 알아봤습니다. 이렇게 이번 포스팅을 끝으로 하둡 소개 및 기초 사용법에 대해 7번에 걸쳐 설명드렸습니다. 하둡에 대해 더 궁금하신 사항이 있으시면 제가 참고한 문서를 보시거나, 시중에 있는 책을 추가로 구매하셔도 좋을 것 같습니다. 다음 포스팅부터는 카프카에 대해 다룰 예정입니다. 모두 그동안 하둡 관련 포스팅에 관심 가져주셔서 감사드리고, 다음 포스팅에서 뵙겠습니다.
_________________________________________________________________________________________________________________________________________________________________________
#### 감사합니다.
_________________________________________________________________________________________________________________________________________________________________________
[레퍼런스]
1. 빅데이터 - 하둡, 하이브로 시작하기(위키독스) : https://wikidocs.net/book/2203