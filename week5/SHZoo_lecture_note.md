# [chapter8] Memory Management

## Logical vs Physical Address
- 메모리에는 주소가 매겨지는데 주소는 논리적인 주소와 물리적인 주소로 나뉨
  - 논리적인 주소(Logical Address) : 가상주소, 프로그램마다 가지는 주소이고 CPU가 보는 주소임
  - 물리적인 주소(Physical Address) : 실제 메모리상에 올라가는 위치
- 주소 바인딩 : 주소를 결정하는 것
  - Symbolic Address -> Logical Address -> Physical Address
  - 프로그래머는 숫자로 된 주소를 사용하지 않고 Symbolic 주소를 사용함. 이후 컴파일이 되면 논리적인 주소로, 실행이 되려면 물리적인 주소로 변환됨

## 주소 바인딩
- Compile time binding 
  - 컴파일 시점에 이미 물리적인 주소가 결정
  - 주소 바꾸려면 컴파일 다시해야 함
  - 비효율적이라 현재는 사용 안 함
- Load time binding
  - 파일을 실행 시키면 논리 주소를 물리 주소로 변환
  - 실행 시 빈 주소에 배치
  - 컴파일러가 재배치가능코드(relocatable code)를 생성한 경우 가능
- Execution(run) time binding
  - 실행되는 도중에 경우에 따라 주소가 바뀜
  - 하드웨어적인(MMU) 지원 필요
  - 현재 사용되는 방법

**Memory-Management Unit(MMU)**
- 논리 주소를 물리 주소로 매핑해 주는 H/W 장치
- MMU scheme
  - 사용자 프로세스가 CPU에서 수행되며 생성해내는 모든 주소값에 대해 base register(=relocation register)의 값을 더함
- user program
  - 논리 주소만을 다룸
  - 실제 물리 주소를 볼 수 없고 알 필요도 없음

<img width="512" src="https://user-images.githubusercontent.com/107851434/193463107-d74bbc79-7052-4e83-b86f-667f8ee5e325.png">

- 기본적으로 레지스터 두 개를 통해 주소변환
  - base resister : 논리 주소에 시작 위치를 더하여 물리적 주소로 변환함 
  - limit resister : 프로그램의 크기를 담고 있음. 다른 프로그램 메모리 주소 침범을 방지하고 침범 시 Trap(일종의 S/W 인터럽트)을 일으켜 CPU제어권 OS에게 넘김

## some Technology(용어 설명)
**Dynamic Loading**
- 프로세스 전체를 메모리에 미리 다 올리는 것이 아니라 해당 루틴이 불려질 때 메모리에 올림
- 운영체제(OS)가 따로 해주는 게 아니라 프로그래머가 라이브러리를 사용하여 직접 함
- 가끔씩 사용되는 많은 양의 코드일 경우 유용

**Overlays**
- 메모리보다 큰 프로그램의 실행을 위해, 하나의 프로그램을 여러 개의 조각으로 분할하여 처리하는 방식
- 하나의 프로그램이라도 동시에 모든 기능이 실행되어야 하는 게 아닌, 일정한 동안에는 프로그램의 일부분만 실행하는 구역성을 이용한 방식
- OS의 지원없이 사용자의 수작업에 의해 구현, 프로그래밍이 복잡

**Swapping**
- 프로그램을 주기억장치에서 처리하다가 다른 프로그램의 실행이 필요한 경우 보조기억장치로(backing store)로 보냄
- Backing store(= swap area)
  - 디스크, 보조기억장치 
- Swap in : 보조기억장치에 있는 프로그램이 주기억장치로 이동
- Swap out : 주기억장치에 있는 프로그램이 보조기억장치로 이동
- 누구를 보낼 것인가?
  - 우선순위(priority)가 낮은 프로세스를 보조기억장치로 보냄
  - 우선순위가 높은 프로세스를 메모리에 올려 놓음
- 효율적인 swapping을 하려면 execution(run) time binding 지원되어야 함(나갔다 들어왔다 할 때 마다 주소변환하여 빈 메모리 영역 아무 곳에나 올릴 수 있음)

**Dynamic Linking**
- Linking을 실행 시간(execution time)까지 미루는 기법
- Static linking
  - 실행 파일 안에 라이브러리가 포함되어 있음
  - 각 프로세스 마다 동일한 라이브러리 포함하므로 메모리 낭비
- Dynamic linking 
  - 컴파일에서 실행파일로 전환될 때 내 코드 안에 라이브러리는 없고 별도의 파일에 존재
    - 라이브러리를 찾을 수 있는 포인터가 존재하여 그 루틴의 주소로 감 
    - OS의 도움이 필요
  
~~Dynamic linking은 솔직히 이해 안 됨...~~

## Allocation of Physical Memory
- OS 상주 영역
  - 낮은 주소 영역은 OS 커널이 사용 
- 사용자 프로그램 영역 
  - 높은 주소 부분은 사용자 프로그램이 사용 

