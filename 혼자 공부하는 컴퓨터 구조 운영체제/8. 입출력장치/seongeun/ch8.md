# 8-1 장치 컨트롤러와 장치 드라이버

## 장치 컨트롤러
아래의 이유로 입출력장치는 CPU, 메모리보다 다루기가 더 까다로움
1. 입출력장치에는 종류가 너무나도 많다.<br />
   키보드, 모니터, USB 메모리, CD-ROM, SSD, 마우스, 프린터, 스피커, 마이크 등 다양한 장치가 있는데 장치마다 속도, 데이터 전송 형식이 다양함 -> 다양한 입출력장치와 정보를 주고받는 방식을 규격화하기가 어렵다.<br />
   ![alt text](image.png)

2. 일반적으로 CPU와 메모리의 데이터 전송률은 높지만 입출려장치의 데이터 전송률은 낮다.<br />
   전송률: 데이터를 얼마나 빨리 교환할 수 있는지를 나타내는 지표<br />
   전송률의 차이는 CPU와 메모리, 입출력장치 간의 통신을 어렵게 함
   ![alt text](image-1.png)

이러한 이유로 입출력장치는 컴퓨터에 직접 연결되지 않고 `장치 컨트롤러`라는 하드웨어를 통해 연결 됨. 장치 컨트롤러는 `입출력 제어기`, `입출력 모듈` 등으로 다양하게 불리기도 함

모든 입출력장치는 각자의 장치 컨트롤러를 통해 컴퓨터 내부와 정보를 주고받고, 장치 컨트롤러는 하나 이상의 입출력장치와 연결되어 있다.
![alt text](image-2.png)

장치 컨트롤러의 역할
- CPU와 입출력장치 간의 통신 중개
- 오류 검출
- 데이터 버퍼링

입출력장치 종류가 많아 정보 규격화가 어려웠던 문제를 장치 컨트롤러가 일종의 번역가 역할을 함으로써 해결할 수 있게 됨. 이 과정에서 자신과 연결된 입출력장치에 문제가 없는지 오류를 검출하기도 함

`버퍼링`: 전송률이 높은 장치와 낮은 장치 사이에 주고받는 데이터를 `버퍼`라는 입시 저장 공간에저장하여 전송률을 비슷하게 맞추는 방법

![alt text](image-3.png)

**장치 컨트롤러의 간략화된 내부 구조**
![alt text](image-4.png)
- 데이터 레지스터<br />
  CPU와 입출력장치 사이에 주고받을 데이터가 담기는 레지스터. 버퍼 역할을 함

- 상태 레지스터<br />
  입출력장치가 입출력 잡업을 할 준비가 되었는지, 입출력 작업이 완료 되엇는지, 입출력 장치에 오류는 없는지 등의 상태 정보 저장
  
- 제어 레지스터<br />
  입출력장치가 수행할 내용에 대한 정보와 명령을 저장

## 장치 드라이버
장치 드라이버: 장치 컨트롤러의 동작을 감지하고 제어함으로써 장치 컨트롤러가 컴퓨터 내부와 정보를 주고받을 수 있게 하는 프로그램

장치 컨트롤러가 입출력장치를 연결하기 위한 하드웨어적인 통로라면, 장치 드라이버는 입출력장치를 연결하기 위한 소프트웨어적인 통로<br />
![alt text](image-5.png)

컴퓨터가 연결된 장치의 드라이버를 인식하고 실행할 수 있다면 그 장치는 컴퓨터 내부와 정보를 주고받을 수 있음

# 8-2 다양한 입출력 방법
장치 컨트롤러와 CPU가 정보를 주고받는 방법
- 프로그램 입출력
- 인터럽트 기반 입출력
- DMA 입출력

![alt text](image-6.png)

## 프로그램 입출력
프로그램 속 명령어로 입출력장치를 제어하는 방법. CPU가 프로그램 속 명령어를 실행하는 과정에서 입출력 명령어를 만나면 CPU는 입출력 장치에 연결된 장치 컨트롤러와 상호작용하며 입출력 작업을 수행함

