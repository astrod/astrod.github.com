---
layout: post
title: "Flyweight 디자인 패턴"
tags:
- design_pattern
---

* toc
{:toc}

# 들어가며
이 포스팅의 내용은 <<Java 언어로 배우는 디자인 패턴>> 20장 Flyweight 패턴을 정리한 것입니다. 여기 나오는 코드는 JDK 1.8에서 작성, 테스트 되었습니다.
이 포스팅에 있는 코드는 [여기](https://github.com/astrod/design-pattern/tree/master/src/main/java/pattern/JU/flyweight) 서 확인하실 수 있습니다. 코드 또한 책에서 예제로 나온 코드를 조금 변경한 것입니다.

# Flyweight 패턴
Flyweight 패턴은 객체를 가볍게 사용하기 위한 패턴입니다. 가상의 세계에서 가볍다는 표현은 적절하지 않을 수 있습니다. 여기서 가볍다는 뜻은, 메모리를 조금 사용한다는 의미입니다.

Java 에서는

~~~java
new SomeThing();
~~~

으로 객체를 생성합니다. 이 인스턴스를 생성하면 자바 힙에 메모리가 할당됩니다. 즉, 새로운 객체를 많이 생성하게 되면 메모리를 더 많이 차지하게 됩니다. 이를 막기 위해 Flyweight 패턴에서는 다음과 같은 기법을 사용합니다.

> 인스턴스를 가능한 대로 공유시켜서, 쓸때없이 new 하지 않도록 한다.

간단하게 예제 프로그램을 만들어서 확인해 보겠습니다.
큰 문자를 표현하는 클래스를 상상해봅시다. 1-9, 그리고 -를 의미하는 BigChar 클래스가 있습니다. BigChar 클래스는 1~9, 그리고 -를 데이터로 가지고 있습니다.

큰 문자를 표현하기 때문에 원래 이미지를 읽어서 String 으로 들고 있게 처리해야 하지만, 번거롭기 때문에 그냥 char를 String으로 바꿔서 저장하겠습니다.

이 문자열을 print 하는 코드를 만들어 보겠습니다.
간단한 다이어그램은 다음과 같습니다.

![다이어그램]({{ site.url }}/asset/Flyweight.png)

## BigChar
BigChar 클래스는 숫자를 나타내는 클래스입니다. 코드는 다음과 같습니다.

~~~java
public class BigChar {
	private char charName;
	private String fontData;

	public BigChar(char charName) {
		this.charName = charName;
		// 원래는 파일을 읽어서 대입해야 하지만, 테스트의 문제로 그냥 입력받은 char 를 string 으로 변환하여 대입
		fontData = String.valueOf(charName);
	}

	public char getCharName() {
		return charName;
	}

	public void print() {
		System.out.println(fontData);
	}
}
~~~
이 클래스는 new를 하기 어려운 큰 숫자들을 다루는 클래스로, 원래는 생성자에서 fontData에 큰 데이터를 입력해야 합니다. 책에서 나온 코드는 파일을 읽어서 fontData에 집어넣는 코드였으나, 테스트의 문제도 있어서 그냥 입력받은 char 를 String으로 변환하여 입력하는 코드로 대체하였습니다.

이 코드만으로는 Flyweight 패턴을 알 수 없습니다. FlyWeight 패턴은 Factory 를 확인해야 명확하게 알 수 있습니다.

## BigCharFactory
BigCharFactory는 BigChar의 인터페이스를 생성하는 공장입니다. 여기서 공유의 방법을 실현하고 있습니다.
pool 필드는 지금까지 생성한 BigChar 를 캐싱하는 역할을 합니다.

코드는 다음과 같습니다.

~~~java
public class BigCharFactory {
	private Map<String, BigChar> pool = new HashMap<>();
	private static BigCharFactory singleton = new BigCharFactory();

	private BigCharFactory() {

	}

	public static BigCharFactory newInstance() {
		return singleton;
	}

	public synchronized BigChar getBigChar(char charName) {
		BigChar bc = pool.get("" + charName);
		if (bc == null) {
			bc = new BigChar(charName);
			pool.put("" + charName, bc);
		}

		return bc;
	}
}
~~~

세 가지 특징을 확인할 수 있습니다.

1. pool 필드에 지금까지 생성한 BigChar 객체를 캐싱한다.
2. Factory는 여러 번 생성할 필요가 없으므로, 싱글톤 패턴을 사용하여 하나의 인스턴스만 유지한다.
3. getBigChar에서는 캐싱된 pool 맵을 확인하여 데이터가 있는지 확인하고, 없으면 만들어서 캐싱한다.
4. getBigChar는 synchronized 로 한다.

4번 같은 경우는 synchronized로 하지 않으면, 멀티 쓰레드 환경에서 문제가 발생할 수 있습니다.

## BigString
BigString 클래스는 BigChar를 모은 큰 문자열 클래스입니다. 데이터로 가지고 있는 BigChars는 BigChar 의 배열이고, 인스턴스를 저장합니다.

코드는 다음과 같습니다.

~~~java
public class BigString {
	private BigChar[] bigChars;

	public BigString(String input) {
		bigChars = new BigChar[input.length()];
		BigCharFactory factory = BigCharFactory.newInstance();
		for(int i = 0; i<input.length(); i++) {
			this.bigChars[i] = factory.getBigChar(input.charAt(i));
		}
	}

	public void print() {
		for(int i =0; i<bigChars.length; i++) {
			bigChars[i].print();
		}
	}

	public BigChar[] getBigChars() {
		return bigChars;
	}
}
~~~

BigChar 객체를 가져올 때는 BigCharFactory를 통하여 객체를 가져와야 합니다. 만약 저렇게 하지 않고

~~~java
this.bigChars[i] = new BigChar(input.charAt(i));
~~~

이렇게 객체를 생성하면 메모리를 점유하게 되므로 Flyweight 패턴이라고 볼 수 없습니다.
이렇게 하면 코드는 완료되었습니다. 사용하는 클래스는 간단합니다.

## FlyWeightService

~~~java
@Service
public class FlyWeightService {
	public BigString findBigString(String input) {
		// print
		BigString bigString = new BigString(input);
		bigString.print();

		return bigString;
	}
}
~~~

BigString 객체를 생성한 후에, print 하는 것이 전부입니다. 이렇게 하면 BigString 객체에서는 BigCharFactory를 통하여 해당하는 BigChar 객체를 저장해 두고 있다가, print 하면 출력합니다.

## 특징
FlyWeight 패턴은 다음과 같은 특징이 있습니다.

### 여러 장소에 영향을 미친다.
객체를 공유하기 때문에, 객체의 데이터를 변경하면 객체를 사용하는 모든 클래스에 영향을 미칩니다. 이는 장점이 될 수도 있고 단점이 될 수도 있습니다. 어쨌든 공유는 하나를 변경하면 그것을 사용하고 있는 장소 전체에 영향을 미치게 됩니다.

따라서 공유객체는 신중하게 정보를 세팅해야 합니다. 잠시 예를 들어보겠습니다.
만약 색상 있는 큰 문자열을 사용한다고 가정해 봅시다. 색의 정보는 어떤 클래스에 제공해야 될까요?
만약 BigChar 클래스에 색 정보를 추가한다고 하면, 인스턴스는 공유되기 때문에 색의 정보 또한 공유됩니다. 이는 같은 숫자는 같은 색 정보를 가지게 된다는 것을 의미합니다. 숫자 3에 빨간색을 세팅하였다면, 숫자 3을 사용하는 모든 클래스는 빨간 숫자 3만 사용할 수 있게 됩니다.

반면에 BigString에 색 정보를 제공한다고 합니다. *세번째 문자의 색은 빨강*이라는 데이터를 추가하는 것입니다. 이렇게 하면 동일한 BigChar 객체도 다른 색 정보를 가질 수 있습니다.

어떤 쪽이 옳다는 것은 아닙니다. 어떤 정보를 공유할지는 사용 목적에 따라 다르기 때문입니다. 그렇기 때문에, 어떤 정보를 공유해야 할지 공유객체에 정보를 신중하게 세팅하여야 합니다.

### intrinsic 과 extrinsic
위에서 설명한 공유시키는 정보/공유시키지 않는 정보는 각기 다음과 같은 이름이 붙어 있습니다.

intrinsic의 사전상 의미는 다음과 같습니다.

1. 〔그 자체에〕 본래 갖추어진, 고유한, 본질적인〔to, in …〕
2. 〔해부〕 （근육신경 등에） 내재한.

다시 말하면, 인스턴스를 어디에서 가지고 있더라도 어떠한 상황에서도 변하지 않는 정보를 일컫는 말입니다. 위의 예제에서는 BigChar 객체의 charName, fontData를 intrinsic 하다고 이야기 할 수 있을 것입니다.

반면에 공유하지 않는 정보는 extrinsic 하다고 합니다. 사전적 의미는 다음과 같습니다.

1. 〈성질, 가치 등이〉비본질적인; 고유의 것이 아닌;
2. 〈원인영향이〉외부(로부터)의.

상황에 따라서 변화하는 정보라는 의미인데요. 상황에 따라서 변하기 때문에 이 정보는 공유객체에 둘 수 없습니다.

## synchronized
BigCharFactory에서 BigChar를 get 하는 코드는 다음과 같습니다.

~~~java
public synchronized BigChar getBigChar(char charName) {
		BigChar bc = pool.get("" + charName);
		if (bc == null) {
			bc = new BigChar(charName);
			pool.put("" + charName, bc);
		}

		return bc;
	}
~~~
이 코드에서 synchronized 가 붙어있는 이유는 멀티쓰레드 환경에 대응하기 위해서라고 말씀드렸었는데요.
만약에 제거한 후에 멀티쓰레드 환경에서 호출하면 어떻게 되는지 테스트 해보겠습니다.

synchronized를 제거한 getBigChar 를 하나 만듭니다.

~~~java
public BigChar getBigCharWithoutSync(char charName) {
		BigChar bc = pool.get("" + charName);
		if (bc == null) {
			System.out.println("bc is null. charName is : " + charName);
			bc = new BigChar(charName);
			pool.put("" + charName, bc);
		}

		return bc;
	}
~~~

이 코드는 pool에서 get 했을때 null인 경우 print를 합니다. 이 코드를 호출할 BigChar의 새로운 생성자도 하나 생성합니다. 파라미터를 하나 더 추가적으로 받을 수 있게 만듭니다.

~~~java
public BigString(String input, boolean isSync) {
		bigChars = new BigChar[input.length()];
		BigCharFactory factory = BigCharFactory.newInstance();
		for(int i = 0; i<input.length(); i++) {
			this.bigChars[i] = factory.getBigCharWithoutSync(input.charAt(i));
		}
	}
~~~

이 코드는 내부에서 Sync가 없는 getBigChar를 호출합니다.

이 생성자를 호출하는 Service method를 하나 만듭니다.

~~~java
public BigString findBigStringWithoutSync(String input) {
		BigString bigString = new BigString(input, false);

		return bigString;
	}
~~~

이 코드를 테스트 해보겠습니다.

~~~java
@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest(classes = Application.class)
public class FlyWeightServiceTest {
	@Autowired
	private FlyWeightService flyWeightService;

	@Test
	public void findBigString() throws Exception {
		// given
		String input = "1234567890";

		// when
		BigChar[] result = flyWeightService.findBigString(input).getBigChars();

		// then
		assertThat(result.length, is(input.length()));
		for(int i = 0; i<result.length; i++) {
			assertThat(result[i].getCharName(), is(input.charAt(i)));
		}
	}

	@Test
	public void findBigStringWithoutSync() throws Exception {
		// given
		String input = "11223344556677889900--";
		Thread thread1 = new Thread(() -> {
			flyWeightService.findBigStringWithoutSync(input).getBigChars();
		});
		Thread thread2 = new Thread(() -> {
			flyWeightService.findBigStringWithoutSync(input).getBigChars();
		});
		Thread thread3 = new Thread(() -> {
			flyWeightService.findBigStringWithoutSync(input).getBigChars();
		});
		Thread thread4 = new Thread(() -> {
			flyWeightService.findBigStringWithoutSync(input).getBigChars();
		});
		Thread thread5 = new Thread(() -> {
			flyWeightService.findBigStringWithoutSync(input).getBigChars();
		});
		Thread thread6 = new Thread(() -> {
			flyWeightService.findBigStringWithoutSync(input).getBigChars();
		});
		Thread thread7 = new Thread(() -> {
			flyWeightService.findBigStringWithoutSync(input).getBigChars();
		});
		Thread thread8 = new Thread(() -> {
			flyWeightService.findBigStringWithoutSync(input).getBigChars();
		});
		Thread thread9 = new Thread(() -> {
			flyWeightService.findBigStringWithoutSync(input).getBigChars();
		});
		Thread thread10 = new Thread(() -> {
			flyWeightService.findBigStringWithoutSync(input).getBigChars();
		});

		// when
		thread1.start();
		thread2.start();
		thread3.start();
		thread4.start();
		thread5.start();
		thread6.start();
		thread7.start();
		thread8.start();
		thread9.start();
		thread10.start();

		// then
	}
}
~~~

findBigStringWithoutSync를 테스트하는 코드입니다. 10개의 쓰레드를 생성하여 findBigStringWithoutSync를 호출합니다. 코드를 돌려 보면 다음과 같은 로그가 출력됩니다.

~~~
bc is null. charName is : 1
bc is null. charName is : 1
bc is null. charName is : 1
bc is null. charName is : 2
bc is null. charName is : 3
bc is null. charName is : 4
bc is null. charName is : 5
bc is null. charName is : 6
bc is null. charName is : 7
bc is null. charName is : 8
bc is null. charName is : 9
bc is null. charName is : 0
bc is null. charName is : -
bc is null. charName is : 1
bc is null. charName is : 2
bc is null. charName is : 2
bc is null. charName is : 2
bc is null. charName is : 2
bc is null. charName is : 2
bc is null. charName is : 2
bc is null. charName is : 1
bc is null. charName is : 1
~~~

charName을 한번씩 생성하는 게 아니라, 여러 번 new가 됨을 확인할 수 있습니다. 따라서 getBigString 메서드는 멀티쓰레드 환경에서도 정확히 동작할 수 있게 처리해야 합니다.

## 사용 예제
java 에서 Integer.valueOf는 내부적으로 캐싱을 합니다.

~~~java
/**
 * Returns an {@code Integer} instance representing the specified
 * {@code int} value.  If a new {@code Integer} instance is not
 * required, this method should generally be used in preference to
 * the constructor {@link #Integer(int)}, as this method is likely
 * to yield significantly better space and time performance by
 * caching frequently requested values.
 *
 * This method will always cache values in the range -128 to 127,
 * inclusive, and may cache other values outside of this range.
 *
 * @param  i an {@code int} value.
 * @return an {@code Integer} instance representing {@code i}.
 * @since  1.5
 */
public static Integer valueOf(int i) {
    if (i >= IntegerCache.low && i <= IntegerCache.high)
        return IntegerCache.cache[i + (-IntegerCache.low)];
    return new Integer(i);
}
~~~

이 메서드는 -128부터 127까지 생성할 때 데이터를 캐싱해 둡니다. valueOf를 호출할 때, 입력값이 저 범위 내라면 객체를 생성하지 않고 캐싱된 객체를 반환합니다. 이 또한 Flyweight 패턴이라 볼 수 있습니다.