**사용자 프로그램 영역 할당 방법**
- 연속할당
  - 각각의 프로세스가 메모리의 연속적인 공간에 적재되도록 하는 것 
    - 고정 분할 : 사용자 프로그램이 들어갈 공간을 미리 나눔
    - 가변 분할 : 프로그램이 실행될 때마다 순서대로 메모리에 차곡차곡 올림
- 불연속할당
  - 프로그램을 특정한 단위로 나누어 메모리에 올림 
    - 페이징(Paging) 기법 : 프로그램을 구성하는 주소 공간을 같은 크기의 page로 자름. 메모리도 page frame으로 자름
    - 세그먼트(Segment) 기법 : 주소 공간을 의미 있는 크기로 자름. 페이징과 달리 크기가 균일하다는 보장 없음

## Contiguous Allocation

<img width="512" src="https://user-images.githubusercontent.com/107851434/193463186-e1d1e314-c5ed-44f7-960c-f733820a4fe2.png">

- 고정분할(Fixed partition) 방식
  - 물리적 메모리를 몇 개의 영구적 분할 함
  - 분할 크기가 모두 동일한 방식과 서로 다른 방식 존재
  - 융통성이 부족하고 내부 조각과 외부 조각 발생
- 가변분할(Variable partition) 방식
  - 프로그램의 크기를 고려해서 할당
  - 외부 조각만 발생함

**내부 조각과 외부 조각**
- 내부 조각(Internal fragmentation) : 프로그램이 할당 후 남는 분할 공간
- 외부 조각(External fragmentation) : 프로그램의 크기가 분할 보다 커서 사용이 안 됨

**Hole**
- 빈(or 가용) 메모리 공간 
- 다양한 크기의 hole들이 메모리 여러 곳에 흩어져 있고, 프로세스가 도착하면 수용가능한 hole을 할당
- **Dynamic Storage-Allocation Problem**
  - '어느 분할 영역에 작업을 할당할 것인가'에 대한 전략
    - First-fit(최초 적합 전략)
      - 메모리에서 사용 가능한 hole을 검색하여, 적재할 수 있는 공간 중 첫번째로 찾아낸 곳에 할당하는 방식
      - 시간에서 유리, 공간에서 불리
    - Best-fit(최적 적합 전략)
      - 사용 가능한 공간 중에서 가장 작은 것을 선택
      - 할당된 후 매우 작은 공간만 남을 수 있음
      - 시간에서 불리, 공간에서 유리
    - Worst-fit(최악 적합 전략)
      - 사용 가능한 공간들 중에서 가장 큰 것을 선택하는 방식 
      - 시간에서 불리, 공간에서 불리
  
**Compaction(압축 기법)**
- 외부 조각을 해결하는 한 가지 방법
- 메모리에 분산된 조각들을 하나의 큰 사용 가능한 공간으로 만드는 작업
- overhead가 크고 어려움
- 압축하는 동안에는 다른 처리를 할 수 없음

## Noncontiguous Allocation
- Paging(페이징 기법)
  - 프로세스의 가상 메모리(Virtual memory)를 **동일한 사이즈**의 page로 나눔 
  - 가상 메모리의 내용이 page단위로 불연속적으로 저장됨
  - physical memory 역시 동일한 크기의 frame으로 나눔
  - logical memory를 동일 크기의 page로 나눔
  - 일반적인 page 크기는 1~4 KByte
  - 외부 조각은 발생하지 않지만 외부 조각은 발생 가능

<img width="512" src="https://user-images.githubusercontent.com/107851434/193463236-6ad84709-ea36-47b3-b521-31806811642a.png">

**Page Table**
- page가 메모리 어디에 올라가 있는지 말해줌
- 페이지 개수만큼 entry 존재
- page table은 용량이 커서 메모리에 상주
- Page-table base register(PTRB) : page table을 가리킴
- Page-table length register(PTLB) : table 크기를 보관
- page를 메모리에 올릴려면 메모리에 두 번 접근해야 함 -> 속도 향상을 위해 별도의 H/W인 TLB(일종의 캐쉬) 사용

<img width="512" src="https://user-images.githubusercontent.com/107851434/193463258-6771173e-55f1-457e-8960-f58cc6073799.png">

- CPU가 논리적인 주소를 주면 TLB를 체크
  - TLB에 주소정보가 저장되어 있음 -> 바로 변환
  - TLB에 주소정보가 없음 -> 메모리 두 번 접근
- TLB는 빈번히 참조되는 극히 일부 Page의 주소정보를 담고 있음
- Effective Access Time(실제 접근 시간)
  - = 2 + ε - α
    - ε : TLB에 접근하는 시간
    - α : TLB로부터 주소변환하는 비율
    - α가 커질 수록 효율적임

