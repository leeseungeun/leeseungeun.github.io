---
title:  "플라스크 (Flask) 파이썬 웹 프레임워크로 RESTful API 서버 만들기 (2)"
date:   2018-08-22 19:51:05 +0900
tags: 파이썬 플라스크 웹프로그래밍 Python Flask 
---
아래의 내용은 개인적으로 공부한 내용을 정리한 것입니다. 잘못된 내용이 있다면 댓글 부탁드립니다!

- - -
이제 본격적으로 API 개발 관련 코드에 대해 설명드리고자 합니다. 구현한 API의 목록은 아래와 같습니다.

HTTP Method|URL|기능
-----------|---|----
GET|/boards/|데이터베이스에서 전체 게시판에 대한 정보를 JSON 형식으로 반환
POST|/boards/|데이터베이스에 새로운 게시판 정보를 추가
GET|/boards/{board_id}|데이터베이스로부터 해당 id의 게시판 정보를 가져와 JSON 형식으로 반환
PATCH|/boards/{board_id}|해당 id의 게시판 정보를 수정해 데이터베이스에 저장
DELETE|/boards/{board_id}|해당 id의 게시판을 데이터베이스로부터 삭제
