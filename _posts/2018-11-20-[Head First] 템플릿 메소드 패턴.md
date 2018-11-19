---
layout: post
title: "[Head First] 템플릿 메소드 패턴"
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
이 글에 나오는 내용은 <<Head First Design Patterns>> 8장, 템플릿 메소드 패턴에 나오는 내용을 정리한 것이다.

# 커피 바리스타 훈련 메뉴얼

다음은 스타버즈 커피와 홍차 만드는 훈련용 메뉴얼이다.

## 스타버즈 커피 만드는 법

1. 물을 끓인다.
2. 끓는 물에 커피를 우려낸다.
3. 커피를 컵에 따른다.
4. 설탕과 우유를 추가한다.

## 스타버즈 홍차 만드는 법

1. 물을 끓인다.
2. 끓는 물에 차를 우려낸다.
3. 차를 컵에 따른다.
4. 레몬을 추가한다.

# 커피 및 홍차 클래스 만들기

이제 "코딩 바리스타" 가 될 차례이다. 커피와 차를 만들기 위한 클래스를 만들어 보자

일단 커피 클래스는 다음과 같다.

~~~java
public class Coffee {
    
    void prepareRecipe() {
        boilWater();
        brewCoffeeGrinds();
        pourInCup();
        addSugarAndMilk();
    }
    
    public void boilWater() {
        System.out.println("물 끓이는 중");
    }
    
    public void brewCoffeeGrinds() {
        System.out.println("필터를 통해서 커피를 우려내는 중");
    }
    
    public void pourInCup() {
        System.out.println("컵에 따르는 중");
    }
    
    public void addSugarAndMilk() {
        System.out.println("설탕과 우유를 추가하는 중");
    }
}
~~~

훈련용 메뉴얼에 나와 있는 내용을 그대로 코드로 옮긴 것이다.

이제 홍차 클래스도 확인해 보자

~~~java
public class Tea {
    
    void prepareRecipe() {
        boilWater();
        steepTeaBag();
        pourInCup();
        addLemon();
    }
    
    public void boilWater() {
        System.out.println("물 끓이는 중");
    }
    
    public void steepTeaBag() {
        System.out.println("차를 우려내는 중");
    }
    
    public void pourInCup() {
        System.out.println("컵에 따르는 중");
    }
    
    public void addLemon() {
        System.out.println("레몬을 추가하는 중");
    }
}
~~~

두 개의 코드를 보면 대단히 흡사해 보인다. 물을 끓이는 작업과 컵에 따르는 작업은 동일하다. 다른 작업은 커피/차를 우리는 것과 첨가물을 추가하는 작업이다.

어떻게 잘 하면 코드 중복을 제거할 수 있지 않을까?

# 코드 중복 제거

아마 이와 같은 방식으로 추상화하면 좋을 거 같다.

~~~java
abstract class CaffeineBeverage {
    abstract void prepareRecipe();
    public void boilWater() {
        System.out.println("물 끓이는 중");
    }
    public void pourInCap() {
        System.out.println("컵에 따르는 중");
    }
} 

class Coffee extends CaffeineBeverage {
    @Override
    void prepareRecipe() {
        // implements ...
    }
    
    public void brewCoffeeGrinds() {
        System.out.println("필터를 통해서 커피를 우려내는 중");
    }
    
    public void addSugarAndMilk() {
        System.out.println("설탕과 우유를 추가하는 중");
    }
}

class Tea extends CaffeineBeverage {
    @Override
    void prepareRecipe() {
        // implements ...
    }
    
    public void steepTeaBag() {
        System.out.println("차를 우려내는 중");
    }
    
    public void addLemon() {
        System.out.println("레몬을 추가하는 중");
    }
}
~~~

공통적인 부분은 추상 클래스로 추출하고, Tea 와 Coffee 가 추상 클래스를 구현하게 만들었다.
썩 나쁘지는 않은 거 같다. 하지만 좀 더 자세히 보니, 다음과 같은 의문이 든다.

prepareRecipe() 메소드도 추상화 할 수 있지 않을까?

만드는 법을 확인해 보면, 두 음료의 제작 알고리즘이 동일함을 알 수 있다.

1. 물을 끓인다.
2. 뜨거운 물을 이용하여 음료를 우려낸다.
3. 만들어진 음료를 컵에 따른다.
4. 각 음료에 맞는 첨가물을 추가한다.

