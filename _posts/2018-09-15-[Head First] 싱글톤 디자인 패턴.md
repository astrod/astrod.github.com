---
layout: post
title: "[Head First] 싱글톤 디자인 패턴"
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
이 글에 나오는 내용은 <<Head First Design Patterns>> 5장, 싱글턴 패턴에 나오는 내용을 정리한 것이다.

# 싱글턴 패턴
때때로 어플리케이션에서는 한 JVM 에 하나만 있어도 되는 객체들이 있다. 스레드 풀, 캐시, 대화상자, 레지스트리 설정은 두 개 존재할 필요가 없고, 오히려 두 개 이상 만들게 되면 프로그램이 이상하게 돌아간다던지 지원을 불필요하게 잡아먹는 문제가 발생할 수 있다.

그럴 때를 위해 싱글턴(Singleton) 패턴이 존재한다. 싱글턴 패턴은 한 JVM 에 하나의 인스턴스만 생성할 수 있는 디자인 패턴이다.

접근 제어자를 적절히 활용하면, 다음과 같이 코드를 작성할 수 있다.

~~~java
public class Singleton {
    private static Singleton uniqueInstance;
    
    private Singleton() {}
    
    public static Singleton getInstance() {
        
        // 객체가 초기화되지 않은 경우에는 객체를 초기화해준다.
        if (uniqueInstance == null) {
            uniqueInstance = new Singleton();
        }
        return uniqueInstance;
    }
}
~~~

자, 그러면 싱글턴 패턴은 이걸로 끝나는 것인가? 이 코드는 때때로 잘 동작하는 것 처럼 보인다. 그러나, 몇 가지 문제가 있다.

예제를 통해 확인해 보자

# 초콜릿 공장
요즘은 대부분의 초콜릿 공장에는 초콜릿을 끓이는 장치(초콜릿 보일러) 를 컴퓨터로 제어한다. 이 보일러에서는 초콜릿과 우유를 받아서 끓이고, 초코바를 만드는 단계로 넘겨준다.

여기에 최신형 초콜릿 보일러를 제어하기 위한 클래스가 있다. 코드를 보면 잘못된 실수를 하지 않기 위해 여러가지 장치가 되어 있음을 알 수 있다.

~~~java
public class ChocolateBoiler {
    private boolean empty;
    private boolean boiled;
    
    public ChocolateBoiler() {
        empty = true; // 이 코드는 보일러가 비어있을 때만 동작한다
        boiled = false; 
    }
    
    public void fill() {
        if(isEmpty()) { // 보일러가 비어있을 때만 재료를 가득 채운다. 원료를 가득 채우고 나면 boiled 를 false 로 돌려놓는다.
            empty = false;
            boiled = false;
            // 보일러에 우유/초콜릿을 혼합한 재료를 집어넣음
        }
    }
    
    /**
    * 보일러에 들어있는 재료를 다음 단계로 넘긴다.
    */
    public void drain() {
        if(!isEmpty() && isBoiled()) {
            // 끓인 재료를 다음 단계로 넘김
            emtpy = true;
        }
    }
    
    /**
    * 보일러에 들어있는 재료를 끓인다. 
    */
    public void boil() {
        if(!isEmpty() && !isBoiled()) {
            emtpy = true;
        }
    }
    
    public boolean isEmpty() {
        return empty;
    }
    
    public boolean isBoiled() {
        return boiled;
    }
}
~~~

위의 코드는 괜찮아보인다. 그러나 만약에 ChocolateBoiler 의 인스턴스 여러개가 각기 생성되면 어떤 문제가 발생할까? 

- 보일러에 들어있는 재료가 끓고 있는데 재료를 부어버릴 수 있다.
- 재료를 동시에 다음 단계로 넘겨서 다음 단계에 문제가 생길 수 있다.

위의 문제를 해결하기 위해, ChocolateBoiler 에 싱글턴 패턴을 적용할 수 있다.

~~~java
public class ChocolateBoiler {
    private boolean empty;
    private boolean boiled;
    private ChocolateBoiler uniqueInstance;
    
    private ChocolateBoiler() {
        empty = true; // 이 코드는 보일러가 비어있을 때만 동작한다
        boiled = false; 
    }
    
    public static ChocolateBoiler getInstance() {
        if(uniqueInstance == null) {
            uniqueInstance = new ChocolateBoiler();
        }
        
        return uniqueInstance;
    }
    
