# 24_03_21_daily_certification

# Java

## ObjectMapper, JsonNode

다음과 같은 JSON schema를 트리 형태로 파싱하고 싶다고 가정하자.

```json
{
    "type": "object",
    "properties": {
      "j": {
        "type": "array",
        "items": {
          "type": "object",
          "properties": {
            "x": {
              "type": "number"
            },
            "y": {
              "type": "number"
            },
            "z": {
              "type": "string"
            },
            "w": {
              "type": "number"
            },
            "v": {
              "type": "array",
              "items": {
                "type": "object",
                "properties": {
                  "a": {
                    "type": "number"
                  },
                  "b": {
                    "type": "string"
                  },
                  "c": {
                    "type": "number"
                  }
                }
              }
            }
          }
        }
      },
      "k": {
          "type" : "object",
          "properties" : {
              "member1" : {"type" : "number"},
              "member2" : {"type" : "string"},
              "member3" : {"type" : "boolean"}
          }
      },
      "l" : {
          "type" : "number"
      },
      "m" : {
          "type" : "array",
          "items": {
              "type" : "number"
          }
      }
    },
    "title": "response json schema",
    "description": "response json schema of tetete service"
  }
  
```

위 JSON Schema를 다음과 같은 트리 형태로 바꾸고 싶다.

![JSONtree.jpeg](24_03_21_daily_certification%2096ddfe00ec6040cf8b2f0d1e886a8609/JSONtree.jpeg)

만들어질 트리의 노드 Node는 다음과 같다.

Node.java

```java
import com.fasterxml.jackson.annotation.JsonInclude;
import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;

import java.util.List;

@Setter
@Getter
@Builder
@NoArgsConstructor
@AllArgsConstructor
public class Node {
  private String nodeName;
  private String type;
  private boolean isArray;
  private List<Node> children;
}

```

ObjectMapper를 통해 JSON 데이터를 트리 구조로 나타내서 탐색하는데 사용할 수 있다. 그렇게 만들어진 트리는 노드가 JsonNode인 트리가 된다.

**build.gradle**

```
dependencies {
    implementation 'com.fasterxml.jackson.core:jackson-databind:2.13.0'
}
```

ObjectMapper와 JsonNode를 사용하기 위해서는 build.gradle에 위의 의존성을 추가해야 한다.

**main 메서드**

```java
public static void main(String[] args) throws Exception {
    String jsonSchema = (JsonSchema 형태 문자열);

    ObjectMapper objectMapper = new ObjectMapper(); //  JsonSchema를 파싱하기 위한 ObjectMapper
    JsonNode rootNode = objectMapper.readTree(jsonSchema);  //  JsonSchema를 JsonNode 트리 형태로 변환
    Node root = getNode(rootNode, "root");      //  JsonNode 트리를 Node 트리로 변환

    printTree(root);  //  Node 트리 출력
  }
```

ObjectMapper로 JsonSchema를 JsonNode 트리로 변환한 후 JsonNode 트리의 각 노드를 postorder 방식으로 순회하며 Node 형태로 변환한다.

**getNode 메서드**

```java
/**
 *
 * @param node : JsonNode 서브트리의 루트 노드
 * @param nodeName : JsonNode 서브트리로부터 변환된 Node 트리 루트 노드의 이름
 * @return : JsonNode 서브트리로부터 변환된 Node 트리의 루트
 */
private static Node getNode(JsonNode node, String nodeName) {
  String type = node.path("type").asText(); //  현재 노드에 저장된 타입

  Node root = new Node(); //  Node 트리의 루트 노드

  root.setNodeName(nodeName);
  root.setType(type);
  root.setArray(type.equals("array"));  //  타입이 배열일 경우 true
  root.setChildren(new ArrayList<>());  //  자식 노드 저장할 리스트

  if(type.equals("object")) { //  현재 노드 타입이 object 타입일 경우 오브젝트의 하위 멤버들을 탐색해서 자식 노드로 삼아야 함
    JsonNode propertiesNode = node.path("properties");  //  현재 노드의 properties 확인
    Iterator<Entry<String, JsonNode>> fieldIterator = propertiesNode.fields();  //  Key : 자식 멤버 이름, Value : 자식 멤버 JsonNode

    while(fieldIterator.hasNext()) {  //  모든 자식 노드들에 대하여
      Map.Entry<String, JsonNode> field = fieldIterator.next();
      String fieldName = field.getKey();      //  자식 노드 이름
      JsonNode fieldNode = field.getValue();  //  자식 노드

      root.getChildren().add(getNode(fieldNode, fieldName));  //  자식 JsonNode를 Node로 변경해서 root의 자식으로 넣어줌
    }
  } else if(type.equals("array")) { //  현재 노드 타입이 array일 경우 어떤 타입 array인지 확인해야 함
    JsonNode itemNode = node.path("items"); //  items 확인
    String itemType = itemNode.path("type").asText(); //  배열 원소 타입 확인

    root.setType(itemType); //  루트 노드 타입 변경
    if(itemType.equals("object")) { //  배열 원소 타입이 object일 경우
      JsonNode propertiesNode = itemNode.path("properties");  //  현재 노드의 properties 확인
      Iterator<Entry<String, JsonNode>> fieldIterator = propertiesNode.fields();  //  Key : 자식 멤버 이름, Value : 자식 멤버 JsonNode

      while(fieldIterator.hasNext()) {  //  모든 자식 노드들에 대하여
        Map.Entry<String, JsonNode> field = fieldIterator.next();
        String fieldName = field.getKey();      //  자식 노드 이름
        JsonNode fieldNode = field.getValue();  //  자식 노드

        root.getChildren().add(getNode(fieldNode, fieldName));  //  자식 JsonNode를 Node로 변경해서 root의 자식으로 넣어줌
      }
    }

  }

  return root;
}
```

