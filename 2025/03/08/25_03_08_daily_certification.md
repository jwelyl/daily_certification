# 25_03_08_daily_certification

# Problem Solving (Algorithm & SQL)

### 프로그래머스 표현 가능한 이진트리

[](https://school.programmers.co.kr/learn/courses/30/lessons/150367#)

```java
class Solution {
    private int ok = 1;
    
    //  가능한 full binary tree 문자열 길이
    private final int[] FBT_LENS = {1, 3, 7, 15, 31, 63, 127, 255};
    
    public int[] solution(long[] numbers) {
        int[] answer = new int[numbers.length];
        
        for(int i = 0; i < numbers.length; i++) {
            long number = numbers[i];
            
            ok = 1;
            char[] fbt = getFullBinTree(number);
            
            preorder(0, fbt.length - 1, false, fbt);
        
            answer[i] = ok;
        }
        
        return answer;
    }
    
    //  pDummy : 부모 노드가 더미 노드일 경우 true
    private void preorder(int start, int end, boolean pDummy, char[] fbt) {
        if(ok == 0)
            return;
        
        int mid = (start + end) / 2;
        
        if(pDummy && fbt[mid] == '1') {   //  부모 노드가 더미 노드인데 현재 노드가 더미 노드가 아닐 경우, 불가능함
            ok = 0;
            return;
        }
        
        if(start == end)
            return;
        
        preorder(start, mid - 1, fbt[mid] == '0', fbt);
        preorder(mid + 1, end, fbt[mid] == '0', fbt);
    }
    
    private char[] getFullBinTree(long number) {
        StringBuilder sb = new StringBuilder();
        String binNum = Long.toBinaryString(number);
        int len = binNum.length();
        int fbtLen = 0;

        for(int l : FBT_LENS) {
            if(len <= l) {
                fbtLen = l;
                break;
            }
        }

        for(int i = 0; i < fbtLen - len; i++)
            sb.append("0");
        sb.append(binNum);
        
        return sb.toString().toCharArray();
    }
}
```