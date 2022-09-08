# [chapter5] CPU Scheduling

## CPU Scheduling의 정의와 필요성
- CPU 스케줄러에 의해 CPU를 할당받을 프로세스를 결정하는 과정
    - CPU burst time이 긴 작업과 짧은 작업, I/O가 빈번히 걸리는 작업과 그렇지 않은 작업 등 프로세스의 종류는 동일한 패턴이 아니므로 CPU 스케줄링이 필요함
    - ready queue에 있는 프로세스 중에서 어떤 프로세스에게 CPU를 할당할 것인지에 따라 시스템의 효율이 달라짐

**CPU 사용 형태에 따른 Job**
- I/O bound time : CPU는 적게 사용되고 사용자와 상호작용하며 I/O에 많은 시간이 걸리는 작업
- CPU bound time : 주로 과학 분야의 계산 등 CPU를 주로 사용하는 작업

## 스케줄링 알고리즘

**스케줄링의 종류**
- Preemptive(선점) Scheduling
  - CPU를 가지고 사용중인 프로세스가 우선순위가 더 높은 프로세스가 들어오면 CPU 사용권을 돌려주는 방식
  - 시분할 시스템 등의 빠른 처리를 요구하는 시스템에 적합
  - 프로세스 변경에 따른 문맥 교환이 발생하여 오버헤드가 발생
- Nonpreemptive(비선점) Scheduling
  - 일단 프로세스가 CPU를 차지하면 자신의 턴이 종료되기 전까지 CPU 사용권을 보장 받음

**스케줄링 성능척도**
- System 관점
  - CPU Utilization(CPU 이용률) : 전체 시간 중에 CPU가 사용된 시간. 높을 수록 좋음
  - Throughput(처리율) : 단위 시간 내에 몇 개의 프로세스를 처리하는지
- Program 관점
  - Turn Around Time(반환시간) : 하나의 일을 마무리하는 데 걸리는 시간
  - Waiting Time(대기시간) : 프로세스가 ready queue에서 CPU를 기다리는 시간
  - Response Time(응답시간) : ready queue에 들어와서 최초로 CPU를 받기까지 소요된 시간

**FCFS(First Come First Served)**
- 프로세스들이 ready queue에 들어온 순서에 따라 CPU를 할당하는 기법
- 직관적이고 간단함
- 공평하지만 시스템 전체의 효율은 고려하지 않음(대기시간이 김)
- 작업 완료시간 예측이 쉬움
- 대화식 방식으로는 적합하지 않음

**SJF(Shortest Job First)**
- 선점 VS 비선점
  - 선점 SJF : 실행 중인 프로세스의 실행시간 보다 더 짧은 프로세스가 ready queue에 오면 CPU빼앗김
  - 비선점 SJF : 실행 중인 프로세스의 실행시간 보다 더 짧은 프로세스가 ready queue에 오더라도 CPU사용권 보장
- ready queue에 있는 프로세스 중에서, 실행시간이 가장 짧은 프로세스가 먼저 실행하는 방식
- 평균 대기시간이 가장 짧아 전체는 행복한 결과
- 실행시간이 짧은 프로세스가 우선순위를 높게 가지므로 일종의 Priority Scheduling임
- 문제점
  - Starvation(무한연기) : 실행시간이 긴 프로세스는 ready queue에 갖혀서 CPU를 하염없이 기다리는 무한연기 상태가 발생할 수 있음
  - CPU 실행시간을 미리 알기 어려움

** Priority Scheduling**
- 선점 VS 비선점
  - 선점 Priority Scheduling : 실행 중인 프로세스보다 우선순위가 높은 프로세스가 ready queue에 들어오면 CPU빼앗김
  - 비선점 Priority Scheduling : 실행 중인 프로세스보다 우선순위가 높은 프로세스가 ready queue에 들어오더라도 CPU사용권 보장
- 프로세스의 중요도에 따라 우선순위를 결정하고, 이에 따라 CPU를 할당하는 방식
- 우선순위가 동일하면 먼저 들어온 프로세스부터 처리
- 우선순위의 종류
  - 시스템에 의해 결정
    - 시간제한(Deadline), 기억장치 요구, 개방된 파일 수, 평균 I/O 실행시간 등에 의해 결정
  - 사용자에 의해 결정
    - 업무별, 작업 실행 정책 등에 따라 결정
- Aging(에이징)
  - 가장 낮은 우선순위를 부여받은 프로세스가 Starvationg(무한연기)에 빠질 수 있는데 이때 우선순위를 한 단계씩 높여주는 것으로 무한연기에서 빠져나올 수 있도록 하는 방법

