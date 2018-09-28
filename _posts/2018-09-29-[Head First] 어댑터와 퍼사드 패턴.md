---
layout: post
title: "[Head First] 어댑터와 퍼사드 패턴"
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
이 글에 나오는 내용은 <<Head First Design Patterns>> 7장, 어댑터 패턴과 퍼사드 패턴에 나오는 내용을 정리한 것이다.

# 어댑터

객체지향 어뎁터(adapter) 가 무엇인지는 그리 어렵지 않게 이해할 수 있을 것이다. 예를 들면 한국에서 쓰던 전자제품을 유럽에 가서 쓰려고 하면 어떻게 해야 할까?

플러그 모양을 바꿔주는 어뎁터를 사용해야 할 것이다. 객체지향 어뎁터도 이와 마찬가지이다. 어떤 인터페이스를 클라이언트에서 요구하는 형태의 인터페이스에 적응시키는 역할을 한다.

# 객체지향 어댑터

어댑터를 어떻게 쓰는지 예제로 확인해 보자. Duck 인터페이스가 있다고 가정하자. 이 인터페이스는 오리의 행동을 결정하는데, 꽥꽥거리거나 나는 행동을 정의한다.

~~~java
public interface Duck {
    public void quack();
    public void fly();
}
~~~

Duck 을 구현하는 MallardDuck 클래스도 확인해 보자

~~~java
public class MallardDuck  implements Duck {
    
    public void quack() {
        System.out.println("Quack");
    }
    
    public void fly() {
        System.out.println("I'm flying");
    }
}
~~~

이제, 새로 등장한 가금류 친구를 만나보자.

~~~java
public interface Turkey {
    public void gobble(); // 칠면조는 꽥꽥거리지 않는다.
    public void fly(); // 칠면조도 날 수 있다. 그러나 오리만큼 멀리 날 수는 없다.
}
~~~

칠면조는 꽥꽥거리지 않기 때문에, quack 메서드 대신에 gobble 메서드가 있다. 

~~~java
public class WildTurkey implements Tuckey {
    public void gobble() {
        System.out.println("Gobble gobble");
    }
    
    public void fly() {
        System.out.println("I'm flying a short distance");   
    }
}
~~~

Duck 객체가 모자라서 Turkey 객체를 대신 사용해야 하는 상황이라고 가정하자. 물론 인터페이스가 다르기 때문에 그대로 사용할 수는 없다. 어뎁터를 만들어 보자.

~~~java
public class TurkeyAdapter implements Duck {
    
    Turkey turkey;
    
    // 원래 형식의 객체에 대한 래퍼런스가 필요하다.
    public TurkeyAdapter(Turkey turkey) {
        this.turkey = turkey;
    }
    
    public void quack() {
        turkey.gobble();
    }
    
    public void fly() { // 칠면조는 오리만큼 멀리 날 수 없기 때문에, 다섯 번 날게 하여 같은 거리를 날 수 있게 하였다.
        for(int i = 0; i < 5; i++) {
            turkey.fly();
        }
    }
}
~~~

위와 같은 방식으로 Turkey 를 Duck 처럼 사용할 수 있는 어뎁터를 만들 수 있다.

# 어댑터 패턴

이제 어댑터가 어떤 것인지 대강 감이 잡혔을 테니, 한 발 뒤로 물러서서 전체적으로 어떤 식으로 돌아가는지 살펴보자.

클라이언트에서는 어댑터를 다음과 같은 순서로 사용한다.

1. 클라이언트에서 타겟 인터페이스를 사용하여 메소드를 호출함으로써 어댑터에 요청을 한다.
2. 어댑터에서는 어댑터 인터페이스를 사용하여 그 요청을 어댑터에 대한 하나 이상의 매소드 호출로 변환한다.
3. 클라이언트에서는 호출 결과를 받지만 중간에 어댑터가 있는지 전혀 알지 못한다.

# 어댑터 패턴의 정의

위의 예제를 통해 어댑터에 대해 충분히 살펴본 거 같으니, 공식적으로 어댑터 패턴이 어떻게 정의되는지 확인해 보자

