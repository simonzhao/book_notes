# Strategy模式 策略模式

整体地替换算法

## 示例程序
* Hand类

	Hand类代表猜拳游戏中的“手势”的类。
	
	字段handValue用int表示出的手势
	
	* 0：石头
	* 1：剪刀
	* 2：布
	
	```Java
	public class Hand {
		public static final int HANDVALUE_GUU = 0; //石头
		public static final int HANDVALUE_CHO = 1; //剪刀
		public static final int HANDVALUE_PAA = 2; //布
		
		public static final Hand[] hand = {
			new Hand(HANDVALUE_GUU),
			new Hand(HANDVALUE_CHO),
			new Hand(HANDVALUE_PAA),
		}
		
		private static final String[] name = {
			"石头", "剪刀", "布",
		}
		
		/**
		 * 猜拳中出的手势的值
		 */
		private int handvalue;
		
		private Hand(int handvalue){
			this.handvalue = handvalue;
		}
		
		/**
		 * 根据手势值获取相应的实例
		 *
		 * @return Hand
		 */
		public static Hand getHand(int handvalue){
			return hand[handvalue];
		}
		
		/**
		 * 如果this胜了h，则返回true
		 *
		 * @return boolean
		 */
		public boolean isStrongerThan(Hand h) {
			return fight(h) == 1;
		}
		
		/**
		 * 如果this输给了h，则返回true
		 *
		 * @return boolean
		 */
		public boolean isWeakerThan(Hand h) {
			return fight(h) == -1;
		}
		
		/**
		 * 计分
		 * 平：0 胜：1 负：-1
		 * 
		 * @return int
		 */
		private int fight(Hand h){
			if (this == h){
				return 0;
			} else if ( (this.handvalue + 1) % 3) == h.handvalue ) {
				return 1;
			} else {
				return -1;
			}
		}
		
		/**
		 * 转换为手势对应的字符串
		 *
		 * @return String
		 */
		public String toString() {
			return name[handvalue];
		}
	}
	```
	
* Strategy接口

	```Java
	public interface Strategy {
		public abstract Hand nextHand();
		public abstract void study(boolean win);
	}
	```
	
* WinningStrategy类

	```Java
	//WinningStrategy.java
	/**
	 * 策略：如果上一局的手势胜了，这一局继续出；如果上一局输了，这一局随机
	 */
	import java.util.Random;
	public class WinningStrategy implements Strategy {
		private Random random;
		private boolean won = false;
		private Hand prevHand;
		
		public WinningStrategy(int seed){
			random = new Random(seed);
		}
		
		public Hand nextHand() {
			if (!won) {
				prevHand = Hand.getHand(random.nextInt(3));
			}
			return prevHand;
		}
		
		public void study(boolean win){
			won = win;
		}
	}
	```
	
* ProbStrategy类

	```Java
	//ProbStrategy.java
	/**
	 * 根据以前的猜拳结果，计算出本次出拳
	 */
	import java.util.Random;
	public class ProStrategy implements Strategy {
		privete Random random;
		private int prevHandValue = 0;
		private int currentHandValue = 0;
		private int[][] history = {
			{1, 1, 1,},
			{1, 1, 1,},
			{1, 1, 1,},
		};
		
		public ProbStrategy(int seed){
			random = new Random(seed);
		}
		
		public Hand nextHand() {
			int bet = random.nextInt(getSum(currentHandValue));
			int handvalue = 0;
			if (bet < history[currentHandValue][0]) {
				handvalue = 0;
			} else if (bet < history[currentHandValue][0] + history[currentHandValue][1]) {
				handvalue = 1;
			} else {
				handvalue = 2;
			}
			prevHandValue = currentHandValue;
			currentHandValue = handvalue;
			return Hand.getHand( handvalue );
		}
		
		private int getSum(int hv){
			int sum = 0;
			for(int i=0; i < 3; i++){
				sum += history[hv][i];
			}
			return sum;
		}
		
		public void study(boolean win){
			if (win) {
				history[prevHandValue][currentHandValue]++;
			} else {
				history[prevHandValue][(currentHandValue + 1) % 3]++;
				history[prevHandValue][(currentHandValue + 2) % 3]++;
			}
		}
	}
	```
	
* Player类

	```Java
	public class Player {
		private String name;
		private Strategy strategy;
		private int wincount;
		private int losecount;
		private int gamecount;
		
		public Player(String name, Strategy strategy){
			this.name = name;
			this.strategy = strategy;
		}
		
		public Hand nextHand() {
			return strategy.nextHand();
		}
		
		public void win() {
			strategy.study(true);
			wincount++;
			gamecount++;
		}
		
		public void lose() {
			strategy.study(false);
			losecount++;
			gamecount++;
		}
		
		public void even() {
			gamecount++;
		}
		
		public String toString() {
			return "[" + name + ":" + gamecount + " games, " + wincount + " win, " + losecount + " lose" + "]";
		}
		
	}
	```
	
* Main类

	```Java
	//Main.java
	public class Main {
		public static void main(String[] args){
			if (args.length != 2){
				System.out.println("Usage: java Main randomseed1 randomseed2");
				System.out.println("Example: java Main 314 15");
				System.exit(0);
			}
			
			int seed1 = Integer.parseInt( args[0] );
			int seed2 = Integer.parseInt( args[1] );
			
			Player player1 = new Player("Taro", new WinningStrategy(seed1) );
			Player player2 = new Player("Hana", new ProStrategy(seed1) );
			
			for (int i=0; i<10000; i++){
				Hand nextHand1 = player1.nextHand();
				Hand nextHand2 = player2.nextHand();
				
				if ( nextHand1.isStrongerThan( nextHand2 ) ) {
					System.outprintln("Winner:" + player1);
					player1.win();
					player2.lose();
				} else if ( nextHand2.isStrongerThan( nextHand2 ) ) {
					System.outprintln("Winner:" + player2);
					player1.lose();
					player2.win();
				} else {
					System.outprintln("Even...");
					player1.even();
					player2.even();
				}
			}
			
			System.out.println("Total result:");
			System.out.println( player1.toString() );
			System.out.println( player2.toString() );
		}
	}
	```
	
## 角色
* Strategy（策略）
* ConcreteStrategy（具体的策略）
* Context（上下文）

## 拓展思路
* 为什么需要特意编写Strategy角色
* 程序运行中也可以切换策略

## 相关设计模式
* Flyweight模式
* Abstract Factory模式
* State模式
