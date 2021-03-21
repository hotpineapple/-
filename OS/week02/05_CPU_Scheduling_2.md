# CPU Scheduling (2)

*CPU Scheduling Algorithms 중 Multilevel Queue와 Multilevel Feedback Queue는 본 강의에서 다뤘지만 같은 파트에 정리하기 위해 `CPU Scheduling (1).md`에 정리하였습니다.*



## 01. Multiple-Processor Scheduling

CPU가 여러 개인 경우 스케줄링은 더욱 복잡해진다.

- Homogeneous processor
  - 반드시 특정 프로세서에서 수행되어야 하는 프로세스가 있는 경우가 있다.
- Load sharing
  - 일부 프로세서에 job이 몰리지 않도록 부하를 적절히 공유하는 메커니즘이 필요하다.
  - 별개의 큐를 두는 방법과 공동 큐를 사용하는 방법이 있다.
- Symmetric Multiprocessing (SMP)
  - 각 프로세서가 각자 알아서 스케줄링을 결정한다.
- Asymmetric multiprocessing
  - 하나의 프로세서가 시스템 데이터의 접근과 공유를 책임지고 나머지 프로세서는 거기에 따른다.



## 02. Real-Time Scheduling

- **데드라인이 있는 job**
  - CPU Scheduling은 반드시 데드라인을 보장해주어야 함 -> How? 미리 스케줄링한 후, 배치.

- Hard real-time systems
  - Hard real-time task는 정해진 시간 안에 반드시 끝내도록 스케줄링 해야 한다.
- Soft real-time computing
  - Soft real-time task는 일반 프로세스에 비해 높은 priority를 갖도록 해야 한다.



## 03. Thread Scheduling

- Local Scheduling
  - User level thread의 경우 사용자 수준의 thread library에 의해 어떤 thread를 스케줄할지 결정한다. 
  - 즉, OS가 결정하는 것이 아닌 사용자 프로세스가 직접 결정한다.
- Global Scheduling
  - Kernel level thread의 경우 일반 프로세스와 마찬 가지로 커널의 단기 스케줄러가 어떤 thread를 스케줄할지 결정한다.



## 04. Algorithm Evaluation

CPU 스케줄링 알고리즘 성능 평가 비교 방법



### 1. Queueing models

- 굉장히 이론적인 방법
- queue에 job들이 도착해서 쌓이게 되면, 도착한 프로세스들을 CPU의 능력(용량)에 따라서 server(CPU)에서 처리한다.

![image-20210321223847933](https://user-images.githubusercontent.com/77573938/111907439-a2726480-8a98-11eb-8e3e-4a2ac118b0cd.png)

- 확률분포로 주어지는 **arrival rate(프로세서들의 도착율)과 service rate(단위시간당 CPU가 얼마나 처리하는지)** 등을 통해 performance index 값을 계산한다.
- 예전에 많이 사용하던 방식. 최근에는 덜 사용하나 여전히 이론적으로는 많이 사용하는 하나의 방법이다.



### 2. Implementation (구현) & Measurement (성능 측정)

- Queueing models과 상반되는 방식
- **실제로 시스템에 알고리즘을 구현하여** 실제 작업(workload)에 대해서 **성능을 측정**하고 비교한다.

- 운영체제 내부(커널)를 고치는 것이 쉽지 않아 좀 더 쉬운 방법인 Simulation을 사용한다.



### 3. Simulation (모의 실험)

- 알고리즘을 **모의** 프로그램으로 작성한 후 trace(실제 프로그램을 통해 추출한 input data)를 입력으로 하여 결과를 비교하는 방법