# 프로세스 Management



Process가 어떻게 만들어지는가?

## 프로세스 생성

- 부모 프로세스가 자식 프로세스 생성 (복제 생성)
- 계층 구조 형성(Tree)
- 프로세스는 자원을 필요로 함.
  - 운영체제로 부터 받음
  - 부모와 공유
- 자원의 공유 (효율적인 운영체제에서, 자원을 낭비하지 않게 하기 위해)
  - 부모와 자식이 모든 자원을 공유
  - 일부
  - 전혀 공유하지 않는 모델
  - 원칙적으로는 공유하지 않음. (경쟁하는 사이)
- Copy-on-wirte(COW) : write가 발생했을 때 그때 copy하겠다
  - 내용이 바뀔 때 새로운 것을 만듦
  - 내용이 바뀌기 전까지는 부모 것을 사용함.
- 실행 (Execution)
  - 부모와 자식이 공존하며 실행되는 모델
  - 자식이 종료될때 까지 기다림.
- 주소 공간(Address space)
  - 자식은 부모의 공간을 `복사`함 (binary and OS data)
  - 자식은 복제된 공간에 새로운 프로그램을 올림
- 유닉스의 예
  - fork() : 시스템 콜을 통해 복제 함.
    - 부모를 그대로 복사 (OS data except PID + binary)
    - 주소 공간 할당
  - fork 다음에 이어지는 exec() 시스템 콜을 통해 새로운 프로그램을 메모리에 올림



## 프로세스 종료

- 프로세스가 마지막 명령 수행 후 OS에 알려줌 (exit)
  - 자식이 부모에게 output data를 보냄 (via wait) , 자식이 항상 먼저 종료됨.
  - 프로세스의 각종 자원들이 운영체제에게 반납됨
- 부모 프로세스가 자식의 수행을 종료시킴 (abort)
  - 자식이 할당 자원의 한계치를 넘어섬
  - 자식에게 할당된 테스크가 더 이상 필요하지 않음
  - 부모가 종료(exit)되는 경우
    - 운영체제는 부모 프로세스가 종료되는 경우, 자식이 더이상 수행되도록 두지 않음. (자식은 항상 먼저 종료됨)
    - 단계적인 종료



## fork() 시스템콜

- A process is created by the fork() system call
  - creates a new address space that is a duplicated of the caller

![image-20221128170756380](C:\Users\multicampus\AppData\Roaming\Typora\typora-user-images\image-20221128170756380.png)

- 자식은 fork() 밑에부터 진행 됨. 부모가 fork()를 하고난 후 program counter 가 if 부분을 가르치고 있고, 자식은 그것과 동일한 pc를 받기 때문.
- process가 복제 되면, 자식과 부모를 구분해줌. fork의 결과 값이 다름. 부모는 양수, 자식은 0을 받음.



## exec() 시스템콜

- A process can execute a different program by the exec() systsme call.

  - replaces the memory image of the caller with a new program.
  - 완전히 새로운 process로 태어나게 하는 역할을 해줌

  ![image-20221129122950926](C:\Users\multicampus\AppData\Roaming\Typora\typora-user-images\image-20221129122950926.png)



- 아래와 같은 경우 exec 이후 나온 코드는 실행이 안됨.

  ![image-20221130152532881](C:\Users\multicampus\AppData\Roaming\Typora\typora-user-images\image-20221130152532881.png)



execlp('프로그램이름', '프로그램이름', args... , (chart *) 0 );



## wait() 시스템 콜

![image-20221130153023608](C:\Users\multicampus\AppData\Roaming\Typora\typora-user-images\image-20221130153023608.png)

- 보통 자식 프로세스가 만들어진 다음 실행하게 됨
- 프로세스 A가 wait() 콜을 호출하면
  - 커널은 child가 종료될 때까지 프로세스 A를 sleep (block)
  - Child process가 종료되면 커널은 프로세스 A를 깨움 (ready)



## exit() 시스템 콜

- 프로세스의 종료
  - 자발적
    - 마지막 statement 수행 후 exit() 시스템콜을 통해 실행
  - 비자발적
    - 부모 프로세스가 자식을 강제로 종료
      - 한계치를 넘어선 자원 요청
      - 더 이상 작업을 수행하지 않아도 될 때
    - 키보드로 kill, break 등 입력
    - 부모가 종료



## 프로세스 간 협력

- 독립적 프로세스
  - 원칙적으로는 독립적
  - 각자의 주소 공간을 가지고, 다른 프로세스의 수행에 영향을 미치지 못함
- 협력 프로세스
  - 협력 메커니즘을 통해 다른 프로세스의 수행에 영향을 미침
- IPC : Interprocess Communication, 프로세스 간 협력 메커니즘
  - message passing : 커널을 통해 메시지 전달
  - shared memory : 주소 공간 공유
    - thread는 주소 공간을 공유하고 있기 때문에 프로세스간 협력은 아니지만, 완전한 협력이 가능함.



## Message Passing

![image-20221130160716607](C:\Users\multicampus\AppData\Roaming\Typora\typora-user-images\image-20221130160716607.png)

- 프로세스 사이에 공유 변수(shared variable)를 일체 사용하지 않고 통신하는 시스템
- Direct communication
  - 통신하려는 프로세스의 이름을 명시적으로 표시

![image-20221130160605180](C:\Users\multicampus\AppData\Roaming\Typora\typora-user-images\image-20221130160605180.png)

- Indirect
  - mailbox (or port)를 통해 메시지를 간접 전달

![image-20221130160644950](C:\Users\multicampus\AppData\Roaming\Typora\typora-user-images\image-20221130160644950.png)

