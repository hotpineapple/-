# Chapter 4. Process Management

# 1. 프로세스 생성(Process Creation)

- 부모 프로세스는 **System call 을 통해 운영체제에 요청**하여 자식 프로세스를 복제 생성함
- 트리(계층)구조를 형성
- 자식 프로세스가 사용하는 자원
    - 운영체제로부터 제공
    - 부모와 공유
- 부모-자식 프로세스 간 자원의 공유정도에 따른 모델링 구분
    - 부모와 자식이 모든 자원을 공유하는 모델
    - 일부를 공유(COW)
    - **전혀 공유하지 않는 모델(**일반적)
- 수행(Execution) 방식에 따른 모델링 구분
    - 부모와 자식은 공존하며 수행되는 모델
    - 자식이 종료(terminate)될 때까지 부모가 기다리(wait)는 모델*

### 프로세스 생성 과정

1. 주소공간(부모프로세스의 문맥) 복제 : fork

    자식프로세스는 부모프로세스의 주소공간과 운영체제의 주소공간 중 부모 프로세스 관련 data를(PCB, resources etc)을 복사함

2. 자식프로세스는 그 공간에 새로운 프로그램을 올림 : exec
- 예) 유닉스의 경우
    - fork 시스템 콜이 새로운 프로세스를 생성
        - 부모를 그대로 복사(**OS data except PID + binary)**
        - 주소 공간 할당
    - fork 다음에 이어지는 exec() 시스템 콜을 통해 새로운 프로그램을 메모리에 올림

### cf. COW(Copy-on-Write) 기법

원칙적으로는 자식 프로세스 또한 별개의 프로세스이므로 생성 시 주소공간을 복사하여 새로운 주소공간을 만들어야 한다. 그러나 만약 내용이 중복된다면 이는 메모리의 낭비일 것이다. 따라서 초기에는 부모와 주소공간을 공유하다가 자식 프로세스에서 수정이 발생했을 때 부모 프로세스 주소공간의 해당 부분만 복사하여 수정하는 방법을 사용하기도 한다.

# 2. 프로세스 종료(Process Termination)

**원칙** : 항상 자식 프로세스가 먼저 종료된 후 부모 프로세스가 종료된다.

### 1. 자발적 종료 :  프로세스가 마지막 명령을 수행한 후 운영체제에 이를 알려줌(exit 시스템 콜)

- 자식이 부모에게 output data를 보냄(via wait)
- 프로세스의 각종 자원들이 운영체제에게 반납됨

### 2. 비자발적 ,강제종료 : 부모 프로세스가 자식의 수행을 종료시킴 (abort)

1. 자식프로세스가 할당 자원의 한계치를 넘어섬
2. 자식프로세스에게 할당된 태스크가 더 이상 필요하지 않음
3. 부모프로세스가 종료(exit)하는 경우
    - 운영체제는 부모프로세스가 종료하는 경우 자식이 더 이상 수행하는 것을 허용하지 않음
    - **단계적인 종료**

# 3-1. fork() 시스템 콜

- A process is created by the `fork()` system call.
    - (Kernel) creates a new address space that is a duplicate of the caller.

(fork 내부코드가 아닌 fork를 호출하는 사용자 프로그램의 코드)

```c
int main()
{
	int pid;
	pid = fork(); //운영체제에 새로운 프로세스 생성을 요청하는 시스템 콜

	if(**pid == 0**) printf("\n Hello, I am child!\n");
	else if(**pid > 0**) printf("\n Hello, I am parent!\n");
}
```

- **자식 프로세스는** 부모 프로세스와 동일한 코드이나
부모 프로세스의 PC 값 또한 복사하여 가지고 있으므로
**pid = fork(); 다음 줄부터 실행**한다.
- fork()의 리턴값을 이용하여 원본과 복사본을 구분하여 각자 다른 일을 수행하도록 관리할 수 있다.
    - 부모 프로세스 : 양수(자식 프로세스의 아이디)
    - 자식 프로세스 : 0

# 3-2. exec() 시스템 콜

- A process can execute a different program by the `exec()` system call.
    - replaces the memory image of the caller with a new program

```c
int main()
{
	int pid;
	pid = fork(); //운영체제에 새로운 프로세스 생성을 요청하는 시스템 콜

	if(pid == 0) /*this is child*/
	{
		 printf("\n Hello, I am child! Now I'll run date.\n");
		 **execlp**("/bin/date", "/bin/date", (char*)0); 
	}
	else if(pid > 0) printf("\n Hello, I am parent!\n"); /*this is parent*/
}
```

