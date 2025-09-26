# 25_09_26_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1329 증가 수열

[1329번: 증가 수열](http://boj.ma/1329/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.Deque;
import java.util.List;

public class Main {
	private static final int MAX = 81; // 항의 최대 개수 = 80

	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();

	private static char[] input;

	// terms[i] : i번째 항 {startIdx, endIdx}
	private static final int[][] terms = new int[MAX][2];

	private static int[] minTerm; // 가능한 마지막 항 중 최소
	private static List<int[]> minSequence = new ArrayList<>();

	public static void main(String[] args) throws IOException {
		input = br.readLine().toCharArray();
		minTerm = new int[] { 0, input.length - 1 };
		minSequence.add(new int[] { 0, input.length - 1 });

		backtracking(0, 0, new ArrayDeque<>());

		for (int i = 0; i < minSequence.size(); i++) {
			int[] term = minSequence.get(i);
			sb.append(getTerm(term));

			if (i < minSequence.size() - 1)
				sb.append(",");
		}

		System.out.println(sb);
	} // main-end

	private static void backtracking(int nth, int idx, Deque<int[]> sequence) {
		if (idx == input.length) { // 더 이상 항을 만들 수 없을 경우
			int cmp = compare(terms[nth - 1], minTerm);

			if (cmp < 0) {
				// 마지막 항이 더 작은 경우 → 교체
				minTerm[0] = terms[nth - 1][0];
				minTerm[1] = terms[nth - 1][1];
				minSequence = deepCopy(sequence);
			} else if (cmp == 0) {
				// 마지막 항이 같은 경우 → tie-break
				if (isBetter(sequence)) {
					minTerm[0] = terms[nth - 1][0];
					minTerm[1] = terms[nth - 1][1];
					minSequence = deepCopy(sequence);
				}
			}
			return;
		}

		if (nth == 0) { // 초항일 경우, 이전 항과 비교 절차 없음
			for (int end = idx; end < input.length; end++) {
				terms[nth][0] = idx;
				terms[nth][1] = end;
				terms[nth + 1][0] = end + 1;

				sequence.offerLast(new int[] { terms[nth][0], terms[nth][1] });
				backtracking(nth + 1, end + 1, sequence);
				sequence.pollLast();
			}
		} else { // 초항이 아닐 경우
			boolean smaller = true; // 현재 항이 이전 항보다 작을 경우

			for (int end = idx; end < input.length; end++) {
				terms[nth][1] = end;

				if (smaller && compare(terms[nth - 1], terms[nth]) >= 0)
					continue;
				if (compare(minTerm, terms[nth]) < 0)
					return;

				smaller = false; // 현재 항은 더 커지기만 하므로 이후로는 확인 불필요
				terms[nth + 1][0] = end + 1;

				sequence.offerLast(new int[] { terms[nth][0], terms[nth][1] });
				backtracking(nth + 1, end + 1, sequence);
				sequence.pollLast();
			}
		}
	}

	private static String getTerm(int[] term) {
		StringBuilder res = new StringBuilder();
		int s = term[0];
		int e = term[1];

		for (int i = s; i <= e; i++)
			res.append(input[i]);

		return res.toString();
	}

	// term1 < term2일 경우 -1, term1 == term2일 경우 0, 그 외에는 1
	private static int compare(int[] term1, int[] term2) {
		int s1 = term1[0];
		int e1 = term1[1];
		int s2 = term2[0];
		int e2 = term2[1];

		// leading zero 제거
		while (s1 <= e1 && input[s1] == '0')
			s1++;
		while (s2 <= e2 && input[s2] == '0')
			s2++;

		int len1 = e1 - s1 + 1;
		int len2 = e2 - s2 + 1;

		if (len1 < len2)
			return -1;
		else if (len1 > len2)
			return 1;

		for (int i = 0; i < len1; i++) {
			int digit1 = input[s1 + i] - '0';
			int digit2 = input[s2 + i] - '0';

			if (digit1 != digit2)
				return digit1 < digit2 ? -1 : 1;
		}

		return 0;
	}

	// sequence 복사 (deep copy)
	private static List<int[]> deepCopy(Deque<int[]> seq) {
		List<int[]> copied = new ArrayList<>();
		for (int[] term : seq)
			copied.add(new int[] { term[0], term[1] });
		
		return copied;
	}
	
	//	마지막 항이 같을 경우 어떤 수열이 더 적합한지 비교
	//	이미 알려진 수열보다 더 적합할 경우 true
	private static boolean isBetter(Deque<int[]> candidate) {
		int candSize = candidate.size();		//	후보 수열의 크기
		int minSeqSize = minSequence.size();	//	이미 알려진 수열의 크기
		int size = Math.min(candSize, minSeqSize);
		
		int idx = 0;
		
		for(int[] candTerm : candidate) {
			if(idx == size)
				break;
			
			int[] term = minSequence.get(idx);
			
			int comp = compare(candTerm, term);	//	항 비교 결과 
			if(comp > 0)		//	앞에서부터 비교했을때 후보 수열이 더 클 경우
				return true;	//	수열 교체
			else if(comp < 0)	//	후보 수열이 더 작을 경우
				return false;	//	교체 x
			
			idx++;	//	아직까지 tie
		}
		
		//	앞의 공통부분이 완전히 같을 경우, 길이가 긴 수열이 중간 항이 더 큼
		return candSize > minSeqSize;
	}
} // Main-class-end
```