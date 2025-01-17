# 24_12_10_daily_certification

```
[#345 koreii] 데일리인증 20241210
1. Operating System
1-1. Paging
	- Page Table
	- Address Translation
	- TLB
1-2. Multilevel Page Table
1-3. Memory Protection
	- Protection bit
	- Valid(v) / Invalid(i) Bit
1-4. Inverted Page Table
1-5. Shared Page
2. 코딩 테스트 대비 알고리즘 학습
- Stack, Queue (LeetCode 232. Implement Queue using Stacks)
- Circular Queue (LeetCode 622. Design Circular Queue)
- Circular Deque (LeetCode 641. Design Circular Deque)
- Deque, Linked List (LeetCode 1670. Design Front Middle Back Queue)
```

```
[#345 koreii] 데일리인증 20241210
1. OS Paging
2. 코딩 테스트 대비 알고리즘 학습
- Stack, Queue (LeetCode 232. Implement Queue using Stacks)
- Circular Queue (LeetCode 622. Design Circular Queue)
- Circular Deque (LeetCode 641. Design Circular Deque)
- Deque, Linked List (LeetCode 1670. Design Front Middle Back Queue)
```

![image.png](24_12_10_daily_certification%20157154b2a3b880449225ca8cffcd2135/image.png)

![image.png](24_12_10_daily_certification%20157154b2a3b880449225ca8cffcd2135/image%201.png)

![image.png](24_12_10_daily_certification%20157154b2a3b880449225ca8cffcd2135/image%202.png)

![image.png](24_12_10_daily_certification%20157154b2a3b880449225ca8cffcd2135/image%203.png)

![image.png](24_12_10_daily_certification%20157154b2a3b880449225ca8cffcd2135/image%204.png)

![image.png](24_12_10_daily_certification%20157154b2a3b880449225ca8cffcd2135/image%205.png)

![image.png](24_12_10_daily_certification%20157154b2a3b880449225ca8cffcd2135/image%206.png)

![image.png](24_12_10_daily_certification%20157154b2a3b880449225ca8cffcd2135/image%207.png)

![image.png](24_12_10_daily_certification%20157154b2a3b880449225ca8cffcd2135/image%208.png)

![image.png](24_12_10_daily_certification%20157154b2a3b880449225ca8cffcd2135/image%209.png)

![image.png](24_12_10_daily_certification%20157154b2a3b880449225ca8cffcd2135/image%2010.png)

![image.png](24_12_10_daily_certification%20157154b2a3b880449225ca8cffcd2135/image%2011.png)

![image.png](24_12_10_daily_certification%20157154b2a3b880449225ca8cffcd2135/image%2012.png)

# Problem Solving (Algorithm & SQL)

### LeetCode **Implement Queue using Stacks**

