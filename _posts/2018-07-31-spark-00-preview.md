---
title:  "스파크 (Spark) 0탄: 들어가기 전에"
date:   2018-07-31 21:54:05 +0900
tags: 데이터엔지니어링 스파크
---

아래의 내용은 개인적으로 공부한 내용을 정리한 것입니다. 잘못된 내용이 있다면 댓글 부탁드립니다!

- - -
  
최근 빅데이터 시장에서의 스파크 (Spark)의 위상이 높아지고 있는 것으로 보입니다.  
채용 공고에서도 이전에 비해 스파크가 빈번하게 등장하는 것 같습니다.  


![Job Description](../assets/images/2018-07-31-spark-00-preview-00-jd.png)


채용 공고에서 등장하는 스파크...!


대세에 따라 스파크 프로젝트를 진행해보고자 합니다.  
스파크 프로젝트를 진행하기 앞서 관련 기술 등을 검토해보고자 합니다.


아래의 정보는 
* T academy 토크ON세미나 9차 빅데이터 입문 Apache Spark
* Karau, H., Konwinski, A., Wendell, P. & Zaharia, M. (2015). 러닝 스파크: 번개같이 빠른 데이터 분석 (박종영 옮김). 경기:제이펍. (원서출판 2015). 


을 기반으로 작성되었습니다.  
## 컴퓨터 클러스터 (Computer cluster)[1][2]
하나의 시스템처럼 동작하는, 연결된 컴퓨터들의 집합을 뜻합니다.  

![Computer cluster](../assets/images/2018-07-31-spark-00-preview-01-computer-cluster.jpg)


각 컴퓨터 (Node, 노드)는 (주로 근거리 통신망을 이용해) 서로 통신하며 업무를 분담해 수행합니다. 이때 클러스터 미들웨어라는 소프트웨어 계층에서 노드들을 관리합니다.  
단일 컴퓨터에 비해, 
* 더 뛰어난 성능과 안정성을 제공합니다.
* 비율 측면에서 보다 효율적입니다.
  
## 맵 리듀스 (Map Reduce))
[1]: https://ko.wikipedia.org/wiki/%EC%BB%B4%ED%93%A8%ED%84%B0_%ED%81%B4%EB%9F%AC%EC%8A%A4%ED%84%B0
[2]: http://tigerbum.tistory.com/20