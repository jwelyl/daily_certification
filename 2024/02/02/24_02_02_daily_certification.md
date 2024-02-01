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

Link State Algorithm을 위해서는 모든 Router에 Network 상황을 Broadcast해야 한다.

**그 범위는 특정 Network로 한다.** (ex) 서강대학교 네트워크), 전체 Internet이 아님

**각 네트워크 주체별로(ex) HYU, SK) 각자의 Routing Algorithm을 사용함**

![1.jpeg](24_02_02_daily_certification%20e3eb2c8d1d3d4372965145c48a7f5c1f/1.jpeg)

네트워크와 네트워크 사이(ex) Sogang과 SKKU 사이)에도 Router가 존재하는데 네트워크와 네트워크 사이의 Routing Algorithm은? 즉 Network 사이의 Routing Algorithm은?

### Distance Vector Algorithm

**Bellman-Ford Algorithm**

# Problem Solving (Algorithm & SQL)

**BOJ 11509 풍선 맞추기**

[11509번: 풍선 맞추기](https://www.acmicpc.net/problem/11509)

**코드**

```kotlin

```