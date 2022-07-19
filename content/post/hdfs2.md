---
title: "HDFS는 어떻게 사용할까요?(2) [HDFS 명령어(상)]"
date: 2022-07-14T15:19:08+09:00
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

# HDFS 명령어는 무엇이 있을까요?
_________________________________________________________________________________________________________________________________________________________________________
안녕하세요. shcDE입니다. 이번 포스팅에서는 HDFS 사용 시 주로 쓰이는 명령어들에 대해 설명드리고자 합니다. 원래는 한 포스팅에 모든 명령어에 대한 정보를 담고자 시도하였으나, 명령어가 제 예상보다 많아서 상, 하에 나누어 포스팅을 진행하겠습니다. 우선 시작 전에 해당 포스팅은 ['빅데이터 - 하둡, 하이브로 시작하기'(위키독스)](https://wikidocs.net/book/2203)를 바탕으로 작성하였음을 알립니다.
_________________________________________________________________________________________________________________________________________________________________________
우선 사용자 커맨드에 대해 설명드리겠습니다. 사용자 커맨드는 hdfs, hadoop 쉘을 이용할 수 있습니다. 일부 커맨드는 hdfs 쉘을 이용해야 합니다. 둘 다 이용할 수 있는 경우 각 쉘의 결과는 동일하며, 사용법은 다음과 같습니다. 
_________________________________________________________________________________________________________________________________________________________________________
```
# dfs 커맨드는 둘 다 동일한 결과를 출력
(base) shcDE-ui-MacBookPro:hadoop shc$ bin/hdfs dfs -ls
(base) shcDE-ui-MacBookPro:hadoop shc$

(base) shcDE-ui-MacBookPro:hadoop shc$ bin/hadoop fs -ls
(base) shcDE-ui-MacBookPro:hadoop shc$ 

# fsck 커맨드도 동일한 결과 출력

$ bin/hdfs fsck /
Connecting to namenode via http://localhost:9870/fsck?ugi=shc&path=%2F
FSCK started by shc (auth:SIMPLE) from /127.0.0.1 for path / at Thu Jul 14 15:59:31 KST 2022


Status: HEALTHY
 Number of data-nodes:	1
 Number of racks:		1
 Total dirs:			3
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
FSCK ended at Thu Jul 14 15:59:31 KST 2022 in 1 milliseconds


The filesystem under path '/' is HEALTHY


$ bin/hadoop fsck /

WARNING: Use of this script to execute fsck is deprecated.

WARNING: Attempting to execute replacement "hdfs fsck" instead.




Connecting to namenode via http://localhost:9870/fsck?ugi=shc&path=%2F

FSCK started by shc (auth:SIMPLE) from /127.0.0.1 for path / at Thu Jul 14 16:00:24 KST 2022





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

FSCK ended at Thu Jul 14 16:00:24 KST 2022 in 0 milliseconds





The filesystem under path '/' is HEALTHY



# fs.defaultFS 설정값 확인

$ bin/hdfs getconf -confKey fs.defaultFS
hdfs://localhost:9000

# 명령어를 인식하지 못함(문서와 내용 다르나 인식 못하는 사실 동일)
$ bin/hadoop getconf
ERROR: getconf is not COMMAND nor fully qualified CLASSNAME.
```
_________________________________________________________________________________________________________________________________________________________________________
다음으로 커맨드 명령어 중 대표적인 파일시스템 쉘을 실행하는 명령어인 dfs에 대해 설명해드리겠습니다. dfs는 hdfs dfs, hadoop fs, hadoop dfs 세가지 형태로 실행이 가능합니다. 사용 방법은 다음과 같습니다. 맥 OS의 경우 명령어가 정상적으로 실행되면 다음과 같이 통과됨을 확인할 수 있습니다.
_________________________________________________________________________________________________________________________________________________________________________
```
(base) shcDE-ui-MacBookPro:hadoop shc$ bin/hdfs dfs -ls
(base) shcDE-ui-MacBookPro:hadoop shc$

(base) shcDE-ui-MacBookPro:hadoop shc$ bin/hadoop fs -ls
(base) shcDE-ui-MacBookPro:hadoop shc$
```
_________________________________________________________________________________________________________________________________________________________________________
cat 명령어는 지정한 파일을 기본 입력으로 읽어서 출력하는 역할을 합니다. 사용 예시는 다음과 같습니다.
_________________________________________________________________________________________________________________________________________________________________________
```
$ bin/hadoop fs -cat /user/testfile01.txt
this is test file 01
```
_________________________________________________________________________________________________________________________________________________________________________

text 명령어는 지정한 파일을 텍스트 형식으로 읽어줍니다. 이 명령어를 통해 gzip, snappy 등의 형식으로 압축된 파일도 자동으로 텍스트 형식으로 출력해 줄 수 있습니다.
_________________________________________________________________________________________________________________________________________________________________________

```
$ bin/hadoop fs -text /user/testfile01.txt
this is test file 01
```
_________________________________________________________________________________________________________________________________________________________________________
ls 명령어는 주어진 경로의 파일 목록을 조회하는 역할을 합니다.
_________________________________________________________________________________________________________________________________________________________________________
```

$ bin/hadoop fs -ls /user/
Found 2 items
drwxr-xr-x   - shc supergroup          0 2022-07-14 15:44 /user/shc
-rw-r--r--   1 shc supergroup         21 2022-07-14 16:17 /user/testfile01.txt

# 사람이 읽을 수 있는 형태로 파일사이즈를 메가, 기가로 변경하여 출력
$ bin/hadoop fs -ls -h /user/
Found 2 items
drwxr-xr-x   - shc supergroup          0 2022-07-14 15:44 /user/shc
-rw-r--r--   1 shc supergroup         21 2022-07-14 16:17 /user/testfile01.txt

# 하위 폴더까지 조회
$ bin/hadoop fs -ls -R /user/
drwxr-xr-x   - shc supergroup          0 2022-07-14 15:44 /user/shc
-rw-r--r--   1 shc supergroup         21 2022-07-14 16:17 /user/testfile01.txt

# 액세스 시간을 조회, 기본설정은 생성 시간, 마지막 접근 시간을 확인하여 파일 정리 가능
$ bin/hadoop fs -ls -u /user/
Found 2 items
drwxr-xr-x   - shc supergroup          0 1970-01-01 09:00 /user/shc
-rw-r--r--   1 shc supergroup         21 2022-07-14 16:17 /user/testfile01.txt

```
_________________________________________________________________________________________________________________________________________________________________________
mkdir 명령어는 디렉토리를 확인하고 생성하는 역할을 합니다.
_________________________________________________________________________________________________________________________________________________________________________
```
# 폴더 확인
$ bin/hadoop fs -mkdir /user/folder
mkdir: `hdfs://localhost:9000/user': No such file or directory

#/user/folder1/folder2를 생성, 상위 폴더가 없으면 자동으로 생성

(base) shcDE-ui-MacBookPro:hadoop shc$ bin/hadoop fs -mkdir -p /user/folder1/folder2
(base) shcDE-ui-MacBookPro:hadoop shc$

# 해당 폴더 존재하는지 확인(folder1 존재 확인)
$ bin/hadoop fs -mkdir /user/folder1
mkdir: `/user/folder1': File exists
```
_________________________________________________________________________________________________________________________________________________________________________
cp 명령어는 HDFS 상의 파일을 복사하는 역할을 합니다.
_________________________________________________________________________________________________________________________________________________________________________
```
(base) shcDE-ui-MacBookPro:hadoop shc$ bin/hadoop fs -cp /user/testfile01.txt /user/testfile02.txt 
(base) shcDE-ui-MacBookPro:hadoop shc$
```
_________________________________________________________________________________________________________________________________________________________________________
mv 명령어는 HDFS 상의 파일을 이동하거나, 파일 이름을 변경할 때 사용합니다.
_________________________________________________________________________________________________________________________________________________________________________
```
(base) shcDE-ui-MacBookPro:hadoop shc$ bin/hadoop fs -mv /user/testfile01.txt /user/testfile03.txt
(base) shcDE-ui-MacBookPro:hadoop shc$
```
_________________________________________________________________________________________________________________________________________________________________________
get 명령어는 HDFS의 파일을 로컬에 복사할 때 사용합니다. 
_________________________________________________________________________________________________________________________________________________________________________
```
(base) shcDE-ui-MacBookPro:hadoop shc$ bin/hadoop fs -get /user/testfile03.txt ./
(base) shcDE-ui-MacBookPro:hadoop shc$

# 동일한 이름의 파일이 존재하면 덮어씀
(base) shcDE-ui-MacBookPro:hadoop shc$ bin/hadoop fs -get -f /user/testfile03.txt ./
(base) shcDE-ui-MacBookPro:hadoop shc$
```
_________________________________________________________________________________________________________________________________________________________________________
put 명령어는 로컬의 파일을 HDFS에 복사할 때 사용합니다. 
_________________________________________________________________________________________________________________________________________________________________________
```
# 해당 파일이 존재할 때는 다음 결과가 나옴
$ bin/hadoop fs -put ./testfile03.txt /user/
put: `/user/testfile03.txt': File exists

# 동일한 이름의 파일이 존재하면 덮어씀
(base) shcDE-ui-MacBookPro:hadoop shc$ bin/hadoop fs -put -f ./testfile03.txt /user/
(base) shcDE-ui-MacBookPro:hadoop shc$
```
_________________________________________________________________________________________________________________________________________________________________________
rm 명령어는 HDFS의 파일을 삭제할 때 사용합니다. 
_________________________________________________________________________________________________________________________________________________________________________
```
$ bin/hadoop fs -rm /user/testfile03.txt
2022-07-14 22:37:56,102 INFO fs.TrashPolicyDefault: Moved: 'hdfs://localhost:9000/user/testfile03.txt' to trash at: hdfs://localhost:9000/user/shc/.Trash/Current/user/testfile03.txt

# 디렉토리를 포함하여 하위의 모든 파일을 삭제, 디렉토리 삭제시 필요함(쓰레기통을 사용하므로 작동 안됨)
$ bin/hadoop fs -rm -r /user/
rm: Cannot move "hdfs://localhost:9000/user" to the trash, as it contains the trash. Consider using -skipTrash option

# 쓰레기통을 사용하지 않고 파일 삭제
$ bin/hadoop fs -rm -skipTrash /user/
rm: `/user': Is a directory
```
_________________________________________________________________________________________________________________________________________________________________________
setrep 명령어는 디렉토리, 파일의 레플리케이션 팩터를 수정합니다. 
_________________________________________________________________________________________________________________________________________________________________________
```
# 디렉토리의 복제개수를 5로 설정
$ bin/hadoop fs -setrep 5 /user/
Replication 5 set: /user/shc/.Trash/Current/user/testfile03.txt
Replication 5 set: /user/testfile02.txt

# bin/hadoop fs -setrep 5 -R /user/ 명령어는 유효하지 않아서 기재하지 않음
```
_________________________________________________________________________________________________________________________________________________________________________
test 명령어는 파일, 디렉토리의 존재 여부를 확인합니다. 
_________________________________________________________________________________________________________________________________________________________________________
```
# -d: 경로가 디렉토리이면 0을 반환합니다.
# -e: 경로가 있으면 0을 반환합니다.
# -f: 경로가 파일이면 0을 반환합니다.
# -s: 경로가 비어 있지 않으면 0을 반환합니다.
# -w: 경로가 존재하고 쓰기 권한이 부여 된 경우 0을 반환합니다.
# -r: 경로가 존재하고 읽기 권한이 부여 된 경우 0을 반환합니다.
# -z: 파일 길이가 0이면 0을 반환합니다.
# /user/test.txt 파일이 존재하면 0을 반환(맥은 그냥 통과)
(base) shcDE-ui-MacBookPro:hadoop shc$ bin/hadoop fs -test -e /user/testfile02.txt
(base) shcDE-ui-MacBookPro:hadoop shc$
```
_________________________________________________________________________________________________________________________________________________________________________
touchz 명령어는 0byte 파일을 생성합니다. 
_________________________________________________________________________________________________________________________________________________________________________
```
(base) shcDE-ui-MacBookPro:hadoop shc$ bin/hadoop fs -touchz /user/test.txt
(base)shcDE-ui-MacBookPro:hadoop shc$
```
_________________________________________________________________________________________________________________________________________________________________________
stat 명령어는 주어진 포맷에 따른 파일의 정보를 확인합니다. 
_________________________________________________________________________________________________________________________________________________________________________
```
# 주요 포맷
# %y : 마지막 수정 시간 
# %x : 마지막 접근 시간 
# %n : 파일 이름 
# %b : 파일 사이즈 (byte)
$ bin/hadoop fs -stat "%y %n" hdfs://127.0.0.1:9870/*
stat: RPC response exceeds maximum data length
```
_________________________________________________________________________________________________________________________________________________________________________
setfacl 명령어는 파일의 ACL을 설정합니다. ls명령으로 확인할 수 있는 파일의 권한과 별도로 권한을 설정할 수있습니다. 
_________________________________________________________________________________________________________________________________________________________________________
```
# user a에게 /user/file의 읽기(r), 쓰기(w) 권한을 줌
(base) shcDE-ui-MacBookPro:hadoop shc$ bin/hadoop fs -setfacl -m user:a:rw- /user/file
(base) shcDE-ui-MacBookPro:hadoop shc$



# /user/file에서 신규로 생성되는 파일, 디렉토리에 대하여 user a에게 의 읽기(r), 쓰기(w) 권한을 줌
(base) shcDE-ui-MacBookPro:hadoop shc$ bin/hadoop fs -setfacl -m default:user:a:rw- /user/file
(base)shcDE-ui-MacBookPro:hadoop shc$
```
_________________________________________________________________________________________________________________________________________________________________________
getfacl 명령어는 파일의 ACL을 확인합니다. 
_________________________________________________________________________________________________________________________________________________________________________
```
$ bin/hadoop fs -getfacl /user/file
# file: /user/file

# owner: shc

# group: supergroup

user::rwx

user:a:rw-

group::r-x

mask::rwx

other::r-x

default:user::rwx

default:user:a:rw-

default:group::r-x

default:mask::rwx

default:other::r-x
```
_________________________________________________________________________________________________________________________________________________________________________
count 명령어는 지정한 경로의 디렉토리 개수, 파일개수, 파일 사이즈 확인합니다. -q, -v 옵션과 함께 사용하여 더 많은 정보를 확인할 수 있습니다. 
_________________________________________________________________________________________________________________________________________________________________________
```
# /user 디렉토리 확인

$ bin/hadoop fs -count /user


           8            3                 42 /user

# 헤더 함께 출력

$ bin/hadoop fs -count -q -v hdfs:///user/file


       QUOTA       REM_QUOTA     SPACE_QUOTA REM_SPACE_QUOTA    DIR_COUNT   FILE_COUNT       CONTENT_SIZE PATHNAME


        none             inf            none             inf            1            0                  0 hdfs:///user/file

```
_________________________________________________________________________________________________________________________________________________________________________
이번 포스팅에서는 명령어를 여기까지 다루겠습니다. 다음 포스팅에서 추가적으로 주요 명령어를 살펴보겠습니다.
_________________________________________________________________________________________________________________________________________________________________________
오늘도 긴 글 읽어주시느라 고생하셨습니다.
_________________________________________________________________________________________________________________________________________________________________________
#### 감사합니다.
_________________________________________________________________________________________________________________________________________________________________________
[레퍼런스]
1. 빅데이터 - 하둡, 하이브로 시작하기(위키독스) : https://wikidocs.net/book/2203
2. 07-HDFS 명령어(빅데이터 - 하둡, 하이브로 시작하기(위키독스)) : https://wikidocs.net/26496