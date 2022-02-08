<p align="center">
    <img width="200px;" src="https://raw.githubusercontent.com/woowacourse/atdd-subway-admin-frontend/master/images/main_logo.png"/>
</p>
<p align="center">
  <img alt="npm" src="https://img.shields.io/badge/npm-6.14.15-blue">
  <img alt="node" src="https://img.shields.io/badge/node-14.18.2-blue">
  <a href="https://edu.nextstep.camp/c/R89PYi5H" alt="nextstep atdd">
    <img alt="Website" src="https://img.shields.io/website?url=https%3A%2F%2Fedu.nextstep.camp%2Fc%2FR89PYi5H">
  </a>
</p>

<br>

# 지하철 노선도 미션
[ATDD 강의](https://edu.nextstep.camp/c/R89PYi5H) 실습을 위한 지하철 노선도 애플리케이션

<br>

## 🚀 Getting Started

### Install
#### npm 설치
```
cd frontend
npm install
```
> `frontend` 디렉토리에서 수행해야 합니다.

### Usage
#### webpack server 구동
```
npm run dev
```
#### application 구동
```
./gradlew bootRun
```


## 단위 테스트 실습
### 단위 테스트 코드 작성하기
- 아래 두 테스트 클래스의 테스트 메서드를 완성해야 함
    - LineServiceMockTest
    - LineServiceTest
- 기존 기능에 대한 테스트 작성이기 때문에 테스트 작성 시 바로 테스트가 성공해야 함

### 비즈니스 로직 리팩터링
- 구간 추가/삭제 기능에 대한 비즈니스 로직은 현재 LineService에 대부분 위치하고 있음
- 비즈니스 로직을 도메인 클래스(Line)으로 옮기기
- 리팩터링 시 LineTest의 테스트 메서드를 활용하여 TDD 사이클로 리팩터링을 진행
- 리팩터링 과정에서 Line 이외 추가적인 클래스가 생겨도 좋음
    - 구간 관리에 대한 책임을 Line 외 별도의 도메인 객체가 가지게 할 수 있음
    

## 🚀 1단계 - 구간 추가 기능 변경

### 변경된 스펙 - 구간 추가 제약사항 변경
- [x] 역 사이에 새로운 역을 등록할 경우
  - [x] 기존 구간의 역을 기준으로 새로운 구간을 추가
    - [x] 기존 구간 A-C에 신규 구간 A-B를 추가하는 경우 A역을 기준으로 추가
    - [x] 기존 구간과 신규 구간이 모두 같을 순 없음(아래 예외사항에 기재됨)
    - [x] 결과로 A-B, B-C 구간이 생김
  - [x] 새로운 길이를 뺀 나머지를 새롭게 추가된 역과의 길이로 설정
  
- [x] 새로운 역을 상행 종점으로 등록할 경우
  - [x] 기존 구간 A-C에 신규 규간 B-A를 추가하는 경우 기존 A역을 기준으로 추가
  - [x] 결과로 B-A, A-C 구간이 생김
  
- [x] 새로운 역을 하행 종점으로 등록할 경우
  - [x] 기존 구간 A-C에 신규 규간 C-B를 추가하는 경우 기존 C역을 기준으로 추가
  
- [x] 노선 조회시 응답되는 역 목록 수정
  - [x] 구간이 저장되는 순서로 역 목록을 조회할 경우 순서가 다르게 조회될 수 있음
  - [x] 아래의 순서대로 역 목록을 응답하는 로직을 변경해야 함
    1. 상행 종점이 상행역인 구간을 먼저 찾는다.
    2. 그 다음, 해당 구간의 하행역이 상행역인 다른 구간을 찾는다.
    3. 2번을 반복하다가 하행 종점역을 찾으면 조회를 멈춘다.
  
### 변경된 스펙 - 예외 케이스
- 역 사이에 새로운 역을 등록할 경우 기존 역 사이 길이보다 크거나 같으면 등록을 할 수 없음
- 상행역과 하행역이 이미 노선에 모두 등록되어 있다면 추가할 수 없음
  - 아래의 이미지에서 A-B, B-C 구간이 등록된 상황에서 B-C 구간을 등록할 수 없음(A-C 구간도 등록할 수 없음)
- 상행역과 하행역 둘 중 하나도 포함되어있지 않으면 추가할 수 없음

### 추가된 기능에 대한 인수 테스트 작성
- 요구사항이나 스펙이 변경될 경우 인수 테스트를 수정해야 함
- 아래에 기재된 변경된 요구사항을 충족하는 인수 테스트를 작성
- 예외 케이스까지 함께 인수 테스트 작성
- 인수 테스트들이 공통으로 필요로 하는 코드의 중복 처리를 고민

### 인수 테스트 이후 TDD
- 인수 테스트 작성 이후 개발 시 TDD를 활용
- 도메인 부분은 필수로 진행
- 서비스 레이어 부분은 선택적으로 진행


## 🚀 2단계 - 구간 제거 기능 변경

### 구간 삭제에 대한 제약 사항 변경 구현
- [x] 기존에는 마지막 역 삭제만 가능했는데 위치에 상관 없이 삭제가 가능하도록 수정
- [x] 종점이 제거될 경우 다음으로 오던 역이 종점이 됨
- [x] 중간역이 제거될 경우 재배치를 함
  - [x] 노선에 A - B - C 역이 연결되어 있을 때 B역을 제거할 경우 A - C로 재배치 됨
  - [x] 거리는 두 구간의 거리의 합으로 정함

### 구간이 하나인 노선에서 마지막 구간을 제거할 때
- [x] 제거할 수 없음

### 이 외 예외 케이스를 고려하기
- [x] 기능 설명을 참고하여 예외가 발생할 수 있는 경우를 검증할 수 있는 인수 테스트를 만들고 이를 성공 시키세요.


## 🚀 3단계 - 경로 조회
- 최단 경로 조회 인수 조건 도출
- 최단 경로 조회 인수 테스트 만들기
- 최단 경로 조회 기능 구현하기

### 요청 / 응답 포맷

#### Request
```
HTTP/1.1 200
Request method:	GET
Request URI:	http://localhost:55494/paths?source=1&target=6
Headers: 	Accept=application/json
Content-Type=application/json; charset=UTF-8
```

#### Response
```
HTTP/1.1 200 
Content-Type: application/json
Transfer-Encoding: chunked
Date: Sat, 09 May 2020 14:54:11 GMT
Keep-Alive: timeout=60
Connection: keep-alive

{
    "stations": [
        {
            "id": 5,
            "name": "양재시민의숲역",
            "createdAt": "2020-05-09T23:54:12.007"
        },
        {
            "id": 4,
            "name": "양재역",
            "createdAt": "2020-05-09T23:54:11.995"
        },
        {
            "id": 1,
            "name": "강남역",
            "createdAt": "2020-05-09T23:54:11.855"
        },
        {
            "id": 2,
            "name": "역삼역",
            "createdAt": "2020-05-09T23:54:11.876"
        },
        {
            "id": 3,
            "name": "선릉역",
            "createdAt": "2020-05-09T23:54:11.893"
        }
    ],
    "distance": 40
}
```

### 힌트
#### 외부 라이브러리 테스트
- 외부 라이브러리의 구현을 수정할 수 없기 때문에 단위 테스트를 하지 않음
- 외부 라이브러리를 사용하는 직접 구현하는 로직을 검증해야 함
- 직접 구현하는 로직 검증 시 외부 라이브러리 부분은 실제 객체를 활용

#### 예외 상황 예시
- 출발역과 도착역이 같은 경우
- 출발역과 도착역이 연결이 되어 있지 않은 경우
- 존재하지 않은 출발역이나 도착역을 조회 할 경우