    public void fill() {
        if(isEmpty()) { // 보일러가 비어있을 때만 재료를 가득 채운다. 원료를 가득 채우고 나면 boiled 를 false 로 돌려놓는다.
            empty = false;
            boiled = false;
            // 보일러에 우유/초콜릿을 혼합한 재료를 집어넣음
        }
    }
    
    public void drain() {
        if(!isEmpty() && isBoiled()) {
            // 끓인 재료를 다음 단계로 넘김
            emtpy = true;
        }
    }
    
    public void boil() {
        if(!isEmpty() && !isBoiled()) {
            emtpy = true;
        }
    }
    
    public boolean isEmpty() {
        return empty;
    }
    
    public boolean isBoiled() {
        return boiled;
    }
}
~~~

아까 앞에서 보았던 것과 같은 싱글턴 패턴을 적용하면 하나의 인스턴스만 생성하게 강제할 수 있다. 이제 안심하고 천천히 싱글턴의 정의를 살펴보자

# 싱글턴 패턴의 정의

지금까지 고전적인 싱글턴 구현법을 알아보았다. 이제 싱글턴 패턴의 정의를 살펴보자.

> 싱글턴 패턴은 해당 클래스의 인스턴스가 하나만 만들어지고, 어디서든지 그 인스턴스에 접근할 수 있도록 하기 위한 패턴이다

위의 정의를 보면 두 가지 조건이 만족해야 함을 알 수 있다.

- 해당 클래스의 인스턴스가 하나만 만들어진다
- 어디서든지 그 인스턴스에 접근할 수 있다.

위의 두 조건을 만족하기 위해서는 우선 클래스에서 자신의 하나뿐인 인스턴스를 관리하게 하면 된다. 즉, 다른 어떤 클래스에서도 이 클래스의 인스턴스를 만들지 못하게 해야 한다.
또한 다른 객체에서 인스턴스가 필요하면 이 클래스에 인스턴스를 요청하고, 싱글턴 클래스에서는 인스턴스가 없다면 생성 후 반환해주면 된다. 이런 lazy loading 은 생성자가 무거운 경우 유용하다.

# JVM 이 되어보자

위의 코드를 가지고 ChocolateBoiler 를 실제로 동작시켜 보았다. 그런데 fill() 메소드에서 아직 초콜릿이 끓고 있는데 재료를 집어넣어버린게 아닌가? 싱글턴 패턴이 제대로 구현되지 않은 건가?

문제를 해결하기 위해 두 개의 JVM 에서 다음과 같은 코드를 실행시켜본다고 가정해보자

~~~java
public static ChocolateBoiler getInstance() {
    if(uniqueInstance == null) {
        uniqueInstance = new ChocolateBoiler();
    }
    
    return uniqueInstance;
}
~~~

만약에 1번 쓰레드에서 getInstance 를 수행할 때, 2번 스레드에서도 동시에 getInstance 를 하면

~~~java
if(getInstanc == null)
~~~

이 부분에서 두 개의 스레드가 동시에 저 라인을 통과해버린다. 두 개의 스레드에서는 각각 uniqueInstance 이 초기화되기 전이라서, null 이기 때문이다. 
그렇기 때문에 동시에 두 개의 인스턴스가 생성되게 되고, 초콜릿이 끓고 있는데 재료를 집어넣는 것과 같은 일이 발생할 수 있다.

어떻게 해야 할까? 일단 동기화를 하는 방법을 생각해 볼 수 있다.

# 멀티스레딩 문제 해결 방법

## getInstance() 를 동기화

~~~java
public class ChocolateBoiler {
    private boolean empty;
    private boolean boiled;
    private ChocolateBoiler uniqueInstance;
    
    private ChocolateBoiler() {
        empty = true;
        boiled = false; 
    }
    
    public static synchronized ChocolateBoiler getInstance() { // getInstance 를 동기화한다.
        if(uniqueInstance == null) {
            uniqueInstance = new ChocolateBoiler();
        }
        
        return uniqueInstance;
    }
}
~~~

이렇게 간단하게 문제를 해결할 수 있다. 코드는 정확히 동작한다. 그러나, 조금 생각해 보면 이렇게 동기화를 하기가 아깝다는 느낌이 들 수 있다. 동기화가 필요한 시점은 이 메소드가 시작되는 때 뿐이다.

바꿔 말하자면, 일단 uniqueInstance 변수에 ChocolateBoiler 가 대입된 이후에는 굳이 이 메소드를 동기화된 상태로 유지할 필요가 없는 것이다. uniqueInstance 를 초기화 한 이후에 해당 메소드를 동기화하는 것은 불필요한 오버헤드만 증가시킬 뿐이다.

