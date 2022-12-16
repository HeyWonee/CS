# 5강 CPU Scheduling

# CPU Scheduling


- Program은 CPU만 연속적으로 쓰는 단계(CPU Bursts)와, I/O 실행하는 단계(I/O Bursts)가 번갈아 일어남.
- 누구한테 줄 것인가

## CPU-burst Time의 분포


- 여러 종류의 job(process)가 섞여 있기 때문에 CPU 스케줄링이 필요함.
  - interactive job(=I/O Bound job)에 적절한 response 제공
  - CPU와 I/O 장치 등 시스템 자원의 `효율적` 사용
- I/O bound job은 CPU를 짧은 시간, 빈번하게 사용
- CPU bound job은 CPU를 긴 시간, 낮은 빈도로 사용
  - CPU 사용 시간이 길기 때문에 CPU 스케줄링으로 사용시간, 우선순위등을 정해야함.

## 프로세스의 특성 분류

- I/O bound process: CPU를 잡고 계산하는 시간보다 I/O에 많은 시간이 필요한 job
  - many short CPU bursts
- CPU bound process : 계산 위주의 job
  - few very long CPU bursts

## CPU Scheduler & Dispatcher

- CPU Scheduler : Ready 상태의 프로세스 중 이번에 CPU를 줄 Process를 고름.
  - 운영체제 안의 CPU 스케줄링을 하는 Code임.
- Dispatcher : CPU의 제어권을 CPU scheduler에 의해 선택된 프로세스에게 넘김.
  - 운영체제 안의 code
  - 이 과정을 `context swtich`라고 함.

### CPU 스케줄링이 필요한 경우

1. Running → Blocked (I/O 요청 시스템 콜)
   - nonpreempitive
2. Running → Ready (할당 시간 만료로 timer interrupt)
3. Blocked → Ready (I/O 완료 후 인터럽트)
4. Terminate
   - nonpreempitive

`* 1 & 4를 제외하고는 선점형(preemptive, 강제로 뺏음)`

## 스케줄링 알고리즘

### Performance Index(=성능 척도)

- 시스템 입장
  - CPU utilization(이용률)
    - keep the CPU as busy as possible
    - 전체 시간 중 CPU가 놀지 않고 일한 시간의 비율
  - Throughtput(처리량)
    - # of processes that complete their execution per time unit
    - 주어진 시간 동안 몇개의 일을 완료 했는지
- Process 입장
  - Turnaround time(소요시간, 반환시간)
    - amount of time to execute a particular process
    - CPU bursts가 끝날때까지 걸린 시간
  - Waiting time(대기 시간)
    - amount of time a process has been waiting in the ready queue
  - Response Time(응답 시간)
    - amount of time it takes from when a request was submitted unitil the first response is produced, not output
    - ready queue에 들어와서 처음으로 CPU를 얻기까지 걸린 시간
    - time sharing 환경에서 처음 CPU를 얻는 시간이 사용자 응답에 중요한 척도임

### FCFS (First-Come First-Served)

- 선입선출
- 비선점형 스케줄링
- 효율적이지 않음
- 프로세스의 도착 순서에 따라 성능에 큰 영향이 있음.
- Convoy effect : 긴 프로세스가 먼저 도착하여 짧은 프로세스가 오래 기다려야 하는 현상

## SJF (Shortest job first)

- 각 프로세스와 다음번 CPU burst time을 가지고 스케줄링에 활용
- CPU burst time이 가장 짧은 프로세스를 제일 먼저 스케줄
- Two schemes
  - Nonpreemptive
  - Preemptive
    - 현재 수행중인 프로세스와 남은 burst time보다 더 짧은 CPU burst time을 가지는 새로운 프로세스가 도착하면 CPU를 빼앗김
    - Shortest Remaining Time First라고도 부름
- `minimun average waiting time`을 보장 (optimal)
- SJF의 문제점
  - Starvation : CPU사용 시간이 긴 Process는 영원히 기다려야함.
    - aging을 활용하여 해결, as time progresses increase the priority of the process
  - CPU 사용시간을 알 수 없기 때문에 현실에서 적용하기 어려움.
    - 과거의 CPU 사용 흔적을 통해 `추정(estimate)`하여 사용
    - exponential averaging
      다음 예측시간 = 현재 실제 사용 시간 + 현재 예측 사용시간의 적절한 비율

### 우선순위 스케줄링 (Priority)

