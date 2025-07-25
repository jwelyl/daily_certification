# 25_07_26_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 16637 괄호 추가하기

[16637번: 괄호 추가하기](http://boj.ma/16637/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	
	private static int N;
	private static char[] expression;
	
	private static int answer = Integer.MIN_VALUE;
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		expression = br.readLine().toCharArray();
	
		bruteforcing(1, expression[0] - '0');
		
		System.out.println(answer);
	}	//	main-end
	
	private static void bruteforcing(int opIdx, int calcRes) {
		if(opIdx >= N) {
			answer = Math.max(answer, calcRes);
			return;
		}
		
		//	opIdx의 연산자 중심으로 좌우 계산 결과
		int nCalc = calc(calcRes, expression[opIdx], expression[opIdx + 1] - '0');
		//	다음 연산자(opIdx + 2번째 연산자) 좌우로 계산 결과 찾기
		bruteforcing(opIdx + 2, nCalc);
	
		if(opIdx + 2 < N) {	//	다음 연산자가 있을 경우
			//	다음 연산자(opIdx + 2번째 연산자) 중심으로 좌우 계산 결과
			int right = calc(expression[opIdx + 1] - '0', expression[opIdx + 2], expression[opIdx + 3] - '0');
			//	calcRes와 right 사이 계산
			nCalc = calc(calcRes, expression[opIdx], right);
			bruteforcing(opIdx + 4, nCalc);
		}
	}
	
	private static int calc(int num1, char op, int num2) {
		int res = num1;
		
		switch(op) {
		case '+':
			res += num2;
			break;
		case '-':
			res -= num2;
			break;
		default:
			res *= num2;
		}
		
		return res;
	}
}	//	Main-class-end
```

### BOJ 16638 괄호 추가하기 2

[16638번: 괄호 추가하기 2](http://boj.ma/16638/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;

public class Main {
	private static final char OPERAND = '0';
	private static final int OPERATOR = Integer.MAX_VALUE;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	
	private static int N;
	private static char[] expression;
	
	private static int answer = Integer.MIN_VALUE;
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		expression = br.readLine().toCharArray();
	
		if(N == 1) {
			System.out.println(expression[0] - '0');
			return;
		}
		
		bruteforcing(1, new boolean[N]);
		
		System.out.println(answer);
	}	//	main-end
	
	private static void bruteforcing(int opIdx, boolean[] chunked) {
		if(opIdx >= N) {
			List<OP> reducedP = new ArrayList<>();	//	괄호 처리된 결과
			
			for(int i = 1; i < N; i += 2) {
				char operator = expression[i];
				
				if(chunked[i]) {	//	operator을 중심으로 괄호로 묶였을 경우
					int lOperand = expression[i - 1] - '0';	//	operator 기준 왼쪽 피연산자
					int rOperand = expression[i + 1] - '0';	//	operator 기준 오른쪽 피연산자
					int calc = calc(lOperand, operator, rOperand);
					
					reducedP.add(new OP(calc, OPERAND));
				}
				else {
					if(i == 1 || !chunked[i - 2])
						reducedP.add(new OP(expression[i - 1] - '0', OPERAND));
					reducedP.add(new OP(OPERATOR, expression[i]));
				}
			}
			
			if(!chunked[N - 2])
				reducedP.add(new OP(expression[N - 1] - '0', OPERAND));
			
			answer = Math.max(answer, calcReducedExpression(reducedP));
			
			return;
		}
		
		//	opIdx번째 연산자 중심으로 괄호 안묶을 경우
		chunked[opIdx] = false;
		bruteforcing(opIdx + 2, chunked);
		
		//	opIdx번째 연산자 중심으로 괄호 묶을 경우
		chunked[opIdx] = true;
		if(opIdx + 2 < N)	//	그 다음 연산자가 있을 경우
			chunked[opIdx + 2] = false;	//	그 다음 연산자 중심으로는 괄호 묶을 수 없음
		bruteforcing(opIdx + 4, chunked);
	}
	
	private static int calc(int lOperand, char operator, int rOperand) {
		int res = lOperand;
		
		switch(operator) {
		case '+':
			res += rOperand;
			break;
		case '-':
			res -= rOperand;
			break;
		default:
			res *= rOperand;
		}
		
		return res;
	}
	
	private static int calcReducedExpression(List<OP> reducedP) {
		if(reducedP.size() == 1)
			return reducedP.get(0).value;
		
		int res = 0;
		
		List<OP> reducedM = new ArrayList<>();	//	곱셈 기호 사라진 결과
		
		for(int i = 1; i < reducedP.size(); i += 2) {
			OP operator = reducedP.get(i);
			
			if(operator.operator == '*') {	//	곱셈일 경우
				if(i == 1 || reducedP.get(i - 2).operator != '*') {
					OP operand = new OP(calc(reducedP.get(i - 1).value, operator.operator, reducedP.get(i + 1).value), OPERAND);
					reducedM.add(operand);
				}
				else {
					OP operand = reducedM.get(reducedM.size() - 1);
					operand.value *= reducedP.get(i + 1).value;
				}
			}
			else {	//	덧셈/뺄셈일경우
				if(i == 1 || reducedP.get(i - 2).operator != '*') {
					OP operand = new OP(reducedP.get(i - 1).value, OPERAND);
					reducedM.add(operand);
				}
				reducedM.add(new OP(OPERATOR, operator.operator));
			}
		}
		
		if(reducedP.get(reducedP.size() - 2).operator != '*')
			reducedM.add(new OP(reducedP.get(reducedP.size() - 1).value, OPERAND));
		
		res = reducedM.get(0).value;
		for(int i = 1; i < reducedM.size(); i += 2)
			res = calc(res, reducedM.get(i).operator, reducedM.get(i + 1).value);

		return res;
	}
	
	//	연산자/피연산자(OPERATOR/ERAND)
	private static class OP {
		public int value;			//	피연산자일경우 그 값
		public char operator;		//	연산자 종류 ('+', '-', '*' 세 가지, OPERAND일 경우 피연산자임)
		
		public OP(int value, char operator) {
			this.value = value;
			this.operator = operator;
		}
		
		@Override
		public String toString() {
			if(operator == OPERAND)
				return value + "";
			else
				return operator + "";
		}
	}
}	//	Main-class-end
```