[Implement Queue using Stacks - LeetCode](https://leetcode.com/problems/implement-queue-using-stacks/description/)

```java
class MyQueue {
    private final Stack<Integer> stack1;
    private final Stack<Integer> stack2;

    public MyQueue() {
        stack1 = new Stack<>();
        stack2 = new Stack<>();
    }
    
    public void push(int x) {
        stack1.push(x);
    }
    
    public int pop() {
        if(stack2.isEmpty()) {  //  stack2가 비었을 경우
            while(!stack1.isEmpty())    //  stack1이 빌 떄까지
                stack2.push(stack1.pop());  //  stack1의 원소를 stack2로 옮김
        }

        return stack2.pop();
    }
    
    public int peek() {
        if(stack2.isEmpty()) {  //  stack2가 비었을 경우
            while(!stack1.isEmpty())    //  stack1이 빌 떄까지
                stack2.push(stack1.pop());  //  stack1의 원소를 stack2로 옮김
        }

        return stack2.peek();
    }
    
    public boolean empty() {
        return stack1.isEmpty() && stack2.isEmpty();
    }
}

/**
 * Your MyQueue object will be instantiated and called as such:
 * MyQueue obj = new MyQueue();
 * obj.push(x);
 * int param_2 = obj.pop();
 * int param_3 = obj.peek();
 * boolean param_4 = obj.empty();
 */
```

### LeetCode **Design Circular Queue**

[Design Circular Queue - LeetCode](https://leetcode.com/problems/design-circular-queue/description/)

```java
class MyCircularQueue {
    private int[] queue;
    private int size;
    private int front;
    private int rear;

    public MyCircularQueue(int k) {
        size = k;
        queue = new int[size + 1];
        front = 0;
        rear = 0;
    }
    
    public boolean enQueue(int value) {
        if(isFull())
            return false;
        
        queue[rear] = value;
        rear = (rear + 1) % (size + 1);
        return true;
    }
    
    public boolean deQueue() {
        if(isEmpty())
            return false;
        
        front = (front + 1) % (size + 1);
        return true;
    }
    
    public int Front() {
        return isEmpty() ? -1 : queue[front];
    }
    
    public int Rear() {
        if(isEmpty())
            return -1;

        if(rear == 0)
            return queue[size];

        return queue[rear - 1];
    }
    
    public boolean isEmpty() {
        return front == rear;
    }
    
    public boolean isFull() {
        return (rear + 1) % (size + 1) == front;
    }
}

/**
 * Your MyCircularQueue object will be instantiated and called as such:
 * MyCircularQueue obj = new MyCircularQueue(k);
 * boolean param_1 = obj.enQueue(value);
 * boolean param_2 = obj.deQueue();
 * int param_3 = obj.Front();
 * int param_4 = obj.Rear();
 * boolean param_5 = obj.isEmpty();
 * boolean param_6 = obj.isFull();
 */
```

### LeetCode **Design Circular Deque**

[Design Circular Deque - LeetCode](https://leetcode.com/problems/design-circular-deque/description/)

```java
class MyCircularDeque {
    private int[] deque;
    private int size;
    private int front;
    private int rear;
    private int cnt;

    public MyCircularDeque(int k) {
        size = k;
        deque = new int[size];
        front = 1;  //  front가 rear보다 한 칸 앞선 상태로 시작
        rear = 0;
        cnt = 0;
    }
    
    public boolean insertFront(int value) {
        if(isFull())
            return false;
        
        cnt++;
        front = (front - 1 + size) % size;
        deque[front] = value;
        return true;
    }
    
    public boolean insertLast(int value) {
        if(isFull())
            return false;
        
        cnt++;
        rear = (rear + 1) % size;
        deque[rear] = value;
        return true;
    }
    
    public boolean deleteFront() {
        if(isEmpty())
            return false;

        cnt--;
        front = (front + 1) % size;
        return true;
    }
    
    public boolean deleteLast() {
        if(isEmpty())
            return false;

        cnt--;
        rear = (rear - 1 + size) % size;
        return true;
    }
    
    public int getFront() {
        return isEmpty() ? -1 : deque[front];
    }
    
    public int getRear() {
        return isEmpty() ? -1 : deque[rear];
    }
    
    public boolean isEmpty() {
        return cnt == 0;
    }
    
    public boolean isFull() {
        return cnt == size;
    }
}

/**
 * Your MyCircularDeque object will be instantiated and called as such:
 * MyCircularDeque obj = new MyCircularDeque(k);
 * boolean param_1 = obj.insertFront(value);
 * boolean param_2 = obj.insertLast(value);
 * boolean param_3 = obj.deleteFront();
 * boolean param_4 = obj.deleteLast();
 * int param_5 = obj.getFront();
 * int param_6 = obj.getRear();
 * boolean param_7 = obj.isEmpty();
 * boolean param_8 = obj.isFull();
 */
```

### LeetCode **Design Front Middle Back Queue**

[Design Front Middle Back Queue - LeetCode](https://leetcode.com/problems/design-front-middle-back-queue/)

```java
class FrontMiddleBackQueue {
    private final Deque<Integer> left;
    private final Deque<Integer> right;

    public FrontMiddleBackQueue() {
        left = new LinkedList<>();
        right = new LinkedList<>();
    }
    
    public void pushFront(int val) {
        left.offerFirst(val);

        //  left의 원소 개수가 right의 원소 개수보다 최대 1개 많도록 유지
        if(left.size() > right.size() + 1)  //  left의 원소가 right의 원소보다 2개 많을 경우
            right.offerFirst(left.pollLast());  //  left의 마지막 원소를 떼서 right의 처음에 넣어줌
    }
    
    public void pushMiddle(int val) {
        if(left.size() == right.size()) //  left, right 크기가 같을 경우
            left.offerLast(val);    //  left가 최대 1개 더 많게 유지
        else {// left가 1개 더 많은 경우
            right.offerFirst(left.pollLast());  //  left의 마지막 값을 빼서 right의 처음에 삽입
            left.offerLast(val);
        }
    }
    
    public void pushBack(int val) {
        right.offerLast(val);

        //  right의 원소 개수가 left의 원소 개수보다 많지 않도록 유지
        if(left.size() < right.size())  //  right의 원소가 left의 원소보다 1개 많을 경우
            left.offerLast(right.pollFirst());  //  right의 첫번째 원소를 떼서 left의 마지막에 넣어줌
    }
    
    public int popFront() {
        if(left.isEmpty())
            return -1;

        int res = left.pollFirst();

        if(left.size() < right.size())  //  right의 원소가 left의 원소보다 1개 많을 경우
            left.offerLast(right.pollFirst());

        return res;
    }
    
    public int popMiddle() {
        if(left.isEmpty())
            return -1;

        int res = left.pollLast();

        if(left.size() < right.size())  //  right의 원소가 left의 원소보다 1개 많을 경우
            left.offerLast(right.pollFirst());

        return res;
    }
    
    public int popBack() {
        int res = -1;

        if(!right.isEmpty())
            res = right.pollLast();
        else if(!left.isEmpty())
            res = left.pollLast();

        if(right.size() + 1 < left.size())  //  left가 right보다 2개 많을 경우
            right.offerFirst(left.pollLast());

        return res;
    }
}

/**
 * Your FrontMiddleBackQueue object will be instantiated and called as such:
 * FrontMiddleBackQueue obj = new FrontMiddleBackQueue();
 * obj.pushFront(val);
 * obj.pushMiddle(val);
 * obj.pushBack(val);
 * int param_4 = obj.popFront();
 * int param_5 = obj.popMiddle();
 * int param_6 = obj.popBack();
 */
```