**Round Robin**
- 선점형 스케줄링
- 각 프로세스는 동일한 크기의 할당시간(Time Slice, Quantum)을 가지고 이 동안에 완료되지 못하면 다음 프로세스에게 CPU 할당을 넘기고, 자신은 ready queue의 가장 뒤로 가서 기다림
- 시분할 시스템을 위해 만들어진 방식으로 비선점 방식인 FCFS 방식을 선점 방식으로 바꾼 것
- Response Time(응답시간)에서 유리
- Quantum 길이의 문제
  - 너무 길면? -> FCFS와 같아짐
  - 너무 짧으면? -> 문맥교환이 빈번해 오버헤드 발생이 증가
- 10~100ms가 적당
- 프로세스 실행시간을 알지 못할 때 유리

**Multi-Level Queue**
- ready queue를 여러 개를 두어 유형별로 프로세스를 큐에 넣는 방식
- queue들은 독립적인 스케줄링 알고리즘을 가짐
  - foreground(대화식 프로세스)
  - background(일괄처리 프로세스, 사람개입X)
높은 우선순위를 가지는 queue에 CPU를 먼저 주고, 우선순위에 따라 queue를 처리함

**Multi-Level Feedback Queue**
- 어느 프로세스가 ready queue에 한 번 배정되면 다른 ready queue로 이동할 수 없는 기존 다단계 큐에서 벗어나 ready queue간 이동이 가능하도록 개선한 방법
- 일단 프로세스가 들어오면 우선순위가 높은 큐에 배정 후 Time Slice를 할당함 그리고 주어진 시간 내에 처리되지 못 하면 우선순위가 낮은 ready queue로 강등당함
- 우선순위가 높은 ready queue는 짧은 Time Slice를 할당받고, 반대로 우선순위 낮은 ready queue는 더 많은 Time Slice를 할당 받음

** Multi-Processor Scheduling**
- CPU가 하나가 아닌 여러 개일 때의 스케줄링은 더욱 복잡해짐
- 프로세스들의 실행시간이 동일한 경우
  - queue에 한줄로 세워서 각 프로세서(CPU)가 알아서 꺼내가게 할 수 있음
  - 그러나 특정 프로세서에서 처리되어야만 하는 프로세스가 존재할 경우 복잡
- Load sharing(부하 분담)
  - 특정 CPU에 너무 많은 Job이 몰리지 않도록해야 함
  - 별개의 큐를 두는 방법 VS 공도 큐 사용하는 방법
- Symmetric Multiprocessing(SMP) VS ASymmetric Multiprocessing(ASMP)
  - Symmetric Multiprocessing(SMP) : 각 프로세서가 독립적으로 스케줄링 결정
  - ASymmetric Multiprocessing(ASMP) : 프로세서 하나가 다른 프로세서들의 스케줄링을 결정

**Real-Time Scheduling**
- Hard Real-Time Systems
  - Deadline 안에 반드시 끝내도록 스케줄링해야 함. Deadline이 지나서 받는 서비스는 의미 없음
- Soft Real-Time Systems
  - Deadline을 꼭 지켜야할 필요는 없고 일반 프로세스에 비해 높은 우선순위를 갖도록 함

**Thread Scheduling**
  - Local Scheduling
    - 사용자 레벨 스레드의 경우 OS가 멀티스레드인지를 모르기에 사용자 수준의 thread library에 의해 어떤 스레드를 스케줄할지 결정
  - Global Scheduling
    - 프로세스 스케줄링과 마찬가지로 커널(OS)이 스레드를 스케줄 함
  
## 알고리즘 평가 방법(Algorithim Evaluation)
- Queueing models(대기모델)
  - 수리적 모델을 이용하여 성능 척도 계산
  - 확률 분포로 주어지는 arrival rate과 service rate 등을 통해 각종 성능 지표 계산
- Impllmentation and Measurement
  - 실제로 시스템에 알고리즘을 구현하여 기존과 비교하는 방식으로 성능 측정
- Simulation
  - 실제 프로그램의 CPU burst를 기반으로 얻은 Input Data를 넣어 모의 실험을 함
  - 사람이 손으로 하기 힘든 계산을 프로그램이 대신 해주기에 유용하고, 강력함

# [chapter6] Process Synchronization

## Process Synchronization 정의와 필요성
- 작업을 진행하기 위한 프로세스들 사이의 통신
  - 여러 개의 프로세스가 협력하여 실행하기 위해서는 프로세스들 간에 데이터를 주고받는 통신이 필요함 
  - 여러 프로세스가 동일한 데이터를 가지고 작업하는 경우, 어느 프로세스가 먼저 실행할지 그리고 언제 끝날지도 알려 주어야함
  - 공유 데이터에 접근하는 프로세스가 딱 하나라면 동기화는 필요 없지만 현실은 많은 프로세스가 접근할 수 있고 또 접근함

