# 24_02_02_daily_certification

# Computer Network

## Network Layer

### Routing vs Forwarding

![Untitled](24_02_02_daily_certification%20e3eb2c8d1d3d4372965145c48a7f5c1f/Untitled.png)

### **Forwarding**

**라우터의 가장 중요한 기능**

- 라우터가 패킷 받았을 때, 패킷 안에 있는 헤더의 dst IP address를 보고, dst IP address와 Forwarding Table의 entry와 매칭시켜서  가장 적합한 IP address로 보내는 것 **(Longest Prefix Matching)**
- Forwarding 자체는 단순히 Forwarding Table Look up임

### **Routing**

**Forwarding Table을 만드는 것 (Routing Algorithm을 통해)**

각 라우터에서 다른 라우터로 가장 최적의 경로를 찾아서 Forwarding Table에 기록함

Router를 Node로, Link를 Edge로 보면 Network는 Graph이고, Routing Algorithm은 Shortest Path Algorithm임

### Routing Algorithm

1. Link State Algorithm (전체 Network Graph가 알려져 있는 경우, Global)
2. Distance Vector Algorithm (이웃 라우터와 정보를 주고받으며 최단 경로를 구하는 경우, Decentralized)

### Link State Algorithm

**Dijkstra Algorithm**

![IMG_0127.jpg](24_02_02_daily_certification%20e3eb2c8d1d3d4372965145c48a7f5c1f/IMG_0127.jpg)

![IMG_0128.jpg](24_02_02_daily_certification%20e3eb2c8d1d3d4372965145c48a7f5c1f/IMG_0128.jpg)

![IMG_0129.jpg](24_02_02_daily_certification%20e3eb2c8d1d3d4372965145c48a7f5c1f/IMG_0129.jpg)

![IMG_0130.jpg](24_02_02_daily_certification%20e3eb2c8d1d3d4372965145c48a7f5c1f/IMG_0130.jpg)

![IMG_0131.jpg](24_02_02_daily_certification%20e3eb2c8d1d3d4372965145c48a7f5c1f/IMG_0131.jpg)

![IMG_0132.jpg](24_02_02_daily_certification%20e3eb2c8d1d3d4372965145c48a7f5c1f/IMG_0132.jpg)

![IMG_0133.jpg](24_02_02_daily_certification%20e3eb2c8d1d3d4372965145c48a7f5c1f/IMG_0133.jpg)

# Problem Solving (Algorithm & SQL)

**BOJ 11509 풍선 맞추기**

[11509번: 풍선 맞추기](https://www.acmicpc.net/problem/11509)

**코드**

```kotlin

```