# 프로세스의 병렬성: 스레드 - Lock
## 개요
동시성 도입부에서 근본 문제점은 다음과 같았다.  
명령어를 원자적으로 처리하고싶은데,   

단일 프로세스 환경에서 인터럽트 발생(혹은 멀티 프로세서에서 여러 스레드가 동시 실행)  
등등의 이유로 그것이 불가능하다는 것.  

이러한 문제를 해결하기 위해 "Lock"이라는 개념을 다룸.  

> 프로그래머는 Critical Section을 Lock으로 감싸고, 이를 통해 해당 Section이 원자적 명령어 처럼 실행되도록 보장 가능.

## 목차
Locks
1. Locks: The Basic Idea
2. Pthread Locks 
3. Building A Lock 
4. Evaluating Locks 
5. Controlling Interrupts 
6. A Failed Attempt: Just Using Loads/Stores 
7. Building Working Spin Locks with Test-And-Set 
8. Evaluating Spin Locks
9. Compare-And-Swap
10. Load-Linked and Store-Conditional 
11. Fetch-And-Add 
12. Too Much Spinning: What Now?
13. A Simple Approach: Just Yield, Baby
14. Using Queues: Sleeping Instead Of Spinning
15. Different OS, Different Support
16. Two-Phase Locks
17. Summary

## 1. Lock - 기본 개념  
락은 잠금이 가능한 사용가능 상태이거나 (어느 스레드도 락을 점유하지 않음)  
혹은, 획득된 상태입니다.  (정확히 하나의 스레드가 락을 점유하고 있고, 아마도 임계구역 안에 있음)  

데이터 타입 내에 다른 정보들, 예를 들어 어떤 스레드가 잠금을 보유하고 있는지에 대한 정보나 잠금 획득 순서를 정하기 위한 큐 등을 함께 저장할 수도 있지만, 이러한 정보는 잠금을 사용하는 사용자에게는 드러나지 않습니다.

락 언락은 간단.  
락 루틴을 호출하면 -> 락 획득 시도 -> 아무도 없음 -> 획득(소유가자 된다)    
이후  
다른 스레드가 락 루틴 호출 -> 락 획득 시도 -> 존재함 -> 해당 호출 반환 안됨(락 해제 전까지)  

=> 다른놈들 임계구역 접근 차단.  

락은 프로그래머에게 아주 약간의 스케쥴링 통제권을 제공함.  
일반적으로 스레드는 프로그래머가 생성, 다만 스케쥴링은 OS 좆대로.  
이떄 락은 이런 통제권일부를 되돌려줌.  
=> 프로그래머가 해당 코드 영역에 락을 걸어서, 단 하나의 스레드만 해당 구역에서 활성화 될수 있도록  보장(강제)할 수 있음.  
=> 카오스같은 OS의 스케쥴링을 좀더 동제 가능한 형태로 바꿔줌.  (아주 조금임)  


## 2. Pthread Locks
우리가 "mutex"라고 부르는게 뭐냐?  
=> "mutual exclusion"; 상호배제임  
스레드간 상호배제를 제공함.    
```
pthread_mutex_t lock = PTHREAD_MUTEX_INITIALIZER;

Pthread_mutex_lock(&lock); // wrapper; exits on failure 
balance = balance + 1;
Pthread_mutex_unlock(&lock);

// 래퍼클래스임을 명심, 언제나 
```

락 걸어라 ㅇㅇ  

POSIX버전에서는 락 및 해제를 "락"인자를 주잖음?  
=> 서로 다른 lock을 사용 가능하기 때문,  

임계구역에 접근할 때, 단 하나의 잠금을 쓰면
> coarse-grained locking

임계구역에, 서로다른 데이터나 자료구조를 각각 다른 잠금으로 보호  
=> 많은 스레드가 서로 다른 잠금 영역 내에서 코드를 실행.  
> find-grained locking


## 3. Building A Lock 
락 생성.  
락이 어캐 동작하는지 프로그래머의 관점으로는 암.  
근데  
락이 
- 어떤 HW 지원?
- 어떤 OS 지원
- 등 우리가 어캐 Lock을 구성할까?

어떻게 코스트가 적은 효율적인 락을 구성할까?  
그리고 다른 추가적인 달성 목표들을 만족하는 락을 구성할까?  

하드웨어 도움 + 운영체제가 어떻게 관여해 락을 구현하는지 알아 볼 것.  

## 4.  락 평가
락구현 이전에, 우리 목표가 뭔지 알아야함. 띠리서, 락 구현에 대해 어떻게 효용성을 평가할 것인지 질문해야함.  
락이 굴러가는지 혹은 잘 굴러간는지 평가하기 위해서는, 몇가지 기준을 세워야함. 

1) 락의 목적인 상호배제를 제공하는지
락이 제대로 동작해 동시다발적인 임계 구역 진입을 막는가?

2) 공정함
락을 얻기 위한 경쟁이, 각 스레드에게 락이 해제시 공평하게 기회가 주어지는
=> 극단적으로, 락 획득 경쟁에 의해 어느 스레드도 락을 못얻는 경우가 존재함?

3) 성능
두가지 질문
1. 경쟁 없는 환경에서 혼자 스레드가 락을 잡았다 풀면 어떤 오버헤드가?
2. 하나의 CPU내에서 멀티플-스레드들이 lock을 위한 경쟁을 하는 경우
   - 멀티플 CPU가 관여하고, 각 CPU들에서 스레드들이 lock을 위해 경쟁하는경우

## 5. 인터럽트 제어











