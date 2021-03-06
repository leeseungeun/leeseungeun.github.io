---
title:  "식별자 (Identifier)"
date:   2018-07-02 17:06:05 +0900
tags: database sql 자격증 SQLD SQL개발자
---

아래의 내용은 *한국데이터진흥원. 2013. SQL 전문가 가이드. 서울: (주)태산애드컴.*의 요약입니다.


## 식별자 (Identifier) 개념
* 엔터티를 대표할 수 있는 속성
* 엔터티는 반드시 하나의 식별자를 가짐
* 키 (Key)와 구분됨
    * 키는 데이터베이스 테이블에 접근하기 위한 매개체



## 식별자의 특징
* 유일성
* 최소성
* 불변성
* 존재성


* 대체 식별자
    * 주식별자 특징과 일치
* 외부 식별자
    * 주식별자 특징과 일치하지 않음
    * 참조 무결성 제약 조건 (Referential Integrity)에 따른 특징



## 식별자 분류 및 표기법
* 대표성 여부에 따른 분류
    * 주식별자 (Primary Identifier)
    * 보조 식별자 (Alternate Identifier)
* 스스로 생성되는지 여부에 따른 분류
    * 내부 식별자
    * 외부 식별자 (Foreign Identifier)
* 단일 속성으로 식별되는지 여부에 따른 분류
    * 단일 식별자 (Single Identifier)
    * 복합 식별자 (Composite Identifier)
* 대체 여부 / 새롭게 만들어진 식별자인지 여부에 따른 분류
    * 본질 식별자
    * 인조 식별자



## 주식별자 도출 기준
* 업무에서 자주 이용되는 속성을 주식별자로 지정
* 이름으로 기술되는 속성은 주식별자로서 사용 지양
    * 이름으로 기술되는 속성만 존재하는 엔터티의 경우 코드 등 새로운 식별자 생성
* 복합 식별자의 경우 너무 많은 속성 (일반적으로 7~8개 이상)이 포함되지 않도록 함
    * 주식별자인 속성은 상속되어, 조인으로 인해 성능이 저하될 수 있음
    * 반정규화 또는 인조 식별자 생성으로써 해결



## 식별자 관계와 비식별자 관계에 따른 식별자
* 외부 식별자 (Foreign Identifier)
    * 다른 엔터티와의 관계를 통해 자식 쪽에 생성되는 속성
* 외부 식별자를 주식별자로 이용할지 여부에 따른 분류
    * 식별자 관계
        * 부모로부터 받은 주식별자를 자식 엔터티의 주식별자로 이용
        * Null 값이 오면 안되므로 부모가 생성되어야 자식 엔터티 생성 가능
        * 강한 연결 관계
        * 실선으로 표현
        * 부모 엔터티의 주식별자만 자식 엔터티의 주식별자로 이용한다면, 부모와 자식은 1:1 관계
        * 부모 엔터티의 주식별자와 자식 엔터티의 속성을 함께 주식별자로 이용한다면, 부모와 자식은 1:M 관계
        * 식별자 관계로만 설정한다면, 데이터 흐름이 길어질수록 주식별자 속성 개수가 증가해 개발 복잡성과 오류 가능성을 유발할 수 있음
    * 비식별자 관계
        * 부모로부터 받은 주식별자를 자식 엔터티의 일반 속성으로만 이용
        * 아래의 경우 비식별자 관계에 의한 외부 속성 생성
            * 부모 없이 자식이 생성될 수 있는 경우
            * 엔터티별로 생명 주기 (Life Cycle)을 다르게 관리할 경우
            * 각각 별도의 관계를 가지는 여러 개의 엔터티가 하나의 엔터티로 통합되어 표현된 경우
            * 자식 엔터티에 별도로 주식별자를 생성하는 게 유리한 경우
        * 약한 연결 관계
        * 점선으로 표현
        * 비식별자 관계로만 설정한다면, 자식 엔터티의 데이터를 조회할 때 기준 속성이 조회 조건으로 걸려 불필요한 조인이 유발됨
* 자식엔터티의 독립적인 주식별자 구성이 업무적, 성능적으로 필요한지 분석해 비식별자 관계 설정