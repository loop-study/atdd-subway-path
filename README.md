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
- [] 역 사이에 새로운 역을 등록할 경우
  - [] 기존 구간의 역을 기준으로 새로운 구간을 추가
    - [] 기존 구간 A-C에 신규 구간 A-B를 추가하는 경우 A역을 기준으로 추가
    - [] 기존 구간과 신규 구간이 모두 같을 순 없음(아래 예외사항에 기재됨)
    - [] 결과로 A-B, B-C 구간이 생김
  - [] 새로운 길이를 뺀 나머지를 새롭게 추가된 역과의 길이로 설정
  
- [] 새로운 역을 상행 종점으로 등록할 경우
  - [] 기존 구간 A-C에 신규 규간 B-A를 추가하는 경우 기존 A역을 기준으로 추가
  - [] 결과로 B-A, A-C 구간이 생김
  
- [] 새로운 역을 하행 종점으로 등록할 경우
  - [] 기존 구간 A-C에 신규 규간 C-B를 추가하는 경우 기존 C역을 기준으로 추가

- [] 노선 조회시 응답되는 역 목록 수정
  - [] 구간이 저장되는 순서로 역 목록을 조회할 경우 순서가 다르게 조회될 수 있음
  - [] 아래의 순서대로 역 목록을 응답하는 로직을 변경해야 함
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

