# [chapter3] Process
---
## 프로세스의 정의
---
-프로그램 중 실행하는 부분, 즉 실행하고 있는 프로그램
-운영체제가 관리하는 최소단위의 작업
-CPU에 의해서 현재 실행되고 있는 프로그램
-PCB(Process Control Block)를 지닌 프로그램
## 프로세스의 문맥(Process Context)
---
프로세스의 현재 상태를 나타내는데 필요한 모든 요소
시분할 시스템에서 특정 프로세스가 어디까지 실행되었는지 알 수 있게 해줌
- CPU와 관련된 하드웨어 문맥
    -Program Counter가 어떠한 것을 가르키고 있는지
    -각종 Resister가 어떤 값을 갖고 있는지
- 프로세스의 주소 공간
    - code, data, stack
- 프로세스 관련 커널 자료 구조
    - PCB(Process Control Block)
    - Kernel stack
## 프로세스 제어 블록(Process Control Block)
---
- 운영체제가 각 프로세스를 관리하기 위해, 프로세스에 대한 정보를 지니고 있는 테이블
- 운영체제가 프로세스를 표현한 것으로, 운영체제의 커널부분에 있음
**구성 요소**
![SC1](https://user-images.githubusercontent.com/107851434/187787310-c5fb7ac7-540b-43c9-a2de-4a43a76dd953.png)
    - pointer : 현재 프로세스가 위치한 주소
    - process state : suspended, running, ready, blocked 등 프로세스의 상태
    - process number : 프로세스 식별자라고도 하며 프로세스를 구별하기 위해 부여된 고유 번호로 주로 정수값
    - program counter : 다음에 실행할 명령어(프로세스)에 대한 주기억장치상의 주소
    - resisters : 각종 CPU 레지스터 정보
    - 메모리 관련 : 해당 프로세스가 주기억장치의 어느 영역에 위치해 있는지를 저장
    - 파일 관련 : 프로세스 실행을 위해 사용하고 있는 입출력 요구사항이나 입출력 장치, 개방된 파일 목록 등
## 프로세스 상태(Process State)
---
프로세스는 시스템 내에 존재하며 처리되기 위해 여러 가지의 사건을 통하며 상태를 변화시킨 후 종료된다.
- Running
    - ready queue에 있는 프로세스가 프로세서(CPU)를 할당받아 실행되는 상태
    - 프로세스가 완료되기 전 주어진 프로세서 할당시간이 종료되면 Time Interrupt가 발생하여 CPU를 내려 놓고 다시 ready queue로 이동
    - 실행 중인 프로세스가 입출력(I/O)처리가 발생하면 자진해서 CPU를 내려놓고 Blocked(대기상태)로 전환
    - 프로세스 사이의 CPU 전환시 문맥교환 발생
- Ready
    - 프로세스가 CPU를 할당받기 위해 기다리고 있는 상태
    - ready queue에 있는 프로세스는 사실 각각의 우선순위가 있음
    - 프로세스 스케줄러(CPU 스케줄러)에 의해 실행할 프로세스가 선택됨
- Blocked(wait, sleep)
    - 프로세스가 실행 중에 I/O 처리가 발생한 경우, 실행 중인 프로세스는 대기상태로 전환
    - block queue에서는 우선순위 없고, I/O처리가 완료되는 것부터 ready queue로 이동
- New
   - 프로세스가 생성중인 상태
- Terminated
    - 프로세스의 실행이 종료되어 처리기 할당이 해제되는 상태
    - 해당 PCB가 삭제됨

![SC2](https://user-images.githubusercontent.com/107851434/187788064-8c7525bf-0a16-46b7-8511-31e075e85bf1.png)

**프로세스 상태 전이 정의**
|||
| ------------ | ------------- |
| Dispatch | Ready => Running  |
| Time Runout | Running => Ready  |
| Block | Running => Blocked  |
| Wake-up | Blocked => Ready  |


- Dispatch 
## 문맥 교환(Context Switching)
---
- Time Interrupt 또는 I/O 처리, 프로세스 완료 등의 이유로 CPU를 다른 프로세스로 넘겨주는 과정
**문맥 교환의 과정**
    - CPU의 처리를 받고 있는 프로세스의 상태를 그 프로세스의 PCB에 저장
    - CPU를 새롭게 얻는 프로세스의 상태를 PCB에서 읽어옴
## CPU 스케줄링 3단계
---
![SC3](https://user-images.githubusercontent.com/107851434/187790024-ff957f3c-c206-4fbc-a91c-8861e812f031.png)

- Long-Term Scheduler(장기 스케줄러)
    - 시작 프로세스 중 어떤 프로세스를 ready queue로 보낼지 결정
    - degree of Multiprogramming을 제어
    - 시분할 시스템에는 보통 장기 스케줄러 없이 일단 ready queue로 보냄
- Medium-Term Scheduler(중기 스케줄러)
    - CPU를 할당받기 위해 기다리고 있는 프로세스가 많은 경우 프로세스를 디스크로 유예(suspended)시킴
    - 일시적인 부하를 조절하는 버퍼 역할로 일시 유예된 프로세스는 디스크에 저장된 후 다시 메모리로 올라오는데 이것을 Swapping이라고 함
        - Swap Out : 주기억장치에서 보조기억장치로 프로세스 이동
        - Swap In : 보조기억장치에서 주기억장치로 프로세스 이동
- Short-Term Scheduler(단기 스케줄러)
    - 프로세스를 실행하기 위해 CPU를 할당받는 시기를 지정하는 과정
    - MultiProgramming 과 Time Sharing System에서 자주 수행되며 수행속도는 millisecond단위로 아주 빠름

**Degree of Multiprogramming**
- 메모리 상에 올라가 있는 프로세스의 개수
- 너무 적거나, 너무 많으면 CPU효율이 떨어지므로 적절한 프로세스를 유지하는 것이 중요함
## 스레드(Thread)
---
프로세스 내에서 프로세스보다 작은 작업 단위, CPU활용의 기본 단위
![SC7](https://user-images.githubusercontent.com/107851434/187789513-8c45bb5e-4752-4a8b-a4d7-5d484e0f32e5.png)

**스레드의 구조**
- program counter
- register set
- stack space

**프로세스와 스레드 비교**
|프로세스(Process)|스레드(Thread)|
| ------------ | ------------- |
|- CPU가 하나인 시스템에서는 한순간 하나의 프로세스만 실행|- CPU가 하나인 경우에도 하나의 프로세스에서 여러 개의 스레드가 동시에 실행 가능|
|- CPU가 하나인 경우, 프로세스는 순차적으로 실행 |CPU가 하나인 경우라도 스레드는 병행이 가능(사실은 스레드의 빠른 교환)|

**스레드 특징**
- 스레드의 실행과 종료 순서는 예측 불가
- 스레드는 프로그램의 외부에선 알 수 없음
- 프로세스가 종료되면 해당 스레드들도 모두 종료
- 각 스레드는 독립적 스케줄링이 가능

**스레드 장점**
- 응용 프로그램의 응답 시간을 단축(응답성 향상)
- 하나의 프로세스에 스레드를 만들면 이들 스레드는 프로세스가 지닌 자원을 모두 공유(자원의 공유)
- 프로세스 생성과 문맥 교환에 비해 다중 스레드로 처리하는 것이 더 빠름(효율성 향상)
- 다중 프로세서에서 다중 스레드를 사용하면 다중 CPU가 동시에 다중 스레드를 처리할 수 있음(병렬)

**스레드 분류**
- Kernel Threads
    - 운영체제의 커널에 의해 스레드를 운용
    - 구현이 쉬우나 속도가 느림
- User Threads
    - 사용자가 만든 라이브러리를 사용하여 스레드를 운용
    - 운영체제는 스레드가 있는지 모름
    - 속도는 빠르지만 구현이 어려움
# [chapter4] Process Management
---
## 프로세스 생성(Process Creation)
---
- 부모 프로세스의 주소 공간을 복사하여 자식 프로세스가 생성
- 하나의 부모 프로세스는 여러 개의 자식 프로세스를 만들 수 있기에 프로세스는 트리구조 형성
- 원칙적으로는 부모와 자식 프로세스간의 자원 공유는 하지 않음
- 생성된 자식 프로세스는 주어진 공간에 새로운 프로그램을 올려 부모 프로세스와는 다른 일을 하는 프로세스를 만듦
## 프로세스 종료(Process Termination)
---
- 프로세스가 마지막 명령을 수행한 후 운영체제에게 이를 알려줌(exit)
- 부모 프로세스가 자식의 수행을 종료시킴(abort)
    - 자식이 너무 많은 자원을 요구하는 경우
    - 자식에게 할당된 태스크가 더 이상 필요하지 않은 경우
    - 부모가 종료(exit)하는 경우
        - 운영체제는 자식 프로세스를 모두 종료 시키고 부모 프로세스를 종료해야 함 
## 프로세스와 관련된 시스템 콜
---
**fork()**
- 프로세스의 생성과 관련된 시스템 콜
- 부모 프로세스의 context를 그대로 복사
- 부모 프로세스와 자식 프로세스 간의 구분은 리턴값으로 하는데, 부모 프로세스는 양의 리턴값을 가지고 자식 프로세스는 0의 리턴값을 가짐

**exec()**
- 부모 프로세스로부터 받은 공간에 새로운 프로그램을 덮어씌우는 시스템 콜
- fork()를 하지 않아도 사용 가능
- exce() 이후의 코드는 실행되지 않음

**wait()**
- 부모 프로세스가 child process가 종료되기를 기다리며 block상태로 들어가는 시스템 콜
- child process 종료시 커널이 프로세스를 ready로 전환 함

**exit()**
- 프로세스를 종료시키는 시스템 콜
    - 자발적 종료
        - 마지막 명령 수행 후 exit() 시스템 콜을 통해 종료
        - 프로그램에 반드시 명시를 안 하더라도 컴파일러가 알아서 넣어줌
    - 비자발적 종료
        - 자식 프로세스가 한계 이상의 자원 요청 시
        - 자식에게 할당된 태스크가 더 이상 필요하지 않는 경우
        - 부모 프로세스가 종료하는 경우
        - 키보드로 kill, break 등을 친 경우
    
## 프로세스 간 협력
---
원칙적으로 부모 프로세스와 자식 프로세스는 자원을 두고 경쟁하는 독립적인 관계이지만 협력을 하여 나은 결과를 만들 수 있음
**협력 방법**
- message passing : 커널을 통해 메시지를 전달
- ![SC5](https://user-images.githubusercontent.com/107851434/187789892-f326e990-7134-47f9-9e5c-a8df5b39626d.png)

    - Direct Communication : 메시지를 보낼 때 받는 프로세스의 이름을 명시적으로 표시
    - Indirect Communication : 받는 프로세스를 따로 표시하지 않고 mailbox를 통해 간접적으로 전달

- shared memory : 서로 다른 프로세스 간에도 일부 주소 공간을 공유하게 하는 매커니즘
    - 두 프로세스는 상당히 신뢰할 수 있는 관계여야 함

# [chapter5] CPU Scheduling
---
## CPU Scheduling의 존재 이유

- 프로그램이 실행이 되면 CPU burst와 I/O burst를 번갈아 가면서 실행이 됨
- 프로그램에 따라 CPU burst의 빈도와 길이가 다르다. 보통 사람이 개입하는 프로그램은 I/O작업이 interactive하게 실행
**I/O bound job** vs **CPU bound job**
![SC6](https://user-images.githubusercontent.com/107851434/187789731-3e06d759-48e1-4e3d-97fd-b36b24ff3023.png)

I/O bound job : CPU를 짧게 쓰고 중간에 I/O가 끼어드는 작업
CPU bound job : CPU를 많이 쓰는 작업
- 여러 종류의 job이 섞여 있기에 CPU 스케줄링이 필요함
- 사람하고 interactive하는 job에 먼저 CPU를 주는 것이 중요

## CPU Scheduler＆Dispatcher
---
**CPU Scheduler**
- ready queue에 있는 프로세스 중 다음 번에 CPU를 줄 프로세스를 선택하는 것
- 운영체제에 내장된 코드에 의해 작동되는 것임

**Dispatcher**
- CPU 제어권을 CPU Scheduler에 의해 선택된 프로세스에게 넘긴다.

**Preemptive** vs **Nonpreemptive**
Preemptive : 프로세스가 사용 중인 CPU를 강제롤 뺏음(ex: time interrupt에 의한 반납)
Nonpreemptive : 프로세스가 자진해서 CPU를 내려 놓음(ex: 프로세스 종류, I/O명령)
