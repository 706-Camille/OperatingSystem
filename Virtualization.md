# process  

#### process : 실행중인 프로그램으로 정의한다.  
프로그램은 disk 상에 존재하며 실행을 위한 명령어와 정적 data의 묶음이다. 이 명령어와 데이터 묶음을 읽고 실행하여 프로그램에 생명을 불어넣는 것이 __운영체제__ 이다.  

--- 
  
#### Multiple processes : 하나의 CPU로 여러개의 process를 어떻게 동시에 동작시킨다는 환상을 제공할까?
__Mechanism__ : 필요한 기능을 구현하는 low-level 메서드나 프로토콜  
  
__Policy__ : Mechanism 위에 존재하는 정책으로 어떤 종류의 결정을 내리는 알고리즘(무엇을 할 것인가?)   
  
__시분할 기법(time sharing)__ : time-sharing 메커니즘을 사용하면 CPU 가상화를 할 수 있다. 예를 들어 1개의 CPU를 가진 컴퓨터 사용자가 A, B, C라는 3개의 프로그램을 동시에 실행하고 싶다고 하면 단순하게 하나의 프로그램을 0.001초 즉 1ms씩 수행 후 다른 프로그램을 수행하게 하는 방법이 Time-sharing 방법이다. A를 0.001초 동안 실행한 뒤 B를 0.001초 동안 실행하고 C를 0.001초 동안 실행한 뒤 다시 A를 0.001초만큼 수행하는 방식으로 프로그램을 수행하게 되면 사용자는 마치 프로그램이 동시에 동작하고 있는 것처럼 보이게 된다. 여기서 A가 수행되다 멈추고 B를 수행하게 되는데 만약 다시 A가 수행될 땐 이전에 멈춘 부분부터 수행하게 해주는 메커니즘은 __문맥 교환Context-Switch__ 라고 한다. 
  
__Scheduling__ : CPU 가상화를 위한 policy에는 Scheduling policy가 있다. 시분할기법을 사용할 때, 운영체제는 어떤 process에게 자원을 할당할 것인가? 어떤 process를 먼저 선택해야 하고 어떤 기준으로 선택해야 하는가?. 다양한 Scheduling Policy가 있고 이는 Scheduling 장에서 다루게 된다.  

---  

#### 프로세스 생성 : 자세하게  
- Load
- Dynamic allocation
- Initialization
- Jump to entry point : main()
   
Load 단계에서는 말 그대로 Disk에 있는 프로그램을 DRAM과 같은 메모리에 프로세스로 적재시키는 것. 예전에 만든 OS는 프로그램이 실행되기 전에 이러한 로드 작업을 한 번에 수행했는데 요즘에 만들어지는 OS는 프로그램 실행에 필요한 데이터만 로드한다. 이러한 것은 paging, swapping 방법으로 가능하게 됐으며 이는 나중에 자세히 다루게 된다.
  
code와 static data가 메모리로 탑재된 후, 특정 크기의 메모리 공간이 프로그램에 stack 용도로 할당되고 main() 함수의 인자인 argc와 argv 벡터를 사용하여 스택을 초기화한다.
  
그 다음 heap을 위한 메모리 영역을 할당한다. C에서 동적으로 할당된 데이터를 저장하기 위해 사용된다. malloc(), free()

Initialization 단계에서는 file descritor(STDIN, STDOUT, STDERR) 입출력과 관계된 초기화 작업을 수행한다.  

위의 모든 준비를 마치면, OS는 프로그램을 실행할 준비가 된 것으로 판단하고 main()함수로 분기한다.  

---
#### Process States
-__Running(실행 중)__ : process 가 processer에 의해 실행되고 있는 상태를 말한다.  
  
-__Ready(준비 완료)__ : 프로그램을 생성 전 단계를 모두 완료하고 이제 실행할 준비가 된 상태. 아직 OS의 Scheduling을 받지 못해 실행 되지 않은 상태    
  
-__Blocked(대기)__ : 프로세스가 다른 사건을 기다리는 동안 프로세스의 수행을 중단시키는 연산.  
ex) 프로세스가 disk에 대한 입출력 요청을 하였을 때 프로세스는 입출력이 완료될 때까지 대기 상태가 되고, 다른 process에게 자원을 할당.  

---
#### Process Control Block(process descriptor) : process list의 node

