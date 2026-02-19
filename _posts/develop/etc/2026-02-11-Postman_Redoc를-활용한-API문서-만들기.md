---
title: 'Postman, Redoc을 활용한 API문서 만들기'
categories: [개발, API]
tags: [api, postman, redoc]
date: 2026-02-11
---


## 1. Postman 의 Collection을 Json 으로 변환
1.1 Git 연동되었을 경우
 - Postman의 Git과 연동된 디렉토리의 `collections` 폴더에 보면 해당 `json` 파일이 존재.


1.2 Postman에서 해당 Collection을 Export 한다
 - Collection v2.1 선택 후 Export

## 2.1 Postman Collection을 OpenAPI(Swagger)로 변환
 - Redoc는 `OpenAPI Specification(OAS)` 포맷으로만 읽을 수 있음.
 - `postman-to-openapi` 를 통해 postman이 관리하는 json 파일을 `OSA` 포맷으로 변환해야 함.
 - `postman-to-openapi`는 nodeJS 17.x 버전에서 실행 됨. 

2.1.1 `postman-to-openapi` 라이브러리 설치
nodeJS 17.x 버전 설치
```bash
nvm install 17.9.1
```

nodeJS 17.x 버전 선택
```bash
nvm use 17.9.1
```

`postman-top-openapi` 설치
```bash
npm install -g postman-to-openapi
```

2.1.2 변환 명령 실행
```bash
npx p2o {기존포스트맨컬렉션}.json -f openapi.yaml
```

## 2.2 웹사이트 이용하기
[p2o.defcon007.com](https://p2o.defcon007.com/){:target="_blank"}


```Plaintext
project/
├── openapi.yaml          # 메인 파일 (기본 정보 및 $ref 연결)
├── paths/                # API 경로별 분리
│   ├── auth.yaml
│   ├── orders.yaml       # 주문 관련 API만 따로 관리
│   └── products.yaml
└── components/           # 공통 모델 분리
    └── schemas.yaml      # OrderStatus 같은 공통 스키마 정의
```