ex) 메모리에 저장된 정보를 하드 디스크에 백업하는 상황
1. CPU는 하드 디스크 컨트롤러의 제어 레지스터에 쓰기 명령을 보냄. <br />
   '메모리에 저장된 정보를 하드 디스크에 백업한다.' === '하드 디스크에 새로운 정보를 쓴다'
   ![alt text](image-7.png)

2. 하드 디스크 컨트롤러는 하드 디스크 상태를 확인함. 하드 디스크가 준비된 상태라면 하드 디스크 컨트롤러는 상태 레지스터에 준비되었다고 표시함
   ![alt text](image-8.png)

3. CPU는 상태 레지스터를 주기적으로 읽어보며 하드 디스크의 준비 여부를 확인하고 하드 디스크가 준비됐음을 CPU가 알게되면 백업할 메모리의 정보를 데이터 레지스터에 쓴다.
   ![alt text](image-9.png)

CPU는 장치 컨트롤러의 레지스터들을 어떻게 알까?
### 메모리 맵 입출력
: 메모리에 접근하기 위한 주소 공간과 입출력 장치에 접근하기 위한 주소 공간을 하나의 주소 공간으로 간주하는 방법.

가령 1,024개의 주소를 표현할 수 있는 컴퓨터가 있을 때 512개는 메모리 주소를, 나머지 512개는 장치 컨트롤러의 레지스터를 표현하기 위해 사용함
![alt text](image-10.png)

주소 공간 일부를 아래와 같이 약속했을 때<br />
![alt text](image-11.png)
CPU는 '517번지를 읽어 들여라'라는 명령어로 프린터 상태를 읽을 수 있고, '518번지에 a를 써라'라는 명령어로 하드 디스크 컨트롤러의 데이터 레지스터로 데이터를 보낼 수 있음

### 고립형 입출력
: 메모리를 위한 주소 공간과 입출력장치를 위한 주소 공간을 분리하는 방법.

가령 1,024개의 주소 공간을 가진 컴퓨터가 있다고 가정.

제어 버스에 '메모리 읽기/쓰기' 선 이외에 '입출력장치 읽기/쓰기'선이 따로 있다면 메모리에도 1,024개의 주소 공간을 활용하고, 입출력장치도 1,023개의 주소 공간을 활용할 수 있음.
![alt text](image-12.png)

고립형 입출력 방식에서 CPU는 입출력장치에 접근하기 위해 메모리에 접근하는 명령어와는 다른(입출력 읽기/쓰기 선을 활성화시키는) 입출력 명령어를 사용함.
![alt text](image-13.png)

## 인터럽트 기반 입출력
입출력장치에 의한 하드웨어 인터럽트는 입출력장치가 아닌 장치 컨트롤러에 의해 발생함. CPU는 장치 컨트롤러에 입출력 작업을 명령하고, 장치 컨트롤러가 입출력장치를 제어하며 입출력을 수행하는 동안 CPU는 다른 일을 함.
장치 컨트롤러가 입출력 작업을 끝낸 뒤 CPU에게 인터럽트 요청 신호를 보내면 CPU는 하던 일을 잠시 백업하고 인터럽트 서비스 루틴을 실행함<br />
![alt text](image-14.png)
![alt text](image-15.png)

여러 입출력장치에서 인터럽트가 동시에 발생한 경우에는 인터럽트들을 어떻게 처리해야할까?
![alt text](image-16.png)

간단하게 인터럽트가 발생한 순서대로 인터럽트를 처리하는 방법이 있다.
가령, 인터럽트 A를 처리하는 도중 발생한 또 다른 인터럽트 B의 요청을 받아들이지 않고, 인터럽트 A 서비스 루틴이 끝난 후 인터럽트 B 서비스 루틴을 실행하는 방법이다.
![alt text](image-17.png)<br />
하지만 인터럽트 중에서도 더 빨리 처리해야하는 인터럽트가 있기 때문에 현실적으로 모든 인터럽트를 전부 순차적으로 해결할 순 없다.<br />
즉, CPU는 우선순위를 고려하여 우선순위가 높은 인터럽트 순으로 여러 인터럽트를 처리할 수 있음.