> 어댑터 패턴 - 한 클래스의 인터페이스를 클라이언트에서 사용하고자 하는 다른 인터패이스로 변환합니다. 어댑터를 이용하면 인터페이스 호환성 문제 때문에 같이 쓸 수 없는 클래스들을 연결하여 쓸 수 있습니다.

이 패턴을 이용하면, 인터페이스를 변환해 주는 어댑터를 통해 호환되지 않는 인터페이스를 사용하는 클라이언트를 그대로 활용할 수 있다. 

어댑터 패턴에는 두 가지 주요한 객체지향 원칙이 반영되어 있다.

첫째는 어댑터를 새로 바뀐 인터페이스로 감쌀 때는 객체 구성(composition) 을 사용한다는 점이다. 이런 접근법을 사용하면, 어댑터의 어떤 서브클래스에 대해서도 어댑터를 사용할 수 있다.

그리고 이 패턴에서는 클라이언트를 특정 구현이 아닌 인터페이스에 연결시킨다. 각각 서로 다른 백엔드 클래스들로 변환시키는 여러 인터페이스를 사용할 수도 있따. 이렇게 인터페이스를 기준으로 코딩하였기 때문에, 타겟 인터페이스만 제대로 지킨다면 나중에 다른 구현을 추가하는 것도 가능하다.

# 객체와 클래스 어댑터

아직 이 패턴에 대해 모든 것을 살펴보지는 않았다. 어댑터에는 두 종류가 있다. 하나는 객체 어댑터, 다른 하나는 클래스 어댑터이다. 우리가 앞에서 살펴본 어댑터는 객체 어댑터이다.

그러면 클래스 어댑터는 무엇이며, 왜 다루지 않은 것일까? 클래스 어댑터 패턴을 쓰려면 다중 상속이 필요한데, 자바에서는 다중 상속이 불가능하기 때문이다. 

클래스 어댑터에서는 타겟과 어댑터 모두의 서브클래스를 만들고, 객체 어댑터에서는 생성자에 파라미터를 받아서 처리한다는 점을 제외하면 별다른 차이점이 없다.


# 어댑터 실전 예제

Duck 클래스는 충분히 보았으니, 실전에서 간단히 어댑터를 사용하는 예를 한번 살펴보려고 한다.

자바를 이전부터 써왔다면, Enumeration 을 리턴하는 elements() 메소드가 구현되어 있었던 초기 컬랙션 형식(Vector, Stack, Hashtable 등) 을 알고 있을 것이다. Enumeration 인터페이스를 사용하면 컬렉션 내의 모든 항목에 손쉽게 접근할 수 있었다.

그 후에, 썬에서 새로운 컬렉션 클래스를 출시하면서, Enumeration 과 마찬가지로 컬렉션에 있는 일련의 항목들에 접근할 수 있게 해 주면서 항목을 제거할 수도 있게 해 주는 Iterator 라는 인터페이스를 이용하기 시작하였다.

이제 Enumeration 인터페이스를 사용하는 구형 코드가 레거시에 남아 있지만, 이제부터 만드는 코드들에서는 Iterator 를 사용할 계획이다. 어댑터 패턴을 적절히 활용하면 좋을 거 같다.

## 두 인터페이스 확인

Iterator 인터페이스는 다음과 같이 생겼다.

~~~java
public interface Iterator<E> {
    
    boolean hasNext();
    E next();
    default void remove() {
        throw new UnsupportedOperationException("remove");
    }
}
~~~

자바 1.8 에 디폴트 메서드가 추가되었는데, 인터페이스에 구현을 추가할 수 있게 해 준다. remove 는 디폴트로 UnsupportedOperationException 을 던지게 되어있다.

Enumeration 인터페이스는 다음과 같이 생겼다.

~~~java
public interface Enumeration<E> {
    
    boolean hasMoreElements();
    E nextElement();
}
~~~

## 어댑터 디자인

