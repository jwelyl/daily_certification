# 24_02_11_daily_certification

# JPA

### Embedded 값 타입 equals, hashCode 오버라이딩 시 주의 사항

```java
@Embeddable
public class Address {
  private String city;

  private String street;

  private String zipcode;

  /* field에 직접 접근하지 않고 getter로 접근해야 proxy일 때도 문제가 없음 */
  @Override
  public boolean equals(Object o) {
    if (this == o) {
      return true;
    }
    if (o == null || getClass() != o.getClass()) {
      return false;
    }
    Address address = (Address) o;
    return Objects.equals(getCity(), address.getCity()) && Objects.equals(getStreet(), address.getStreet()) && Objects.equals(getZipcode(), address.getZipcode());
  }

  @Override
  public int hashCode() {
    return Objects.hash(getCity(), getStreet(), getZipcode());
  }

	/* getter, private setter */
}
```

동등성 비교를 위해 equals와 hashCode를 오버라이딩해야 하는데 기본적으로 IDE(Intellij)가 제공해주는 @Override 메서드를 사용하는 것이 좋다.

또한 필드에 직접 접근하지 않고, getter를 사용해 접근해야 한다. 필드에 직접 접근할 경우 proxy일 때 값이 가져와지지 않는 문제가 발생한다.

### Embedded 값 타입의 장점

**column 공통 관리**

```java
@Embeddable
public class Address {
  @Column(length = 10)
  private String city;

  @Column(length = 20)
  private String street;

  @Column(length = 5)
  private String zipcode;

 /* getter, private setter, equals, hashCode */
}
```

city, street, zipcode 같은 column 속성이 Member, Delivery마다 따로 존재하면 이를 공통적으로 관리하기 어렵다. 예를 들어 city의 길이를 10 이하로 해야 할 때 일일이 제약 조건을 걸어줘야 한다.

하지만 Address로 관리하면 Address에만 제약 조건을 걸어주면 Member, Delivery의 column에도 자동적으로 제약이 적용된다.

![Untitled](24_02_11_daily_certification%206d97a7b9e51d4e18afb34878a683d292/Untitled.png)

![Untitled](24_02_11_daily_certification%206d97a7b9e51d4e18afb34878a683d292/Untitled%201.png)

**유용한 메서드 사용 가능**

```java
@Embeddable
public class Address {
  @Column(length = 10)
  private String city;

  @Column(length = 20)
  private String street;

  @Column(length = 5)
  private String zipcode;

  public String fullAddress() {
    return getCity() + " " + getStreet() + " " + getZipcode();
  }

  public boolean isValid() {
    return getCity() != null && getStreet() != null && getZipcode() != null;
  }

	/* getter, private setter, equals, hashCode */
}
```

주소가 유효한지, 전체 주소는 어떤지 등 유용한 메서드를 추가해서 Address를 사용하는 모든 객체에서 사용할 수 있다.

# Problem Solving (Algorithm & SQL)

**BOJ 3745 오름세**

[3745번: 오름세](https://www.acmicpc.net/problem/3745)

O(nlogn)에 LIS를 찾는 문제이다.

**코드**

```kotlin
import java.util.*
import java.io.*
import kotlin.collections.ArrayList

class BOJ_1365 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0

    private val lis = ArrayList<Int>()

    fun solve() {
        while(true) {
            val input = br.readLine()

            if(input == null) {
                print(sb)
                break
            }

            n = input.trim().toInt()

            lis.clear()
            lis.add(Int.MIN_VALUE)

            tokens = StringTokenizer(br.readLine())
            for(i in 0 ..< n) {
                val num = tokens.nextToken().toInt()

                if (num > lis[lis.size - 1]) //  lis에 저장된 마지막 수보다 클 경우 LIS에 추가, 길이 1 증가
                    lis.add(num)
                else {  //  num과 가장 가깝지만 더 큰 값 찾아서 대치시킴
                    val pos = binarySearch(num)

                    lis[pos] = num
                }
            }

            sb.append("${lis.size - 1}\n")
        }
    }

    //  크기 순으로 num 다음 수의 index 직전 index 반환
    private fun binarySearch(num : Int) : Int {
        var start = 0
        var end = lis.size - 1

        while(start < end) {
            val mid = (start + end) / 2

            if(lis[mid] < num)  //  num이 중간 수보다 더 클 경우 오른쪽에서 찾기
                start = mid + 1
            else    //  num이 중간 수보다 더 작거나 같을 경우
                end = mid
        }

        return end
    }
}

fun main() {
    BOJ_1365().solve()
}
```