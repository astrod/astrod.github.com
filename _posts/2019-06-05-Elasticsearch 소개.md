---
layout: post
title: "Elasticsearch 소개 - 설치"
tags:
- elasticsearch
category:
- elasticsearch
---

# 들어가며

이 포스팅의 글은 elastic.co/kr 의 김종민님이 업로드하신 동영상을 요약한 것이다.
회사에서 Elasticsearch 를 사용하고 있는데, 회사에서는 elasticsearch farm 을 제공하고 있어, 실상 이렇게 알 필요는 없다.
그러나 좀 더 이론적 기반을 갖춰놓으면 좋을 거 같아, 김종민님이 진행해 주신 강의를 언제 한번 정리하려고 마음먹고 있었는데 시간이 되어 간단하게 정리하려고 한다. 

이 글은 2018년도 중순에 작성하였는데, 게을러서 좀 더 정리하고 올려야지 생각하고 미뤄두다가 영영 드리프트 폴더에 묻힐 거 같아 지금이라도 업로드한다.
잠깐 훑어보았지만 설치와 간단한 설명이기 때문에, 크게 변경된 것은 없으리라 생각한다.

# 개요

## Elastic Stack

Kibana + Elasticsearch + Beats + Logstash 를 묶은 것을 지칭한다. 계속해서 새로운 스택이 추가되고 있다.
위의 기술 스택은 모두 오픈소스로 제공된다.

## X-Pack

Elasticsearch 에서 유료로 제공하는 기능이다. 보고서를 만들거나 머신러닝을 하는 것과 같은 기능을 제공한다.

## Elastic Cloud

엘라스틱서치를 클라우드 환경에서 제공하는 서비스이다. 여기에 추가적으로 기업, 혹은 거래 조직에서 필요로 하는 클라우드 서비스가 있는 경우, Elastic Cloud Enterprise 를 사용할 수 있다.
조직마다 각기 서로 다른 Elasticsearch 환경이 필요한 경우 각각 조직마다 다른 환경을 제공할 수 있다. 큰 조직에서 사용한다.

# Elasticsearch

- 엘라스틱 스택의 심장
- 데이터를 저장하고 필요한 데이터를 검색할 수 있다.
- 스케일아웃이 가능하고 고가용성을 지원한다. 
- 실시간으로 full-text 검색이 가능하며 Aggregation 으로 데이터 집계가 가능하다.

## 사용처

- 데이터 처리
- 로그 분석
- 검색

## 설명

- 분산 처리 시스템
- 스케일아웃이 가능하며 고가용성을 가지고 있다.

## Elasticsearch 클러스터링 과정

- 무결성과 가용성을 위해 샤드의 복제본을 만든다.
- 같은 내용의 복제본과 샤드는 서로 다른 노드에 저장된다.
- 만약 노드가 유실된다면(시스템 다운, 혹은 네트워크 단절) 유실된 샤드들이 살아있는 노드로 복제된다.
    - 이를 통해 샤드의 수는 변함없이 무결성을 유지한다.

### Shard

- 데이터 저장의 단위
- Elasticsearch 는 기본적으로 데이터를 Shard 단위로 나누어 저장한다.

### Node

- 하나의 Elasticsearch 프로세스를 Node 라고 한다. Node 를 여러 개 띄워 클러스터링 환경을 구축할 수 있다.

## 검색 과정

- 검색 요청을 받은 노드가, 각 샤드로 검색 명령어를 전달한다.
- 샤드에서는 쿼리에 맞는, 해당하는 도큐먼트를 리턴한다.
    - 처음에는 도큐먼트 id, ranking 점수를 리턴한다.
- 랭킹 점수를 기반으로 데이터를 정렬후, 필요한 데이터를 샤드에 요청한다.
- 전체 문서 내용 등의 정보가 리턴되어 클라이언트로 전달된다.
    - 어떤 노드에 검색요청을 하여도, 동일한 데이터를 반환한다.
    
## REST API

- Elasticsearch 는 REST API 를 제공한다.
- 동일한 URI 는 동일한 도큐먼트를 가리키고, 그 URI 로 업데이트 요청을 하면 도큐먼트가 업데이트된다.

## full-text 검색 엔진

- 데이터를 저장할 때, 데이터 전처리 과정을 거치게 된다.
- 데이터를 저장할 때, 가공하여 색인 과정을 거친다.
- 데이터를 호출할 때 역색인 과정을 거쳐 데이터를 반환한다.

## 실시간 검색 엔진

