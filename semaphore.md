## Semaphore
#### 다양한 범주의 병행성 문제 해결을 위해서는 락(lock)과 조건 변수(conditional variable)가 모두 필요하다
#### semaphore는 락과 컨디션 변수로 모두 사용할 수 있다.
#### semaphore는 초기값에 의해 동작이 결정된다. 사용하기 전 "제일 먼저" 값을 초기화 해야 한다. 

#### semaphore 초기화
```
sem_t s;   
sem_init(&s, 0, 1);    // 두 번째 인자는 0이다. 같은 프로세스 내의 쓰레드 간에 세마포어를 공유한다는 것을 의미한다.
```

#### semaphore : sem_wait() sem_post()의 정의
```
int sem_wait(sem_t *s) {
  decrement the value of semaphore s by one;
  wait if value of semaphore s is negative;
}
int sem_post(sem_t *s) {
  increment the value of semaphore s by one;
  if there are one or more threads waiting, wake one;
}
```
### 이진 semaphore (lock)
```
sem_t m;
sem_init(&m, 0, X); // X로 세마포어를 초기화하기. 이때 X가 가져야 할 값은?
sem_wait(&m);
// critical section 여기에 배치
sem_post(&m);
```
초기값은 1이 되어야 한다. 쓰레드가 두 개인 경우를 가정해보자
![KakaoTalk_20240602_032300010](https://github.com/706-Camille/OperatingSystem/assets/123271815/c518a096-8efa-4ee9-bbb8-7409e23c2ab6)
![KakaoTalk_20240602_032322190](https://github.com/706-Camille/OperatingSystem/assets/123271815/cf64c6fa-b490-45b9-8a5b-8d0d911057db)   
-----------------------------------------------------------------------------
### conditional variable로서의 semaphore
![KakaoTalk_20240602_032757943](https://github.com/706-Camille/OperatingSystem/assets/123271815/fce5745c-a9c3-4da0-ac44-73bb3df44d81)

![KakaoTalk_20240602_032821105](https://github.com/706-Camille/OperatingSystem/assets/123271815/50313ac9-235a-4e78-9dab-f41180199256)

![KakaoTalk_20240602_032821105_01](https://github.com/706-Camille/OperatingSystem/assets/123271815/6d59f75e-2a1b-45e2-a184-2dc79434781b)

### 생산자/소비자 (유한 버퍼) 문제
```
int buffer[MAX];
int fill = 0;
int use = 0;

void put(int value) {
  buffer[fill] = value;      //  f1 라인
  fill = (fill + 1) % MAX;   //  f2 라인
}
int get() {
  int tmp = buffer[use];     //  g1 라인
  use = (use + 1) % MAX;     //  g2 라인
  return tmp;
}
```
