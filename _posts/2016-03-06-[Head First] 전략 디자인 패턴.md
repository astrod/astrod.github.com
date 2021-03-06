---
layout: post
title: "[Head First] 전략(strategy) 디자인 패턴"
tags:
- design_pattern
- book
- java
category:
- design_pattern
---

* toc
{:toc}

# 들어가며
이 글에 나오는 내용은 헤드 퍼스트 디자인 패턴에서 나오는 내용을 정리한 것이다.

# 간단한 어플리케이션의 요구사항
SimUDuck이라는 오리 연못 시뮬레이션 게임을 만들어보자. 이 게임에서는 헤엄도 치고 꽥꽥거리는 오리들을 화면에 보여줄 수 있다. 각각의 구채적인 오리 클래스는 Duck 이라는 클래스를 상속하고 있다.

~~~java
class Duck {
	quack()
	swim()
	display()
}

MallardDuck extends Duck {
	display() {
		// 적당한 모양을 표시한다
	}
}

RedheadDuck extends Duck {
	display() {
		// redheadduck에 어울리는 모양을 표시한다.
	}
}
~~~

이러한, 평화롭고 모두가 행복한 구조에서 잘 살아왔지만 경영진에서 요구사항을 바꾸었다! 이제는 오리들이 날아다닐 수 있도록 해야 한다.

## 오리는 날아야 한다
직장 상사는 요구사항을 보내줬다. 오리는 이제 날아야 한다. 일주일 정도 시간이 주어졌다. fly 메서드를 Duck 클래스에 추가하면 되지 않을까? 어쨌든 모든 오리는 날개가 있으니까 날 수도 있겠지...?

~~~java
class Duck {
	quack()
	swim()
	display()
	fly() // 오리가 날 수 있게 fly 메서드를 추가했다.
}

MallardDuck extends Duck {
	display() {
		// 적당한 모양을 표시한다
	}
}

RedheadDuck extends Duck {
	display() {
		// redheadduck에 어울리는 모양을 표시한다.
	}
}
~~~

## 문제가 발생
생각해 보면 모든 오리는 날 수 없다. 오리 연못에는 고무 오리도 있었던 것이다! 하늘을 날아다니는 고무오리는 조금 이상하다. 그렇다면 fly method를 오버라이드하는건 어떨까?

~~~java
MallardDuck extends Duck {
	display() {
		// 모양을 표시한다.
	}
	fly() {
		// 아무것도 하지 않는다.
	}
}
~~~
그렇다면 만약에 오리가 여러 마리가 생기면? 오리들은 각자 자신의 코드를 오버라이드하여 fly 를 구현해야 할 것이다. 굉장히 번거로운 일이 될 것이다.
또한 만약에 나무오리가 추가되면? 나무오리는 소리도 못 내고, 날수도 없어야 한다.

결국 전체의 오리가 아닌, 일부 오리만 날거나 꽥꽥거릴 수 있는 좋은 방법을 찾아야 한다.

## 문제는 무엇인가?
근본적인 문제는 상속의 한계이다. 상속을 통하면 상속하는 모든 객체가 메서드를 구현해야 하는 문제가 발생한다. 상속을 사용하지 않는 방법이 있을까?
이런 순간에 생각해야 하는 첫 번째 원칙은 다음과 같다.

**애플리케이션에서 달라지는 부분을 찾아내고, 달라지지 않는 부분으로부터 분리시킨다.**

즉, 코드에 요구사항이 있을 때마다 코드를 확인하여, 변경될 수 있는 부분과 불변하는 부분을 찢어낸다. 그렇게 하고 바뀌는 부분을 캡슐화하면 나중에 바뀌는 부분만 확장시킬 수 있다.

그럼 오리들의 행동에서 변경되는 부분은? 날거나, 짖는 행동이다.
fly()와 quack() 메서드는 오리들마다 다를 수 있다. 그렇다면 이 두 가지 행동만 갖는 클래스 집합을 구성해야 할 것이다.

## 오리의 행동 디자인
일단 최대한 유연하게 만드는게 좋을 거 같다. 오리 클래스를 찢어내는 이유는 최대한 확장가능하고 유연하게 만들기 위함이니까.

- 유연하게 만든다.
- Duck의 인스턴스에 행동을 할당할 수 있어야 한다(행동에 대한 세터 메소드를 포함한다)

여기서 두 번째 원칙이다.

**구현이 아닌 인터페이스에 맞춰 프로그래밍한다.**

각 행동은 인터페이스(FlyBehavior, QuackBehavior)로 표현하고, 행동을 구현할 때 이런 인터페이스를 구현하도록 할 것이다. 나는 행동과 꽥꽥거리는 행동은 Duck클래스에서 구현하지 않을 것이다. 구 디자인에서는 quack(), fly()와 같은 행동은 구현에 맞춰서 프로그래밍해야 했기 때문에 딱히 방법이 없었지만, 이를 인터페이스로 사용하면 좀 더 유연할 수 있다.

~~~ java
public interface FlyBehavior() {
	fly()
}

class FlyWithWings implements FlyBehavior() {
	fly() {
		// 이렇게 날아라. 나는 방법을 구현
	}
}
~~~

