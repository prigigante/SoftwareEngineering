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

## Lock - 기본 개념  