그러면 다음 그림과 같은 Node 트리가 만들어진다.

![JSONtree.jpeg](24_03_21_daily_certification%2096ddfe00ec6040cf8b2f0d1e886a8609/JSONtree%201.jpeg)

트리가 잘 만들어졌는지 확인하기 위해 preorder로 순회하는 코드를 작성한다.

**printTree 메서드**

```java
private static int order = 1;  //  순회 순서
/**
 *
 * @param root : 출력할 트리의 루트 노드
 * 루트 노드를 기준으로 preorder 방식으로 순회하며 정보 출력함
 */
private static void printTree(Node root) {
  System.out.println("(" + order++ + ")");
  System.out.println("name = " + root.getNodeName());
  System.out.println("type = " + root.getType());
  System.out.println("isArray = " + root.isArray());  //  현재 노드 정보들 출력

  for(Node child : root.getChildren())  //  자식 노드들 돌면서
    printTree(child); //  자식 노드들 정보 출력
}
```

예상 순회 순서는 다음과 같다.

![preorder.jpeg](24_03_21_daily_certification%2096ddfe00ec6040cf8b2f0d1e886a8609/preorder.jpeg)

실제 순회를 콘솔로 출력하면 다음과 같다.

```
(1)
name = root
type = object
isArray = false
(2)
name = j
type = object
isArray = true
(3)
name = x
type = number
isArray = false
(4)
name = y
type = number
isArray = false
(5)
name = z
type = string
isArray = false
(6)
name = w
type = number
isArray = false
(7)
name = v
type = object
isArray = true
(8)
name = a
type = number
isArray = false
(9)
name = b
type = string
isArray = false
(10)
name = c
type = number
isArray = false
(11)
name = k
type = object
isArray = false
(12)
name = member1
type = number
isArray = false
(13)
name = member2
type = string
isArray = false
(14)
name = member3
type = boolean
isArray = false
(15)
name = l
type = number
isArray = false
(16)
name = m
type = number
isArray = true

```

# Problem Solving (Algorithm & SQL)

**BOJ 13140 Hello World!**