![alt text](image-18.png)

플래그 레지스터 속 인터럽트 비트가 활성화되어 있는 경우, 혹은 인터럽트 비트를 비활성화해도 무시할 수 없는 인터럽트인 NMI가 발생한 경우 CPU는 우선순위가 높은 인터럽트부터 처리함

많은 컴퓨터에서는 PIC(프로그래머블 인터럽트 컨트롤러)라는 하드웨어를 사용하여 우선순위를 반영한 다중 인터럽트를 처리함
![alt text](image-19.png)<br />
PIC: 여러 장치 컨트롤러에 연결되어 장치 컨트롤러에서 보낸 하드웨어 인터럽트 요청들의 우선순위를 판별한 뒤 CPU에 지금 처리해야 할 하드웨어 인터럽트는 무엇인지 알려주는 장치<br />
   각 핀에는 CPU에 하드웨어 인터럽트 요청을 보낼 수 있는 약속된 하드웨어가 연결되어 있음.

PIC에 연결된 장치 컨트롤러들이 동시에 하드웨어 인터럽트 요청을 보내면 PIC는 이들의 우선순위를 판단하여 CPU에 가장 먼저 처리할 인터럽트를 알려줌
![alt text](image-20.png)

<PIC의 다중 인터럽트 처리 과정>
1. PIC가 장치 컨트롤러에서 `인터럽트 요청 신호(들)`를 받아들임
2. PIC는 인터럽트 우선순위를 판단한 뒤 CPU에 처리해야 할 `인터럽트 요청 신호`를 보냄
3. CPU는 PIC에 `인터럽트 확인 신호`를 보냄
4. PIC는 데이터 버스를 통해 CPU에 `인터럽트 벡터`를 보냄
5. CPU는 인터럽트 벡터를 통해 인터럽트 요청의 주체를 알게 되고, 해당 장치의 `인터럽트 서비스 루틴`을 실행함

일반적으로 더 많고 복잡한 장치들의 인터럽트를 관리하기 위해 PIC를 두 개 이상 계층적으로 구성함<br />
![alt text](image-21.png)

> 참고: PIC는 인터럽트 비트를 통해 막을 수 있는 하드웨어 인터럽트만 조정함. 즉 NMI까지 우선순위를 판별하진 않음

## DMA 입출력
프로그램 기반 입출력과 인터럽트 기반 입출력은 메모리간의 이동을 CPU가 주도하고, 이동하는 데이터도 반드시 CPU를 거침
=> 입출력 장치를 위한 연산 때문에 시간을 뺏기게 됨

DMA: CPU를 거치지 않고도 입출력장치와 메모리가 상호작용할 수 있는 입출력 방식<br />
   DMA 입출력을 하기 위해서는 시스템 버스에 연결된 `DMA 컨트롤러`라는 하드웨어가 필요함

   ![alt text](image-22.png)

### DMA 입출력 과정
1. CPU는 DMA 컨트롤러에 입출력 장치의 주소, 수행할 연산(읽기/쓰기), 읽거나 쓸 메모리의 주소 등과 같은 정보로 입출력 작업을 명령함
2. DMA 컨트롤러는 CPU 대신 장치 컨트롤러와 상호작용하며 입출력 작업을 수행함. 이때 DMA 컨트롤러는 필요한 경우 메모리에 직접 접근하여 정보를 읽거나 씀
3. 입출력 작업이 끝나면 DMA 컨트롤러는 CPU에 인터럽트를 걸어 작업이 끝났음을 알림


