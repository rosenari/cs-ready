## OS, 응용프로그램, 하드웨어의 관계

- OS는 응용프로그램이 요청하는 메모리를 허가, 분배한다.
- OS는 응용프로그램이 요청하는 CPU 시간을 제공한다.
- OS는 응용프로그램이 요청하는 IO 장치 사용을 허가/제어한다.

## 운영체제가 제공하는 인터페이스

- SHELL(쉘)
    - 사용자가 OS 기능과 서비스를 조작가능하도록 인터페이스를 제공
    - `CLI(터미널)와 GUI(그래픽 인터페이스)` 환경 두종류가 있다.

- API
    - `특정 언어의 라이브러리 형태로 제공`한다.(대표적으로 C언어 라이브러리)
    - 예로 open() 함수도 API이다.

- 시스템 콜
    - 사실 운영체제는 시스템 콜이라는 명령 또는 함수를 제공하는데, `API 내부에서 시스템 콜을 호출하는 형태`가 대부분이다.

`API 내부적으로 시스템 콜이 사용된다.`

## 운영체제 개발 순서

1. 커널(운영체제의 핵심영역) 개발
2. 시스템 콜개발
3. API 개발 (EX: C 라이브러리)
4. SHELL 프로그램 개발 (C언어로 API를 사용해 SHELL 프로그램을 개발한다.)
5. 응용프로그램 개발

> 시스템 콜은 표준이 존재한다. POSIX API와 WIN API가 대표적인데, `POSIX API란 유닉스, 리눅스에서 사용되는 표준` OS 인터페이스 이며, `WIN API는 윈도우에서 사용되는 표준` OS 인터페이스이다.

## CPU Protection Rings

- CPU는 명령어 실행시 두가지 모드 중 하나로 실행되는데, 사용자 모드와 커널 모드라는 것이 있다.
- 커널 모드는 사용하는 특권 명령어 실행과 OS 기능을 사용할때, 필요한 모드이다.
- `사용자 모드는 응용프로그램이 사용`하며, `커널 모드는 OS가 사용`한다.

## 시스템 콜과 커널모드

- 커널 모드에서만 사용가능한 기능들이 존재한다.
- `커널 모드로 실행하려면, 반드시 시스템 콜을 사용`해야한다.
- 시스템 콜은 운영체제가 제공한다.

## 코드예제로 보는 시스템콜과 커널모드

```C
#include <unistd.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>

int main(){
    int fd;
    fd = open("data.txt",O_RDONLY);
    //1. open함수 호출시 내부적으로 파일을 여는 시스템 콜(sys_open())을 호출한다.
    //2. 시스템콜 호출전 커널모드로 전환된다.
    //3. sys_open함수 호출
    //4. 파일 열기 연산 수행
    //5. 사용자 모드 전환
    //6. 아래줄 명령어 실행

    if(fd == -1){
        printf("Error : can not open file\n");
        return 1;
    }else{
        printf("File opened and now close\n");
        close(fd);
        return 0;
    }
}
```

> 응용프로그램에서 `open Api 호출시 시스템 콜이 호출되면서 커널모드로 전환`되어 명령이 실행되고, 다시 사용자모드로 전환되어 응용프로그램으로 돌아간다.

## 프로세스 스케줄링의 종류

1. 배치 처리 시스템
    - 컴퓨터 프로그램이 `실행 요청 순서에 따라 순차적으로 프로그램을 실행`하는 방식
    - 한번에 여러 프로그램을 순차적으로 실행 가능
    - 배치 처리 시스템은 여러 한계가 존재한다.
        - 순차적으로 실행되기에 실행시간이 긴 프로그램이 먼저 실행되며, 그 뒤의 프로그램은 너무 많은 시간을 기다려야한다.
        - 음악을 들으면서 문서작성을 하는 동시 작업이 안된다.(동시에 여러프로그램 실행)
        - 여러 사용자가 동시에 하나의 컴퓨터를 사용할수 없다.(다중 사용자 지원)

> 배치 처리 시스템의 한계로 시분할, 멀티프로그래밍과 같은 시스템이 나왔다.

2. 시분할 시스템

    - `다중 사용자 지원`을 위해 컴퓨터 `응답시간을 최소화`하는 시스템
    - 각 프로그램의 cpu사용시간을 짧게 쪼개어 사용자 인터렉션에 빠르게 반응하도록한다.


3. 멀티 태스킹 시스템

    - `단일 cpu에서 여러 응용프로그램이 동시에 실행되는 것처럼` 보이도록 한다.
    - 시분할과 마찬가지로 cpu사용시간을 쪼개서 각 응용프로그램에 분배함으로써, 마치 동시에 실행되는 것처럼 보이게한다.
    - `매우 짧은 시간간격은 사용자가 느끼지 못한다.` 예를 들어 키보드 입력후 0.01초 뒤에 화면에 표시된다고해서 끊킨다고 생각하지 않음.

> 멀티 프로세싱 : 멀티 태스킹과 반대되는 개념이다. 멀티 태스킹은 단일 cpu로 여러 프로그램을 사용하는 것이나, `멀티 프로세싱은 여러 cpu로 하나의 프로그램을 병렬로 실행하여 실행 속도를 극대화 하는 시스템`이다.

4. 멀티 프로그래밍 시스템

- `최대한 cpu를 많이 활용`하도록 하는 시스템이다.(cpu가 노는시간을 최소화한다.)
- 여러 응용프로그램 실행을 짧은 시간에 완료할 수 있다.

> 멀티 프로그래밍의 이해
응용 프로그램 실행시 온전히 cpu를 쓰지 않고, 다른 작업을 중간에 필요로 하는 경우가 많다.
`파일을 읽는다거나, 프린팅을 한다거나 하는 작업은 cpu 작업이 아닌 다른 하드웨어의 작업`이다. 예를 들어 `코드에 파일을 읽는 코드가 있다면, 파일을 읽어올때까지 cpu는 명령어 실행을 중지`하고, 대기하기때문에, `cpu가 놀게된다`. 이때, `이 시간에 다른 프로그램의 명령어를 수행`하여 `cpu 활용시간을 극대화` 하는 방법이 바로 멀티 프로그래밍이다.

`실제로 시분할,멀티 프로그래밍,멀티 태스킹 시스템은 유사한 기술이 사용되고, 유사한 의미로 통용되고 있으나, 목적에 따라 세부적으로 구분 할 수 있다.`