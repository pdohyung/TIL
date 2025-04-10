### GC(Garbage Collection)란 ?

가비지 컬렉션은 Java의 메모리 관리 방법의 하나로, JVM의 Heap 영역에서 동적으로 할당된 메모리 중 쓸모없는 메모리 객체를 주기적으로 모아 제거하는 프로세스다.

- 한정된 메모리 자원 관리를 대신 해주기 때문에 메모리를 효율적으로 사용할 수 있고, 개발자는 개발에만 집중할 수 있다.
- 하지만 메모리를 언제 해제할지 정확하게 제어하기 힘들고, GC가 동작하는 동안에는 다른 동작을 멈추기 때문에 오버헤드가 발생한다.

> **Stop-The-World**  
> GC가 동작하는 동안, GC 관련 Thread를 제외한 모든 Thread는 멈추게 되어 서비스 이용에 차질이 생긴다.  
> 이에 따라 GC가 자주 실행되면 성능 하락의 요인이 된다.  
> 특히 실시간성 서비스에는 신중히 고려해야 한다.  
> 따라서 서비스의 사용성을 유지하면서 효율적으로 GC를 실행하고, 오버헤드를 최소화하는 것이 중요하다.

### GC의 대상

GC는 특정 객체가 garbage인지 판단하기 위해 도달 능력 개념을 사용한다.

> **Reachable:** 객체가 참조되고 있는 상태  
> **Unreachable:** 객체가 참조되고 있지 않은 상태 **(GC의 대상)**

JVM에서 객체들은 Heap 영역에서 생성되고 Method Area, Stack Area에서는 생성된 객체의 주소를 참조한다.  
따라서 참조되지 않은 상태의 객체들만 GC의 대상이 된다.

### GC의 청소 방식

GC는 Mark-Sweep이란 알고리즘을 사용하여 객체를 청소한다.

> **Mark 과정:** Root Space로부터 그래프 순회를 통해 연결된 객체들의 참조 상태를 마킹한다.  
> **Sweep 과정:** Unreachable 상태의 객체들을 Heap 영역에서 제거한다.  
> **Compact 과정:** Sweep 후에 분산된 객체들을 Heap 영역의 시작 지점으로 모아, 메모리가 할당된 부분과 할당되지 않은 부분으로 압축한다.

Root Space: GC에서 Root Space는 Heap 영역 메모리를 참조하는 **Method Area, Stack Area, Static 변수** 등을 말한다.

### GC의 동작 과정

Heap 영역은 다음 내용을 전제로 설계되었다.

1. 대부분의 객체는 금방 접근 불가능한 상태(Unreachable)가 된다.
2. 오래된 객체가 새로운 객체를 참조하는 것은 아주 적게 존재한다.

정리하자면, 객체는 대부분 일회성이며 메모리에 오랫동안 남아있는 경우는 드물다는 것이다.  
그래서 JVM은 효율적인 메모리 관리를 위해, 객체의 생존 시간에 따라 물리적으로 Heap 영역을 나누게 되었다.

- Young 영역 (Young Generation)
    - 새롭게 생성된 객체가 할당되는 지역이다.
    - 대부분의 객체가 금방 Unreachable 상태가 되기 때문에, 많은 객체가 생성되었다가 사라진다.
    - Young 영역에 대한 GC를 Minor GC라고 부른다.

- Old 영역 (Old Generation)
    - Young 영역에서 Reachable 상태를 유지하여 살아남은 객체가 복사되는 영역이다.
    - Young 영역보다 크게 할당되며, 수명이 긴 객체들 위주라서 garbage는 적게 발생한다.
    - Young 영역에서 age 임계값에 다다른 객체들이 Old 영역으로 Promotion 된다.
    - Old 영역에 대한 GC를 Major GC라고 부른다.

Heap 영역은 더 효율적인 GC를 위해 Young 3가지 영역으로 나눈다.

- Eden
    - new를 통해 생성된 객체가 위치한다.
    - 정기적인 쓰레기 수집 후 살아남은 객체들은 Survivor 영역으로 보낸다.
- Survivor 1 / Survivor 2
    - 최소 1번의 GC에서 살아남은 객체가 존재하는 영역
    - Survivor 영역 중 하나는 반드시 비어있어야 한다.

- Minor GC 과정
    1. 처음 생성된 객체는 Eden 영역에 위치
    2. Eden 영역이 가득 차면 Minor GC를 실행
    3. Mark 동작을 통해 Reachable 객체 탐색
    4. 살아남은 객체는 Survivor 영역으로 이동, age값 + 1
    5. Unreachable 객체는 메모리를 해제 (Sweep)

- Major GC 과정
    1. Young 영역에서 많은 Promotion으로 인해 Old 영역의 메모리가 부족해지면, Major GC가 발생
    2. Old 영역의 모든 객체를 검사하여 참조되지 않은 객체를 모두 삭제
    3. Young 영역에 비해 큰 공간을 가지고 있어서 많은 시간을 소요

Major GC가 일어나면 Thread가 멈추고, 큰 영역에서 Mark-Sweep을 수행하기 때문에 Stop-The-World 문제가 발생한다.  
그래서 JVM의 GC 알고리즘은 끊임없이 발전해 왔다.

### GC 알고리즘 종류

- Serial GC
    - Single Thread로 처리하기에 Stop-The-World 시간이 가장 길다.
    - Minor GC에서 Mark-Sweep을 사용하고, Major GC에서 Mark-Sweep-Compact를 사용한다.
- Parallel GC
    - Java 8의 Default GC
    - Serial GC와 기본 알고리즘은 같지만, Minor GC를 Multi Thread로 수행한다.
    - Serial GC에 비해, Stop-The-World 시간이 감소
- G1 GC
    - Java 9+ 버전의 Default GC
    - 기존의 GC 알고리즘은 Heap 영역을 Young / Old 영역으로 나눠서 사용했지만, G1 GC는 Region이라는 개념을 도입했다.
    - Heap 영역을 체스같이 분할하여 Eden, Survivor, Old 등 역할을 고정이 아닌 동적으로 부여한다.
    - garbage가 가득 찬 영역을 인식하고 빠르게 회수하여 공간을 확보하므로, 결국 GC 빈도가 줄어드는 효과를 얻는다.