그렇다면, prepareRecipe() 메서드도 추상화시킬 수 있는 방법이 있지 않을까?

# prepareRecipe() 추상화하기

1. 우선 첫 번째 문제점은, Tea 에서는 steepTegBag 과 addLemon 메소드를 사용하지만, Coffee 에서는 brewCoffeeGrinds 와 addSugarAndMild 메서드를 사용한다는 점이다.

그러나 잘 따져보면, 커피를 우려내는 것과 티백을 물에 넣어 홍차를 우려내는 것은 크게 다르지 않다. 사실 거의 비슷하다고 볼 수 있다. 그러면, brew() 라는 메소드를 새로 만들고, 커피를 만들든 홍차를 만들든 위의 메소드를 사용하도록 하자.

이와 마찬가지로, 설탕과 우유를 추가하는 것과 레몬을 추가하는 것 또한 크게 다르지 않다. 그러니까 addCondiments() 라는 메소드를 양쪽에서 모두 사용해도 좋다.

~~~java
void prepareRecipe() {
    boilWater();
    brew();
    pourInCup();
    addCondiments();
}
~~~

2. 새로운 prepareRecipe() 메소드가 준비되었다. 이제 이 메소드를 코드에 집어넣어보자. 우선, CaffeineBeverage 부터 시작하자

~~~java
public abstract class CaffeineBeverage {
    
    final void prepareRecipe() { // 상속받는 클래스에서 이 순서를 변경할 수 없게 final 로 선언한다.
        boilWater();
        brew();
        pourInCup();
        addCondiments();
    }
    
    abstract void brew(); // Tea 와 Coffee 메소드에서 brew 와 addCondiments 를 각기 다른 방식으로 구현할 것이다.
    
    abstract void addCondiments();
    
    void boilWater() {
        System.out.println("물 끓이는 중");
    }
    
    void pourInCup() {
        System.out.println("컵에 따르는 중");
    }
}
~~~

3. 이제 Tea 와 Coffee 클래스를 구현한다. 이 두 클래스에서 음료를 만드는 법은 CaffeineBeverage 에 의해 결정되므로, brew 와 addCondiments 만 따로 구현해주면 된다.

~~~java
public class Tea extends CaffeineBeverage {
    public void brew() {
        System.out.println("차를 우려내는 중");
    }
    
    public void addCondiments() {
        System.out.println("레몬을 추가하는 중");
    }
}

public class Coffee extends CaffeineBeverage {
    public void brew() {
        System.out.println("필터로 커피를 우려내는 중");
    }
    
    public void addCondiments() {
        System.out.println("설탕과 커피를 추가하는 중");
    } 
}
~~~

# 템플릿 메소드 패턴

지금까지 한 것이 템플릿 메소드 패턴이다. CaffeineBeverage 클래스에 들어있는 게 바로 **템플릿 메소드**이다.

~~~java
 public abstract class CaffeineBeverage {
     
     /**
     * 템플릿 메소드. 어떤 알고리즘에 대한 템플릿(틀)역할을 한다. 이 경우에는 카페인이 들어있는 음료를 만들기 위한 알고리즘의 템플릿이다. 
     */  
     final void prepareRecipe() { 
         boilWater(); // 템플릿 내에서 각 단계는 메소드로 표현된다.
         brew();
         pourInCup(); // 어떤 메소드는 이 클래스 내에서 구현된다.
         addCondiments(); // 어떤 메소드는 서브클래스에서 처리된다. 서브클래스에서 구현할 메소드는 abstract 로 클래스 내부에서 선언된다.
     }
     
     abstract void brew(); // Tea 와 Coffee 메소드에서 brew 와 addCondiments 를 각기 다른 방식으로 구현할 것이다.
     
     abstract void addCondiments();     
     
     void pourInCup() {
         // implements
     }
}
~~~

템플릿 메소드에서는 알고리즘들의 각 단계들을 정의하며, 그 중 한 개 이상의 단계가 서브클래스에 의해 제공될 수 있다.

맨 처음에 만들었던 Tea, Coffee 클래스에 비해, 템플릿 메소드 패턴이 적용된 Tea, Coffee 클래스는 다음과 같은 장점이 있다.