DMA 입출력으로 메모리 내의 정보를 하드 디스크에 백업하는 작업 과정
![alt text](image-23.png)
![alt text](image-24.png)

DMA 컨트롤러는 시스템 버스로 메모리에 직접 접근이 가능하지만, 시스템 버스는 공용 자원이기 때문에 동시 사용이 불가능하다.<br />
즉, CPU가 시스템 버스를 사용할 때 DMA 컨트롤러는 시스템 버스를 사용할 수 없고 반대의 경우도 마찬가지이다.

그래서 DMA 컨트롤러는 CPU가 시스템 버스를 이용하지 않을 때마다 조금씩 시스템버스를 이용하거나, CPU가 일시적으로 시스템 버스를 이용하지 않도록 허락을구하고 시스템 버스를 집중적으로 이용함

![alt text](image-25.png)

### 입출력 버스
CPU, 메모리, DMA 컨트롤러, 장치 컨트롤러가 모두 같은 버스를 공유하는 구성에서는 DMA를 위해 한 번 메모리에 접근할 때마다 시스템 버스를 두 번 사용하게 되는 부작용이 있음

ex) 메모리 내 정보를 하드 디스크로 백업하는 상황<br />
메모리에서 DMA 컨트롤러로 데이터를 가져오기 위해 시스템 버스를 한 번 사용하고, DMA 컨트롤러의 데이터를 장치 컨트롤러로 옮기기 위해 시스템 버스를 또 한 번 사용함<br />
![alt text](image-26.png)

DMA를 위해 시스템 버스를 너무 자주 사용하면 그만큼 CPU가 시스템 버스를 이용하지 못함=> DMA 컨트롤러와 장치 컨트롤러들을 `입출력 버스`라는 별도의 버스에 연결하여 해결 가능
![alt text](image-27.png)

대부분의 컴퓨터에는 입출력 버스가 있음. 다시말해 대부분의 입출력장치(장치 컨트롤러)는 시스템 버스가 아닌 입출력 버스와 연결됨.

입출력 버스에는 `PCI버스`, `PCI Express(PCIe) 버스` 등 여러 종류가 있음<br />
![alt text](image-28.png)<br />
`PCIe 슬롯`: 여러 입출력 장치들을 PCIe 버스와 연결해 주는 통로.


---

## 📖

Q1. 버퍼링에 대해서 설명하시오.

A1. 전송률이 높은 장치와 낮은 장치 사이에 주고받는 데이터를 버퍼라는 입시 저장 공간에저장하여 전송률을 비슷하게 맞추는 방법을 버퍼링이라 합니다.

Q2. 메모리 맵 입출력 방식과 고립형 입출력 방식의 차이점에 대해 설명하시오.

A2. 메모리 맵 입출력 방식은 메모리와 같은 입출력 장치는 같은 주소 공간을 사용하지만 고립형 입출력 방식은 분리된 주소 공간을 사용하고, 메모리 맵 입출력 방식은 메모리 주소 공간이 축소되지만 고립형 입출력은 메모리 주소 공간이 축소되지 않는다는 점, 또 메모리 맵 입출력 방식은 메모리와 입출력 장치에 같은 명령어 사용이 가능하지만 고립형 입출력 방식은 입출력 전용 명령어를 사용한다는 점에서 차이가 있다.

Q3. PIC의 다중 인터럽트 처리 과정에 대해 설명하시오.

A3. 다중 인터럽트가 발생할 경우 PIC는 장치 컨트롤러에서 인터럽트 요청 신호들을 받아들이고, 인터럽트 우선순위를 판단한 뒤 CPU에 처리해야 할 인터럽트 요청 신호를 보냅니다. CPU는 PIC에 인터럽트 확인 신호를 보내고 PIC는 데이터 버스를 통해 CPU에 인터럽트 벡터를 보냅니다. CPU는 인터럽트 벡터를 통해 인터럽트 요청의 주체를 알게 되고, 해당 장치의 인터럽트 서비스 루틴을 실행합니다.