클래스는 이런 식으로 구현되어야 할 거 같다. 생성자에 Enumeration 을 받아서 어댑터로 감싼다. 그 어댑터는 타겟 인터페이스(이 경우에는 Iterator) 를 구현해야 할 것이다.

hasNext() 와 next() 메소드는 타겟에서 어댑터로 바로 연결된다. 그러나 remove() 는 어떻게 해야 될까?

일단 구현해 보자

~~~java
class EnumerationIterator<E> implements Iterator {
    private Enumeration<E> enumeration;
    
    public IteratorAdapter(Enumeration enumeration) {
        this.enumeration = enumeration;
    }
    
    boolean hasNext() {
        return enumeration.hasMoreElements();
    }
    
    E next() {
        return enumeration.nextElement();
    }
}
~~~

remove() 는 구현하지 않았다. 어댑터의 차원에서 완벽하게 작동하는 remove() 메소드를 구현할 수 있는 방법은 현재 없다. 그나마 가장 좋은 방법은 예외를 던지는 것인데, 이미 디폴트 메서드에서 예외를 던지고 있다.

따라서 따로 오버라이드 하지는 않았다.

반대로 Enumeration 을 만들어내는 구형 클래스에 대해 사용할 수 있는 어댑터를 만들어 보자

~~~java
public class IteratorAdapter<E> implements Enumeration {
    private Iterator<E> iterator;
    
    public IteratorAdapter(Iterator iterator) {
        this.iterator = iterator;
    }
    
    public boolean hasMoreElements() {
        return iterator.hasNext();
    }
    
    public E nextElement() {
        return iterator.next();
    }
}
~~~ 

이렇게 구현할 수 있다.


# 객체지향 퍼사드

이 포스팅에서는 또 다른 패턴에 대해 다룬다. 퍼사드 패턴인데, 퍼사드 패턴에 대해 자세히 알아보기 전에 다음 예제를 살펴보자.

홈 씨어터 구축에 대해 생각해 보자. 몇 주의 노력 끝에 DVD 플레이어, 프로젝터, 자동 스크린, 서라운드 음향 및 팝콘 기계까지 갖춘 시스템을 구성했다. 이제 즐길 일만 남았다.

1. 팝콘 기계를 켠다
2. 팝콘 튀기기 시작
3. 전등을 어둡게 조절
4. 스크린을 내린다
5. 프로젝터를 켠다
6. 프로젝터로 DVD 신호가 입력되도록 한다
7. 프로젝터를 와이드 스크린 모드로 전환한다
8. 앰프를 켠다
9. 앰프 입력을 DVD 로 전환한다
10. DVD 플레이어를 킨다
11. DVD 를 재생한다

코드로 살펴보자

~~~java
popper.on();
popper.pop();

lights.dim(10);

screen.down();

projector.on();
projector.setInput(dvd);
projector.wideScreenMode();

amp.on();
amp.setDvd(dvd);
amp.setSurroundSound();
amp.setVolume(5);

dvd.on();
dvd.play(movie);
~~~

홈 씨어터 사용법이 너무 길고 복잡하다! 그리고 만약에 시스템이 업그레이드 된다면? 또 다른 작동 방법을 배워야 한다. 이 일을 간단하게 처리하고 싶다. 손짓 한 번으로 간단하게 홈 씨어터를 동작하면 좋지 않을까?

# 전등, 카메라, 퍼사드!

퍼사드는 이런 경우에 사용한다. 퍼사드 패턴을 쓰면 훨씬 쓰기 쉬운 인터페이스를 제공하는 퍼사드 클래스를 구현함으로써 복잡한 시스템을 훨씬 쉽게 사용할 수 있다. 어떤 식으로 작동하는지 간단하게 살펴보자

