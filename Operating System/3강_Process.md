## 프로세스의 개념

![image-20221124072356220](C:\Users\multicampus\AppData\Roaming\Typora\typora-user-images\image-20221124072356220.png)

- Process is a program in execution
- 프로세스의 문맥(context) : 프로세스의 현재 상태
  - CPU 수행 상태를 나타내는 하드웨어 문맥
    - Program Counter : 다음에 실행될 code를 가리키고 있음
    - 각종 register
  - 프로세스의 주소 공간
    - code, data, stack, heap
    - 프로세스가 하나 실행될때마다 운영체제의 커널 주소 공간 data에서 PCB, stack에서 커널스택이 생성됨
  - 프로세스 관련 커널 자료 구조
    - PCB(Process Control Block)
    - Karnel stack



## 프로세스의 상태

![image-20221124072934701](C:\Users\multicampus\AppData\Roaming\Typora\typora-user-images\image-20221124072934701.png)

![image-20221124073117462](C:\Users\multicampus\AppData\Roaming\Typora\typora-user-images\image-20221124073117462.png)

- Running : CPU를 잡고 instruction을 수행중인 상태
- Ready : CPU를 기다리는 상태(메모리 등 다른 조건을 모두 만족하고)
- Blocked(wait, sleep) : CPU를 주어도 당장 instructin을 수행할 수 없는 상태
  - Process 자산이 요청한 event(ex:IO)가 즉시 만족되지 않아 이를 기다리는 상태로, 디스크에서 file을 읽어와야 하는 경우등
  - 자신이 요청한 event가 만족되면 ready
- suspended : 중계 스케줄러에 의해 메모리를 빼앗긴 상태를 말함. CPU 뿐 아니라 외부에서 강제로 정지시켜놓은 상태임. `외부에서 resume`해야 active
- New : 프로세스가 생성중인 상태
- Terminated : 수행이 끝난 상태



## Process Control Block

![image-20221124073739531](C:\Users\multicampus\AppData\Roaming\Typora\typora-user-images\image-20221124073739531.png)

- 운영체제가 각 프로세스를 관리하기 위해 프로세스당 유지하는 정보

- 구성 요소

  1. OS가 관리상 사용하는 정보

     - Process state, Process ID

     - scheduling information, priority

  2. CPU 수행 관련 하드웨어 값

     - Program counter, registers

  3. 메모리 관련

     - Code, data, stack의 위치 정보

  4. 파일 관련

     - Open file desciprtors.



## 문맥 교환(Context Switch)

- CPU를 한 프로세스에서 다른 프로세스로 넘겨주는 과정
- CPU가 다른 프로세스에게 넘어갈 때 운영체제는 다음을 수행
  - CPU를 내어주는 프로세스의 상태를 그 프로세스의 PCB에 저장 (PCB는 커널의 Data영역)
  - CPU를 새롭게 얻는 프로세스의 상태를 PCB에서 읽어옴

![image-20221124075013983](C:\Users\multicampus\AppData\Roaming\Typora\typora-user-images\image-20221124075013983.png)

- System call이나 Interrupt 발생시 반드시 context switch가 일어나는 것은 아님
  - Process A -> OS -> Process A는 context switch 가 아님



## 프로세스를 스케줄링하기 위한 큐

- Job Queue : 현재 시스템 내에 있는 모든 프로세스의 집합
- Ready queue : 현재 메모리 내에 있으면서 CPU를 잡아서 실행되기를 기다리는 프로세스의 집합
- Device queues : I/O device의 처리를 기다리는 프로세스의 집합



## 스케줄러

- Long-term(job scheduler)
  - 시작 프로세스 중 어떤 것들을 ready queue로 보낼지 결정
  - 프로세스에 memory(및 각종 자원)을 주는 문제(Process 생성 메모리에 올라가는 것을 허락 받음 admitted)
  - degree of Multiprogramming을 제어
  - 시분할에는 보통 장기 스케줄러가 없음
- Short-term(CPU scheduler)
  - 어떤 프로세스를 다음번에 running 시킬지 결정
  - 프로세스에 CPU를 주는 문제
  - 충분히 빨라야함(millisecond)
- Medium-term(Swapper)
  - 여유 공간 마련을 위해 프로세스를 통째로 메모리에서 디스크로 쫓아냄
  - 프로세스에게서 memory를 뺏는 문제
  - degree of Multiprogramming을 제어
    - 메모리에 process가 너무 많이 올라가 있으면 성능이 떨어짐
  - suspended 상태로 만듦

![image-20221124092031106](C:\Users\multicampus\AppData\Roaming\Typora\typora-user-images\image-20221124092031106.png)

##  

## Thread

![image-20221124092819438](C:\Users\multicampus\AppData\Roaming\Typora\typora-user-images\image-20221124092819438.png)

- Process 내부에 CPU 수행 단위가 여러개 있는 것
- 메모리 주소 공간 중 Code, data, heap 영역을 공유
- PCB에 Program counter를 여러개를 둠 (현재 어느 부분을 실행하고 있는지를 가르킴)
  - process state나 각종 자원들은 공유함
- Thread 구성 : program counter, register set, stack만 별도로 가지고 있음.
- 다중 스레드로 구성되었을 때 하나의 스레드가 blocked 상태일 때 다른 스레드가 실행 됨



## 쓰레드 실행

- Kernel Threads : CPU schedueling 하듯이 넘겨줌
- User Threads : library에 의해 지원됨. 커널은 모르고 유저 스스로 쓰레드를 관리함.
  - 따라서 커널이 볼때는 일반적인 프로세스로 보임. 