- 우선순위가 높은 CPU에 할당
- Preemptive, nonpreemptive
  - 우선순위가 높은 것이 들어오면 기존의 것을 뺏느냐 끝날때까지 기다리느냐에 의한 차이
- SJF는 일종의 priority scheduling임.
  - 이때의 우선순위는 predicted next CPU burst time

### Round Robin (RR)

- 각 프로세스는 동일한 크기의 할당 시간(time quantum)을 가짐
- 할당 시간이 지나면 프로세스는 선점되고(preempted) ready queue의 제일 뒤에 가서 다시 줄을 섬.
- 예측할 필요가 없음.
- 응답시간이 빨라짐
- Performance
  - q large ⇒ FCFS
  - q samll ⇒ context switch 오버헤드가 커짐
- SJF 보다 average turnaround time이 길지만 `response time은 더 짧음`

## Multilevel Queue


- Ready Queue를 여러줄로 분할함.
  - `foreground`(interactive)
  - `background`(batch - no human interaction)
- 각 큐는 독립적인 스케줄링 알고리즘을 가짐
  - `foreground` : RR, 사람과 interaction을 하는 것이기 때문에 response time을 더 짧아야 함.
  - `background` : FCFS, 응답시간 보다는 context switch overhead를 줄이기 위함.
- 큐에 대한 스케줄링이 필요
  - Fixed priority scheduling : starvation이 발생할 수 잇음(항상 background 대비 foreground가 우선하는 경우)
  - Time slice : 각 큐에 CPU time을 적절 비율로 할당
    - ex) 80 % to foreground in RR, 20% to background in FCFS
- 골품제같이 계급(우선순위가 나누어져있음)
  - 태어난 출신에 따라 우선순위를 극복하지 못함

## Multilevel Feedback Queue


- 프로세스가 다른 큐로 이동 가능
- 처음 들어오는 Process가 우선순위가 가장 높은(quantum = 8)로 할당 됨.
  그 시간안에 다 처리하지 못하면 강등(quantum = 16) 되고, 또 강등되면 FCFS로 처리가 되는 방식임.
  ⇒ 시간이 짧은 process에 더 높은 우선순위를 부여함.
- 시간을 예측할 필요 없음.

## Multiple-Processor Scheduling

- CPU가 여러 개인 경우 스케줄링은 더욱 복잡해짐
- `Homogeneous processor`
  - Queue에 한줄로 세워서 각 프로세스가 알아서 꺼내가게 할 수 있음.
  - 반드시 특정 프로세서에서 수행되어야 하는 프로세스가 있는 경우에는 문제가 더 복잡해짐 like 미용실?
- `Load sharing`
  - 특정 CPU에 job이 몰리지 않도록 부하를 적절히 공유하는 메커니즘 필요
  - 별개 큐 vs 공동 큐를 사용하는 방법이 있음.
- `Symmetric Multiprocessing (SMP)`
  - 각 프로세서가 각자 알아서 scheduling 결정, 대등함.
- `Asymmetric multiprocessing`
  - 하나의 프로세서가 시스템 데이터의 접근과 공유를 책임지고 나머지 프로세서는 거기에 따름

## Real-Time

- 반드시 deadline을 보장해주어야 함.
- Hard real-time systems
  - 정해진 시간 안에 반드시 끝내야 함.
- Soft real-tiem systems
  - Soft real-time task는 일반 프로세스에 비해 높은 우선순위를 갖도록 해야함.

## Thread

- Local Scheduling
  - User level thread의 경우 사용자 수준의 thread library에 의해 어떤 thread를 스케줄할지 결정
- Global Scheduling
  - Kernel level thread의 경우 일반 프로세스와 마찬가지로 커널의 단기 스케줄러가 어떤 thread를 스케줄할지 결정

## Algorithm Evaluation


- `Queueing models`
  - 확률 분포로 주어지는 arrival rate와 service rate등을 통해 performance index값을 계산(현재는 많이 사용하지 않음, `이론`적으로 많이 사용됨)
- `Implementation (구현) & Measurement (성능 측정)`
  - `실제` 시스템에 알고리즘을 구현하여 실제 작업에 대해서 성능을 측정 비교
- `Simulation (모의 실험)`
  - 알고리즘을 모의 프로개름으로 작성 후 trace를 입력으로 하여 결과 비교
  - 조금 더 쉽게 사용할 수 있는 방법임.
