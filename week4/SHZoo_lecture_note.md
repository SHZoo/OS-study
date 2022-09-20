# [chapter6] Process Synchronization

## Synchronization과 관련된 문제들
- Synchronization과 관련된 고전적인 문제를 살펴 봄

**Bounded-Buffer Problem**
- Producer-Consumer Problem(생산자-소비자 문제)라고 불리기도 함
  - Producer : 공유 버퍼에 데이터를 만들어 집어넣음
  - Consumer : 공유 버퍼에 들어 있는 데이터를 꺼냄
- 발생할 수 있는 문제
  - 용량 문제 : 버퍼의 크기는 유한해서 버퍼 공간 크기 이상의 데이터를 저장할 수 없고 소비자는 버퍼가 비어있다면 가져올 수 없음
  - 동기화 문제 : Producer와 Consumer는 여러 명일 수 있기 때문에 다음과 같은 문제가 생길 수 있음
    - 버퍼의 특정 공간에 2명의 Producer가 동시에 데이터를 집어넣으려는 경우
    - 버퍼의 특정 공간의 데이터를 2명의 소비자가 동시에 가져가려는 경우
- 해결 방법
  - mutex(0과 1을 가지는 변수) 변수를 통해 버퍼에 lock을 걸어 한 사람만 접근할 수 있게 함
    - Producer는 버퍼에 Empty가 있으면 mutex를 통해 lock을 걸고 데이터를 집어넣고 다시 lock을 풀고 버퍼가 차있다는 것을 알리는 full을 증가시킨다.
    - Consumer는 버퍼에 Full이 있으면 mutex를 통해 lock을 걸고 버퍼의 데이터를 가져가고 다시 lock풀고 버퍼가 비어있다는 것을 알리는 Empty를 증가시킨다,

**Readers-Writers Problem**
- DB(공유 데이터)에 접근하여 데이터를 읽으려는 Reader와 데이터를 쓰려고 하는 Writer 두 가지 프로세스가 있음
- Read는 DB에 Reader가 있어도 동시에 들어가서 읽을 수 있지만, Write는 반드시 한 번에 한 사람만 DB에 접근해야 함
- 발생할 수 있는 문제
  - 동기화 문제 : Reader와 Writer가 동시에 DB에 접근할 수 있음
- 해결 방법
- db에 lock을 걸어 read가 일어날 땐 write가 접근할 수 없게 만들고, write가 접근할 땐 read가 접근할 수 없게 만듦
  - 아무도 없는 db에 최초로 read를 하러 들어갈 때 readcount변수를 1로 만들어주어 write가 접근할 수 없게 lock을 걺.
  - readcount가 1인 상태에서 다른 reader가 들어온다면 별도로 lock을 걸 필요 없이 바로 접근 가능
  - readcount는 공유변수이므로 모든 reader들이 변경 가능하고, readcount의 올바른 연산을 위해 mutex라는 변수를 두어 제대로 값이 바뀔 수 있게 함
  - read가 끝나면 readcount를 0으로 만들어 lock을 해제 대기 중이 write가 있다면 db에 접근할 수 있음
- Starvation(기아 문제)
  - DB에 Reader가 너무 많이 도착해서 Write가 실행이 되지 못하고 무한 대기에  빠질 수 있음
    - 큐에 우선순위를 부여해서 일정 Reader 이후로는 Write가 DB에 접근할 수 있게 하여 해결할 수 있음 

**Dining-Philosophers Problem**
- 전형적인 동기화 문제로 문제 상황은 5명의 철학자가 원탁에 앉아서 식사를하는 문제
- 할 수 있는 일
  - 밥 먹기 : 철학자의 왼쪽과 오른쪽에 있는 젓가를 집어 식사를 함
  - 생각하기 : 젓가락을 내려 놓고 생각을 함
- 발생할 수 있는 문제
  - Deadlock(교착 상태) : 모든 철학자들이 동시에 자신의 왼쪽 젓가락을 잡는다면, 모든 철학자들은 오른쪽 젓가락이 사용 가능해질 때까지 기다리고 있음 -> 누구도 식사를 하지 못하는 상황 발생 
