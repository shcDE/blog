---
title: "파이썬을 활용하여 맵리듀스 구현하기"
date: 2022-07-17T17:54:03+09:00
categories:
- development
tags:
- development
keywords:
- data engineering, python, development, mapreduce
image: wordcloud-gdaa80a00f_1280.png
---
![워드클라우드](https://github.com/shcDE/pictures/blob/main/images_for_blog/wordcloud-gdaa80a00f_1280.png?raw=true)
_________________________________________________________________________________________________________________________________________________________________________
# 맵리듀스란 무엇이고, 어떻게 파이썬으로 구현할까요?
_________________________________________________________________________________________________________________________________________________________________________
안녕하세요. shcDE입니다. 이번 포스팅에서는 맵리듀스에 대하여 알아보겠습니다. 맵리듀스는 하둡과 파이썬에서 주로 구현할 수 있는데, 저는 이번 포스팅에서는 빠르고 간편하게 구현하기 위해 파이썬을 선택하였습니다. 여기에서 맵리듀스는 프로그래밍 모델의 역할과 동시에 구현체를 역할을 수행하는 프레임워크로, 그 구현체는 '분산처리엔진' 역할을 하는 하둡의 중심 모듈 중 하나에 해당합니다. 맵리듀스의 맵은 흩어져 잇는 데이터를 Key, Value의 형태로 연관성 있는 데이터 분류로 묶는 작업을 의미하며, 리듀스는 맵화한 작업 중 중복 데이터를 제거하고 원하는 데이터를 추출하는 작업을 뜻합니다. 사실 하둡에서 해당 프레임워크를 사용하는 것이 정석이지만, 아직 제가 자바보다는 파이썬에 익숙하여, 우선 이번 포스팅에서는 파이썬으로 구현할 예정입니다. 나중에 자바도 잘 다루게 되면 그 때 하둡 내에서 맵리듀스를 실행하는 방법에 대해 포스팅하겠습니다.
_________________________________________________________________________________________________________________________________________________________________________
우선 파이썬에서 맵리듀스를 구현하기 위해 map, filter, reduce 함수를 사용할 예정입니다. 시작 전에 세 함수에 대해 간단히 설명드리겠습니다.
_________________________________________________________________________________________________________________________________________________________________________
1. Map : 함수와 리스트를 인자로 받아 그 리스트로부터 원소를 하나씩 꺼내서 함수를 적용시킨 다음, 그 결과를 새로운 리스트에 담아주는 역할을 하는 함수
2. Filter : 리스트에 있는 원소들을 함수에 적용시켜 결과가 참인 값들로 새로운 리스트를 만들어주는 함수
3. Reduce : 순서형 자료인 문자열, 리스트, 튜플의 원소를 누적하여 적용시키는 함수
_________________________________________________________________________________________________________________________________________________________________________
우선 첫번째로 Map 함수를 활용한 예시는 다음과 같습니다.
_________________________________________________________________________________________________________________________________________________________________________
```python
# 매핑 예시
mynum = ['1', '2', '3', '4']
mynum_int = list(map(int, mynum))
print(mynum_int)

mynum_square = list(map(lambda x: x*x, mynum_int))
print(mynum_square)
```
```
[1, 2, 3, 4]
[1, 4, 9, 16]
```
_________________________________________________________________________________________________________________________________________________________________________
Filter 함수를 사용한 예시는 다음과 같습니다.
_________________________________________________________________________________________________________________________________________________________________________
```python
# 필터링 예시
mynum = range(-5, 5)
mynum_plus = list(filter(lambda x: x > 0, mynum))
# mynum의 각 원소 x에 대해 lambda x: x > 0인지 체크하는 필터를 적용
print(mynum_plus)
```
```
[1, 2, 3, 4]
```
_________________________________________________________________________________________________________________________________________________________________________
Reduce 함수를 사용한 예시는 다음과 같습니다.
_________________________________________________________________________________________________________________________________________________________________________
```python
# 리듀싱 함수 예시
from functools import reduce
mynum = [1, 2, 3, 4, 5]
add = reduce((lambda x, y: x + y), mynum)
# reduce는 내부에 관리하는 x 변수에 mynum의 각 원소 y를 차례차례 더하여 x에 반영

print(add)
```
```
15
```
_________________________________________________________________________________________________________________________________________________________________________
위와 같이 map, filter, reduce 함수를 사용하면 간편하게 맵리듀스를 구현할 수 있습니다. 하지만 작동 원리를 이해하기 위해 매핑 함수와 리듀스 함수를 따로 구현해보도록 하겠습니다.
_________________________________________________________________________________________________________________________________________________________________________
매핑 함수는 다음과 같이 구현하였습니다.
_________________________________________________________________________________________________________________________________________________________________________
```python
#테스트 데이터
text = 'hello python'

# 텍스트 단어를 스플릿 내서 list에 저장
def mapper(text):
    split = []
    for i in text:
        split.append((i, 1))
    return split
```
_________________________________________________________________________________________________________________________________________________________________________
리듀스 함수는 다음과 같이 구현하였습니다.
_________________________________________________________________________________________________________________________________________________________________________
```python
# mapper에서 받은 값 중 같은 값은 더하고 알파벳 모두 나눔
def reducer(split):
    out = {}
    for i in split:
        if i[0] not in out.keys():
            out[i[0]] = 1
        else:
            out[i[0]] += 1
    return out
```
_________________________________________________________________________________________________________________________________________________________________________
나눈 알파벳을 출력한 결과는 다음과 같습니다.
_________________________________________________________________________________________________________________________________________________________________________
```python
# 나눈 알파벳 출력
reducer(mapper(text))
```
```
{'h': 2, 'e': 1, 'l': 2, 'o': 2, ' ': 1, 'p': 1, 'y': 1, 't': 1, 'n': 1}
```
_________________________________________________________________________________________________________________________________________________________________________
이렇게 간단한 단어를 알파벳으로 나누어 출력하는 실습을 진행하였습니다. 이제 본격적으로 한 문단의 아티클을 들고 와서 실습을 진행하겠습니다. 가져온 아티클의 출처는 [링크](https://www.devlane.com/blog/data-engineering-trends-in-2022) 클릭 시 확인할 수 있습니다.
_________________________________________________________________________________________________________________________________________________________________________
```python
# 파이썬에서 맵리듀스를 구현하기 위해 자체 내장 라이브러리 collections 호출
import collections
from collections import defaultdict
import string

# 텍스트 출처 : https://www.devlane.com/blog/data-engineering-trends-in-2022
text = """
[DataOps and MLOps]
As it happened with DevOps (Development + Operations), QAops (Quality Assurance + Operations), 2022 will increase the adoption of DataOps and MLOps, for Data Engineering and Machine Learning teams.
The DataOps approach implements the same benefits of DevOps and QAOps for Data professionals. It helps build and maintain streamlined, agile data analytics pipelines iteratively and incrementally. 
Data in businesses is ingested, prepared, and orchestrated in different ways. This creates an increasing demand for automation and pipeline integration tools that help data teams visualize their workflows in unified tools. 
This year, businesses will start implementing DataOps processes, which, combined with the already adopted Agile methodologies, will help companies reach next-level data analytics and decision making.
The DataOps approach will enable organizations to implement automated data pipelines in their private, multi-cloud, or hybrid environments.
The main objective of DataOps and MLOps is to accelerate the development and maintenance cycle of analytics and data models.
Traditionally reserved for big companies, mid-sized companies will start implementing Data and MLOps processes in 2022. Even start-ups with their typical urgency for reliable data will begin implementing some aspects of these new data-driven engineering approaches.
"""

# 특수문자 제거
for character in string.punctuation:
    text = text.replace(character, '')

# 결과 초기값 설정 및 단어 공백으로 분리
result = defaultdict(int)
word = text.split()

# 단어개수 카운팅
for i in word:
    result[i] += 1

# 카운팅한 단어 개수 출력
print(result)
```
```
defaultdict(<class 'int'>, {'DataOps': 6, 'and': 13, 'MLOps': 4, 'As': 1, 'it': 1, 'happened': 1, 'with': 3, 'DevOps': 2, 'Development': 1, 'Operations': 2, 'QAops': 1, 'Quality': 1, 'Assurance': 1, '2022': 2, 'will': 6, 'increase': 1, 'the': 4, 'adoption': 1, 'of': 5, 'for': 5, 'Data': 4, 'Engineering': 1, 'Machine': 1, 'Learning': 1, 'teams': 2, 'The': 3, 'approach': 2, 'implements': 1, 'same': 1, 'benefits': 1, 'QAOps': 1, 'professionals': 1, 'It': 1, 'helps': 1, 'build': 1, 'maintain': 1, 'streamlined': 1, 'agile': 1, 'data': 6, 'analytics': 3, 'pipelines': 2, 'iteratively': 1, 'incrementally': 1, 'in': 5, 'businesses': 2, 'is': 2, 'ingested': 1, 'prepared': 1, 'orchestrated': 1, 'different': 1, 'ways': 1, 'This': 2, 'creates': 1, 'an': 1, 'increasing': 1, 'demand': 1, 'automation': 1, 'pipeline': 1, 'integration': 1, 'tools': 2, 'that': 1, 'help': 2, 'visualize': 1, 'their': 3, 'workflows': 1, 'unified': 1, 'year': 1, 'start': 2, 'implementing': 3, 'processes': 2, 'which': 1, 'combined': 1, 'already': 1, 'adopted': 1, 'Agile': 1, 'methodologies': 1, 'companies': 3, 'reach': 1, 'nextlevel': 1, 'decision': 1, 'making': 1, 'enable': 1, 'organizations': 1, 'to': 2, 'implement': 1, 'automated': 1, 'private': 1, 'multicloud': 1, 'or': 1, 'hybrid': 1, 'environments': 1, 'main': 1, 'objective': 1, 'accelerate': 1, 'development': 1, 'maintenance': 1, 'cycle': 1, 'models': 1, 'Traditionally': 1, 'reserved': 1, 'big': 1, 'midsized': 1, 'Even': 1, 'startups': 1, 'typical': 1, 'urgency': 1, 'reliable': 1, 'begin': 1, 'some': 1, 'aspects': 1, 'these': 1, 'new': 1, 'datadriven': 1, 'engineering': 1, 'approaches': 1})
```
_________________________________________________________________________________________________________________________________________________________________________
해당 방식을 통해 장문의 글에서도 맵리듀스를 적용할 수 있음을 확인할 수 있었습니다. 하지만 이렇게 구현하면 매번 같은 코드를 작성해야 하는 불편함이 있기에 함수를 생성하여 다시 한 번 호출하겠습니다.
_________________________________________________________________________________________________________________________________________________________________________
```python
# 아까 가져온 장문의 텍스트 다시 가져옴
text = """
[DataOps and MLOps]
As it happened with DevOps (Development + Operations), QAops (Quality Assurance + Operations), 2022 will increase the adoption of DataOps and MLOps, for Data Engineering and Machine Learning teams.
The DataOps approach implements the same benefits of DevOps and QAOps for Data professionals. It helps build and maintain streamlined, agile data analytics pipelines iteratively and incrementally. 
Data in businesses is ingested, prepared, and orchestrated in different ways. This creates an increasing demand for automation and pipeline integration tools that help data teams visualize their workflows in unified tools. 
This year, businesses will start implementing DataOps processes, which, combined with the already adopted Agile methodologies, will help companies reach next-level data analytics and decision making.
The DataOps approach will enable organizations to implement automated data pipelines in their private, multi-cloud, or hybrid environments.
The main objective of DataOps and MLOps is to accelerate the development and maintenance cycle of analytics and data models.
Traditionally reserved for big companies, mid-sized companies will start implementing Data and MLOps processes in 2022. Even start-ups with their typical urgency for reliable data will begin implementing some aspects of these new data-driven engineering approaches.
"""

# 특수문자 제거
for character in string.punctuation:
    text = text.replace(character, '')

# word_count 함수 생성해서 맵리듀싱 진행
def word_count(text):
    result = defaultdict(int)
    word = text.split()

    for i in word:
        result[i] += 1
    return result

# 맵리듀스 단어 개수 카운팅 결과 출력
word_count(text)
```
```
defaultdict(int,
            {'DataOps': 6,
             'and': 13,
             'MLOps': 4,
             'As': 1,
             'it': 1,
             'happened': 1,
             'with': 3,
             'DevOps': 2,
             'Development': 1,
             'Operations': 2,
             'QAops': 1,
             'Quality': 1,
             'Assurance': 1,
             '2022': 2,
             'will': 6,
             'increase': 1,
             'the': 4,
             'adoption': 1,
             'of': 5,
             'for': 5,
             'Data': 4,
             'Engineering': 1,
             'Machine': 1,
             'Learning': 1,
             'teams': 2,
             'The': 3,
             'approach': 2,
             'implements': 1,
             'same': 1,
             'benefits': 1,
             'QAOps': 1,
             'professionals': 1,
             'It': 1,
             'helps': 1,
             'build': 1,
             'maintain': 1,
             'streamlined': 1,
             'agile': 1,
             'data': 6,
             'analytics': 3,
             'pipelines': 2,
             'iteratively': 1,
             'incrementally': 1,
             'in': 5,
             'businesses': 2,
             'is': 2,
             'ingested': 1,
             'prepared': 1,
             'orchestrated': 1,
             'different': 1,
             'ways': 1,
             'This': 2,
             'creates': 1,
             'an': 1,
             'increasing': 1,
             'demand': 1,
             'automation': 1,
             'pipeline': 1,
             'integration': 1,
             'tools': 2,
             'that': 1,
             'help': 2,
             'visualize': 1,
             'their': 3,
             'workflows': 1,
             'unified': 1,
             'year': 1,
             'start': 2,
             'implementing': 3,
             'processes': 2,
             'which': 1,
             'combined': 1,
             'already': 1,
             'adopted': 1,
             'Agile': 1,
             'methodologies': 1,
             'companies': 3,
             'reach': 1,
             'nextlevel': 1,
             'decision': 1,
             'making': 1,
             'enable': 1,
             'organizations': 1,
             'to': 2,
             'implement': 1,
             'automated': 1,
             'private': 1,
             'multicloud': 1,
             'or': 1,
             'hybrid': 1,
             'environments': 1,
             'main': 1,
             'objective': 1,
             'accelerate': 1,
             'development': 1,
             'maintenance': 1,
             'cycle': 1,
             'models': 1,
             'Traditionally': 1,
             'reserved': 1,
             'big': 1,
             'midsized': 1,
             'Even': 1,
             'startups': 1,
             'typical': 1,
             'urgency': 1,
             'reliable': 1,
             'begin': 1,
             'some': 1,
             'aspects': 1,
             'these': 1,
             'new': 1,
             'datadriven': 1,
             'engineering': 1,
             'approaches': 1})
```
_________________________________________________________________________________________________________________________________________________________________________
확실히 함수를 생성하여 맵리듀스를 실행한 결과, 코드를 반복적으로 작성할 일도 줄어들 뿐만 아니라, 결과 출력도 깔끔하게 나온 것을 확인할 수 있었습니다. 이렇게 다양한 방식으로 파이썬에서 맵리듀스를 구현해봤습니다. 다음에 다시 맵리듀스에 대해 공부할 계기가 생긴다면, 꼭 하둡을 직접 사용하여 구현할 수 있도록 실력을 쌓겠습니다.
_________________________________________________________________________________________________________________________________________________________________________
이상으로 파이썬을 활용한 맵리듀스 구현에 대한 포스팅을 마치겠습니다. 모두 긴 글 읽어주시느라 고생 많으셨고, 다음 한 주도 화이팅입니다.
_________________________________________________________________________________________________________________________________________________________________________
#### 감사합니다.
_________________________________________________________________________________________________________________________________________________________________________
[레퍼런스]
1. Data Engineering Trends in 2022 : https://www.devlane.com/blog/data-engineering-trends-in-2022