[13140번: Hello World!](https://www.acmicpc.net/problem/13140)

h, w, e, o, l, r, d 총 7개의 문자에 각각 0~9 사이의 수를 하나씩 배정한다. 서로 같은 수가 배정된 두 문자가 존재해서는 안되며 h, w는 가장 앞의 자릿수가 될 것이므로 0일 수 없다.

브루트포스로 모든 경우의 수를 따져도 약 10^7의 경우의 수가 된다. 굳이 backtracking을 사용하지 않고 7중 for문으로도 해결할 수 있다.

**틀린 코드**

```java
import java.io.*;

public class Main {
    private static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    
    private static int n;
    private final static boolean[] used = new boolean[10];
    
    public static void main(String[] args) throws IOException {
        n = Integer.parseInt(br.readLine());
        
        for(int d = 0; d < 10; d++) {
            used[d] = true;
            for(int e = 0; e < 10; e++) {
                if(used[e]) continue;
                used[e] = true;
                
                for(int h = 1; h < 10; h++) {
                    if(used[h]) continue;
                    used[h] = true;
                    
                    for(int l = 0; l < 10; l++) {
                        if(used[l]) continue;
                        used[l] = true;
                        
                        for(int o = 0; o < 10; o++) {
                            if(used[o]) continue;
                            used[o] = true;
                            
                            for(int r = 0; r < 10; r++) {
                                if(used[r]) continue;
                                
                                for(int w = 1; w < 10; w++) {
                                    if(used[w]) continue;
                                    
                                    if((h * 10_000 + e * 1_000 + l * 100 + l * 10 + o) + (w * 10_000 + o * 1_000 + r * 100 + l * 10 + d) == n) {
                                        print(d, e, h, l, o, r, w);
                                        return;
                                    }
                                    
                                    used[w] = false;
                                }   //  w-end
                                
                                used[r] = false;
                            }   //  r-end
                            
                            used[o] = false;
                        }   //  o-end
                        
                        used[l] = false;
                    }   //  l-end
                    
                    used[h] = false;
                }   //  h-end
                
                used[e] = false;
            }   //  e-end 
            
            used[d] = false;
        }   //  d-end
        
        System.out.println("No Answer");
    }    //   main-end
    
    private static void print(int d, int e, int h, int l, int o, int r, int w) {
        System.out.println("  " + h + e + l + l + o);
        System.out.println("+ " + w + o + r + l + d);
        System.out.println("-------");
        System.out.printf("%7d%n", n);
    }
}   //   Main-class-end
```

하지만 위의 코드는 틀렸다. used[w] = true 처리를 누락했기 때문이다. 바로 틀리는 것도 아니고 85%쯤 가서 틀려서 의심하기 어려웠고, 실제 시험이면 예제만 맞았다고 제출할 것이기 때문에 더욱 발견할 가능성이 낮다.

이렇게 단순하고 반복적인 코드 작성은 실수할 가능성이 높고, 그렇게 발생한 실수를 못 찾을 가능성은 더더욱 높다.

아무튼 위 코드를 맞게 고치면

**맞은 코드**

```java
import java.io.*;

public class Main {
    private static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    
    private static int n;
    private final static boolean[] used = new boolean[10];
    
    public static void main(String[] args) throws IOException {
        n = Integer.parseInt(br.readLine());
        
        for(int d = 0; d < 10; d++) {
            used[d] = true;
            for(int e = 0; e < 10; e++) {
                if(used[e]) continue;
                used[e] = true;
                
                for(int h = 1; h < 10; h++) {
                    if(used[h]) continue;
                    used[h] = true;
                    
                    for(int l = 0; l < 10; l++) {
                        if(used[l]) continue;
                        used[l] = true;
                        
                        for(int o = 0; o < 10; o++) {
                            if(used[o]) continue;
                            used[o] = true;
                            
                            for(int r = 0; r < 10; r++) {
                                if(used[r]) continue;
                                used[r] = true;
                                
                                for(int w = 1; w < 10; w++) {
                                    if(used[w]) continue;
                                    
                                    if((h * 10_000 + e * 1_000 + l * 100 + l * 10 + o) + (w * 10_000 + o * 1_000 + r * 100 + l * 10 + d) == n) {
                                        print(d, e, h, l, o, r, w);
                                        return;
                                    }
                                }   //  w-end
                                
                                used[r] = false;
                            }   //  r-end
                            
                            used[o] = false;
                        }   //  o-end
                        
                        used[l] = false;
                    }   //  l-end
                    
                    used[h] = false;
                }   //  h-end
                
                used[e] = false;
            }   //  e-end 
            
            used[d] = false;
        }   //  d-end
        
        System.out.println("No Answer");
    }    //   main-end
    
    private static void print(int d, int e, int h, int l, int o, int r, int w) {
        System.out.println("  " + h + e + l + l + o);
        System.out.println("+ " + w + o + r + l + d);
        System.out.println("-------");
        System.out.printf("%7d%n", n);
    }
}   //   Main-class-end
```

h, w, e, o, l, r, d  알파벳에 수 배정 순서는 관계가 없다. 테스트해봤다.

아무튼 위와 같은 반복적인 코드는 실수를 유발할 가능성이 매우 높으므로 우아하게 풀어보자.

**백트래킹 이용 코드**

```java
import java.io.*;

public class Main {
  private static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

  private static int n;
  private final static int[] nums = {-1, -1, -1, -1, -1, -1, -1}; //  각각 h, w, e, o, l, r, d에 배정될 수, 미배정 시 -1
  private final static boolean[] visited = new boolean[10];

  private static boolean find = false;    //    찾은 경우 true

  public static void main(String[] args) throws IOException {
    n = Integer.parseInt(br.readLine());

    dfs(0);

    if(!find)
      System.out.println("No Answer");
  }   //  main-end

  private static void dfs(int depth) {
    if(find)    //    이미 되는 경우를 찾은 경우
      return;

    if(nums[0] == 0 || nums[1] == 0)    //    탐색 시작하고 h 또는 w가 0일 경우 가지치기
      return;

    if(depth == 7) {    //    완성됐을 경우
      int hello = nums[0] * 10_000 + nums[2] * 1000 + nums[4] * 110 +nums[3];
      int world = nums[1] * 10_000 + nums[3] * 1000 + nums[5] * 100 + nums[4] * 10 + nums[6];

      if(hello + world == n) {
        find = true;

        System.out.printf("%7d\n", hello);
        System.out.printf("+%6d\n", world);
        System.out.println("-------");
        System.out.printf("%7d\n", n);
      }

      return;
    }

    for(int digit = 0; digit < 10; digit++) {
      if(!visited[digit]) {    //   아직 digit이 쓰이지 않았을 경우
        nums[depth] = digit;
        visited[digit] = true;
        dfs(depth + 1);
        visited[digit] = false;
      }
    }
  }
}   //  Main-class-end
```

탐색이 시작되기 전 각 알파벳을 -1로 초기화하고, 탐색이 시작된 후 h 또는 d에 0이 배정되면 탐색을 바로 중단하도록 구현했다. 만약 -1로 초기화하지 않으면 시작하자마자 h = 0, d = 0이어서 탐색이 중단되어 버린다. 반복되는 로직을 dfs로 구현하니 코드 양도 적어져서 실수를 빠르게 찾을 수 있다.