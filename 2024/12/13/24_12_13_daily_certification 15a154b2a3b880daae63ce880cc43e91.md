# 24_12_13_daily_certification

```
[#347 koreii] 데일리인증 20241212
1. 코딩 테스트 대비 알고리즘 학습
- Bruteforcing, Binary Search, HashMap (LeetCode 1. Two Sum)
```

# Problem Solving (Algorithm & SQL)

### LeetCode Two Sum

[Two Sum - LeetCode](https://leetcode.com/problems/two-sum/description/)

**Bruteforcing**

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        for(int i1 = 0; i1 < nums.length - 1; i1++) {
            for(int i2 = i1 + 1; i2 < nums.length; i2++) {
                if(nums[i1] + nums[i2] == target)
                    return new int[] {i1, i2};
            }
        }
        
        return null;
    }
}
```

**Sorting + Binary Search**

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        Number[] numbers = new Number[nums.length];
        for(int i = 0; i < nums.length; i++)
            numbers[i] = new Number(nums[i], i);

        Arrays.sort(numbers);

        for(int i = 0; i < numbers.length - 1; i++) {
            int idx = binarySearch(i + 1, nums.length - 1, numbers, target - numbers[i].number);

            if(idx != -1)
                return new int[] {numbers[i].idx, idx};
        }

        return null;
    }

    private int binarySearch(int start, int end, Number[] numbers, int target) {
        while(start <= end) {
            int mid = (start + end) / 2;

            if(numbers[mid].number == target)
                return numbers[mid].idx;

            if(numbers[mid].number < target)
                start = mid + 1;
            else
                end = mid - 1;
        }
        
        return -1;
    }
}

class Number implements Comparable<Number> {
    public int number;
    public int idx;

    public Number(int number, int idx) {
        this.number = number;
        this.idx = idx;
    }

    @Override
    public int compareTo(Number other) {
        return Integer.compare(this.number, other.number);
    }
}
```

**HashMap**

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> map = new HashMap<>();
        
        for(int i = 0; i < nums.length; i++) {
            int num = nums[i];

            int idx = map.getOrDefault(target - num, -1);

            if(idx != -1)
                return new int[] {idx, i};
        
            map.put(num, i);
        }
            
        return null;
    }
}
```