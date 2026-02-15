# 26_02_16_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1036 36진수

[1036번: 36진수](http://boj.ma/1036/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.math.BigInteger;
import java.util.ArrayList;
import java.util.Collections;
import java.util.HashSet;
import java.util.List;
import java.util.Set;

public class Main {
  private static final char MAX = 'Z';

  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    int n = Integer.parseInt(br.readLine());
    int k;

    String[] num36s = new String[n];
    BigInteger sum = BigInteger.ZERO;
    List<Profit> profitList = new ArrayList<>();
    Set<Character> zSet = new HashSet<>();
    BigInteger answer = BigInteger.ZERO;

    for(int i = 0; i < n; i++) {
      num36s[i] = br.readLine();
      sum = sum.add(convert36ToDecimal(num36s[i]));
    }

    k = Integer.parseInt(br.readLine());

    for(int digit = 0; digit < 36; digit++) {
      BigInteger increasedSum = BigInteger.ZERO;
      BigInteger profit;
      char ch = digit <= 9 ? (char) ('0' + digit) : (char) ('A' + digit - 10);

      for(int i = 0; i < n; i++) {
        char[] changed = num36s[i].toCharArray();
        for(int j = 0; j < changed.length; j++) {
          if(changed[j] == ch)
            changed[j] = MAX;
        }

        increasedSum = increasedSum.add(convert36ToDecimal(new String(changed)));
      }

      profit = increasedSum.subtract(sum);
      profitList.add(new Profit(ch, profit));
    }

    Collections.sort(profitList);

    for(Profit profit : profitList) {
      if(zSet.size() == k)
        break;

      zSet.add(profit.change);
    }

    for(int i = 0; i < n; i++) {
      char[] changed = num36s[i].toCharArray();
      for(int j = 0; j < changed.length; j++) {
        if(zSet.contains(changed[j]))
          changed[j] = MAX;
      }

      answer = answer.add(convert36ToDecimal(new String(changed)));
    }

    System.out.println(answer.toString(36).toUpperCase());
  } //  main-end

  private static BigInteger convert36ToDecimal(String radix36) {
    BigInteger result = BigInteger.ZERO;
    BigInteger mult = BigInteger.ONE;

    for(int i = radix36.length() - 1; i >= 0; i--) {
      char digit = radix36.charAt(i);
      int num = '0' <= digit && digit <= '9' ? digit - '0' : digit - 'A' + 10;

      result = result.add(mult.multiply(BigInteger.valueOf(num)));
      mult = mult.multiply(new BigInteger("36"));
    }

    return result;
  }

  private static class Profit implements Comparable<Profit> {
    public char change;
    public BigInteger profit;

    public Profit(char change, BigInteger profit) {
      this.change = change;
      this.profit = profit;
    }

    @Override
    public int compareTo(Profit other) {
      return other.profit.compareTo(this.profit);
    }
  }
} //  Main-class-end

```