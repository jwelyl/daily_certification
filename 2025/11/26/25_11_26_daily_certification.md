# 25_11_26_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 10840 구간 성분

[10840번: 구간 성분](http://boj.ma/10840/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.HashSet;
import java.util.Set;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    char[] text1 = br.readLine().toCharArray();
    char[] text2 = br.readLine().toCharArray();
    int len1 = text1.length;
    int len2 = text2.length;

    int[][] prefixSum1 = new int[26][len1 + 1];
    int[][] prefixSum2 = new int[26][len2 + 1];
    Set<Segment> set = new HashSet<>();
    int answer = 0;

    for(int i = 1; i <= len1; i++) {
      for(char ch = 'a'; ch <= 'z'; ch++) {
        if(text1[i - 1] == ch)
          prefixSum1[ch - 'a'][i] = prefixSum1[ch - 'a'][i - 1] + 1;
        else
          prefixSum1[ch - 'a'][i] = prefixSum1[ch - 'a'][i - 1];
      }
    }
    for(int i = 1; i <= len2; i++) {
      for(char ch = 'a'; ch <= 'z'; ch++) {
        if(text2[i - 1] == ch)
          prefixSum2[ch - 'a'][i] = prefixSum2[ch - 'a'][i - 1] + 1;
        else
          prefixSum2[ch - 'a'][i] = prefixSum2[ch - 'a'][i - 1];
      }
    }

    for(int from = 1; from <= len1; from++) {
      for(int to = from; to <= len1; to++) {
        Segment segment = new Segment();
        for(int i = 0; i < 26; i++)
          segment.counts[i] = (prefixSum1[i][to] - prefixSum1[i][from - 1]);
        set.add(segment);
      }
    }

    for(int from = 1; from <= len2; from++) {
      for(int to = from; to <= len2; to++) {
        Segment segment = new Segment();
        for(int i = 0; i < 26; i++)
          segment.counts[i] = (prefixSum2[i][to] - prefixSum2[i][from - 1]);
        if(set.contains(segment))
          answer = Math.max(answer, to - from + 1);
      }
    }

    System.out.println(answer);
  }    //    main-end

  private static class Segment {
    public final int[] counts = new int[26];

    @Override
    public boolean equals(Object obj) {
      if(obj == this) return true;
      if(obj == null || obj.getClass() != this.getClass()) return false;

      Segment other = (Segment) obj;
      for(int i = 0; i < counts.length; i++)
        if(counts[i] != other.counts[i]) return false;

      return true;
    }

    @Override
    public int hashCode() {
      int hash = 0;
      for(int count : counts)
        hash = hash * 31 + count;
      return hash;
    }
  }
}    //    Main-class-end
```