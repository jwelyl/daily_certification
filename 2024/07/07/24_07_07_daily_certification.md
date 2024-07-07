# 24_07_07_daily_certification

# Computer Architecture

# Memory

# RAM

![ram.jpeg](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/Memory%2020bb5d14ab9545cb98b5ab0e2770896e/ram.jpeg)

# RAM의 용량과 성능

![small_ram.jpeg](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/Memory%2020bb5d14ab9545cb98b5ab0e2770896e/small_ram.jpeg)

**RAM의 용량이 적으면 여러 Process를 메모리에 올려 놓을 수가 없어서, CPU가 한 번에 여러 Process를 실행하기 어렵다.**

![large_ram.jpeg](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/Memory%2020bb5d14ab9545cb98b5ab0e2770896e/large_ram.jpeg)

**RAM의 용량이 크다면 여러 Process를 메모리에 올려 놓을 수 있어서, CPU가 한 번에 여러 Process를 실행하는데 유리하다.**

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/Memory%2020bb5d14ab9545cb98b5ab0e2770896e/Untitled.png)

책장 : SSD

책상 : RAM

책 : 실행할 명령어 집합, 프로세스

# RAM의 종류

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/Memory%2020bb5d14ab9545cb98b5ab0e2770896e/Untitled%201.png)

## DRAM (Dynamic RAM)

- **저장된 데이터가 동적으로 사라지는 RAM**
- 전원에 연결된 상태에서도 저장된 데이터가 동적으로 사라진다.
- 데이터 소멸을 막기 위해 주기적으로 재활성화(다시 저장)해야 한다.
- **실제 주기억장치로 사용되는 RAM**
    - (상대적으로) 소비전력이 낮다.
    - 가격이 낮다
    - 집적도가 높아서 대용량으로 설계할 수 있다.

## SRAM (Static RAM)

- **저장된 데이터가 정적으로 사라지지 않는 RAM**
- 전원에 연결된 상태에서는 저장된 데이터가 사라지지 않는다.
- DRAM보다 I/O 속도가 빠르다.
- **Cache Memory에 사용된다. (대용량일 필요는 없지만 빨라야 하는 경우)**
    - 소비전력이 높다.
    - 가격이 높다.
    - 집적도가 낮아서 대용량으로 설계할 수 없다.

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/Memory%2020bb5d14ab9545cb98b5ab0e2770896e/Untitled%202.png)

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/Memory%2020bb5d14ab9545cb98b5ab0e2770896e/Untitled%203.png)

## SDRAM (Synchronous DRAM)

- DRAM의 발전된 형태
- Clock 신호와 동기화된 DRAM

## DDR SDRAM (Double Data Rate SDRAM)

- SDRAM의 발전된 형태
- **최근에 가장 대중적으로 사용되는 Main Memory가 DDR4 SDRAM**
- 대역폭을 넓혀서 속도를 빠르게 만든 SDRAM
- DDR2 SDRAM, DDR3 SDRAM, **DDR4 SDRAM**, … (2배씩 대역폭 넓어짐)

# Address Space

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/Memory%2020bb5d14ab9545cb98b5ab0e2770896e/Untitled.jpeg)

**CPU와 Process는 현재 Memory의 몇 번지에 무엇이 저장되어 있는지 다 알지 못한다.**

**메모리에 저장된 값들은 매번 변한다.**

- 새롭게 실행되는 프로그램은 메모리에 새로 적재된다.
- 실행이 끝난 프로세스는 메모리에서 삭제된다.
- 같은 프로그램을 실행하더라도 실행할 때마다 적재되는 메모리 주소가 달라진다.

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/Memory%2020bb5d14ab9545cb98b5ab0e2770896e/Untitled%204.png)

## Physical Address

- Memory 입장에서 바라본 주소
- **실제 정보가 저장된 H/W상의 주소**

## Logical Address

- CPU와 실행 중인 프로그램 입장에서 바라본 주소
- **실행 중인 프로그램 각각에 부여된 0번지부터 시작하는 주소**

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/Memory%2020bb5d14ab9545cb98b5ab0e2770896e/Untitled%205.png)