<img width="440" alt="process control block" src="https://github.com/706-Camille/OperatingSystem/assets/123271815/29dcc5cf-7847-4c3c-acb9-ec49ddbc9cea">
  
PCB는 하나의 자료구조로 각각의 프로세스가 하나씩 가지고 있다. PCB에는 프로세스 상태, 프로세스 ID, Program Counter, 메모리 관리 정보, CPU 스케줄링 정보, 등이 저장되어있으며 Context Switch에 사용되는 정보도 저장된다.  

---

# Process API and Limited Direct Execution
#### fork() system call  
__Process 복사__  
- Address Space
- PCB(Process COntrol Block)
- File Descriptor Table
<img width="602" alt="image" src="https://github.com/706-Camille/OperatingSystem/assets/123271815/0993659e-f398-43c1-a474-e0fdc6bd9a02">
--- 

<img width="541" alt="Direct Execution Protocol" src="https://github.com/706-Camille/OperatingSystem/assets/123271815/f90ddd22-d0f4-4c7e-92c4-8495f89ae2d0">  
  
Direct Execution은 프로그램을 한 번 수행하면 종료될 때까지 수행하는 방법.  
하지만 이 방법으로는 지금 구현하려고 하는 CPU 가상화를 구현할 수 없다. 우선 한 번 실행된 프로세스에 대해 제어를 할 수 없으며 Time Sharing을 할 수 없다. 즉 이러한 방법에 어떠한 Limit를 줘야 CPU 가상화를 구현할 수 있게 된다. 

#### __Limited Direct Execition__ (하면 안되는일 (보호되어야 하는 일) 제한하기)
  
CPU의 모드를 분리. User mode, Kernel mode로 상태를 구분하는 것이다. User mode 상태에서는 수행할 수 있는 작업에 제한을 둬서 해당 작업을 수행하려고 하면 오류를 발생시킨다. 이를 보완하기 위해 Kernel mode라는 OS가 실행되는 상태를 만들었고 I/O 요청과 같은 권한이 필요한 작업은 Kernel mode 상태에서 수행하게 된다.  
   
하지만 만약 User mode의 프로세스가 Kernel mode에서 수행 가능한 작업(privileged 명령어)을 원할 땐 어떻게 해야할까? 이럴 때 사용하는게 system call이다. System call은 user mode 프로세스에게 kernel mode에서 수행가능한 작업을 수행할 수 있게 해 준다. System call이 실행되면 trap명령으로 권한을 바꿔 작업을 수행하고 수행 후엔 OS가 return-from-trap 명령으로 다시 원래의 권한으로 돌아온다.  
    
__process Trap (User Mode) -> OS (Kernel Mode) -> Process (User Mode)__    
   
Trap명령이 수행되면 프로세스는 program counter, flags, register 정보를 kernel stack에 넣는다. 그런 뒤 return-from-trap 명령이 수행되면 이러한 데이터를 모두 제거한다. 
  