1. CaffeineBeverage 클래스에서 작업을 처리하기 때문에, 알고리즘이 하나로 통일된다.
2. Tea, Coffee 에 중복된 코드가 존재하지 않는다.
3. 알고리즘이 한 군데에 모여 있기 때문에 그 부분만 고치면 된다.
4. 카페인 음료를 추가할 때는 CaffeineBeverage 클래스를 extends 한 후, 몇 가지 메소드만 추가하면 된다.
5. CaffeineBeverage 클래스에 알고리즘에 대한 지식이 집중되어 있으며, 일부 구현만 서브클래스에 의존한다.

# 템플릿 메소드 패턴의 정의

> 템플릿 메소드 패턴에서는 메소드에서 알고리즘의 골격을 정의합니다. 알고리즘의 여러 단계 중 일부는 서브클래스에서 구현할 수 있습니다. 템플릿 메소드를 이용하면 알고리즘의 구조는 그대로 유지하면서 서브클래스에서 특정 단계를 재정의할 수 있습니다.

이 패턴은 알고리즘의 틀을 만들기 위해 사용한다. 틀이란, 일련의 단계들로 알고리즘을 정의한 메소드이다. 여러 단계 가운데 하나 이상이 추상 메소드로 정의되며, 그 추상 메소드는 서브클래스에서 구현된다. 이렇게 하면 서브클래스에서 일부분을 구현할 수 있으면서도, 알고리즘의 구조는 바꾸지 않을 수 있다.

코드로 보면 다음과 같다.

~~~java
abstract class AbstractClass {
    
    /**
    * 템플릿 메소드에서는 각 단계들을 순서대로 정의하는데, 각 단계는 메소드로 표현된다. 
    */
    final void templateMethod() {
        primitiveOperation1();
        primitiveOperation2();
        concreteOperation();
        hook();
    }
    
    default void primitiveOperation1();
 
    default void primitiveOperation2(); // 각 단계중 두 개는 서브 클래스에서 구현하도록 하였다.
    
    /**
    * 구상 단계는 추상 클래스 내에서 정으된다. 이 메소드는 final 이기 때문에 오버라이드 할 수 없다. 
    * 이 메소드는 추상 클래스에서 호출할 수도 있고, 서브클레스에서 호출하여 사용할 수도 있다. 
    */
    final void concreteOperation() {
        // implements
    }
    
    void hook(); // 아무것도 하지 않는 구상 메소드를 정의할 수도 있다. 이런 메소드는 후크라고 부른다.
}
~~~

# 템플릿 메소드와 후크

후크(hook) 는 추상 클래스에서 선언되는 메소드긴 하지만, 기본적인 내용만 구현되어 있거나 아무 코드도 들어있지 않은 메소드이다. 이렇게 하면 서브클래스 입장에서 다양한 위치에서 알고리즘에 끼어들 수 있다. 코드로 보면 다음과 같다.

~~~java
public abstract class CaffeineBeverageWithHook {
    
    void prepareRecipe() {
        boilWater();
        brew();
        pourInCup();
        if (customerWantsCondiments()) {
            addCondiments();
        }
    }
    
    abstract void brew();
    abstract void addCondiments();
    
    void boilWater() {
        System.out.println("물 끓이는 중");
    }
    
    /**
    * 이 메소드는 후크 메소드로, 필요한 경우에는 하위 클래스에서 오버라이드 할 수 있다. 
    */
    boolean customerWantsCondiments() {
        return true;
    }
}
~~~
 
# 후크 활용

후크를 활용하려면 서브클래스에서 오버라이드해야 한다. 이 예에서는 CaffeineBeverage 에서 알고리즘의 특정 부분을 처리할지 여부를 결정하기 위한 용도로 후크를 사용하였다.

~~~java
public class CoffeeWithHook extends CaffeineBeverageWithHook {
    
    public void brew() {
        // implements
    }
    
    public void addCondiments() {
        // implements
    }
    
    public boolean customerWantsCondiments() {
        String answer = getUserInput();
        
        if (answer.toLowerCase().startsWith("y")) {
            return true;
        }
        
        return false;
    }
    
    private String getUserInput() {
        String answer = null;
        
        System.out.println("커피에 우유와 설탕을 넣어 드릴까요?");
        
        BufferedReader in = new BufferedReader(new InputStreamReader(System.in));
        
        try {
            answer = in.nextLine();
        } catch (IOException ex) {
            System.err.println("IO 오류");
        }
        
        if (answer == null) {
            return "no";
        }
        
        return answer;
    }
}
~~~ 

# 정리

정리하면 다음과 같다.

