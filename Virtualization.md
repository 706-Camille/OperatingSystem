# chapter 4 : process  

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