**example**

| 연산-box   | 저장-Box       |
|----------|--------------|
| (1)CPU   | Memory       |
| (2)컴퓨터내부 | 디스크          |
| (3)프로세스  | 그 프로세스의 주소공간 |

**Race Condition(경합 조건)**
- 두 개 이상의 프로세스가 공통 자원을 동시에 읽거나 쓸 때, 공유 데이터에 대한 접근이 어떤 순서에 따라 이루어졌는지에 따라 그 실행 결과가 같지 않고 달라지는 상황
  - OS에서의 race condition
    - Interrupt handler VS Kernel
      - 커널모드 running 중 interrupt가 발생하여 interrupt 처리 루틴 수행 -> 양쪽 다 커널 코드와 커널 주소 공간을 공유하게 됨(충돌)
      - 커널모드에서 하던 일을 끝낼 때까지 interrupt를 받아들이지 않고 연기하여 해결
    - 커널 모드 수행 중 Time Slice가 끝나 CPU 빼앗김
      - 커널 모드에서 수행 중일 때는 CPU를 빼앗지 않고 커널 모드에서 사용자 모드로 돌아갈 때 CPU 빼앗음
    - MultiProcessor
      - 한 번에 한 CPU만 커널에 진입하도록 함
      - 공유 데이터 접근 시 접근하려는 데이터에 Lock을 걸어 다른 CPU가 접근 못하도록 함

## 임계구역(Critical Section)
- 다중 프로그래밍 시스템에서 여러 개의 프로세스가 공유하는 자원이나 데이터에 대하여, 어느 한 시점에 하나의 프로세스만이 자원이나 데이터를 사용하도록 지정된 공유 자원 또는 영역
  - 임계구역에는 하나의 프로세스만 접근
  - 임계구역에서 프로세스가 나오면 다른 프로세스가 들어갈 수 있음
  - 임계구역 내에서는 처리를 신속하게 진행해야 함
  - 프로세스가 임계구역 접근을 요청하면, 일정 시간 내에 사용을 허락해야 함

## Mutual Exclusion
  - Mutual Exclusion(상호배제 조건)
    - 두 개 이상의 프로세스가 동시에 임계구역에 있어서는 안 됨
  - Progress(진행 조건)
    - 임계구역 내에 프로세스가 없을 때, 이때 임계구역으로 들어가려는 프로세스가 있으면 들어가게 해줘야 함
  - Bounded Waiting(한계대기 조건)
    - 임계구역으로 들어가려는 프로세스를 무한연기 시켜서는 안 됨

## 임계구역 해결 방법 

**Peterson's Algorithm**
- 두 개의 공유 변수인 flag와 turn 사용
- 상대방이 들어가려는 의사가 없으면 turn에 관계없이 내가 들어갈 수 있음
- 상대방이 들어가려는 의사가 있으면 turn변수가 늦게 수행된 프로세스가 기회 양보
- 문제점
  - Busy Waiting(=spin lock)
    - CPU와 memory를 while문에서 계속 소모하면서 wait함

**Test and Set 방법**
- 하드웨어적 방법 중 하나로 값을 읽는 작업과 setting 하는 작업을 automic하게 하는 방법

**Semaphores**
- 각 프로세스에 제어 신호를 전달하여 순서대로 작업을 수행하는 기법
- 세마포 변수는 P연산(세마포 변수 획득하는 과정)과 S연산(다 쓰고 반납하는 과정)이 있음
- P연산은 Lock을 거는 과정, S연산은 Lock을 푸는 과정
- Busy Waiting 발생하지만 해결 가능(Block and WakeUp 방식을 통하여)
  - Block/Wakeup
    - 세마포 변수를 획득 못한 친구들은 세마포를 기다리며 Block상태로 들어감
    - V연산 시 단순히 반납 하는 것이 아니라 세마포 변수를 기다리는 친구가 있어면 깨워줘야 함
    - 세마포 변수를 반납했는데 S값이 0이하면 기다리는 친구를 깨워야 함
- Busy Wait VS Block and WakeUp
  - 일반적으로 Block and WakeUp이 좋음
  - Critical Section이 짧은 경우 -> Busy Wait이 유리함
  - Critical Section이 긴 경우 -> Block and WakeUp이 유리함

**DeadLock and Starvation**
- 서로가 하나씩 자원을 가진 상황이고, 두 가지 자원을 얻은 후 반납한다면 상대방이 자원이 내려 놓을 때까지 영원히 기다림
- 해결법은 To be Announced