인터페이스에 맞춰서 프로그래밍 하라는 건 사실 다형성을 이용하라는 것과 같은 의미이다. 예를 들면

~~~ java
Dog d = new Dog();
d.bark()
~~~

라고 할 수도 있지만, 인터페이스나 상위 형식에 맞춰서 작업을 하게 되면

~~~ java
Animal animal = new Dog();
animal.makeSound();
~~~

이와 같이 인터페이스의 메서드를 호출할 수 있다.

## Duck 의 행동을 구현하는 방법
여기서는 FlyBehavior과 QuackBehavior, 두 가지의 인터페이스를 사용한다.

~~~ java
interface FlyBehavior {
	fly()
}

interface QuackBehavior {
	quick()
}

class FlyWithWings implements flyBehavior {
	fly() {
		// 날아다니는 행동을 구현
	}
}

class FlyNoWay implements flyBehavior {
	fly() {
		// 아무것도 안 한다.
	}
}
~~~
QuackBehavior도 인터페이스를 제공하면, 인터페이스를 임플리먼트 하는 클래스들이 Quack메서드를 따로따로 구현하는, 위와 같은 방식대로 작업을 한다. 
이런 식으로 디자인하면 다른 행동의 객체에서도 나는 행동과 꽥꽥거리는 행동을 사용할 수 있따. 또한 기종의 행동 클래스를 수정하거나 Duck클래스를 건드리지 않고도 새로운 행동을 추가할 수 있다.

**변경되는 지점의 클래스를 따로 묶어, 인터페이스로 제공한다.**

## Duck 클래스와 인터페이스 통합
- Duck 클래스에 flyBehavior와 quackBehavior라는 인터페이스 형식의 인스턴스 변수를 추가한다.
- 오리 객체들은 실행시에 이 변수에 대한 레퍼런스를 설정한다.
- Duck 클래스, 모든 서브클레스에서 fly와 quack 메소드도 제거한다.
- 그 후에 performQuack, performFly메서드를 추가한다. 이 메서드는 내부에서 인터페이스의 구현체를 재호출한다.

~~~ java
class Duck {
	FlyBehavior flyBehavior;
	QuackBehavior quackBehavior;
	
	public void performQuack() {
		quackBehavior.quack();
	}
	
	public void performFly() {
		flyBehavior.fly();
	}	
	
	// 기타 다른 오리 행동에 대한 메서드. fly와 quack은 제거한다.
}
~~~

다형성을 따르기 때문에, 이 코드에서는 FlyBehavior, QuackBehavior의 구현체가 뭔지 알 필요가 없다. 그 구현채들에게 fly와 quack 메서드가 있다는 걸 알 수 있고, 그걸 실행시킬 수 있다는 게 중요하다.

그러면 저 인터페이스 형식의 변수에 구현채를 어떻게 넣을까?

~~~ java
public class MallardDuck extends Duck {
	public MallardDuck() {
		quackBehavior = new Quack(); // 사용할 클래스를 넣는다.
		flyBehavior = new FlyWithWings(); // 날 수 있는 오리이므로 fly가 가능한 구현채를 넣는다.
	}
}
~~~

이렇게 하면 문제가 또 발생한다 : 동적으로 quackBehavior와 flyBehavior를 변경할 수 없다. 동적으로 메서드를 바꿀 수 있을까?

## 동적으로 행동을 지정하는 방법
set 메서드를 추가한다.

~~~ java
public void setFlyBehavior(FlyBehavior fb) {
	flyBehavior = fb;
}

public void setQuackBehavior(QuackBehavior qb) {
	quackBehavior = qb;
}
~~~

## 구성
각 오리들은 FlyBehavior와 QuackBehavior가 있고, 각각 행동과 꽥꽥거리는 행동을 위임받는다. 두 클래스를 이와 같은 방식으로 합치는 것을 구성(composition)이라고 한다. 여기에 나와있는 오리 클래스는 행동 객체를 끼워 넣음으로서, 행동을 부여받게 된다.

이것은 디자인 패턴의 세 번째 원칙이기도 하다.

**상속보다는 구성을 활용한다.**

구성을 이용하면 시스템의 유연성을 크게 향상시킬 수 있다. 구성은 여러 디자인 패턴에서 쓰이게 된다.

## 스트래티지 패턴(strategy pattern)
이와 같은 방법을 전략(strategy) 패턴이라고 한다. 이 패턴을 조금 더 정확하게 정리하자면 다음과 같다.

**전략 패턴에서는 알고리즘군을 정의하고 각각을 캡슐화하여 교환하여 사용할 수 있도록 만든다. 전략 패턴을 활용하면 알고리즘을 활용하는 클라이언트와는 독립적으로 알고리즘을 변경할 수 있다.**

위와 같은 예제에서는, 오리의 나는 행동과 꽥꽥거리는 행동을 인터페이스로 정의하고, 캡슐화했다. 위의 인터페이스를 교환하므로써 클라이언트(Duck)와 별개로 알고리즘을 변경할 수 있는 것이다.

## 결론
스트레지디 패턴은 여러군대에서 아주 많이 사용한다. 스프링에서도 자주 사용하고 있으며 디자인 패턴 전체를 놓고 비교해도, 매우 강력한 패턴이다.



