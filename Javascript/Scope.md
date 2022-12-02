# 스코프

참고한 곳 : [[10분 테코톡] 🍧 엘라의 Scope & Closure](https://www.youtube.com/watch?v=PVYjfrgZhtU&t=656s)



![image-20221201022049979](C:\Users\multicampus\AppData\Roaming\Typora\typora-user-images\image-20221201022049979.png)

- 다른 코드가 참조할 수 있는 유효 범위
- 변수 이름, 함수 이름, 클래스 이름과 같은 `식별자`가 본인이 선언된 위치에 따라 다른 코드에서 `자신이 참조될 수 있을지 없을지` 결정하는 것
- 선언 시점에 따라, 전역과 지역변수로 나누어짐



## 스코프 체인

![image-20221201022436503](C:\Users\multicampus\AppData\Roaming\Typora\typora-user-images\image-20221201022436503.png)

- 중첩 함수 : 함수 내부에서 정의된 함수

- 외부 함수 : 중첩 함수를 포함한 함수

![image-20221201022533809](C:\Users\multicampus\AppData\Roaming\Typora\typora-user-images\image-20221201022533809.png)

- inner에서 전역스코프로 각 스코프의 상위 scope로 흐름(반방향성)
  - 이를 스코프 체인이라고 함.
  - 이게 물리적으로 존재하고, Javascript 엔진은 스코프 체인을 통해 변수를 참조함.
  - 이러한 흐름은 무조건 상위로만 흐르고, 하위에서 상위 스코프로만의 참조가 가능함
  - 참조할 스코프가 없다면 ReferenceError



## 스코프 레벨

1. 블록 레벨 : if, for, 함수 => 대부분 프로그래밍 언어, let과 const
   - ES6 이후 Javascript도 블록 레벨 Scope를 가질 수 있음
2. 함수 레벨 : only 함수 => javascript (val)



## 동적 스코프 vs 정적 스코프

- 상위 스코프가 결정 되는 시점
- 동적 스코프 : 함수가 호출되는 시점 (Runtime)
- 정적 스코프 : 함수가 정의되는 시점에 결정 (정의되는 시점 === 렉시컬 스코프)
  - Javascript는 렉시컬 스코프를 따름
  - Javascript에서 함수는 정의 될 때, 본인의 내부 슬롯에 상위 스코프에 대한 참조를 저장 함.
    - 함수 호출 => 실행 컨텍스트 생성 => 실행 컨텍스트 스택에 push + 렉시컬 환경 생성 => 실행 컨텍스트 스택에 해당 함수를 pop하여 제거
    - 렉시컬 환경 : 어떠한 코드가 어디서 실행이 되고 어떤 코드들이 있는지에 대한 정보를 담고 있는 환경
      - 하나의 자료구조로, 포함하는 식별자, 식별자에 바인딩 된 값, 상위 렉시컬 환경에 대한 참조	