## 더 효율적인 방법은 없을까?

혹시 다른 방법은 없을까? 몇 가지 방법을 생각해 볼 수 있다.

### getInstance() 의 속도가 그리 중요하지 않다면 그냥 둔다

이래도 된다. getInstance() 메소드가 애플리케이션에 큰 부담을 주지 않는다면 그냥 놔둬도 된다. 다만, 메소드를 동기화하면 성능이 100 배 정도 저하된다는 것은 기억해두어야 한다. 만약에 getInstance() 가 병목으로 작용한다면 어떻게 해야 할까?

### 인스턴스를 필요할 때 생성하지 말고, 처음부터 만들어버린다.

인스턴스를 다음과 같이 처음부터 만들어버리는 것도 좋은 방법이다.

~~~java
public class ChocolateBoiler {
    private boolean empty;
    private boolean boiled;
    private static ChocolateBoiler uniqueInstance = new ChocolateBoiler(); // static 변수에서 싱글톤 인스턴스를 생성한다. 이렇게 하면 스레드-세이프하다.
    
    private ChocolateBoiler() {
        empty = true;
        boiled = false; 
    }
    
    public static ChocolateBoiler getInstance() {
        return uniqueInstance;
    }
}
~~~

이렇게 하면 JVM 에서 유일한 인스턴스를 생성해 준다. 생성되기 전에는, 어떤 스레드로 uniqueInstance 정적 변수에 접근할 수 없다.

### DLC(Double-checking Locking) 을 써서 getInstance() 에서 동기화되는 부분을 줄인다

DCL 를 사용하면 일단 인스턴스가 생성되어 있는지를 확인한 다음, 생성되어 있지 않았을 때만 동기화를 할 수 있다. 이렇게 하면 처음에만 동기화를 하고 나중에는 동기화를 하지 않아도 된다.

코드는 다음과 같다.

~~~java
public class ChocolateBoiler {
    private boolean empty;
    private boolean boiled;
    private volatile ChocolateBoiler uniqueInstance; // 반드시 volatile 로 변수를 선언해야 한다.
    
    private ChocolateBoiler() {
        empty = true;
        boiled = false; 
    }
    
    public static ChocolateBoiler getInstance() {
        if (uniqueInstance == null) { // 인스턴스가 있는지 확인하고, 없으면 동기화 된 블록으로 진입한다.
            synchronized (ChocolateBoiler.class) {
                if (uniqueInstance == null) { // 블록으로 들어온 후에도 다시 변수가 null 인지 체크한다.
                    uniqueInstance = new ChocolateBoiler();
                }
            }
        }
        return uniqueInstance;
    }
}
~~~

주의해야 할 점은 DCL 는 자바 1.4 이전 버전에서는 사용할 수 없다는 것이다. 자바 1.4 이전의 JVM 에서는 volatile 키워드를 사용하더라도 동기화가 잘 되지 않는 것이 많다.

# 싱글턴 동기화 방법 선택

자, 다음과 같이 결정하면 된다.

1. 속도가 중요하지 않다면 getInstance 를 동기화한다.
2. 항상 필요한 인스턴스라면 static 으로 선언한 인스턴스 변수에 초기화한다.
3. 속도가 중요하다면 Double-check Locking 을 활용한다.

# 싱글턴의 서브클래스

싱글턴은 서브클래스를 만들 수 없다. 왜냐면 생성자가 private 으로 선언되어 있기 때문이다. 만약에 생성자를 public 으로 고치게 된다면, 다른 클래스에서 인스턴스를 만들 수 있기 때문에 더 이상 싱글턴이라고 볼수 없다.

생성자를 고친다고 하더라도 싱글턴은 정적 변수를 바탕으로 구현하기 때문에, 서브클래스를 만들면 모든 서브클래스가 똑같은 인스턴스 변수를 공유하게 되는 문제가 있다. 이를 막기 위해서는 베이스 클래스에서 레지스트리 같은 걸 구현해 놓아야 한다.

그러나 가장 우선적으로 고려해야 할 점은, 싱글턴을 확장해서 무엇을 할 것인지이다. 싱글턴의 서브클래스를 만들어야 할 상황이 왔다는 것은 애플리케이션에 싱글턴을 꽤 자주 사용하고 있다는 신호일 수 있다.
그런 상황이라면, 전반적인 디자인을 다시 고려해보는 게 좋다. 싱글턴은 제한된 용도로 특수한 상황에서 사용하기 위해 만들어진 디자인 패턴이기 때문이다.















