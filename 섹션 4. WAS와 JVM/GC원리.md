# Java 8 JVM Heap 영역 및 메모리 관리

## 1. JVM Heap 영역

![image](https://github.com/user-attachments/assets/fc73404d-417b-4392-85e8-f50a35bab2b1)

JVM의 Heap 영역은 애플리케이션 실행 중 동적으로 생성되는 객체를 저장하는 공간이며, JVM 구동 시 파라미터로 크기를 설정할 수 있다. 이 영역은 유한한 자원이므로, 적절한 메모리 관리가 중요하다.

### 주요 개념

- Heap 구조
    - **Eden 영역**: 객체가 처음 생성되는 공간
    - **Survivor 영역**: Eden에서 살아남은 객체들이 이동하며, Survivor 1과 Survivor 2를 번갈아 사용
    - **Old Generation**: Survivor 영역에서 오래 살아남은 객체가 이동. 이 영역의 메모리 소모량이 많아지면 Full GC가 발생
- Garbage Collection(GC)
    - **Minor GC**: Eden 및 Survivor 영역을 대상으로 수행. 보통 1초 이내 완료
    - **Major(Full) GC**: Old Generation을 포함한 전체 Heap을 대상으로 수행. 수초 이상 소요될 수 있으며, 애플리케이션 지연(stop-the-world)을 유발

### 장애 상황

- Full GC 또는 Out of Memory(OOM) 발생 시 JVM 자체가 다운될 수 있음
- Old Generation에 객체가 과도하게 쌓이면 Full GC 빈도가 증가하여 성능 저하 가능

### 실무 팁

- Old Generation까지 살아남는 객체를 최소화하도록 설계

## 2. Java 8 이후의 Metaspace
![image](https://github.com/user-attachments/assets/fd855457-feb2-4f32-9dca-b61e3b408d75)


Java 8부터 기존의 Permanent Generation이 제거되고 **Metaspace**라는 새로운 메모리 영역이 도입되었다.

### Metaspace 특징

- **저장 내용**:
    - 클래스 메타데이터(로드된 클래스, 메서드 등)를 저장
    - 새로운 객체가 생성될 때, 해당 객체의 메타정보(주소값 등)가 Metaspace에 저장됨
- **위치**:
    - Java Heap이 아닌 Native 메모리 영역에서 동작
    - Native 메모리, 즉 CPU와 RAM 하드웨어 수준에서 직접 활용하여 성능 및 속도 최적화가 이루어진 것으로 추정됨

### 활용 사례

- Spring 프레임워크에서 Reflection을 통해 `.class`를 동적으로 로드할 때 사용
- TPS(Transaction Per Second)에 따라 메모리 소모량이 달라지므로 적절한 설정 필요

## 3. JVM Garbage Collector (GC)

GC는 Heap 영역에서 참조되지 않는 객체를 제거하여 메모리를 회수하는 역할을 한다.

### GC 과정

1. **GC 대상 선정**
    - 참조되지 않는(Unreachable) 객체를 대상으로 수행
    - GC Roots를 기준으로 참조 여부를 판단
2. **GC Roots**
    - GC 프로세스의 시작점으로, 참조 관계를 추적하여 Reachable 객체와 Unreachable 객체를 구분
    - 즉, GC Roots가 사라진다는 것은 이를 참조하는 의존관계가 없어진다는 것
    - 주요 종류:
        - Stack 데이터: 로컬 변수 및 매개변수
        - Static 데이터: 클래스의 static 필드
        - JNI(Java Native Interface) 데이터:
            - 네이티브 코드에서 참조하는 Java 객체
            - 실행 과정:
                1. Java에서 네이티브 메서드를 선언하고 호출
                2. 네이티브 코드(C/C++)에서 Java 객체를 참조하거나 조작
                3. 결과를 Java로 반환
3. **GC 유형**
    - Minor GC: Eden 및 Survivor 영역 대상, 빠르게 수행
    - Major(Full) GC:
        - Old Generation 포함 전체 Heap 대상, 느리고 stop-the-world 발생
        - 수초 이상 진행되기도 하며, 이 지연으로 인해 **DB Connection이 끊기는 등의 운영 문제가 발생할 수 있음**

## 4. 살아남는 객체 관리 전략

- Old Generation까지 이동하는 객체 수를 줄이는 것이 중요
- 불필요한 객체 생성을 피하고, 적절한 객체 생명주기 관리를 통해 GC 부담 완화

## 결론

- JVM 메모리 관리는 성능 최적화와 안정적인 애플리케이션 운영에 핵심적인 요소이다.
- 특히, GC로 인한 성능 문제나 장애 발생 시에는 Heap Dump 분석 역량이 필요할 수도 있다.
- 적절한 JVM 옵션 설정과 코드 최적화를 통해 효율적인 메모리 사용을 유지해야 한다.

## References

- [image1 - JVM Memory](https://mirinae312.github.io/develop/2018/06/04/jvm_memory.html)
- [image 2 - JVM GC 에 대한 이해](https://hyungjoon6876.github.io/jlog/2018/07/07/how-jvm-works.html)
- [Guide to Garbage Collector Roots](https://www.baeldung.com/java-gc-roots)
- [JNI (JAVA Native Interface)](https://velog.io/@vrooming13/JNI-JAVA-Native-Interface)
