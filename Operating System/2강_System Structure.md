# System Structure & Program Execution



## 컴퓨터 시스템 구조

![image-20221123074007471](C:\Users\multicampus\AppData\Roaming\Typora\typora-user-images\image-20221123074007471.png)

- 인풋 : I/O device에서 컴퓨터 내부로 전달
- 아웃풋은 : 컴퓨터에서 I/O device로 전달

- device controller : I/O 내부를 컨트롤 하는것, 각 device를 전담하는 작은 CPU
- local buffer : device controller도 작업 공간을 필요로 함 like 메모리
- CPU안에는 메모리 보다 더 빠른 저장 공간 = register
- mode bit : CPU가 실행하는 것이 ㅣOS일지 사용자 프로그램인지 구분해 주는 것
- interrupt line : CPU는 항상 메모리랑만 일함. 경우에 따라 키보드 입력, 파일등을 읽어 오라는 요청을 controller에게 일을 시킴?
- CPU가 무한루프를 만낫을 때? timer라는 하드웨어를 두고 있어 특정 프로그램이 CPU 독점을 막아줌
- 인스트럭션 실행, 인터럽트 라인 체크, 타이머가 인터럽트 걸면  CPU 제어권이 OS로 넘어감. 오래 걸린놈말고 다른놈한테 넘겨줌. 
- I/O를 접근하는 것은 OS만 가능함. 프로그램이 I/O작업이 필요하면 OS에게 제어권을 넘겨줌



## Mode bit

- 사용자 모드(1) vs 모니터(OS, 0) 모드가 1, 0으로 값이 다름
- 0일 때는 OS가 CPU를 가지고 있을 때 모든 권한이 있음
- 1일 때 제한 사항이 있음 ex) I/O 접근 instruction 불가



## 타이머

- 특정 프로그램이 CPU 독점을 막기 위해서 정해진 시간이 흐른 뒤 OS에게 제어권이 넘어감
- 타이머 값이 0이 되면 interrupt 발생
- 시분할 구현



## device controller

- 해당 device의 I/O를 관리하는 일종의 작은 CPU => HADRWARE
- 제어 정보를 위한 control register, status register를 가짐
- data register인 local buffer를 가지고 있음
- device driver : OS 코드 중 각 장치별 처리 루틴 => SW
  - I/O를 위해 CPU가 OS의 device driver를 실행함



## DMA controller

- 키보드 입력 같이 interrupt가 빈번한 경우 DMA controller가 직접 메모리를 복사하는 일을 해줌. 그 작업이 끝나면 CPU에 interrupt



## I/O 수행

- 모든 입출력 명력은 특권 명령(mode bit = 0)
- 시스템콜
  - 사용자 프로그램이 운영체제의 함수를 호출하여 I/O요청 하는 것
  - 일반 함수 호출과는 다름(일반 함수 호출은 메모리 주소를 단순히 바꿈)
  - 프로그램이 직접 Interrupt trap => mode bit이 0으로 바뀜 => O/S가 올바른 요청인지 확인 후 I/O 수행 => 완료 시 제어권을 시스템콜 다음 명령으로 옮김



## 인터럽트

- 인터럽트 당한 시점의 레지스터와 Program counter를 save 한 후 CPU의 제어를 인터럽트 처리 루틴에 넘김
- 인터럽트 백터 : 해당 인터럽트의 처리 루틴 주소를 가지고 있음
- 인터럽트 처리 루틴 : 해당 인터럽트를 처리하는 커널 함수
- 넓은의미
  - Interrupt (하드웨어 인터럽트)
  - Trap (소프트웨어 인터럽트)
    - Exception : 프로그램이 오류를 범한 경우
    - System call : 프로그램이 커널 함수를 호출하는 경우



다음 프로그램을 실행하기 전 항상 interrupt를 확인함.

interrupt마다 어떤 것을 실행하는지에 대한 정보는 OS에 있음. = 인터럽트 벡터



## 동기식 입출력과 비동기식 입출력

