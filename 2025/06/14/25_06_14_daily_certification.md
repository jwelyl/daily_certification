# 25_06_14_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 9015 정사각형

[9015번: 정사각형](http://boj.ma/9015/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.HashSet;
import java.util.Objects;
import java.util.Set;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static final Set<Pos> posSet = new HashSet<>();
	
	private static int T;
	private static int N;
	private static int maxArea;
	private static Pos[] posArr;
	
	public static void main(String[] args) throws IOException {
		T = Integer.parseInt(br.readLine());
		
		for(int tc = 1; tc <= T; tc++) {
			N = Integer.parseInt(br.readLine());
			
			posSet.clear();
			maxArea = 0;
			
			posArr = new Pos[N];
			
			for(int p = 0; p < N; p++) {
				st = new StringTokenizer(br.readLine());
				posArr[p] = new Pos(Integer.parseInt(st.nextToken()), Integer.parseInt(st.nextToken()));
				
				posSet.add(posArr[p]);
			}
			
			for(int i1 = 0; i1 < N - 1; i1++) {
				for(int i2 = i1 + 1; i2 < N; i2++) {
					Pos p1 = posArr[i1];
					Pos p2 = posArr[i2];
					Pos p3 = new Pos();
					Pos p4 = new Pos();
					
					//	선분 pos1, pos2를 한 변으로 하는 정사각형이 존재할 경우 이 때 정사각형 넓이
					int area = Pos.area(p1, p2);
					boolean square = false;
					
					if(p1.x == p2.x) {
						int len = Math.abs(p2.y - p1.y);
						
						p3.x = p1.x - len;
						p3.y = p1.y;
						p4.x = p2.x - len;
						p4.y = p2.y;
						
						if(posSet.contains(p3) && posSet.contains(p4))
							square = true;
						else {
							p3.x = p1.x + len;
							p4.x = p2.x + len;
							
							if(posSet.contains(p3) && posSet.contains(p4))
								square = true;
						}
					}
					else if(p1.y == p2.y) {
						int len = Math.abs(p2.x - p1.x);
						
						p3.x = p1.x;
						p3.y = p1.y + len;
						p4.x = p2.x;
						p4.y = p2.y + len;
						
						if(posSet.contains(p3) && posSet.contains(p4))
							square = true;
						else {
							p3.y = p1.y - len;
							p4.y = p2.y - len;
							
							if(posSet.contains(p3) && posSet.contains(p4))
								square = true;
						}
					}
					else {
						Pos pos1 = p1.x > p2.x ? p2 : p1;
						Pos pos2 = p1.x > p2.x ? p1 : p2;
						
						int diffX = pos2.x - pos1.x;
						int diffY = Math.abs(pos2.y - pos1.y);
						
						if(pos2.y > pos1.y) {
							p3.x = pos2.x - diffY;
							p3.y = pos2.y + diffX;
							p4.x = pos1.x - diffY;
							p4.y = pos1.y + diffX;
							
							if(posSet.contains(p3) && posSet.contains(p4))
								square = true;
							else {
								p3.x = pos2.x + diffY;
								p3.y = pos2.y - diffX;
								p4.x = pos1.x + diffY;
								p4.y = pos1.y - diffX;
								
								if(posSet.contains(p3) && posSet.contains(p4))
									square = true;
							}
						}
						else {
							p3.x = pos2.x - diffY;
							p3.y = pos2.y - diffX;
							p4.x = pos1.x - diffY;
							p4.y = pos1.y - diffX;
							
							if(posSet.contains(p3) && posSet.contains(p4))
								square = true;
							else {
								p3.x = pos2.x + diffY;
								p3.y = pos2.y + diffX;
								p4.x = pos1.x + diffY;
								p4.y = pos1.y + diffX;
								
								if(posSet.contains(p3) && posSet.contains(p4))
									square = true;
							}
						}
					}
					
					if(square)
						maxArea = Math.max(maxArea, area);
				}
			}
			
			sb.append(maxArea).append("\n");
		}
		
		System.out.print(sb);
	} //	main-end
	
	private static class Pos {
		public int x;
		public int y;
		
		public Pos() {}
			
		public Pos(int x, int y) {
			this.x = x;
			this.y = y;
		}
		
		@Override
		public boolean equals(Object other) {
			if(this == other)
				return true;
			
			if(getClass() == other.getClass()) {
				Pos pos = (Pos)other;
				
				return x == pos.x && y == pos.y;
			}
			
			return false;
		}
		
		@Override
		public int hashCode() {
			return Objects.hash(x, y);
		}
		
		//	두 점 p1, p2를 잇는 선분을 한 변으로 하는 정사각형 넓이
		public static int area(Pos p1, Pos p2) {
			return (p2.x - p1.x) * (p2.x - p1.x) + (p2.y - p1.y) * (p2.y - p1.y);
		}
	}
} //	Main-class-end
```

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.HashSet;
import java.util.Set;
import java.util.StringTokenizer;

public class Main {
	//	좌표 범위 : -10_000 ~ 10_000 -> 0 ~ 20_000
	private static final int OFFSET = 10_000;
	//	-10_000 <= x, y <= 10_000인 (x, y) -> (x + OFFSET) * MULT + (y + OFFSET) 
	private static final int MULT = 100_000;
	
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static final Set<Integer> convertedPosSet = new HashSet<>();
	
	private static int T;
	private static int N;
	private static int maxArea;
	private static Pos[] posArr;
	
	public static void main(String[] args) throws IOException {
		T = Integer.parseInt(br.readLine());
		
		for(int tc = 1; tc <= T; tc++) {
			N = Integer.parseInt(br.readLine());
			
			convertedPosSet.clear();
			maxArea = 0;
			
			posArr = new Pos[N];
			
			for(int p = 0; p < N; p++) {
				st = new StringTokenizer(br.readLine());
				posArr[p] = new Pos(Integer.parseInt(st.nextToken()), Integer.parseInt(st.nextToken()));
				
				convertedPosSet.add(convertPosToInt(posArr[p]));
			}
			
			for(int i1 = 0; i1 < N - 1; i1++) {
				for(int i2 = i1 + 1; i2 < N; i2++) {
					Pos p1 = posArr[i1];
					Pos p2 = posArr[i2];
					Pos p3 = new Pos();
					Pos p4 = new Pos();
					int posInt3 = 0;
					int posInt4 = 0;
					
					//	선분 pos1, pos2를 한 변으로 하는 정사각형이 존재할 경우 이 때 정사각형 넓이
					int area = Pos.area(p1, p2);
					boolean square = false;
					
					if(p1.x == p2.x) {
						int len = Math.abs(p2.y - p1.y);
						
						p3.x = p1.x - len;
						p3.y = p1.y;
						p4.x = p2.x - len;
						p4.y = p2.y;
						posInt3 = convertPosToInt(p3);
						posInt4 = convertPosToInt(p4);
						
						if(convertedPosSet.contains(posInt3) && convertedPosSet.contains(posInt4))
							square = true;
						else {
							p3.x = p1.x + len;
							p4.x = p2.x + len;
							posInt3 = convertPosToInt(p3);
							posInt4 = convertPosToInt(p4);
							
							if(convertedPosSet.contains(posInt3) && convertedPosSet.contains(posInt4))
								square = true;
						}
					}
					else if(p1.y == p2.y) {
						int len = Math.abs(p2.x - p1.x);
						
						p3.x = p1.x;
						p3.y = p1.y + len;
						p4.x = p2.x;
						p4.y = p2.y + len;
						posInt3 = convertPosToInt(p3);
						posInt4 = convertPosToInt(p4);
						
						if(convertedPosSet.contains(posInt3) && convertedPosSet.contains(posInt4))
							square = true;
						else {
							p3.y = p1.y - len;
							p4.y = p2.y - len;
							posInt3 = convertPosToInt(p3);
							posInt4 = convertPosToInt(p4);
							
							if(convertedPosSet.contains(posInt3) && convertedPosSet.contains(posInt4))
								square = true;
						}
					}
					else {
						Pos pos1 = p1.x > p2.x ? p2 : p1;
						Pos pos2 = p1.x > p2.x ? p1 : p2;
						
						int diffX = pos2.x - pos1.x;
						int diffY = Math.abs(pos2.y - pos1.y);
						
						if(pos2.y > pos1.y) {
							p3.x = pos2.x - diffY;
							p3.y = pos2.y + diffX;
							p4.x = pos1.x - diffY;
							p4.y = pos1.y + diffX;
							posInt3 = convertPosToInt(p3);
							posInt4 = convertPosToInt(p4);
							
							if(convertedPosSet.contains(posInt3) && convertedPosSet.contains(posInt4))
								square = true;
							else {
								p3.x = pos2.x + diffY;
								p3.y = pos2.y - diffX;
								p4.x = pos1.x + diffY;
								p4.y = pos1.y - diffX;
								posInt3 = convertPosToInt(p3);
								posInt4 = convertPosToInt(p4);
								
								if(convertedPosSet.contains(posInt3) && convertedPosSet.contains(posInt4))
									square = true;
							}
						}
						else {
							p3.x = pos2.x - diffY;
							p3.y = pos2.y - diffX;
							p4.x = pos1.x - diffY;
							p4.y = pos1.y - diffX;
							posInt3 = convertPosToInt(p3);
							posInt4 = convertPosToInt(p4);
							
							if(convertedPosSet.contains(posInt3) && convertedPosSet.contains(posInt4))
								square = true;
							else {
								p3.x = pos2.x + diffY;
								p3.y = pos2.y + diffX;
								p4.x = pos1.x + diffY;
								p4.y = pos1.y + diffX;
								posInt3 = convertPosToInt(p3);
								posInt4 = convertPosToInt(p4);
								
								if(convertedPosSet.contains(posInt3) && convertedPosSet.contains(posInt4))
									square = true;
							}
						}
					}
					
					if(square)
						maxArea = Math.max(maxArea, area);
				}
			}
			
			sb.append(maxArea).append("\n");
		}
		
		System.out.print(sb);
	} //	main-end
	
	private static class Pos {
		public int x;
		public int y;
		
		public Pos() {}
			
		public Pos(int x, int y) {
			this.x = x;
			this.y = y;
		}
		
		public static int area(Pos pos1, Pos pos2) {
			return (pos1.x - pos2.x) * (pos1.x - pos2.x) + (pos1.y - pos2.y) * (pos1.y - pos2.y);
		}
	}
	
	private static int convertPosToInt(Pos pos) {
		return (pos.x + OFFSET) * MULT + (pos.y +OFFSET);
	}
} //	Main-class-end
```