1. 홈 씨어터 시스템용 퍼사드를 만들어 보자. watchMovie() 같이 몇 가지 간단한 메소드만 들어있는 HomeTheaterFacade 라는 클래스를 새로 만들어야 한다.
2. 퍼사드 클래스에는 홈 씨어터 구성요소들을 하나의 서브시스템으로 간주하고, watchMovie() 메소드에서는 서브시스템의 메소드들을 호출하여 필요한 작업을 처리한다.
3. 이제 클라이언트 코드에서는 홈 씨어터 퍼사드에 있는 watchMovie() 를 호출한다.
4. 퍼사드를 사용하더라도 서브시스템에는 여전히 직접 접근할 수 있다. 서브시스템의 고급 기능이 필요하다면 언제든지 마음대로 사용할 수 있다.

퍼사드를 사용하면 클라이언트 구현과 서브시스템을 분리할 수 있다. 예를 들어, 홈 씨어터 시스템을 업그래이드 한다고 하더라도 여전히 watchMovie() 를 호출하는 것으로 모든 문제를 해결할 수 있다.

얼핏 보면 어댑터 패턴과 퍼사드 패턴 모두 클래스를 감싼다고 생각할 수 있다. 어댑터 패턴과 퍼사드 패턴의 차이점은 용도에 있다. 어댑터 패턴은 인터페이스를 변경하여 클라이언트에서 필요로 하는 인터페이스로 적응시키기 위한 용도로 쓰인다.
반면에 퍼사드 패턴은 어떤 서브시스템에 대한 간단한 인터페이스를 제공하기 위한 용도로 사용된다. 감싸는 클래스의 개수는 중요하지 않다.

# 홈 씨어터 퍼사드 구축

실제로 시스템을 구축해 보자

~~~java
public class HomeTheaterFacade {
    Amplifier amp;
    Tuner tuner;
    DvdPlayer dvd;
    CdPlayer cd;
    Projector projector;
    TheaterLights lights;
    Screen screen;
    PopcornPopper popper;
    
    public HomeTheaterFacade(Amplifier amp,
                             Tuner tuner,
                             DvdPlayer dvd,
                             CdPlayer cd,
                             Projector projector,
                             TheaterLights lights,
                             Screen screen,
                             PopcornPopper popper) {
        this.amp = amp;
        this.tuner = tuner;
        this.cd = cd;
        this.projector = projector;
        this.lights = lights;
        this.screen = screen;
        this.popper = popper;
    }
    
    // 위의 인스턴스 변수를 모두 사용하는 watchMovie 퍼사드를 만든다.
    public void watchMovie(String movie) {
        System.out.println("Get ready to watch a movie...");
        popper.on();
        popper.pop();
        lights.dim(10);
        screen.down();
        projector.on();
        projector.setInput(dvd);
        projector.wideScreenMode();
        amp.on();
        amp.setDvd(dvd);
        amp.setSurroundSound();
        amp.setVolume(5);
        dvd.on();
        dvd.play(movie);        
    }
    
    // 영화를 종료하는 endMovie 퍼사드를 만든다.
    public void endMovie() {
        System.out.println("Get ready to watch a movie...");
        popper.off();
        lights.on();
        screen.up();
        projector.off();
        amp.off();
        dvd.stop();
        dvd.enject();
        dvd.off();
    }
}
~~~  

이제 퍼사드를 사용하는 클라이언트 코드를 만들어 보자

~~~java
public class HomeTheaterTestDrive {
    public static void main(String [] args) {
        HomeTheaterFacade homeTheater = new HomeTheaterFacade(amp, tuner, dvd, cd, projector, screen, lights, popper);
        
        homeTheater.watchMovie("Raiders of the Lost Ark"); // 퍼사드 메서드 호출
        homeTheater.endMovie();
    }
}
~~~

# 퍼사드 패턴의 정의

퍼사드 패턴은 간단한 패턴이지만, 별 볼일 없는 패턴은 아니다. 퍼사드 패턴을 사용하면 클라이언트와 서브시스템이 서로 긴밀하게 연결되지 않아도 되기 때문에 서로 확장성이 생긴다.

퍼사드 패턴이 공식적으로 어떻게 정의되는지 확인해 보자

> 퍼사드 패턴 - 어떤 서브시스템의 일련의 인터페이스에 대한 통합된 인터페이스를 제공합니다. 퍼사드에서 고수준 인터페이스를 정의하기 때문에 서브시스템을 더 쉽게 사용할 수 있습니다.