![image-20221123215555931](C:\Users\multicampus\AppData\Roaming\Typora\typora-user-images\image-20221123215555931.png)

- 동기식
  - I/O 요청 후 입출력 작업이 완료된 후에야 제어가 사용자 프로그램에 넘어감
  - 구현 방법 1
    - I/O 끝날 때까지 CPU를 낭비시킴
    - 매시점 하나의 I/O만 일어날 수 있음
  - 2
    - I/O가 완료될 때까지 해당 프로그램에게서 CPU를 빼앗음
    - I/O처리를 기다리는 줄에 그 프로그램을 줄 세움
    - 다른 프로그램에게 cpu를 줌
- 비동기식
  - i/o가 시작된 후 입출력 작업이 끝나기를 기다리지 않고 제어가 사용자 프로그램에 즉시 넘어감
- 두 경우 모두 i/o의 완료는 인터럽트로 알려줌



## DMA (Direct Memory Access)

- 메모리를 접근할 수 있는 장치 (원래는 CPU 뿐)
- 빠른 입출력 장치를 메모리에 가까운 속도로 처리하기 위해 사용
- cpu의 중재 없이 DEVICE CONTROLLER가 DEVICE의 BUFFER STORAGE의 내용을 메모리에 BLOCK 단위로 직접 전송
- 버퍼에 특정 크기에 data에 쌓이면 DMA가 메모리로 카피를 함. 블럭에 해당하는 I/O가 끝낫으면 INTERRUPT를 걸어 CPU에 알려줌(메모리에 카피 다 해놔써)



## 서로 다른 입출력 명령어

![image-20221123220412823](C:\Users\multicampus\AppData\Roaming\Typora\typora-user-images\image-20221123220412823.png)

- 메모리를 접근 하는 instruction과 I/O를 접근하는 instruction이 있음. (좌)
- I/O장치도 메모리에 연장 주소를 붙임. = Memory Mapped I/O



## 저장장치 계층 구조

![image-20221123220602629](C:\Users\multicampus\AppData\Roaming\Typora\typora-user-images\image-20221123220602629.png)

- Main Memory - DRAM
- 위로 갈수록 속도가 빠르고, 단위 공간당 가격이 비싸서 용량이 적음
- 휘발성 매체 or 아니다 DRAM, 캐시, Register = 휘발성
- Executable = bite단위로 접근 가능함.
- 캐싱 : 재사용 목적, 같은것을 2번째 요청할 때는 밑에까지 읽어들일 필요 없음 용량이 작아지기때문에 모든 것을 다 읽지는 못하고 어떻게 메모리 관리할것인지 관리하는 ㅓㄳ이 필요함



## 프로그램의 실행(메모리 load)

![image-20221124035118200](C:\Users\multicampus\AppData\Roaming\Typora\typora-user-images\image-20221124035118200.png)

- 실행 파일 형태로 File system에 저장됨.
- 실행하면 주소 공간이 형성됨(Virtual memory, code, data, heap, stack) ->  메모리에 올라가 Process가 됨
- 독자적인 주소 공간을 물리 메모리에 올려서 저장함. 당장 필요한 부분만 메모리에 올려둠



## 커널 주소 공간의 내용 (운영체제 커널)

- code : 자원을 효율적으로 관리하기 위한 코드, 편리한 서비스 제공을 위한 코드, 시스템콜 및 인터럽트 처리 코드
- data : 운영체제가 사용하는 자료 구조가 있음. 하드웨어 종류 별 자료구조
  - PCB : 프로그램들을 관리하기 위한 자료구조
- stack : 



## 사용자 프로그램이 사용하는 함수

- 코드에 정의 되어 있음
- 사용자 정의 함수
- 라이브러리 함수
- 커널 함수
  - 운영체제 프로그램의 함수
  - 커널 함수의 호출 = 시스템 콜을 통해 인터럽트 라인을 설정 후 CPU 제어권이 커널로 넘어가게 하여 실행함



## 프로그램의 실행

![image-20221124035732774](C:\Users\multicampus\AppData\Roaming\Typora\typora-user-images\image-20221124035732774.png)