![06 제한적 Direct Excution_1](https://github.com/706-Camille/OperatingSystem/assets/123271815/8832fbdb-45ba-4716-bc92-1aed39c3897e)

그렇다면 OS는 trap을 어떻게 사용할 수 있을까? OS가 trap을 처리하기 위해선 trap table이라는 것을 사용한다. 이는 부팅할 때 초기화 된다.
Trap Table에는 소프트웨어적 사건들을 처리하기 위한 함수가 들어있다. 각 함수들마다 System-call number 번호가 정의되어있고 각 System call number를 호출하여 trap을 처리할 수 있다.   

![KakaoTalk_20240330_203154839](https://github.com/706-Camille/OperatingSystem/assets/123271815/70203236-ecc0-44eb-9e7e-53be7a20c1eb)  

Interrupt Service Routine (ISR) 트랩 핸들러 위치 ->  Trap, H/W Interrupt, H/W Exception 처리  
Interrupt Stack Pointer (ISP) 커널 스택 위치  
Interrupt (Trap) Vecrtor Pointer 트랩 테이블 사용  


1. Exception -> Exception handler
2. H/W Interrupt -> interrupt timer를 통한 제어 양도
3. S/W TRAP -> Trap handler

---  

# Scheduling

__Wokrload assumption__
그럼 스케줄링 정책을 이해하기 위해서 몇 가지 가정을 정의해 보자. 여기서 가정하는 부분의 내용을 wordload라고 부르며 workload를 많이 알 수록 스케줄링 정책을 잘 설계할 수 있습니다. Workload의 뜻을 다시 짚고 가자면 사전적으로는 작업량이라고 볼 수 있고 컴퓨터 과학의 입장에서 보면 하나의 프로세스의 특징들을 고려해 얼마만큼 자원을 필요로 하느냐를 뜻한다. 지금 정의하는 workload 가정들은 비현실적이지만 하나씩 알아가 보며 이러한 가정들을 제거한다.

- 모든 작업은 한 번 실행될 때 실행시간이 동일하다.  
- 모든 작업은 동시에 도착한다  
- 작업이 시작되면 끝날 때까지 실행한다.  
- 모든 작업은 CPU에서만 작동한다. I/O를 발생시키지 않는다.  
- 모든 작업의 실행시간을 알고 있다.

__스케줄링 정책들을 비교하는 기준__  
  
-Turnaround Time (반환 시간)  
  Turnaround Time = Completion Time - Arrival Time
  반환 시간은 프로세스가 완료된 시간에서 프로세스가 도착한 시간을 뺀 시간이다. 즉 어떤 프로세스가 완료될 때까지 걸린 시간이라고 보면 된다.


-Response Time (응답 시간)  
  Response Time = FirstRun Time - Arrival Time
  응답 시간은 프로세스가 처음 실행되는 시간에서 프로세스가 도착한 시간을 뺀 시간이다. 예를 들어 P1 프로세스가 0초에 도착하여 5초에 
  처음 실행되었다면 응답 시간은 5초가 된다.

## FIFO (First In, First Out)  
- "모든 작업은 동일한 수행 시간을 갖는다" 가정을 없애면? -> 시간이 많이 필요한 작업이 먼저 수행된다면 뒤에 도착하는 빨리 끝날 수 있는 작업들이 수행되지 못하게 된다. (convoy effect)
  
## SJF (Shortest Job First)
- convoy effect를 해결하는 방법으로 수행시간이 짧은 애들부터 수행.
- "모든 작업은 동일한 시간에 도착한다" 가정을 없애면? -> response time이 너무 길다. 어떤 작업이 현재 수행되고 있는 작업보다 짧아도 늦게 도착하면 수행되기 위해 기다려야 한다.  
    
## STCF (Shortest Time-to-Complition First)  
- "작업이 시작되면 끝날 때까지 실행한다." 가정을 없애면? -> timer interrupt와 context switch 기능을 추가. STCF는 preempt(선점) 스케줄러. 선점 스케줄러라는 것은 어떤 작업을 수행하는 도중에 다른 작업을 수행할 수 있도록 스케줄링할 수 있는 스케줄러를 말한다. 지금까지의 스케줄러들은 모두 non-preemptive 스케줄러였기 때문에 아까 SJF와 같은 문제가 발생했다.
  
## RR (Round Robin)
- Response Time을 고려하면 Timer Interval을 짧게 하면 할 수록 Response Time을 줄일 수 있다. -> 효율적? NO
- Context Switching 비용을 고려해서 Timer Interval을 정해야함. 너무 짧게 하면 Overhead가 커진다.

## MFLQ (Multi-Level Feedback Queue)
- Rule 1 : if Priority(A) > Priority(B), A runs (B doesn't)  
- Rule 2 : if Priority(A) = Priority(B), A & B run in Round Robin  
- Rule 3 : When a job enters the system, it is placed at the highest priority (the topmost queue).  
- Rule 4 (기존의것) :  
           - (a) If a job uses its allotment while running, its priority is reduced (i.e, it moves down one queue)  
           - (b) if a job gives up the CPU (for example, by performing an I/O operation) before the allotment is up, it stays at the same priority level(i.e., its allotment is reset) I/O 작업 우선순위 유지, 할당량 리셋

많은 I/OP Job이 존재한다면? -> I/O Job들만 실행 (높은 우선 순위 유지) 서로 주고 받기만 함  
긴 Job (낮은 우선순위 queue)은 CPU를 사용할 수 없는 상태(starvation)가 발생.   

-Rule 5 (새로운 규칙) : After some time period S, move all the jobs in the system to the topmost queue. 오랜 시간 CPU를 사용하지 못했다면, 우선 순위 상승  

Rule 4 process가 스케쥴러를 속인다면? ex) 10초의 time slice를 가진 스케쥴러에서 의도적으로 9.9초마다 CPU를 양도하는 작업이 들어온다면 계속해서 높은 우선순위를 유지할 것. 이를 해결하기 위해 규칙 4a, 4b를 통합하여 새로운 규칙을 정의.  

-Rule 4 : Once a job uses up its time allotment at a given level (regardless fo how many times it has given up the CPU), its priority is reduced (i.e., it moves down one queue) 작업은 모든 우선순위에서 주어진 time slice를 모두 사용하면 우선순위 감소.

## Scheduling: Proportional Share  
proportional share(비례 지분) 스케줄러. fairness(형평성)을 기준으로 스케줄링.  

### Lottery Scheduling
- 무작위 추첨, 원하는 비율을 보장 하지 않음.
- 장시간 실행되면, 목표 비율에 도달 가능성 높아짐 (대수의 법칙)

### Ticket Mechanisms
- Ticket CUrrency : 국제통화, 지역통화
- Ticket transfer : CS서버에서 유용. Client가 서버에게 잠시 자신에게 부여된 티켓을 빌려줌으로써, 해당 작업을 빠르게 마치고 반환.   
- TIcket inflation : 프로세스들이 협력하는 상황에서 사용할 수 있으며 만약 프로세스들이 경쟁 상태라면 의미가 없음. 프로세스들이 서로 협력하는 상황이라고 가정하고 예를 들면 어떤 프로세스가 갑자기 엄청 빠르게 수행되어야 하는 상황이 생긴다. 이 경우 프로세스에게 부여된 티켓 수를 엄청나게 늘려서 CPU를 차지하게 만들 수 있다.

### fairness

A 프로세스 도착시간 0초, 수행 시간 : 10초, 완료 시간 10초 C1 = 10초  
B 프로세스 도착시간 0초, 수행시간 : 10초, 완료시간 20초 C2 = 20초  
fairness = C1 / C2 = 10 / 20 = 0.5  
   
위와 같은 상황이 최악의 상황입니다. A 프로세스가 모두 끝난 뒤에야 B 프로세스가 동작하기 때문이죠. 그럼 가장 좋은 경우를 살펴보겠습니다.  
   
A 프로세스 도착시간 0초, 수행시간 : 10초, 완료시간 19.9999초 C1 = 19.9999초    
B 프로세스 도착시간 0초, 수행시간 : 10초, 완료시간 20초 C2 = 20초    
fairness = C1 / C2 = 19.9999 / 20 = 1    

## Stride Scheduling  
- Deterministic fair-share scheduler
- stride (보폭) 자신의 티켓수의 반비례

## CFS (Linux Completely Fair Scheduler)
- virtual runtime (vruntime) : counting 기반 테크닉 사용  
각 프로세스들은 실행될 때마다 virtual runtime이 누적된다. 스케줄러는 어떤 프로세스를 다음에 실행할지 결정할 때 프로세스들 중 virtual runtime이 가장 작은 프로세스를 선택.
- CFS가 자주 실행되면? -> context switching 비용 증가 -> bad performance
- sched latency (예정된 지연시간)
- MIN_GRANULARITY : time slice가 이 시간 보다 작지 않도록 한다. ( 문맥 교환 최대 비용 결정) 
  
![KakaoTalk_20240331_185712561](https://github.com/706-Camille/OperatingSystem/assets/123271815/cd63d3c5-ecf1-4d7c-8898-83970365e670)

![KakaoTalk_20240331_185937849](https://github.com/706-Camille/OperatingSystem/assets/123271815/9ae3ab17-7ae6-4d7d-878d-d9f2aaafd86b)  

---  

# Address Space

## Multiprogramming and Time Sharing


![image](https://github.com/706-Camille/OperatingSystem/assets/123271815/1cc05937-ff4f-4668-bae7-40c87e5c9f49)

실제로 메모리 가상화를 하면 위의 그림과 같이 여러 개의 프로세스들이 메모리의 일정 부분을 함께 사용하게 된다. 위의 그림과 같이. 그런데 이렇게 여러 프로세스들이 한 번에 메모리에 존재하게 되면 보안 상 문제가 발생할 수 있다. 예를 들어 C 프로세스의 경우 128KB~192KB에 있는 데이터에만 접근해야 하는데 갑자기 B 프로세스의 메모리 공간에 침입해서 데이터를 조작하게 되면 이는 큰 보안상 문제이다. 따라서 이러한 문제를 잘 해결해야 하는 과제가 있습니다. 실제로 이렇게 자신의 메모리 공간 말고 다른 공간에 접근하려고 하면 Segmentation Fault라는 오류가 나타나는 것을 코딩 중에 자주 볼 수 있다.

![KakaoTalk_20240331_192817883](https://github.com/706-Camille/OperatingSystem/assets/123271815/ea515815-e3cb-4a1e-b3a4-d3fdf3bc4838)  

## OS의 GOALS
1. transparency (투명성) : 모든 프로세스는 자신이 모든 물리 메모리를 이용할 수 있어야 한다. OS가 가상화하고 간섭하는 사실을 몰라야 한다.  HW(MMU)의 도움   
2. efficiency (효율성) : 프로세스 전환 비용 최소화, 시간적, 공간적 비용 최소화. HW(TLB)의 도움
3. protection (보호) : 프로세스는 OS를 훼손/변경 혹은 영향을 줄 수 없다.
   프로세스는 다른 프로세스를 훼손/변경 혹은 영향을 줄 수 없다. -> isolate(격리)

---  

# Address Translation (Dynamic Relocation)
LDE, (System call 발생 시, 타이머 인터럽트 발생 시)에는 OS가 프로그램의 실행에 관여하는 방식. 즉 하드웨어의 지원과 OS의 관리로 CPU를 효율적으로 제어하는 가상화 방법.  
LDE에 대한 일반적인 접근 방식에 메모리 가상화를 도입할 수 있는 방법은 Address Translation 이다.  
주소 변환을 통해 하드웨어는 virtual address(가상 주소)를 physical address(실제 주소)로 변환.  

## Assumptions
1. 프로세스는 연속적인 메모리 공간을 사용한다.
2. 프로세스가 필요한 메모리는 항상 실제 메모리보다 작다
3. 모든 프로세스는 같은 크기의 메모리를 사용한다.

## Dynamic (Hardware-based) Relocation
-base, bounds (limit) 레지스터를 활용. (CPU에 존재하는 MMU 하드웨어) 
-실제 메모리 주소 = 가상주소 + base 레지스터
-가상주소의 값이 bounds(limit)보다 크면 protection을 위해 오류 발생.

![KakaoTalk_20240331_202159272](https://github.com/706-Camille/OperatingSystem/assets/123271815/9d6fe41a-d3a7-490d-bdb2-48d1dae51408)  
![KakaoTalk_20240331_202607432](https://github.com/706-Camille/OperatingSystem/assets/123271815/7423fb65-82ec-44b8-9799-1c703c898e00)  

ex)
<img width="494" alt="image" src="https://github.com/706-Camille/OperatingSystem/assets/123271815/7a6121aa-690f-4ba4-9596-62f402cb2dd8">

---

# Segmentation : Generalized Base/Bounds
- 최적화
- 사용하지 않는 공간 최적화
  
![image](https://github.com/706-Camille/OperatingSystem/assets/123271815/f2253c0e-3b84-46f2-be70-0e88a371edf5)

위의 예에서 가상 메모리의 크기는 16KB입니다. 즉 14비트로 표현할 수 있습니다. 그리고 segment는 총 3개입니다. Code는 00, Heap은 01, Stack은 11로 구별할 수 있습니다. 이를 위해 상위 2개 비트로는 segment를 구분하고 하위 12개의 비트로 offset을 계산할 수 있습니다. 실제로 위의 그림에서 가상 주소 1000을 변환했고 동일한 결과가 나오는 것을 볼 수 있습니다.

## Stack

---  

# Paging 
메모리 공간을 관리하는 방법은 두 가지가 있다.
- segmentation : 메모리 크기를 가변적으로 사용 -> external fragmentation 문제
- paging : 가상 주소 공간을 가변 크기가 아닌 고정 크기로 나누어서 메모리에 할당.

고정 크기 단위 = page  
physical memory에서는 page frame  

이점  
- 유연성 : 힙과 스택의 커지는 방향 관계 없음  
- 단순함 : 빈 공간 리스트 -> 빈 페이지 선택

## address translation
  
![가상주소](https://github.com/706-Camille/OperatingSystem/assets/123271815/9fa345bf-4792-4a57-b3f4-c87c79aa6877)

page table : address translation(주소 변환) 정보를 저장하는 것
page table은 프로세스마다 존재하는 구조.

가상주소의 구조 -> VPN(virtual Page Number) and offset 

![address_translation](https://github.com/706-Camille/OperatingSystem/assets/123271815/96c3dfc9-812a-4658-89e3-c256cabe3efa)

변환이 필요한 부분? Offset은 동일하다. Page Size가 모두 동일하기 때문에 변환이 필요 없음  
VPN(virtual page number) -> PFN(page frame number) 변환 필요

Page Table의 크기?    
32bit 주소 공간, 4KB Page Size의 경우  
20 bit VPN + 12 bit Offset (4KB = 12bit)  
페이지의 개수 = 2^20 1,048,575  
Size(Page Table Entry) = 4byte 이라고 할 때,
Size(Page Table) = 4 byte * 2 ^ 20 = 4MB -> 한개의 프로세스를 위한 공간  

![image](https://github.com/706-Camille/OperatingSystem/assets/123271815/4dc79291-be7b-42a9-89e4-3dba708deb02)
Page Table Entry의 구성  
- Valid bit : 유효성
- Present bit : 물리 메모리에 있는지 여부
- Protection bit : Read/Write/Execution 여부 (공유)
- Reference bit : 참조 여부

![image](https://github.com/706-Camille/OperatingSystem/assets/123271815/25455d22-cfa9-4bc6-804d-a1a5799a1128)

~~~
int array[1000];

for(int i = 0; i < 1000; i++) {
    array[i] = 0;
}
~~~
~~~
가상주소 
1024   movl  $0x0, (%edi, %eax, 4) 
1028   incl  %eax
1032   cmpl  $0x03e8, %eax
1036   jne   0x1024
~~~

![image](https://github.com/706-Camille/OperatingSystem/assets/123271815/652376bc-d442-40da-ad81-8c7ab48fb98f)  
위의 그림은 1000번의 반복 중 5번의 반복의 메모리 접근을 나타낸 그림이다. 한 번의 반복이 아까 어셈블리로 나타내면 4줄의 어셈블리 코드로 나타낸다. 이를 통해 우선 4번의 메모리 접근이 발생합니다. 한 번의 반복이 진행될 때 코드 부분의 page에는 4번의 메모리 접근, 1번 Array 메모리에 접근해서 업데이트, 그리고 5번의 page table 접근이 발생한다. 즉 한 번의 반복을 위해서 10번의 메모리 접근이 발생하는 것. 매우 비효율적!

--- 

# TLB ( translation-lookaside buffer ) : adress-translation cache  

페이지 테이블 접근을 위한 메모리 읽기 작업은 엄청난 성능 저하를 유발.  
핵심 질문 : 어떻게 주소 변환 속도를 빠르게 할까. -> 페이징에서 발생하는 추가 메모리 참조를 피해야함.

- TLB 는 MNU의 일부이다.
- virtual memory 참조 시, 하드웨어는 먼저 TLB에 원하는 변환 정보가 있는지 확인. 만약 있다면 page table를 참조 하지 않고 주소 변환 (TLB hit).
- TLB에 원하는 변환 정보가 없다면 (TLB miss), 페이지 테이블에 접근하여 프로세스가 생성한 가상 메모리 참조가 valid하고 접근가능하다면(protection bit check) TLB로 update.
- TLB miss를 줄여야함 -> TLB hit rate 를 높여야함!
- spatiai locality(공간 지역성) : 프로그램이 메모리 주소 x를 읽거나 쓰면, x와 인접한 메모리 주소를 접근할 확률이 높다는 사실에 근거.
- temporal localtity(시간 지역성) : 최근에 접근된 명령어 또는 데이터는 곧 다시 접근될 확률이 높다는 사실에 근거.
- TLB 는 크기가 작을수록 빠르다. 캐시의 크기 <----> 속도 trade off 관계
- fully associative(완전 연관) 방식

### CISC (complex-instruction set computers) : TLB miss 를 하드웨어가 처리. 하드웨어가 페이지 테이블에 대한 명확한 정보를 가지고 있어야함.  
ex) x86 CPU : multi-level page table 사용.  

## RISC (reduced instruction set computing) : software-managed TLB  
TLB miss -> 하드웨어는 exception 발생시킨다. -> 커널모드 -> trap handler 실행 ( 페이지 테이블을 검색하여 변환 정보를 찾고 -> TLB 접근 가능한 특권명령어로 TLB 갱신 후 리턴)  
TLB miss handler를 접근하는 과정에서 TLB miss가 발생할 수 있음. 해법은 두가지
- TLB miss handler의 주소를 '물리' 주소로 표시 unmap 되어 있으므로 TLB miss가 나지 않음.
- TLB의 일부를 핸들러 코드 주소를 저장하는데 영구히 할당하는것. 항상 hit 됨 (wired)  

## 문맥 교환 시 TLB 내용을 어떻게 관리하는가  
문맥 교환 시, 기존 TLB 내용을 지우는 것 (valid bit를 0으로 만드는 것), 비용이 너무 큼. ASID (address space Identifier) 도입. -> process 들이 TLB 공간을 공유할 수 있음.  
valid bit (TLB 내용이 유효한지) , ASID ( 프로세스 별로 TLB 변환 정보를 구분할 수 있다 ), protection bit (r-x 권한)

## cache replacement 정책
LRU (least recently used) : 지역성을 최대한 활용하는 것이 목적. 사용되지 않은지 오래된 항목일수록, 앞으로도 사용될 가능성이 작으며, 교체 대상으로 적합하다는 가정에 근거.

---  

# small tables
___핵심 질문 : page table을 어떻게 더 작게 만들까___   

페이징의 두 번째 문제점은 pagetable의 크기이다. 예를들어 페이지의 크기가 4KB(2^12)이고 페이지 테이블의 각 항목의 크기는 4byte인 32bit 주소 공간을 가정해보자.  
address space에는 대략 백만개(2^32 / 2 ^12)의 가상 페이지가 존재할 것이다. 여기에 page table의 엔트리 크기를 곱하면 4byte * 2 ^ 20 = 4MB가 된다.  
프로세스가 100개라면 400MB이다. 메모리 부담을 해결할 수 있는 방법을 알아보자.  

## 페이지 크기를 더 키운다면?  
페이지 테이블의 크기를 간단하게 줄일 수 있는 방법이 있다. 페이지의 크기를 증가시키는 것이다.  
ex) page의 크기를 16KB로 키운다면, VPN은 18bit offset은 14bit, page table의 크기는 2^18 * 4 byte = 1MB.  
하지만 부작용을 수반한다. 페이지 내부 공간이 증가하면서 internal fragmentation 문제 발생.  

## hybrid 접근 방법 : paging 과 segment
- 두가지 방법을 결합하여 페이지 테이블 크기를 줄이는 방법.

![image](https://github.com/706-Camille/OperatingSystem/assets/123271815/aa8dc6e9-a13c-471a-8bc6-0676c49b6cc9)  
위와 같이 16KB의 가상 주소 공간을 가지며 page 크기가 1KB인 상황을 가정해보자. Code 부분에서 1개의 page, heap 부분에서 1개의 page, stack에서 2개의 페이지를 사용하고 있다. 그리고 각각의 page는 실제 메모리에 할당되어 있습니다. 위의 상태를 보면 현재 page table이 얼마나 비효율적 인지 알 수 있다. 총 16개의 page entry가 존재할 수 있지만 정작 사용하는 것은 4개인데도 불구하고 page table은 모든 공간을 유지하고 있다. 이 상황에서 segmentation 기법에서 사용하는 base, limit 레지스터를 활용하여 메모리 낭비를 줄여보자. 실제 segmentation에서와는 다르게 base, limit 레지스터를 각 segment의 page table에 접근할 수 있도록 사용한다. base 레지스터는 page table을 가리키는 데 사용하고 limit 레지스터는 page table의 끝을 나타낼 수 있다.  

![image](https://github.com/706-Camille/OperatingSystem/assets/123271815/b60f59e1-49aa-4f3b-b379-b01eab957dfd)

page의 크기가 4KB, 32bit 가상 주소공간을 갖는 시스템에서 4개의 segment로 분할된 주소 공간  
SN (세그멘트 비트) 2bit, VPN, offset으로 구성  
드문드문 사용되는 (sparsely used) 힙의 경우에는 페이지 테이블의 낭비를 면치 못할 수가 있다. 또 외부 단편화를 유발한다.  

## Multi Level Page Table  