1. 알고리즘에서 특정 단계를 제공해야만 하는 경우는 추상 메소드를 사용한다. 단, 알고리즘의 특정 부분이 선택적으로 적용된다면 후크를 사용하면 된다.
2. 후크는 템플릿 메소드에서 앞으로 발생할 일 또는 막 일어난 일에 대해 서브클래스에서 반응할 기회를 제공하기 위한 용도로 쓰인다.
3. 서브클래스에서는 모든 추상 메소드를 정의하여야 한다. 그렇기 때문에, 추상 메소드가 너무 많아지면 좋지 않다. 그러나 너무 메소드를 큼직하게 나눠 놓으면 유연성이 떨어지기 때문에 밸런스를 맞추어야 한다.

# 헐리우드 원칙

또 다른 디자인 원칙이 여기서 나타난다. 이 원칙은 헐리우드 원칙이라고 부른다.

> 먼저 연락하지 마세요. 저희가 먼저 연락 드리겟습니다.

헐리우드 원칙을 활용하면 의존성 부패를 방지할 수 있다. 의존성 부패는 어떤 고수준 구성요소가 저수준 구성요소에 의존하고, 저수준 구성요소가 다시 고수준 구성요소에 의존하고 이런 식으로 의존성이 복잡하게 꼬여 있는 상황을 일컫는다.

헐리우드 원칙을 활용하면, 저수준 구성요소에서 시스템에 접속을 할 수는 있지만 언제 어떤 식으로 그 구성요소들을 사용할지는 고수준 구성요소에 결정하게 된다.

# 헐리우드 원칙과 템플릿 메소드 패턴

헐리우드 원칙과 템플릿 메소드 패턴 사이의 관계는 금방 확인할 수 있다. 템플릿 메소드 패턴을 사용하면 '우리가 먼저 연락할 테니 연락하지 마.' 라고 이야기하는것돠 같다.

템플릿 메소드 패턴에서 서브클래스는 추상클래스에 의존한다. 서브클래스에서는 구체적인 클래스가 아니라, 추상클래스의 추상화되어있는 부분에 의존하게 된다. 그렇기 떄문에 전체 시스템의 의존성이 줄어들게 된다.

# 야생의 템플릿 메소드 패턴

템플릿 메소드 디자인 패턴은 자주 사용되는 패턴이라서 야생에서도 어렵지 않게 발견할 수 있다. 그러나, 패턴 교제에서 주로 보여주는 패턴과 실제로 사용되는 디자인 패턴 사이에 차이가 있기 때문에, 유심히 보지 않으면 템플릿 메소드 패턴인지 알기 어려운 경우가 있다.

이 패턴이 자주 쓰이는 이유는 프레임워크를 만드는데 아주 훌륭한 디자인 도구이기 때문이다. 프레임워크에서 작업이 처리되는 순서를 정하면서도, 구체적인 로직은 서브클래스에 두고 후크로 로직의 실행여부를 결정할 수 있다.

야생에서 발견할 수 있는 디자인 패턴을 확인해 보자

# 템플릿 메소드를 이용한 정렬

자바의 Arrays.java 에는 정렬할 떄 사용할 수 있는 편리한 템플릿 메소드가 있다.

~~~java
class Arrays {
    public static void sort(Object[] a) {
        if (LegacyMergeSort.userRequested)
            legacyMergeSort(a);
        else
            ComparableTimSort.sort(a, 0, a.length, null, 0, 0);
    }
    /** To be removed in a future release. */
    private static void legacyMergeSort(Object[] a) {
        Object[] aux = a.clone(); // 배열의 복사본을 생성한다.
        mergeSort(aux, a, 0, a.length, 0);
    }
    
        private static void mergeSort(Object[] src,
                                      Object[] dest,
                                      int low,
                                      int high,
                                      int off) {
            int length = high - low;
    
            // Insertion sort on smallest arrays
            if (length < INSERTIONSORT_THRESHOLD) {
                for (int i=low; i<high; i++)
                    for (int j=i; j>low &&
                             ((Comparable) dest[j-1]).compareTo(dest[j])>0; j--)
                        swap(dest, j, j-1);
                return;
            }
    
            // Recursively sort halves of dest into src
            int destLow  = low;
            int destHigh = high;
            low  += off;
            high += off;
            int mid = (low + high) >>> 1;
            mergeSort(dest, src, low, mid, -off);
            mergeSort(dest, src, mid, high, -off);
    
            // If list is already sorted, just copy from src to dest.  This is an
            // optimization that results in faster sorts for nearly ordered lists.
            if (((Comparable)src[mid-1]).compareTo(src[mid]) <= 0) {
                System.arraycopy(src, low, dest, destLow, length);
                return;
            }
    
            // Merge sorted halves (now in src) into dest
            for(int i = destLow, p = low, q = mid; i < destHigh; i++) {
                if (q >= high || p < mid && ((Comparable)src[p]).compareTo(src[q])<=0)
                    dest[i] = src[p++];
                else
                    dest[i] = src[q++];
            }
        }
}    
~~~  

