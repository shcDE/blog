---
title: "HDFS는 어떻게 사용할까요?(3) [HDFS 명령어(하)]"
date: 2022-07-15T13:24:52+09:00
categories:
- development
tags:
- development
keywords:
- data engineering, hadoop, development, hdfs
image: elephants-g07073c915_1920.jpg
---
![elephant](https://github.com/shcDE/pictures/blob/main/images_for_blog/elephants-g07073c915_1920.jpg?raw=true)
_________________________________________________________________________________________________________________________________________________________________________

# 이번에 배울 HDFS 명령어는 무엇이 있을까요?
_________________________________________________________________________________________________________________________________________________________________________

안녕하세요. shcDE입니다. 이번 포스팅에서는 이전 포스팅에서 다루지 못했던 명령어를 다루겠습니다. 포스팅 시작 전에, 해당 게시물은 ['빅데이터 - 하둡, 하이브로 시작하기'(위키독스)](https://wikidocs.net/book/2203)를 바탕으로 작성하였음을 알려드립니다.
_________________________________________________________________________________________________________________________________________________________________________
우선 fsck 명령어에 대해 말씀드리겠습니다. fsck 커맨드는 HDFS 파일시스템의 상태를 체크하는 명령어 입니다. 해당 커맨드는 파일시스템에 블록 상태 확인, 파일의 복제 개수를 확인하여 결과를 알려줍니다. 예시와 함께 확인해보겠습니다.
_________________________________________________________________________________________________________________________________________________________________________
fsck 명령어는 다음과 같은 형식으로 입력할 수 있습니다.
_________________________________________________________________________________________________________________________________________________________________________
```
$ hdfs fsck <path> [-list-corruptfileblocks 
                     [-move | -delete | -openforwrite] 
                     [-files [-blocks [-locations | -racks]]]] 
                     [-includeSnapshots] [-storagepolicies] [-blockId <blk_Id>]
```
_________________________________________________________________________________________________________________________________________________________________________
fsck /는 하둡 서버 내 모든 디렉토리를 확인하는 명령어입니다.
_________________________________________________________________________________________________________________________________________________________________________
```
$ bin/hdfs fsck /

Connecting to namenode via http://localhost:9870/fsck?ugi=shc&path=%2F

FSCK started by shc (auth:SIMPLE) from /127.0.0.1 for path / at Fri Jul 15 13:32:33 KST 2022





Status: HEALTHY

 Number of data-nodes:	1

 Number of racks:	1

 Total dirs:	3

 Total symlinks:	0



Replicated Blocks:

 Total size:	0 B

 Total files:	0

 Total blocks (validated):	0

 Minimally replicated blocks:	0

 Over-replicated blocks:	0

 Under-replicated blocks:	0

 Mis-replicated blocks:	0

 Default replication factor:	1

 Average block replication:	0.0

 Missing blocks:	0

 Corrupt blocks:	0

 Missing replicas:	0

 Blocks queued for replication:	0



Erasure Coded Block Groups:

 Total size:	0 B

 Total files:	0

 Total block groups (validated):	0

 Minimally erasure-coded block groups:	0

 Over-erasure-coded block groups:	0

 Under-erasure-coded block groups:	0

 Unsatisfactory placement block groups:	0

 Average block group size:	0.0

 Missing block groups:	0

 Corrupt block groups:	0

 Missing internal blocks:	0

 Blocks queued for replication:	0

FSCK ended at Fri Jul 15 13:32:33 KST 2022 in 1 milliseconds





The filesystem under path '/' is HEALTHY
```
_________________________________________________________________________________________________________________________________________________________________________
fsck /user/hadoop/는 /user/hadoop/ 디렉토리의 상태를 확인하는 명령어입니다.
_________________________________________________________________________________________________________________________________________________________________________

```
$ bin/hdfs fsck /user/hadoop/
Connecting to namenode via http://localhost:9870/fsck?ugi=shc&path=%2Fuser%2Fhadoop
FSCK started by shc (auth:SIMPLE) from /127.0.0.1 for path /user/hadoop at Fri Jul 15 13:33:43 KST 2022


Status: HEALTHY
 Number of data-nodes:	1
 Number of racks:		1
 Total dirs:			1
 Total symlinks:		0

Replicated Blocks:
 Total size:	0 B
 Total files:	0
 Total blocks (validated):	0
 Minimally replicated blocks:	0
 Over-replicated blocks:	0
 Under-replicated blocks:	0
 Mis-replicated blocks:		0
 Default replication factor:	1
 Average block replication:	0.0
 Missing blocks:		0
 Corrupt blocks:		0
 Missing replicas:		0
 Blocks queued for replication:	0

Erasure Coded Block Groups:
 Total size:	0 B
 Total files:	0
 Total block groups (validated):	0
 Minimally erasure-coded block groups:	0
 Over-erasure-coded block groups:	0
 Under-erasure-coded block groups:	0
 Unsatisfactory placement block groups:	0
 Average block group size:	0.0
 Missing block groups:		0
 Corrupt block groups:		0
 Missing internal blocks:	0
 Blocks queued for replication:	0
FSCK ended at Fri Jul 15 13:33:43 KST 2022 in 1 milliseconds


The filesystem under path '/user/hadoop' is HEALTHY
```
_________________________________________________________________________________________________________________________________________________________________________
-list-corruptfileblocks는 오염된 파일이 있는지 확인하는 명령어입니다. "The filesystem under path '/user' has 0 CORRUPT files" 출력 결과를 통해 오염된 파일이 없음을 확인할 수 있습니다.
_________________________________________________________________________________________________________________________________________________________________________
```
$ bin/hdfs fsck /user -list-corruptfileblocks
Connecting to namenode via http://localhost:9870/fsck?ugi=shc&listcorruptfileblocks=1&path=%2Fuser
The filesystem under path '/user' has 0 CORRUPT files
```
_________________________________________________________________________________________________________________________________________________________________________
-delete 명령어는 오염된 파일을 제거하는 명령어입니다. 해당 예시는 오염된 파일이 없기에 "The filesystem under path '/user' is HEALTHY" 메세지가 호출됩니다.
_________________________________________________________________________________________________________________________________________________________________________

```
$ bin/hdfs fsck /user -delete
Connecting to namenode via http://localhost:9870/fsck?ugi=shc&delete=1&path=%2Fuser
FSCK started by shc (auth:SIMPLE) from /127.0.0.1 for path /user at Fri Jul 15 13:35:12 KST 2022


Status: HEALTHY
 Number of data-nodes:	1
 Number of racks:		1
 Total dirs:			2
 Total symlinks:		0

Replicated Blocks:
 Total size:	0 B
 Total files:	0
 Total blocks (validated):	0
 Minimally replicated blocks:	0
 Over-replicated blocks:	0
 Under-replicated blocks:	0
 Mis-replicated blocks:		0
 Default replication factor:	1
 Average block replication:	0.0
 Missing blocks:		0
 Corrupt blocks:		0
 Missing replicas:		0
 Blocks queued for replication:	0

Erasure Coded Block Groups:
 Total size:	0 B
 Total files:	0
 Total block groups (validated):	0
 Minimally erasure-coded block groups:	0
 Over-erasure-coded block groups:	0
 Under-erasure-coded block groups:	0
 Unsatisfactory placement block groups:	0
 Average block group size:	0.0
 Missing block groups:		0
 Corrupt block groups:		0
 Missing internal blocks:	0
 Blocks queued for replication:	0
FSCK ended at Fri Jul 15 13:35:12 KST 2022 in 1 milliseconds


The filesystem under path '/user' is HEALTHY
```
_________________________________________________________________________________________________________________________________________________________________________
fsck /user -files는 /user 디렉토리 내 파일들을 출력하는 명령어입니다. ```/user <dir>```, ```/user/hadoop <dir>```와 같은 형태로 나오고 디렉토리 상태도 같이 출력해줍니다.
_________________________________________________________________________________________________________________________________________________________________________

```
$ bin/hdfs fsck /user -files
Connecting to namenode via http://localhost:9870/fsck?ugi=shc&files=1&path=%2Fuser
FSCK started by shc (auth:SIMPLE) from /127.0.0.1 for path /user at Fri Jul 15 13:35:56 KST 2022

/user <dir>
/user/hadoop <dir>

Status: HEALTHY
 Number of data-nodes:	1
 Number of racks:		1
 Total dirs:			2
 Total symlinks:		0

Replicated Blocks:
 Total size:	0 B
 Total files:	0
 Total blocks (validated):	0
 Minimally replicated blocks:	0
 Over-replicated blocks:	0
 Under-replicated blocks:	0
 Mis-replicated blocks:		0
 Default replication factor:	1
 Average block replication:	0.0
 Missing blocks:		0
 Corrupt blocks:		0
 Missing replicas:		0
 Blocks queued for replication:	0

Erasure Coded Block Groups:
 Total size:	0 B
 Total files:	0
 Total block groups (validated):	0
 Minimally erasure-coded block groups:	0
 Over-erasure-coded block groups:	0
 Under-erasure-coded block groups:	0
 Unsatisfactory placement block groups:	0
 Average block group size:	0.0
 Missing block groups:		0
 Corrupt block groups:		0
 Missing internal blocks:	0
 Blocks queued for replication:	0
FSCK ended at Fri Jul 15 13:35:56 KST 2022 in 1 milliseconds


The filesystem under path '/user' is HEALTHY
```

_________________________________________________________________________________________________________________________________________________________________________
fsck /user -files -blocks는 block 형태의 파일 리스트를 확인할 수 있는 명령어입니다. 별 다른 특이사항이 없기에 "bin/hdfs fsck /user -files" 명령어와 동일한 결과를 출력합니다.
_________________________________________________________________________________________________________________________________________________________________________
```
$ bin/hdfs fsck /user -files -blocks
Connecting to namenode via http://localhost:9870/fsck?ugi=shc&files=1&blocks=1&path=%2Fuser
FSCK started by shc (auth:SIMPLE) from /127.0.0.1 for path /user at Fri Jul 15 13:36:53 KST 2022

/user <dir>
/user/hadoop <dir>

Status: HEALTHY
 Number of data-nodes:	1
 Number of racks:		1
 Total dirs:			2
 Total symlinks:		0

Replicated Blocks:
 Total size:	0 B
 Total files:	0
 Total blocks (validated):	0
 Minimally replicated blocks:	0
 Over-replicated blocks:	0
 Under-replicated blocks:	0
 Mis-replicated blocks:		0
 Default replication factor:	1
 Average block replication:	0.0
 Missing blocks:		0
 Corrupt blocks:		0
 Missing replicas:		0
 Blocks queued for replication:	0

Erasure Coded Block Groups:
 Total size:	0 B
 Total files:	0
 Total block groups (validated):	0
 Minimally erasure-coded block groups:	0
 Over-erasure-coded block groups:	0
 Under-erasure-coded block groups:	0
 Unsatisfactory placement block groups:	0
 Average block group size:	0.0
 Missing block groups:		0
 Corrupt block groups:		0
 Missing internal blocks:	0
 Blocks queued for replication:	0
FSCK ended at Fri Jul 15 13:36:53 KST 2022 in 1 milliseconds


The filesystem under path '/user' is HEALTHY
```
_________________________________________________________________________________________________________________________________________________________________________
이제 운영자 커맨드에 대해 알아보겠습니다. 운영자 커맨드는 주로 실행, 설정 관련 명령어가 많으며, 대표적인 명령어의 사용 예시는 다음과 같습니다.
_________________________________________________________________________________________________________________________________________________________________________
우선 balancer 명령어입니다. 해당 명령어 사용 예시는 다음과 같습니다.
_________________________________________________________________________________________________________________________________________________________________________
```
# 둘다 balancer를 실행

$ bin/hdfs balancer
2022-07-15 13:38:07,953 INFO balancer.Balancer: namenodes  = [hdfs://localhost:9000]
2022-07-15 13:38:07,955 INFO balancer.Balancer: parameters = Balancer.BalancerParameters [BalancingPolicy.Node, threshold = 10.0, max idle iteration = 5, #excluded nodes = 0, #included nodes = 0, #source nodes = 0, #blockpools = 0, run during upgrade = false]
2022-07-15 13:38:07,955 INFO balancer.Balancer: included nodes = []
2022-07-15 13:38:07,955 INFO balancer.Balancer: excluded nodes = []
2022-07-15 13:38:07,955 INFO balancer.Balancer: source nodes = []
Time Stamp               Iteration#  Bytes Already Moved  Bytes Left To Move  Bytes Being Moved  NameNode
2022-07-15 13:38:07,958 INFO balancer.NameNodeConnector: getBlocks calls for hdfs://localhost:9000 will be rate-limited to 20 per second
2022-07-15 13:38:08,020 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
2022-07-15 13:38:08,974 INFO balancer.Balancer: dfs.namenode.get-blocks.max-qps = 20 (default=20)
2022-07-15 13:38:08,974 INFO balancer.Balancer: dfs.balancer.movedWinWidth = 5400000 (default=5400000)
2022-07-15 13:38:08,974 INFO balancer.Balancer: dfs.balancer.moverThreads = 1000 (default=1000)
2022-07-15 13:38:08,974 INFO balancer.Balancer: dfs.balancer.dispatcherThreads = 200 (default=200)
2022-07-15 13:38:08,974 INFO balancer.Balancer: dfs.balancer.getBlocks.size = 2147483648 (default=2147483648)
2022-07-15 13:38:08,974 INFO balancer.Balancer: dfs.balancer.getBlocks.min-block-size = 10485760 (default=10485760)
2022-07-15 13:38:08,975 INFO balancer.Balancer: dfs.datanode.balance.max.concurrent.moves = 100 (default=100)
2022-07-15 13:38:08,975 INFO balancer.Balancer: dfs.datanode.balance.bandwidthPerSec = 104857600 (default=104857600)
2022-07-15 13:38:08,980 INFO balancer.Balancer: dfs.balancer.max-size-to-move = 10737418240 (default=10737418240)
2022-07-15 13:38:08,980 INFO balancer.Balancer: dfs.blocksize = 134217728 (default=134217728)
2022-07-15 13:38:08,996 INFO net.NetworkTopology: Adding a new node: /default-rack/127.0.0.1:9866
2022-07-15 13:38:08,997 INFO balancer.Balancer: 0 over-utilized: []
2022-07-15 13:38:08,997 INFO balancer.Balancer: 0 underutilized: []
Jul 15, 2022 1:38:08 PM           0                  0 B                 0 B                0 B                  0  hdfs://localhost:9000
The cluster is balanced. Exiting...
Jul 15, 2022 1:38:09 PM  Balancing took 1.642 seconds



$ bin/hadoop balancer
WARNING: Use of this script to execute balancer is deprecated.
WARNING: Attempting to execute replacement "hdfs balancer" instead.

2022-07-15 13:38:50,432 INFO balancer.Balancer: namenodes  = [hdfs://localhost:9000]
2022-07-15 13:38:50,433 INFO balancer.Balancer: parameters = Balancer.BalancerParameters [BalancingPolicy.Node, threshold = 10.0, max idle iteration = 5, #excluded nodes = 0, #included nodes = 0, #source nodes = 0, #blockpools = 0, run during upgrade = false]
2022-07-15 13:38:50,433 INFO balancer.Balancer: included nodes = []
2022-07-15 13:38:50,434 INFO balancer.Balancer: excluded nodes = []
2022-07-15 13:38:50,434 INFO balancer.Balancer: source nodes = []
Time Stamp               Iteration#  Bytes Already Moved  Bytes Left To Move  Bytes Being Moved  NameNode
2022-07-15 13:38:50,436 INFO balancer.NameNodeConnector: getBlocks calls for hdfs://localhost:9000 will be rate-limited to 20 per second
2022-07-15 13:38:50,496 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
2022-07-15 13:38:51,135 INFO balancer.Balancer: dfs.namenode.get-blocks.max-qps = 20 (default=20)
2022-07-15 13:38:51,136 INFO balancer.Balancer: dfs.balancer.movedWinWidth = 5400000 (default=5400000)
2022-07-15 13:38:51,136 INFO balancer.Balancer: dfs.balancer.moverThreads = 1000 (default=1000)
2022-07-15 13:38:51,136 INFO balancer.Balancer: dfs.balancer.dispatcherThreads = 200 (default=200)
2022-07-15 13:38:51,136 INFO balancer.Balancer: dfs.balancer.getBlocks.size = 2147483648 (default=2147483648)
2022-07-15 13:38:51,136 INFO balancer.Balancer: dfs.balancer.getBlocks.min-block-size = 10485760 (default=10485760)
2022-07-15 13:38:51,136 INFO balancer.Balancer: dfs.datanode.balance.max.concurrent.moves = 100 (default=100)
2022-07-15 13:38:51,136 INFO balancer.Balancer: dfs.datanode.balance.bandwidthPerSec = 104857600 (default=104857600)
2022-07-15 13:38:51,140 INFO balancer.Balancer: dfs.balancer.max-size-to-move = 10737418240 (default=10737418240)
2022-07-15 13:38:51,140 INFO balancer.Balancer: dfs.blocksize = 134217728 (default=134217728)
2022-07-15 13:38:51,151 INFO net.NetworkTopology: Adding a new node: /default-rack/127.0.0.1:9866
2022-07-15 13:38:51,152 INFO balancer.Balancer: 0 over-utilized: []
2022-07-15 13:38:51,152 INFO balancer.Balancer: 0 underutilized: []
Jul 15, 2022 1:38:51 PM           0                  0 B                 0 B                0 B                  0  hdfs://localhost:9000
The cluster is balanced. Exiting...
Jul 15, 2022 1:38:51 PM  Balancing took 884.0 milliseconds
```
_________________________________________________________________________________________________________________________________________________________________________
dfsadmin 커맨드는 hdfs의 관리를 위한 정보를 설정 및 변경할 수 있는 명령어입니다. 주 사용 목적은 쿼타(Quota) 설정, 노드 리프레쉬, 노드 동작 및 정지가 있습니다. 주요 사용 예시는 다음과 같습니다. 
_________________________________________________________________________________________________________________________________________________________________________
```
# dfsadmin 커맨드 전체 명령어

$ bin/hdfs dfsadmin 
Usage: hdfs dfsadmin
Note: Administrative commands can only be run as the HDFS superuser.
	[-report [-live] [-dead] [-decommissioning] [-enteringmaintenance] [-inmaintenance]]
	[-safemode <enter | leave | get | wait | forceExit>]
	[-saveNamespace [-beforeShutdown]]
	[-rollEdits]
	[-restoreFailedStorage true|false|check]
	[-refreshNodes]
	[-setQuota <quota> <dirname>...<dirname>]
	[-clrQuota <dirname>...<dirname>]
	[-setSpaceQuota <quota> [-storageType <storagetype>] <dirname>...<dirname>]
	[-clrSpaceQuota [-storageType <storagetype>] <dirname>...<dirname>]
	[-finalizeUpgrade]
	[-rollingUpgrade [<query|prepare|finalize>]]
	[-upgrade <query | finalize>]
	[-refreshServiceAcl]
	[-refreshUserToGroupsMappings]
	[-refreshSuperUserGroupsConfiguration]
	[-refreshCallQueue]
	[-refresh <host:ipc_port> <key> [arg1..argn]
	[-reconfig <namenode|datanode> <host:ipc_port> <start|status|properties>]
	[-printTopology]
	[-refreshNamenodes datanode_host:ipc_port]
	[-getVolumeReport datanode_host:ipc_port]
	[-deleteBlockPool datanode_host:ipc_port blockpoolId [force]]
	[-setBalancerBandwidth <bandwidth in bytes per second>]
	[-getBalancerBandwidth <datanode_host:ipc_port>]
	[-fetchImage <local directory>]
	[-allowSnapshot <snapshotDir>]
	[-disallowSnapshot <snapshotDir>]
	[-shutdownDatanode <datanode_host:ipc_port> [upgrade]]
	[-evictWriters <datanode_host:ipc_port>]
	[-getDatanodeInfo <datanode_host:ipc_port>]
	[-metasave filename]
	[-triggerBlockReport [-incremental] <datanode_host:ipc_port> [-namenode <namenode_host:ipc_port>]]
	[-listOpenFiles [-blockingDecommission] [-path <path>]]
	[-help [cmd]]

Generic options supported are:
-conf <configuration file>        specify an application configuration file
-D <property=value>               define a value for a given property
-fs <file:///|hdfs://namenode:port> specify default filesystem URL to use, overrides 'fs.defaultFS' property from configurations.
-jt <local|resourcemanager:port>  specify a ResourceManager
-files <file1,...>                specify a comma-separated list of files to be copied to the map reduce cluster
-libjars <jar1,...>               specify a comma-separated list of jar files to be included in the classpath
-archives <archive1,...>          specify a comma-separated list of archives to be unarchived on the compute machines

The general command line syntax is:
command [genericOptions] [commandOptions]
```
_________________________________________________________________________________________________________________________________________________________________________
-report 명령어를 통해 HDFS의 전체 사용량과 각 노드의 상태를 확인할 수 있습니다. 사용 예시는 다음과 같습니다.
_________________________________________________________________________________________________________________________________________________________________________
```
$ bin/hdfs dfsadmin -report

Configured Capacity: 409000189952 (380.91 GB)

Present Capacity: 103079079936 (96.00 GB)

DFS Remaining: 103079071744 (96.00 GB)

DFS Used: 8192 (8 KB)

DFS Used%: 0.00%

Replicated Blocks:

Under replicated blocks: 0

Blocks with corrupt replicas: 0

Missing blocks: 0

Missing blocks (with replication factor 1): 0

Low redundancy blocks with highest priority to recover: 0

Pending deletion blocks: 0

Erasure Coded Block Groups: 

Low redundancy block groups: 0

Block groups with corrupt internal blocks: 0

Missing block groups: 0

Low redundancy blocks with highest priority to recover: 0

Pending deletion blocks: 0



-------------------------------------------------

Live datanodes (1):



Name: 127.0.0.1:9866 (localhost)

Hostname: 172.30.1.9

Decommission Status : Normal

Configured Capacity: 409000189952 (380.91 GB)

DFS Used: 8192 (8 KB)

Non DFS Used: 303063531520 (282.25 GB)

DFS Remaining: 103079071744 (96.00 GB)

DFS Used%: 0.00%

DFS Remaining%: 25.20%

Configured Cache Capacity: 0 (0 B)

Cache Used: 0 (0 B)

Cache Remaining: 0 (0 B)

Cache Used%: 100.00%

Cache Remaining%: 0.00%

Xceivers: 0

Last contact: Fri Jul 15 13:43:11 KST 2022

Last Block Report: Fri Jul 15 13:26:41 KST 2022

Num of Blocks: 0
```
_________________________________________________________________________________________________________________________________________________________________________
-safemode 명령어는 세이프모드에 진입하고 빠져나올 수 있게 도와줍니다. 각 명령어는 다음과 같은 역할을 합니다. 
_________________________________________________________________________________________________________________________________________________________________________

|옵션|비고|
|---|------------------|
|get|세이프 모드 상태를 확인|
|enter|세이프 모드 진입|
|leave|세이프 모드 복구|
|wait|세이프 모드이면 대기하다가, 세이프모드가 끝나면 회복|

_________________________________________________________________________________________________________________________________________________________________________
```
$ bin/hdfs dfsadmin -safemode get
Safe mode is OFF

$ bin/hdfs dfsadmin -safemode enter
Safe mode is ON

$ bin/hdfs dfsadmin -safemode get
Safe mode is ON

$ bin/hdfs dfsadmin -safemode leave
Safe mode is OFF

$ bin/hdfs dfsadmin -safemode wait
Safe mode is OFF
```
_________________________________________________________________________________________________________________________________________________________________________
-triggerBlockReport 명령어는 데이터노드에게 블록리포트를 전달하는 역할을 합니다. 사용 예시는 다음과 같습니다.
_________________________________________________________________________________________________________________________________________________________________________
```
$ bin/hdfs dfsadmin -triggerBlockReport datanode_host_name:datanode_ipc_port

triggerBlockReport: Does not contain a valid host:port authority: datanode_host_name:datanode_ipc_port

Usage: hdfs dfsadmin [-triggerBlockReport [-incremental] <datanode_host:ipc_port> [-namenode <namenode_host:ipc_port>]]
```
_________________________________________________________________________________________________________________________________________________________________________
fetchdt 명령어는 현재 v3으로 넘어간 이후로 사라진 명령어이므로 여기서는 스킵하겠습니다.
_________________________________________________________________________________________________________________________________________________________________________
checknative 명령어는 서버에 설치 된 네이티브 압축 라이브러리 상태를 확인합니다. 사용 예시는 다음과 같습니다.
_________________________________________________________________________________________________________________________________________________________________________
```
$ bin/hadoop checknative -a

2022-07-15 13:57:10,371 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable

Native library checking:

hadoop:  false 

zlib:    false 

zstd  :  false 

bzip2:   false 

openssl: false 

ISA-L:   false 

PMDK:    false 

2022-07-15 13:57:10,450 INFO util.ExitUtil: Exiting with status 1: ExitException
```
_________________________________________________________________________________________________________________________________________________________________________
네이티브 설정이 되지 않았기에, 라이브러리 상태가 전부 false로 확인됨을 알 수 있습니다. 하지만 나중에 네이티브 설정이 완료된 이후에 다시 확인할 수 있는 상황이 발생할 수 있으니 우선은 core-site.xml 파일에 해당 코드를 입력하여 저장하겠습니다.
_________________________________________________________________________________________________________________________________________________________________________
```
<property>  
  <name>io.compression.codecs</name>  
  <value>org.apache.hadoop.io.compress.GzipCodec,org.apache.hadoop.io.compress.DefaultCodec,org.apache.hadoop.io.compress.SnappyCodec</value>  
</property>
```
_________________________________________________________________________________________________________________________________________________________________________
이상으로 HDFS 기본 명령어에 대해 상, 하 편에 걸쳐 모두 확인하였습니다. 다음 포스팅은 다시 HDFS 기본 사용법에 대해 다룰 예정입니다.
_________________________________________________________________________________________________________________________________________________________________________
오늘도 긴 글 읽어주시느라 고생하셨습니다.
_________________________________________________________________________________________________________________________________________________________________________
#### 감사합니다.
_________________________________________________________________________________________________________________________________________________________________________
[레퍼런스]
1. 빅데이터 - 하둡, 하이브로 시작하기(위키독스) : https://wikidocs.net/book/2203
2. 07-HDFS 명령어(빅데이터 - 하둡, 하이브로 시작하기(위키독스)) : https://wikidocs.net/26496