**Logical Address는 CPU와 CPU가 실행하는 Process 입장에서의 주소이므로 상대적인 주소이다.**

- 인터넷 브라우저가 논리 주소 0~A, 게임이 0~B, 메모장이 0~C의 주소를 가질 수 있으며, 동일한 주소가 다른 프로세스에 존재 가능하다. (인터넷 브라우저의 0번지와, 게임의 0번지 모두 존재 가능하다.)

**Physical Address는 H/W Memory 입장에서의 주소로 절대적인 주소이다.**

- 물리 주소는 서로 다른 프로세스가 같은 주소를 가질 수 없다. 0번지는 오직 하나만 존재한다.

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/Memory%2020bb5d14ab9545cb98b5ab0e2770896e/Untitled%206.png)

**Process의 Logical Address를 Physical Address로 변환해야 한다.**

## MMU (Memory Management Unit)

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/Memory%2020bb5d14ab9545cb98b5ab0e2770896e/Untitled%207.png)

- CPU가 이해하는 주소는 Logical Address이다.
- CPU가 Memory로부터 Address Bus를 통해 주소를 주고 받을 때 MMU를 거친다.
- CPU가 Address Bus를 통해 주소를 전달할 때는 Logical Address가 Physical Address로 변한다.
- CPU가 Address Bus를 통해 주소를 받을 때는 Physical Address기  Logical Address로 변한다.

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/Memory%2020bb5d14ab9545cb98b5ab0e2770896e/Untitled%208.png)

**Logical Address를 Base Register와 더해서 Physical Address를 구한다.**

- Base Register : Process의 시작 물리 주소(기준 주소)

![mmu.jpeg](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/Memory%2020bb5d14ab9545cb98b5ab0e2770896e/mmu.jpeg)

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/Memory%2020bb5d14ab9545cb98b5ab0e2770896e/Untitled%209.png)

# Memory Protection

**다른 Process의 Physical Address를 침범하는 명령어를 실행할 수 없다.**

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/Memory%2020bb5d14ab9545cb98b5ab0e2770896e/Untitled%2010.png)

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/Memory%2020bb5d14ab9545cb98b5ab0e2770896e/Untitled%2011.png)

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/Memory%2020bb5d14ab9545cb98b5ab0e2770896e/Untitled%2012.png)

## Limit Register

- 다른 Process의 영역을 침범할 수 있는 명령어의 실행을 막는다.
- Base Register가 실행 중인 Process의 가장 작은 Physical Address를 저장한다.
- **Limit Register는 실행 중인 Process의 최대 Logical Address를 저장한다.**
- **Base Register 값 ≤ Process’s Physical Address < Base Register 값 + Limit Register 값**
- **0 ≤ Process’s Logical Address < Limit Register 값**

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/Memory%2020bb5d14ab9545cb98b5ab0e2770896e/Untitled%2010.png)

Limit Register 값 = 1000

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/Memory%2020bb5d14ab9545cb98b5ab0e2770896e/Untitled%2013.png)

Base Register = 100, Limit Register = 150이므로, 100 ~ 249가 유효한 Physical Address이다. 따라서 위 명령은 실행될 수 없다.

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/Memory%2020bb5d14ab9545cb98b5ab0e2770896e/Untitled%2014.png)

Base Register = 1500 Limit Register = 1000이므로, 1500 ~ 2499가 유효한 Physical Address이다. 따라서 위 명령은 실행될 수 없다.

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/Memory%2020bb5d14ab9545cb98b5ab0e2770896e/Untitled%2015.png)

# Cache Memory

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/Memory%2020bb5d14ab9545cb98b5ab0e2770896e/Untitled%2016.png)

**CPU 연산은 Register에 접근하여 이루어지는데 이에 비해 Memory에 접근하는 시간은 압도적으로 느리다.**

## **Memory Hierarchy**