퍼사드 패턴은 단순화된 인터페이스를 통해서 서브시스템을 더 쉽게 사용할 수 있도록 해 준다. 이게 전부이다.

# 최소 지식 원칙

최소 지식 원칙에 따르면, 객체 사이의 상호작용은 될 수 있으면 아주 가까운 "친구" 사이에만 허용해야 한다. 

> 최소 지식 원칙 - 정말 친한 친구하고만 얘기하라.

이게 무슨 의미일까? 시스템을 디자인할 때 어떤 객체든 그 객체와 상호작용을 하는 클래스의 개수에 주의해야 하며, 그런 객체들과 어떤 식으로 상호작용을 하는지에도 주의를 기울여야 한다는 뜻이다.
이 원칙을 잘 따르면 여러 클래스들이 복잡하게 얽혀서 시스템의 한 부분을 변경하였을 때 다른 부분까지 줄줄이 고쳐야 되는 상황을 미리 방지할 수 있다.

그렇다면 어떻게 여러 객체하고 인연을 맺는 것을 피할 수 있을까? 이 원칙에서는 몇 가지 가이드라인을 제시한다. 어떤 메소드에서든지 다음 네 종류의 객체의 메소드만 호출하면 된다.

- 객체 자체
- 메소드에 매개변수로 전달된 객체
- 그 메소드에서 생성하거나 인스턴스를 만든 객체
- 그 객체에 속하는 구성요소

예제로 살펴보자

~~~java
public float getTemp() {
    Thermometer thermometer = station.getThermometer();
    return thermometer.getTemp();
}
~~~

위의 코드를 보면, station 에서 thermometer 객체를 가져와서 그 객체의 getTemp() 메소드를 직접 호출한다. 이렇게 하면 호출하는 측에서 temp 객체와 직접적인 연관을 맺기 때문에, 불필요한 의존성이 증가되게 된다.

~~~java
public float getTemp() {
    return station.getTemp(); // station 클래스에 thermometer 를 대신 요청해주는 메소드를 추가했다.
}
~~~

이렇게 하면 의존하는 클래스의 개수를 줄일 수 있다.

한 가지 예제를 더 확인해 보자

~~~java
public class Car {
    Engine engine;
    
    public Car() {
        
    }
    
    public void start(Key key) {
        Doors doors = new Doors(); // 내부에서 생성한 객체의 메소드는 호출해도 된다.
        
        boolean authorized = key.turns(); // 매개변수로 전달된 객체의 메소드는 호출해도 된다.
        
        if (authorized) {
            engine.start(); // 객체에 속하는 구성요소의 메소드는 호출해도 된다.
            updateDashboardDisplay(); // 객체 내에 있는 메소드는 호출해도 된다.
            doors.lock(); // 직접 생성하거나 인스턴스를 만든 객체의 메소드는 호출해도 된다.
        }
    }
}
~~~

위에서 호출한 메소드들은 모두 호출해도 괜찮은 메소드이다. 그럼 다음과 같은 케이스는 어떠한가?

~~~java
public boolean validPrice(Key key) {
    Price price = key.getPrice();
    return price.isValidPrice();
}
~~~

price 객체는 key 라는 파라미터에서 가져온 객체이니 호출해도 된다. 그러나, price 객체 내부의 메서드를 호출하면 안 된다. 이 경우에는 Key 클래스에 메서드를 추가하여 클래스를 감춰야 한다.

이 원칙을 잘 따르면 객체들 사이의 의존성을 줄일 수 있고, 소프트웨어 관리가 더 용이해질 수도 있다. 하지만 이 원칙을 적용하다 보면 다른 구성요소에 대한 메서드 호출을 처리하기 위해 래퍼 클래스를 더 만들어야 할 수도 있다. 그러다 보면 시스템이 더 복잡해지고, 개발시간도 늘어나고, 성능도 떨어질 수 있다.

외줄타기를 하듯이 균형을 잘 맞춰서 원칙을 적용해야 할 것이다. 
