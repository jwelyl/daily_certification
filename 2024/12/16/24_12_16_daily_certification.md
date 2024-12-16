# 24_12_16_daily_certification

### LeetCode 3Sum

[3Sum - LeetCode](https://leetcode.com/problems/3sum/description/)

**Binary Search**

```java
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        Set<List<Integer>> set = new HashSet<>();

        Arrays.sort(nums);
        for(int i1 = 0; i1 < nums.length - 2; i1++) {
            for(int i2 = i1 + 1; i2 < nums.length - 1; i2++) {
                int target = -nums[i1] - nums[i2];

                int idx = binarySearch(nums, i2 + 1, nums.length - 1, target);

                if(idx != -1) //  target이 존재할 경우
                    set.add(Arrays.asList(nums[i1], nums[i2], target));
            }
        }

        return new ArrayList<>(set);
    }

    private int binarySearch(int[] nums, int start, int end, int target) {
        while(start <= end) {
            int mid = (start + end) / 2;

            if(nums[mid] == target)
                return mid;

            if(nums[mid] < target)
                start = mid + 1;
            else
                end = mid - 1;
        }

        return -1;
    }
}
```

**Two-Pointer**

```java
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        Set<List<Integer>> set = new HashSet<>();

        Arrays.sort(nums);

        for(int i = 0; i < nums.length - 2; i++) {
            int pivot = nums[i];

            int start = i + 1;
            int end = nums.length - 1;

            while(start < end) {
                int sum2 = nums[start] + nums[end];

                if(sum2 == -pivot) {
                    set.add(Arrays.asList(pivot, nums[start], nums[end]));

                    start++;
                    end--;
                }
                else if(sum2 < -pivot)
                    start++;
                else
                    end--;
            }
        }
        

        return new ArrayList<>(set);
    }
}
```