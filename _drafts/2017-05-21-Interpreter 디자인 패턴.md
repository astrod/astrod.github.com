---
title: "Interpreter 디자인 패턴"
tags:
- design_pattern
---

# 들어가며
이 포스팅의 내용은 <<Java 언어로 배우는 디자인 패턴>> 23장 Interpreter 패턴을 정리한 것입니다. 여기 나오는 코드는 JDK 1.8에서 작성, 테스트 되었습니다.
이 포스팅에 있는 코드는 [여기](https://github.com/astrod/design-pattern/tree/master/src/main/java/pattern/JU/interpreter) 서 확인하실 수 있습니다.
코드 또한 책에서 예제로 나온 코드를 조금 변경한 것입니다.

# Interpreter 패턴
디자인 패턴의 목적 중 하나는, 클래스의 재이용성을 높이는 것입니다. 재이용성이란 한 번 작성한 클래스 별로 수정하지 않고 몇 번이고 사용할 수 있도록 하는 것입니다.
이 포스팅에서는 인터프리터 패턴에 대해 이야기 할 것입니다. 인터프리터 패턴은 프로그램이 해결하려는 문제를 간단한 '미니 언어'로 표현합니다. 구체적인 문제를 미니 언어로 기술된 미니 프로그램으로 표현합니다. 미니 언어는 그 자체만으로 동작하지 않습니다. 따라서, Java 언어로 통역(interpreter) 역할을 하는 프로그램을 만들어 둡니다.

통역 프로그램은 미니 언어를 해석하고, 미니 프로그램을 해석/실행합니다. 이 통역 프로그램을 인터프리터라고도 부릅니다. 해결해야 할 문제에 변화가 생기면, 프로그램 자체를 수정하는 것이 아닌 미니 프로그램 자체를 수정합니다.

## 미니 언어
이 포스팅에서 예제 프로그램을 설명하기 전에, 다룰 미니 언어에 대해 이야기하겠습니다. 여기서는 무선조정으로 자동차를 움직이기 위한 언어를 생각해 보겠습니다. 자동차를 움직여서 가능한 일은 다음 세 가지입니다.

1. 앞으로 1미터 전진(go)
2. 우회전(right)
3. 좌회전(left)

실제 자동차는 위치를 변경하지 않고 오른쪽/왼쪽으로 움직일 수 없지만, 여기서는 위치를 변경하지 않고도 턴 테이블이 회전하듯이 방향을 바꿀 수 있다고 가정합니다.
이것만으로는 너무 단순하기 때문에 반복 명령을 추가해 보겠습니다.

4. 반복(repeat)

이 명령들을 조합하여 자동차를 움직일 수 있습니다.

### 미니 프로그램 예제
몇 가지 예제를 살펴보겠습니다. 다음에 표시하는 것은 자동차를 전진시키고 멈추는 미니 프로그램입니다.

~~~
program go end
~~~

1. program : 프로그램의 시작을 알립니다.
2. go : 앞으로 1미터 전진시킵니다.
3. end : 프로그램을 끝냅니다.

다음은 자동차가 앞으로 전진한 후, 제자리에서 오른쪽으로 회전하여 되돌아오는 미니 프로그램입니다. program과 end 사이에 임의의 명령을 넣을 수 있습니다.

~~~
program go right right go end
~~~

다음은 오른쪽으로 회전하는 것이 아니라, 정사각형을 그리고 제자리로 돌아오는 미니 프로그램입니다.

~~~
program go right go right go right go right end
~~~

위의 프로그램을 이렇게 변경할 수 있습니다.

~~~
program repeat 4 go right end end
~~~

물론, repeat 안에서 repeat를 사용할 수도 있습니다.

### 미니 언어의 문법
여기에서 미니 언어의 문법을 설명하겠습니다. 여기에서 사용할 표기법은 BNF라고 불리는 것의 변형입니다. BNF는 Backus-Naur Form, 또는 Backus Normal Form의 약자로 언어의 문법을 표기할 때 자주 이용됩니다.

~~~
<program> : : = program <command list>
<command list> : : = <command> * end
<command> : : = <repeat command> | <primitive command>
<repeat command> : : = repeat <number> <command list>
<primitive command> : : = go | right | left
~~~

예제 프로그램의 인터프리터가 사용하는 미니 언어의 문법은 다음과 같습니다.
BNF는 이전에 ALGOL 60을 기술하기 위해 사용된 표기법입니다. BNF와 유사한 표기법이 B.C. 수백 년 전에 산스크리트어의 구문을 기술하기 위해서 사용된 적도 있었습니다.

표기법은 다음과 같습니다.

1. 화살표 왼쪽에 위치한 텍스트(예제에서는 : : = 왼쪽에 위치한 텍스트)가 정의되고자 하는 문법입니다.

예를 들면, <program\> : : = program <command list\> 에서 <program\>은 정의되어야 하는 문장입니다. 꺽쇠괄호(<)는 흔히 추상화의 이름을 구분하는데 사용됩니다. 위의 텍스트에서 왼쪽의 <program\>을 LHS(Left-Hand side)라고 부릅니다.

2. : : = 오른쪽에 위치한 텍스트는 RHS(Right-Hand side)라고 부릅니다. 이는 LHS의 정의이며, 토큰, 어휘항목, 다른 추상화에 대한 참조 등으로 구성됩니다. 이를 규칙(rule)혹은 생성(production)이라고 부릅니다.

3. <program\>과 같이 꺽쇄로 묶인 기호를  논터미널 기호라고 부릅니다. 반면에 0, 1 ... 과 같이 직접 나타낼 수 있는 기호를 터미널 기호라고 합니다. | 와 같이 BNF에서 사용되는 특수한 기호를 메타 기호라고 합니다.

다음 포스팅이 잘 설명되어 있는 거 같아 링크 남깁니다. [링크](http://blog.shar.kr/969)

이 미니언어에서 추가된 문법이 있습니다.

~~~
<command list> : : = <command>* end
~~~

에서 0회 이상의 반복을 나타내는 * 이 있습니다. 이는 기본 BNF에는 없는 기호입니다.

## 예제 프로그램
이 포스팅에서 제작할 예제 프로그램은 미니 언어를 구문해석한 것입니다. 앞에서 미니 언어의 내용을 설명할 때, 미니 프로그램의 각 부분을 분해하여 이야기했습니다. 미니 프로그램이 주어졌을 때, 구문 트리를 메모리 상에 만드는 처리가 구문 해석입니다.

여기서는 미니 프로그램이 주어졌을 때 구문 트리를 구축하는 예제 프로그램을 제작해 볼 예정입니다.

다이어그램은 다음과 같습니다.

![다이어그램]({{ site.url }}/asset/interpreter.PNG)

### Node
Node 클래스는 구문 트리의 각 부분을 구성하는 최상위 클래스입니다. 여기에는 추상 메서드 parse만 선언되어 있습니다. parse는 구현체가 구현하게 됩니다. 파라미터로 넘어가는 Context는 구문해석을 실행하고 있는 상황을 나타내는 클래스입니다.

~~~java
public abstract class Node {
    public abstract void parse(Context context) throws ParseException;
}
~~~

### TokenType
미니 언어에서 사용하는 토큰의 타입을 정의한 Enum 클래스입니다. 또한 입력받은 값이 end인지, repeat인지 판정하는 메서드도 포함하고 있습니다.

~~~java
public enum TokenType {
    PROGRAM("program"),
    COMMAND("command"),
    GO("go"),
    RIGHT("right"),
    LEFT("left"),
    END("end"),
    REPEAT("repeat");

    private final String type;

    TokenType(String type) {
        this.type = type;
    }

    public String getType() {
        return type;
    }

    public static TokenType findTokenTypeByType(String type) {
        TokenType [] values = TokenType.values();
        for(TokenType tokenType : values) {
            if(tokenType.getType().equals(type)) {
                return tokenType;
            }
        }

        return null;
    }

    public boolean isEnd() {
        return END == this;
    }

    public boolean isRepeat() {
        return REPEAT == this;
    }

    public boolean isUndefinedToken() {
        return GO != this && LEFT != this && RIGHT != this;
    }
}
~~~

### ProgramNode
이 클래스는 다음과 같은 미니 프로그램을 코드로 옮겼습니다.

~~~
<program> : : = program <command list>
~~~

이 클래스는 Node형의 List를 데이터로 들고 있습니다. 이는 <command list\>에 대응하는 데이터를 저장하기 위함입니다.