- **CPU와 가까이 존재하는 저장 장치는 빠르고, 멀리 존재하는 저장 장치는 느리다.**
- **접근 속도가 빠른 저장 장치는 상대적으로 저장 용량이 작고, 가격이 비싸다.**
- **접근 속도가 느린 저장 장치는 상대적으로 저장 용량이 크고, 가격이 저렴하다.**
- **ex) Register vs RAM vs USB**
- 접근 속도와 비용, 용량은 Trade-Off 관계

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/Memory%2020bb5d14ab9545cb98b5ab0e2770896e/Untitled%2017.png)

![memory_hierachy.png](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/Memory%2020bb5d14ab9545cb98b5ab0e2770896e/memory_hierachy.png)

## Cache Memory

- **CPU Register와 Main Memory(DRAM) 사이에 존재하는 SRAM 기반 저장 장치**
- Register보다 용량이 크고, Main Memory보다 접근 속도가 빠르다.
- CPU의 연산 속도와 Main Memory 접근 속도의 차이를 조금이나마 줄이기 위해 사용한다.
- Main Memory에서 CPU에서 사용할 일부 데이터를 미리 저장해두는 용도

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/Memory%2020bb5d14ab9545cb98b5ab0e2770896e/Untitled%2018.png)

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/Memory%2020bb5d14ab9545cb98b5ab0e2770896e/Untitled%2019.png)

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/Memory%2020bb5d14ab9545cb98b5ab0e2770896e/Untitled%2020.png)

## Hierarchical Cache

Cache Memory도 L1, L2, L3 Cache 계층을 이룬다.

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/Memory%2020bb5d14ab9545cb98b5ab0e2770896e/Untitled%2021.png)

**접근 속도 : Register > L1 > L2 > L3 > Main Memory**

**용량 : Register < L1 < L2 < L3 < Main Memory**

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/Memory%2020bb5d14ab9545cb98b5ab0e2770896e/Untitled%2022.png)

각각의 코어마다 캐시가 다르게 존재할 경우, 데이터 일관성이 깨질 수 있다. Sync를 맞추는것이 중요하다.

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/Memory%2020bb5d14ab9545cb98b5ab0e2770896e/Untitled%2023.png)

L1 캐시를 명령어와 데이터를 분리하기도 한다.

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/Memory%2020bb5d14ab9545cb98b5ab0e2770896e/Untitled%2024.png)

# **Locality of reference**

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/Memory%2020bb5d14ab9545cb98b5ab0e2770896e/Untitled%2025.png)

**CPU가 자주 사용할 법한 내용이 뭘까?**

CPU가 Cache Memory에 저장된 값을 활용하는 경우 **Cache Hit**, 그렇지 않을 경우 **Cache Miss**라고 한다.

### Cache Hit

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/Memory%2020bb5d14ab9545cb98b5ab0e2770896e/Untitled%2026.png)

### Cache Miss

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/Memory%2020bb5d14ab9545cb98b5ab0e2770896e/Untitled%2027.png)

### Cache Hit Ratio

```
Cache Hit Ratio = Cache Hit / (Cache Hit + Cache Miss)
```

- Cache Hit Ratio가 높을 수록 좋은 Cache Memory이다. 요즘은 80% 이상임
- CPU가 사용할 법한 데이터를 잘 예측해야 한다.

## Locality of reference

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/Memory%2020bb5d14ab9545cb98b5ab0e2770896e/Untitled%2028.png)

## Temporal Locality

최근에 접근했던 메모리 공간에 다시 접근하려는 경향 (시간 지역성)

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/Memory%2020bb5d14ab9545cb98b5ab0e2770896e/Untitled%2029.png)

num 변수는 9번 동안 계속 쓰임. 같은 메모리 주소에 여러 번 접근하게 됨.

## Spatial Locality

접근한 메모리 공간 근처를 접근하려는 경향 (공간 지역성)

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Computer%20Architecture%203148bc2581ca47c6854333a13f18f830/Memory%2020bb5d14ab9545cb98b5ab0e2770896e/Untitled%2030.png)