- 해결 방법
 - 4명의 철학자만이 테이블에 동시에 앉을 수 있도록 함
 - 짝수(홀수) 철학자는 왼쪽(오른쪽) 젓가락부터 잡도록 함
 - 젓가락 두 개 모두 잡을 수 있을 때에만 젓가락을 잡을 수 있게 함
 - 철학자가 젓가락을 잡고 일정 시간 내에 반대편 젓가락을 잡지 못하는 경우 젓가락을 다시 내려놓게 함 -> 간단하지만 일정 시간을 기다려야 하는 딜레이 발생

## Monitor
- Semaphore의 문제점
  - 한 번의 실수가 시스템에 치명적인 영향 가능
    - P연산을 하고 V대신 다시 P연산을 하면 Deadlock이 걸려 어느 누구도 자원을 획득할 수 없음
    - V연산을 하고 P연산을 하는 실수를 하면 critical Section에 동시에 들어가는 Mutual exclusion 원칙에 위배 
  - 프로그래머가 코딩을 하기에 어려움
  - 정확성의 입증이 어려움
  - 자발적 협력(voluntary cooperation)이 필요

**Monitor**
- 프로그래밍 언어 차원에서 동시접근문제 해결
- 프로그래머가 따로 lock을 걸 필요 없음
- Condition Variable
  - x.wait():
    - 프로세스가 조건이 충족되지 않아 기다려야 하면 x라는 줄에 가서 잠 들게하는 용도로 사용
  - x.signal():
    - 프로세스 실행 후 잠 자고 있는 프로세스가 있으면 깨워주는 용도

# [chapter7] Deadlock

## Deadlock(교착상태)
- 둘 이상의 프로세스가 자신이 확보하고 있는 자원(HW/SW)을 지니고 있는 상태에서, 상대방이 가지고 있는 자원을 요구하면서, 양쪽 모두 작업을 수행하지 못하고 무한정 기다리는 현상
- 프로세스가 자원을 사용하는 절차
  - Request(자원을 요청), Allocate(자원을 할당), Use(자원을 사용), Release(자원을 반납)

## Deadlock 발생 조건
- Mutual Exclusion(상호배제 조건) 
  - 프로세스가 자원을 배타적으로 점유하고 있어서 다른 프로세스들이 해당 자원을 동시에 사용할 수 없음
- Non-Preemption(비선점 조건)
  - 프로세스가 자원을 점유하고 있는 동안에 스스로 내려 놓지 않는 이상 뺏을 수 없음
- Hold and Waint(점유와 대기 조건)
  - 프로세스가 어떤 자원을 점유하고 있는 상태에서 다른 자원을 기다림 
- Circular Wait(순환 조건)
  - 점유와 대기를 하는 프로세스들의 관계가 사이클을 이루고 있음
  - 그래프에 사이클이 없으면 deadlock이 아님
  - 그래프에 사이클이 있으면
    - 자원의 인스턴스가 1개일 때 -> deadlock
    - 자원의 인스턴스가 2개 이상일 때 -> deadlock의 가능성이 존재

## Deadlock 처리 방법
- Deadlock Prevention
- Deadlock Avoidance
- Deadlock Detection and Recovery
- Deadlock Ignorance
  - 시스템은 deadlock을 책임지고 않고 사용자가 종료하거나 처리하도록 함
  - 현대의 운영체제는 Deadlock Ignorance 채택
  - Deadlock은 자주 발생하지 않기에 별도로 관리하고 처리하는 Overhead를 줄일 수 있음

## Deadlock Prevention (교착상태 예방)
- 교착상태가 발생하지 않도록 사전에 시스템을 제어하는 방법으로, 교착상태 발생 필요조건 중에서 어느 하나를 제거
- 임의적인 자원 할당과 대기로 Utilization 저하, Throughput 감소, Starvation 문제
**Mutual Exclusion**
- 여러 프로세스가 동시에 공유 자원을 사용할 수 있으면 deadlock을 예방 가능 그러나, 공유자원 중 어느 한 시점에 하나의 프로세스만 사용해야하는 것이 있으므로 현실적으로 적용 힘듦 