현재는 하위호환성을 위해 남아있는 코드지만, 야생의 템플릿 패턴을 확인하기 위해 위 코드를 확인해 보자. 사용자가 Legacy Merge Sort 를 사용하기로 요청하면, Arrays 클래스에서는 배열의 복사본을 만든 후에 mergeSort 에 해당 값을 전달해 준다.

mergeSort 메소드에는 정렬 알고리즘이 들어있으며, compareTo 에 의해 결과가 결정된다. 위의 로직을 간단하게 살펴보자.

1. INSERTIONSORT_THRESHOLD(value 는 7) 보다 작은 경우에는 insertion sort 를 수행한다. insertion sort 시에는 두 원소의 값을 비교하는 알고리즘이 필요한데, 이를 **compareTo** 로 Object 에서 구현하게 하였다.
2. 7과 같거나 큰 경우는 재귀호출을 통해 mergeSort 를 구현하고 있다. 재귀호출을 통해 리스트를 분할하고, 이후에 두 개의 리스트를 머지한다. 이 때 각 리스트에 값들의 크고 작음을 알기 위해 **compareTo** 를 호출한다.

즉, compareTo 메소드가 Object 에 구현되어 있어야 함을 알 수 있다.

위의 구조는 추상클래스와 서브클래스를 사용하지 않았을 뿐이지, 템플릿 메소드 패턴 구조와 동일하다. 그러면 왜 이런 구조를 만들었을까?

sort() 를 디자인한 사람들은 모든 배열에서 이 메소드를 쓸 수 있도록 하려고 했다. 그래서 sort() 를 static method 로 만들었다. 이는 별 문제가 되지 않는데, 수퍼클래스에 들어있는 것과 동일하게 생각하면 되기 때문이다.

더 큰 문제점은 sort() 가 특정 수퍼클래스에 정의되어 있는 것이 아니기 때문에, sort() 메소드에서 compareTo 를 제대로 구현하였는지 알아낼 수 있는 방법이 필요하다는 것이다. 이 문제를 해결하기 위해서 Comparable 이라는 인터페이스가 도입되었다.

Comparable 인터페이스는 다음과 같다.

~~~java
public interface Comparable<T> {
    public int compareTo(T o);
}
~~~ 

이 인터페이스를 구현한 다음, 이를 Arrays.sort 에 파라미터로 넘기면 된다.

~~~java
public class Duck implements Comparable {
    String name;
    int weight;
    
    public Duck(String name, int weight) {
        this.name = name;
        this.weight = weight;
    }    
    
    public int compareTo(Object object) {
        Duck otherDuck = (Duck)object;
        
        if (this.weight < otherDuck.weight) {
            return -1;
        }
        
        if(this.weight == otherDuck.weight) {
            return 0;
        }
        
        if (this.weight > otherDuck.weight) {
            return 1;
        }
    }
}
~~~

이제 위의 Duck 객체를 배열로 Arrays.sort 에 넘기면 List<Duck> 은 compareTo 에 구현한 로직에 맞춰서 정렬되게 된다.

이것 또한 템플릿 메소드 패턴인지 애매하게 느낄 수 있다. 템플릿 메소드 패턴의 정의를 보면, 알고리즘을 구현하고, 일부 단계는 서브클래스에서 구현한 것을 써서 처리한다고 나와 있다. Arrays.sort 에서는 위와 같이 패턴을 적용하기에는 몇 가지 제약사항이 있었다.

- 자바 배열의 서브클래스를 만들 수 없지만, 어떤 배열에서도 정렬 기능을 사용할 수 있어야 한다.

그렇기 때문에 static method 를 정의하고, 알고리즘에서 대수비교를 하는 부분을 정렬될 객체에서 구현하게 한 것이다.

