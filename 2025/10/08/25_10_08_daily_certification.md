# 25_10_08_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 16209 수열 섞기

[16209번: 수열 섞기](http://boj.ma/16209/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.Collections;
import java.util.Deque;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;

	private static final List<Integer> posList = new ArrayList<>();
	private static final List<Integer> negList = new ArrayList<>();
	private static int zeroCnt = 0; // 주어진 수열에 포함된 0의 개수

	private static final Deque<Integer> posDeque = new ArrayDeque<>(); // 양수 덱
	private static final Deque<Integer> negDeque = new ArrayDeque<>(); // 음수 덱

	private static int N;

	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());

		st = new StringTokenizer(br.readLine());
		for (int i = 0; i < N; i++) {
			int num = Integer.parseInt(st.nextToken());

			if (num > 0)
				posList.add(num);
			else if (num == 0)
				zeroCnt++;
			else
				negList.add(num);
		}

		Collections.sort(posList, Collections.reverseOrder()); // 양수 리스트 내림차순 정렬
		Collections.sort(negList); // 음수 리스트 오름차순 정렬 (절댓값 기준 내림차순 정렬)

		//	양수 덱에 양수를 내림차순으로 앞뒤 번갈아가며 넣기
		for (int i = 0; i < posList.size(); i++) {
			int pos = posList.get(i);

			if (i % 2 == 0)
				posDeque.offerFirst(pos);
			else
				posDeque.offerLast(pos);
		}
		// 	음수 덱에 음수를 오름차순으로 앞뒤 번갈아가며 넣기
		for (int i = 0; i < negList.size(); i++) {
			int neg = negList.get(i);

			if (i % 2 == 0)
				negDeque.offerFirst(neg);
			else
				negDeque.offerLast(neg);
		}

		if (zeroCnt > 0) { // 수열에 0이 있을 경우, 양수 부분 + 0 부분 + 음수 부분 / 음수 부분 + 0 부분 + 양수 부분 으로 두면 최대
			while (!posDeque.isEmpty())
				sb.append(posDeque.pollFirst()).append(" ");
			while (zeroCnt > 0) {
				sb.append("0 ");
				zeroCnt--;
			}
			while (!negDeque.isEmpty())
				sb.append(negDeque.pollFirst()).append(" ");
		} else { // 수열에 0이 없을 경우
			if (posDeque.isEmpty()) { // 음수만 존재할 경우
				while (!negDeque.isEmpty())
					sb.append(negDeque.pollFirst()).append(" ");
			} else if (negDeque.isEmpty()) { // 양수만 존재할 경우
				while (!posDeque.isEmpty())
					sb.append(posDeque.pollFirst()).append(" ");
			} else { // 양수, 음수 모두 존재할 경우, 양수와 음수가 맞닿아 곱이 음수가 됨, 그 음수가 최대가 되게 (0에 가깝게)
				boolean posFront = false;	//	양수 부분의 가장 앞의 수가 가장 작을 경우 true, 가장 뒤의 수가 가장 작을 경우 false
				boolean negFront = false;	//	음수 부분의 가장 앞의 수가 가장 클 경우 true, 가장 뒤의 수가 가장 클 경우 false

				if (posDeque.peekFirst() < posDeque.peekLast()) // 양수 부분의 가장 앞의 수가 가장 작을 경우
					posFront = true;
				if (negDeque.peekFirst() > negDeque.peekLast()) // 음수 부분의 가장 앞의 수가 가장 클 경우
					negFront = true;

				if (posFront && negFront) {	//	앞-앞이 맞닿아야 할 경우
					while (!negDeque.isEmpty()) // 음수 수열 뒤집어서 앞에 두기
						sb.append(negDeque.pollLast()).append(" ");
					while (!posDeque.isEmpty())
						sb.append(posDeque.pollFirst()).append(" ");
				} else if (!posFront && !negFront) {	//	뒤-뒤가 맞닿아야 할 경우
					while (!negDeque.isEmpty())
						sb.append(negDeque.pollFirst()).append(" ");
					while (!posDeque.isEmpty()) // 양수 수열 뒤집어서 앞에 두기
						sb.append(posDeque.pollLast()).append(" ");
				} else if (posFront) {	//	음수 수열 뒤-양수 수열 앞이 맞닿아야 할 경우
					while (!negDeque.isEmpty())
						sb.append(negDeque.pollFirst()).append(" ");
					while (!posDeque.isEmpty())
						sb.append(posDeque.pollFirst()).append(" ");
				} else {	//	양수 수열 뒤-음수 수열 앞이 맞닿아야 할 경우
					while (!posDeque.isEmpty())
						sb.append(posDeque.pollFirst()).append(" ");
					while (!negDeque.isEmpty())
						sb.append(negDeque.pollFirst()).append(" ");
				}
			}
		}

		System.out.print(sb);
	} // main-end
} // Main-class-end
```