**Non-preemption**
- 자원을 점유하고 있는 프로세스가, 다른 프로세스가 점유하고 있는 자원을 요구할 때, 자신이 점유하고 있는 자원을 반납하고 기다리게 함
  - 문제점 - 뺏으면 엉망이 되는 프로세스에서 사용하면 Overhead가 큼

**Hold and Wait**
- 프로세스가 실행되기 전에 필요한 모든 자원을 할당하거나, 모든 자원을 사용할 수 있을 때만 자원 요구
  - 문제점 - 자원의 공유가 어렵고 낭비 발생, 끝내 필요한 자원을 할당 받지 못하는 프로세스 발생

**Circular Wait**
- 모든 자원 유형에 할당 순서를 정하여 정해진 순서대로만 자원 할당
  - 문제점 - 예상하지 못한 순서의 자원요구 발생 시 실행이 어렵다
  - 새로운 자원 추가되면 자원의 순서번호를 재구성해야 하여, 프로그램 수정이 필요
  - 긴급한 작업처리는 자원할당에 어려움 발생

## Deadlock Avoidance (교착상태 회피)

**은행원 알고리즘(Banker's Algorithm)**
![markdown2](https://user-images.githubusercontent.com/107851434/191195920-a8b97c3e-bf89-42ad-8060-0b093422e702.png)
- 자원당 인스턴스가 여러 개일 때 사용
- 사전에 자신이 필요한 자원의 최대량(Max) 파악. 이때 시스템에 존재하는 자원수 초과 X
- 프로세스가 자원을 요구하면, 시스템이 자원을 주더라도 Deadlock이 발생하지 않을 거라고 판단하면(즉 Safe)라면 할당하고, 그렇지 못하면 자원이 있어도 주지 않음
- Worst Case(최악의 경우)를 상정하고 대비하는 방법
- safe state를 유지할 수 있는 sequence가 존재한다면 <ex) P_1, P_3, P_4, P_2, P_0> Deadlock 없이 모든 프로세스 실행 가능
- 한계점
  - 자원이 남아 있는데도 Deadlock이 발생을 우려하여 할당하지 않기에 비효율적임
  - 현대의 운영체제는 자원을 동적으로 처리하기 때문에 최대 요구량을 파악하기 어려움

## Deadlock Detection (교착상태 탐지)

**자원 할당 그래프 알고리즘(Resource Allocation Graph Algorithm)**
![markdown](https://user-images.githubusercontent.com/107851434/191195502-3042a68e-6708-4e6f-a0c6-a2a5b6bd1905.png)
- 프로세스가 잠재적으로 사이클을 형성할 가능성이 있는지(Unsafe), 없는지(Safe) 판단하는 알고리즘
  - 자원당 인스턴스가 하나일 때 이용
  - 사이클을 찾는 시간 복잡도는 O(N^2)
    - 그래프에서 간선의 개수는 N(N-1)임(방향 그래프일 때만, 무방향 그래프에서는 N(N-1)/2) 
- 자원당 인스턴스가 여러 개면 테이블을 그려서 Deadlock 계산

## Deadlock Recovery (교착상태 회복)

- 교착상태가 발생한 것을 탐지하여, 발생시킨 프로세스 강제 종료시킴
**Process Termination(프로세스 종료 방식)** 
  - Deadlock에 연관된 프로세스를 전부 종료시키는 방식
  - Deadlock을 발생시킨 프로세스 중 하나를 종료하고 나머지 프로세스의 진행을 확인하는 방식

**Resource Preemption(자원 선점 방식)**
  - Deadlock에 있는 프로세스가 지닌 자원을 뺏어서(선점하여) 다른 프로세스에 할당하고, 해당 프로세스를 일시 정지시키는 방식
  - 우선순위가 낮은 프로세스, 실행할 정도가 적은 프로세스, 사용 자원이 적은 프로세스 등의 자원을 선점
  - 고려사항
    - 특정 프로세스가 실행되지 못하는 Starvation이 발생할 수 있음 
      - Cost factor에 rollback 횟수도 같이 고려하여 방지

## Deadlock Ignorance (교착상태 무시)

- Deadlock 발생 여부에 상관 없이 아무런 조치를 취하지 않음
- Deadlock을 처리하는 방법 자체가 Overhead가 큼
- 사용자가 Deadlock을 감지하고 종료시키도록 함