## Two-Level Page Table
- 시간을 희생하여 공간적 이득을 얻기 위해 이단계 Page Table 사용
- 32bit 체제에서 주소 공간은 약 4GB
  - page의 사이즈가 4KByte라고 하면 1M개의 entry가 필요함
  - entry가 4B시 프로세스당 4M의 page table 필요 -> 대부분의 프로그램은 4G 주소 공간의 극히 일부만 사용하므로 공간 낭비가 심함

<img width="512" src="https://user-images.githubusercontent.com/107851434/193463277-64fdd0c2-2506-4fcd-be0e-219f3bb57dab.png">

- page table 자체를 page로 만듦
  - 사용되는 영역만 inner page table을 만들고 사용되지 않는 주소공간은 outer page table의 엔트리값을 null을 설정
  - 사용되는 영역만 메모리에 올리고 사용되지 않으면 메모리에 안 올리는 메커니즘
  - inner page table의 크기는 page의 크기와 같음
- page 하나의 크기가 4KByte(2^12 Byte)
  - page안에서 byte단위로 구분하려면 12bit가 필요함
  - 20bit를 안쪽, 바깥쪽 어떻게 나눌까?
    - 안 쪽 페이지 테이블이 테이블화 되어 들어감 -> 안 쪽 테이블 안에 엔트리는 1K개 있음 -> 구분위해 10bit 필요
    - 바깥쪽 페이지 테이블을 위한 bit는 10bit 필요

**Multi Paging and Performance**
- 주소 공간이 더 커지면 다단계 페이지 테이블 필요
- 페이지 테이블 여러번 접근해야 하기에 시간에서 손해이지만 대부분의 주소변환은 TLB를 사용하여 한 번의 메모리 접근으로 주소 변환이 가능하기에 실제로는 시간 상에서 이익

**Memory Protection**
- 페이지 테이블의 각 entry마다 부가적인 bit를 둠
  - valid-invalid bit
    - valid : 논리 주소가 실제로 메모리에 올라가 있음
    - invalid : 실제로 사용되지 않는 논리 주소이거나 swap area에 있음
  - Protection bit
    - 연산에 대한 접근 권한 표시(read/write/read-only)

**Inverted Page Table**
<img width="512" src="https://user-images.githubusercontent.com/107851434/193463300-e6d5738b-b7ca-4905-9bae-1711094b38cd.png">

- 시스템 안에 페이지 테이블에 하나만 존재
- 페이지 테이블의 엔트리가 프로세스의 페이지 개수만큼 존재하는 게 아니라 물리적인 메모리의 페이지 프레임의 개수만큼 존재
- 공간상의 이점, 시간상 overhead(page 전체를 탐색해야 함)
- 논리적인 페이지 번혼 뿐만 아니라 프로세스 id까지 같이 저장해야 중복되지 않음
- associative register를 사용하여 병렬적으로 탐색하여 시간을 줄일 수 있음

**Shared Page**
- Shared code는 별도로 올리는게 아니라 한 카피만 물리적 메모리에 올려서 공간상 이점을 살림
- 제약 조건
  - 페이지들은 read-only로 설정하여 프로세스 간에 하나의 code만 메모리에 올림 
  - 동일한 논리 주소에 위치하여야 함(동일한 물리적 주소를 갖는 건 당연)

## Segmentation
- 페이징 기법과 비슷하나 프로그램을 의미있는 단위로 분할
- Segment table
  - Segment-table base register(STBR) : 물리적 메모리에서의 segment table 위치
  - Segment-table length register(STLR) : 프로그램에서 segment 수
- table 엔트리에 segment의 시작 위치와 더불어 segment의 길이도 가지고 있음
- 프로그램이 사용하는 segment 수가 STLR보다 크면 trap 발생
- 외부 조각 발생 가능
- Segmentation 단독으로 사용하는 경우는 거의 없고, paging와 함께 사용함
- 의미 단위로 작동하기에 공유(Sharing)과 보안(Protection)에 있어 paging보다 훨씬 효과적
- table을 위한 메모리 공간의 낭비가 심한 것은 segmenation이 아니라 page가 더 큼
  - 실제 분할되는 segment는 몇 개 안 돼서 공간 낭비가 상대적으로 적음 

## Paged Segmentation

<img width="512" src="https://user-images.githubusercontent.com/107851434/193463337-39e5ef8a-cd4e-4c28-b089-e4057892c79b.png">

- segment 하나가 여러 페이지로 구성
  - 메모에 올라갈 때는 page단위로 올라가 Allcation 문제가 안 생김 
  - 의미단위로 해야하는 것은 segment table 차원에서 함
- segment table entry가 segment의 base address(기준 주소)를 지니지 않고, page table의 base address 지님

## 나오며
- [Chapter8] Memory Management는 특히 물리적 메모리 관리에 관한 내용임.
- 주소 변환을 주로 다뤘는데 이때 운영체제의 역할은 없고 H/W가 담당함
  - 프로세스가 CPU를 가지고 있으면서 메모리에 접근하는 것은 운영체제에 도움을 받지 않음
  - I/O 장치에 접근할 때만 OS에 접근