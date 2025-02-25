# 25_02_26_daily_certification

# Problem Solving (Algorithm & SQL)

### LeetCode 4Sum

[4Sum - LeetCode](https://leetcode.com/problems/4sum/)

```java
class Solution {
    public List<List<Integer>> fourSum(int[] nums, int target) {
        List<List<Integer>> res = new ArrayList<>();
        Set<String> set = new HashSet<>();
        int len = nums.length;

        Arrays.sort(nums);

        for(int a = 0; a < len - 3; a++) {
            for(int b = a + 1; b < len - 2; b++) {
                for(int c = b + 1; c < len - 1; c++) {
                    long sum3 = (long)nums[a] + nums[b] + nums[c];
                    long find = (long)target - sum3;

                    if(find < nums[c] || find > nums[len - 1])
                        continue;

                    int idx = binarySearch(nums, c + 1, len - 1, find);

                    if(idx != -1) {
                        List<Integer> list = new ArrayList<>();
                        StringBuilder key = new StringBuilder();
                        list.add(nums[a]);
                        key.append(nums[a]).append("@");
                        list.add(nums[b]);
                        key.append(nums[b]).append("@");
                        list.add(nums[c]);
                        key.append(nums[c]).append("@");
                        list.add(nums[idx]);
                        key.append(nums[idx]).append("@");

                        if(!set.contains(key.toString())) {
                            set.add(key.toString());
                            res.add(list);
                        }
                    }
                }
            }
        }    

        return res;
    }

    //  nums의 [start, end]에서 target의 위치, target이 존재하지 않으면 -1 반환
    private int binarySearch(int[] nums, int start, int end, long target) {
        while(start <= end) {
            int mid = (start + end) / 2;
            long value = nums[mid];

            if(value == target)
                return mid;
            if(value < target)      //  더 오른쪽에서 찾아야 하는 경우
                start = mid + 1;
            else if(value > target) //  더 왼쪽에서 찾아야 하는 경우
                end = mid - 1;
        }

        return -1;
    }
}
```