- 한 프로세스나 배치를 통하여 결과를 도출하는 것이 아니라, 데이터를 저장할 때 그 데이터를 색인한다.
- 따라서 실시간으로 데이터 검색 가능하다.

## Aggregation

- 집계 기능을 제공한다.
- 내가 저장하고 있는 특정 값에 대하여 통계를 내고, 그룹 단위로 묶어 별도의 통계를 보는 것과 같은 일이 가능하다.
- 검색 뿐만 아니라 분석에도 Elasticsearch 를 활용하고 있다.

# Elasticsearch 실습
해당 실습은 리눅스 환경에서 이루어졌다. 

## 설치

~~~shell
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.3.0.tar.gz
tar xfz elasticsearch-6.3.0.tar.gz
ln -s elasticsearch-6.3.0.tar.gz elasticsearch
cd elasticsearch
~~~

위의 명령어를 통해 다운로드, 압축 해제, 폴더 진입을 할 수 있다.
Elasticsearch 는 java 위에서 동작하기 때문에 기본적으로 java 가 설치되어 있어야 한다. 

~~~shell
java version "1.8.0_161"
Java(TM) SE Runtime Environment (build 1.8.0_161-b12)
Java HotSpot(TM) 64-Bit Server VM (build 25.161-b12, mixed mode)
~~~

기본적으로 java 1.8 대에서 실행되며, 실행 환경은 https://www.elastic.co/support/matrix 에서 확인할 수 있다.

## 디렉토리 확인

- bin : elasticsearch 가 실행되는 파일이 존재함
- config : elasticsearch 가 실행될 때 환경을 설정하는 파일 존재
    - elasticsearch.yml : elasticsearch 가 실행이 될때 물고 올라가는 설정 파일
        - Network : 네트워크 상에서 노드를 실행할 때 필요한 설정. 디폴트는 주석처리이며, 이 경우 로컬로 동작한다.
        - Discovery : elasticsearch node 끼리는 zen discovery 라는 스팩을 사용하여 서로 연결한다. 이 설정을 통해 노드끼리 연결 가능하다.
        

## 실행

~~~shell
bin/elasticsearch
~~~ 

위의 명령어로 실행시킬 수 있으며, 실행시키면 다음의 로그를 확인할 수 있다.

~~~shell
[2018-07-01T22:42:07,478][INFO ][o.e.x.s.t.n.SecurityNetty4HttpServerTransport] [vTJrfB6] publish_address {127.0.0.1:9200}, bound_addresses {[::1]:9200}, {127.0.0.1:9200}
~~~

이는 9200 번 포트를 물고 elasticsearch 가 동작하였다는 의미이며, 제대로 동작하는지 다음과 같이 확인할 수 있다.

~~~shell
curl -XGET localhost:9200

{
  "name" : "vTJrfB6",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "vF9zlyRrS-WF7tUoteoEgw",
  "version" : {
    "number" : "6.3.0",
    "build_flavor" : "default",
    "build_type" : "tar",
    "build_hash" : "424e937",
    "build_date" : "2018-06-11T23:38:03.357887Z",
    "build_snapshot" : false,
    "lucene_version" : "7.3.1",
    "minimum_wire_compatibility_version" : "5.6.0",
    "minimum_index_compatibility_version" : "5.0.0"
  },
  "tagline" : "You Know, for Search"
}
~~~

클러스터 이름은 elasticsearch, node 이름은 vTJrfB6, 버전은 6.3.0 임을 확인할 수 있다.
그러면, 클러스터 환경을 구축해 보자. elasticsearch 를 각기 세 번 압축을 풀고 클러스터 환경을 구축할 것이다.

~~~shell

# node 3개 생성

mv elasticsearch-6.3.0 node-1
tar xfz elasticsearch-6.3.0.tar.gz
mv elasticsearch-6.3.0 node-2
tar xfz elasticsearch-6.3.0.tar.gz
mv elasticsearch-6.3.0 node-3

# 설정 변경

cd node-1
vi config/elasticsearch.yml

# 내부의 클러스터 네임 변경
cluster.name: es-1
node.name:node-1

# 실행
bin/elasticsearch
~~~

실행하면 노드네임이 node-1 로 변경되었음을 확인할 수 있다.
위의 GET 요청을 다시 해 보면