- 자식 프로세스에서 기존의 코드를 date(현재 날짜와 시각을 출력하는 리눅스 커맨드) 라는 새로운 프로그램으로 덮어씌움
- 다시 원래의 코드로 돌이킬 수는 없음.
- fork() 없이 exec()만 단독 사용 가능하나 이 경우 **exec()호출 이후 코드는 실행되지 않는다.**

    ```c
    int main()
    {
    	printf("1");
    	execlp("echo","echo","hello","3",(char*)0); 
    	printf("2");
    }
    //실행 결과 : 1 과 hello 3 출력 (2는 출력되지 않음)
    ```

    cf. echo : 뒤에 나오는 argument들을 차례로 출력하는 리눅스 커맨드

# 3-3. wait() 시스템 콜

- 프로세스 A가 자식 프로세스를 생성한 후 wait() 시스템콜을 호출하면
    - 커널은 **자식 process가 종료될 때까지** 프로세스 A를 sleep시킨다(block 상태) → *자식이 종료될 때까지 부모가 기다리는 모델
    - 자식 process가 종료되면 커널은 프로세스 A를 깨운다(ready 상태)
- 예) 리눅스 커맨드창에 프로그램이름을 입력하면 그 프로그램은 Shell(쉘)의 자식프로세스 형태로 생성 및 실행되며 이것이 종료될 때까지 쉘 프롬프트는 wait() 시스템콜을 하여 기다림.


# 3-2. exit() 시스템 콜

### 프로세스의 종료

1. 자발적 종료
    - 마지막 statement 수행 후 exit() 시스템 콜
    - 프로그램에 명시적으로 적어주지 않아도 컴파일러는 main함수가 리턴되는 위치에서 exit() 시스템 콜이 호출되도록 한다.
2. 비자발적 종료(외부)
    - 부모 프로세스가 자식 프로세스를 강제 종료시킴
        - 자식 프로세스가 한계치를 넘어서는 자원을 요청하는 경우
        - 자식에게 할당된 태스크가 더 이상 필요하지 않은 경우
    - 키보드로 kill, break 등을 입력한 경우
    - 부모프로세스가 종료되는 경우
        - 부모 프로세스가 종료되기 전 그 자식프로세스들이 먼저 종료되어야 한다

## 요약 : 프로세스와 관련한 시스템 콜

| 이름 | 내용|

| ---- | ---- |

| fork() | create a child(copy) |

| exec() | overlay new image |

| wait() | sleep until child is done |

| exit() | frees all the resources, notify parent |

# 4. 프로세스 간 협력

### 1. 독립적 프로세스(Independent process)

- 프로세스는 각자의 주소공간을 가지고 수행되므로 원칙적으로 하나의 프로세스는 다른 프로세스의 수행에 영향을 미치지 못함

### 2. 협력 프로세스(Cooperating process)

- 프로세스 협력 매커니즘을 통해 하나의 프로세스가 다른 프로세스의 수행에 영향을 미칠 수 있음
- 프로세스 간 협력 매커니즘 (IPC : Inter-process Communication)
    1. **메시지를 전달**하는 방법
        - message passing : 커널을 통해 메시지 전달
    2. **주소공간을 공유**하는 방법
        - shared memory : 서로 다른 프로세스 간에도 일부 주소 공간을 공유하도록 하는 shared memory 메커니즘이 있음
        - 최초에는 커널로 주소공간 공유 관련 시스템 콜 필요하나
        - 이후에는 사용자 프로세스 간 직접 통신하므로 두 프로세스 모두 높은 신뢰성이 요구됨

        cf. thread : 스레드는 하나의 프로세스에 포함되므로 프로세스 간 협력으로 보기에는 어렵지만 동일한 process를 구성하는 스레드 간에는 주소공간을 공유하므로 협력 가능함.


## 4-1. Message Passing

- Message system : 프로세스 사이에 공유변수(shared variable)를 일절 사용하지 않고 **운영체제 커널를 통하여 통신**하는 시스템임.
1. Direct Communication :  메시지를 전달할 프로세스 이름을 명시
2. Indirect Communication : mail box (또는 port)를 통해 간접적으로 메시지 전달