~~~shell
{
  "name" : "node-1",
  "cluster_name" : "es-1",
  "cluster_uuid" : "vF9zlyRrS-WF7tUoteoEgw",
  "version" : {
    "number" : "6.3.0",
    "build_flavor" : "default",
    "build_type" : "tar",
    "build_hash" : "424e937",
    "build_date" : "2018-06-11T23:38:03.357887Z",
    "build_snapshot" : false,
    "lucene_version" : "7.3.1",
    "minimum_wire_compatibility_version" : "5.6.0",
    "minimum_index_compatibility_version" : "5.0.0"
  },
  "tagline" : "You Know, for Search"
}
~~~

클러스터명과 노드명이 모두 설정대로 변경되었음을 알 수 있다. 이제 2번 노드를 띄울 것이다. 2번 노드는 설정파일 변경 없이 인라인에서 설정을 세팅할 것이다.

~~~shell
cd node-2
bin/elasticsearch -E cluster.name=es-1 -E node.name=node-2
~~~

node-1과 node-2는 같은 클러스터명이 세팅되었는데, node-2 는 node-1 을 마스터로 하고, 9201 번 포트를 통해 뜬다.
같은 호스트에서 실행한 경우에는 네트워크 호스트 설정을 하지 않아도 클러스터명이 동일하면 자동으로 바인딩 된다.

~~~shell
curl localhost:9201

{
  "name" : "node-2",
  "cluster_name" : "es-1",
  "cluster_uuid" : "vF9zlyRrS-WF7tUoteoEgw",
  "version" : {
    "number" : "6.3.0",
    "build_flavor" : "default",
    "build_type" : "tar",
    "build_hash" : "424e937",
    "build_date" : "2018-06-11T23:38:03.357887Z",
    "build_snapshot" : false,
    "lucene_version" : "7.3.1",
    "minimum_wire_compatibility_version" : "5.6.0",
    "minimum_index_compatibility_version" : "5.0.0"
  },
  "tagline" : "You Know, for Search"
}
~~~

로 node-2 에 클러스터는 es-1 로 실행되었음을 알 수 있다.

## 도큐먼트 인서트

~~~shell
curl -X PUT "localhost:9200/twitter/_doc/1" -H 'Content-Type: application/json' -d'
{
    "user" : "kimchy",
    "post_date" : "2009-11-15T14:12:12",
    "message" : "trying out Elasticsearch"
}
'
~~~

위와 같이 하면 node-1 에 위의 도큐먼트가 삽입된다. twitter 라는 index 로 데이터를 인서트했는데, 이렇게 하면 twitter 라는 인덱스가 생성된다.
다음과 같이 조회할 수 있다.

~~~shell
curl -XGET localhost:9200/twitter/_doc/1
{"_index":"twitter","_type":"_doc","_id":"1","_version":1,"found":true,"_source":
{
    "user" : "kimchy",
    "post_date" : "2009-11-15T14:12:12",
    "message" : "trying out Elasticsearch"
}
}


curl -XGET localhost:9201/twitter/_doc/1
{"_index":"twitter","_type":"_doc","_id":"1","_version":1,"found":true,"_source":
{
    "user" : "kimchy",
    "post_date" : "2009-11-15T14:12:12",
    "message" : "trying out Elasticsearch"
}
}
~~~

9201 번으로 요청해도 같은 응답값을 가져올 수 있다. 왜냐하면 9200 번과 9201 번이 같은 클러스터로 바인딩되어있기 때문이다.
이제 node-3 을 실행시켜보겠다. node-3 은 클러스터명을 es-2 로 하고 노드명은 node-3 으로 하도록 하자

~~~shell
cd node-3
bin/elasticsearch -E cluster.name=es-2 -E node.name=node-3
~~~

위의 노드는 클러스터명이 다르기 때문에 클러스터링되지 않는다. 따라서 

~~~shell
curl -XGET localhost:9202/twitter/_doc/1
~~~

이와 같이 조회하면 데이터가 나오지 않는다.

# Kibana

Kibana 는 Elasticsearch 에 있는 데이터를 시각화하여 보여주는 툴이다.

## 설치

~~~shell
wget https://artifacts.elastic.co/downloads/kibana/kibana-6.3.0-linux-x86_64.tar.gz
cd apps
tar xfz kibana-6.3.0-linux-x86_64.tar.gz
ln -s kibana-6.3.0-linux-x86_64 kibana
~~~

키바나를 다운로드 받고 압축을 푼다.  

## 실행

~~~shell
bin/kibana
~~~

같은 서버에서 키바나와 Elasticsearch 를 동시에 실행시키면 자동으로 연동된다. 기본 포트는 5601 번이다.

