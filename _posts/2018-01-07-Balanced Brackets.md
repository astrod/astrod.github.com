---
layout: post
title: "Balanced Brackets"
tags:
- algorithm
---

* toc
{:toc}

# 문제
[링크](https://www.hackerrank.com/challenges/balanced-brackets/problem)

간단하게 설명하면 다음과 같다.
'{[()]}' 와 같이, 좌우로 밸런스 잡힌 문자열이 들어오면 "YES" 리턴,
'{[(])}' 처럼 {} 이 괄호 안이 [( 뒤에 ])가 와서 밸런스가 맞지 않는 문자열은 "NO" 리턴

# My Solution
생각은 이랬다.

1. 스택을 만들어서 입력 스트링을 모두 스택에 붓는다.
2. 스택에서 pop 한다.
3. pop을 한 문자가 }, [, ) 중 하나이면, 스택2에 넣는다.
4. pop을 한 문자가 {, [, ( 중 하나이면, 스택 2에서 문자열을 꺼내서 두 문자열을 비교한다. 두 문자열이 닫힘 관계 - (), [], {} - 라면, 위의 과정을 계속 수행하고 아니라면 "NO"를 리턴한다.

코드는 다음과 같다.

~~~java
static String isBalanced(String s) {
    LinkedList<Character> stack = new LinkedList<>();
    LinkedList<Character> closeStack = new LinkedList<>();
    for(int i = 0; i<s.length(); i++) {
        stack.add(s.charAt(i));
    }
    
    int loopSize = stack.size();
    
    for(int j = 0; j<loopSize; j++) {
        Character c = stack.removeLast();
        
        if(c.equals(')') || c.equals(']') || c.equals('}')) {
            closeStack.add(c);
        } else {
            if(closeStack.isEmpty()) {
                return "NO"; 
            }
            Character close = closeStack.removeLast();
            if(!isPair(c, close)) {
                return "NO";
            }
        }                                  
    }
        
    return "YES";
}
~~~

코드를 submit 했는데, 18개 테스트 세트중에 9번 테스트 세트를 통과하지 못했다. 테스트 세트를 열어봤는데 600여개의 테스트 세트중에 무엇이 틀린건지 눈으로 찾을 수가 없어서, 눈 디버깅 하다가 포기. 해커랭크는 뭐가 틀렸는지 보여주는 기능 넣어줘야 된다 진짜...

그래서 다른 사람이 한 답을 보기로 결정

# Solution
답은 이와 같다.

1. 스택에 문자열 한 개를 넣는다.
2. 스택에 문자가 두 개 이상 들어가 있는 경우, 스택에서 가장 최근에 들어온 두 문자를 비교한다. 두 문자가 닫힘 관계이면 스택에서 pop을 두번 한다.
3. 위의 과정을 모두 수행한 후에 스택에 남은 문자가 있는지 체크

Balanced Brackets 인 경우에는, 가장 최근에 들어온 문자에 닫힘문자가 들어와야 한다. 예를 들면, '{[()]}' 은 ( 문자열 다음 문자열이 ) 이기 때문에 두 문자열이 동시에 pop 되면서 사라지게 된다.

반대로, {[(])}은 ( 뒤에 ]가 들어오면서 문자열이 사라지지 않는다.
코드는 다음과 같다.

~~~java
static String isBalanced(String s) {
    LinkedList<Character> stack = new LinkedList<>();
    char upperElement = 0;
    for (int i = 0; i < s.length(); i++) {
        if (!stack.isEmpty()) {
            upperElement = stack.peek();
        }
        stack.push(s.charAt(i));
        if (!stack.isEmpty() && stack.size() > 1) {
            if ((upperElement == '[' && stack.peek() == ']') ||
                    (upperElement == '{' && stack.peek() == '}') ||
                    (upperElement == '(' && stack.peek() == ')')) {
                stack.pop();
                stack.pop();
            }
        }
    }
    return stack.isEmpty() ? "YES" : "NO";
}
~~~

## 시간/공간 복잡도 Time/Space Complexity

### Time Complexity 
- 문자열 전체를 순회하는 시간 : O(n)
- 비교연산 / pop : O(1)

이므로 총 시간 복잡도는 O(n) 이다.

### Space Complexity
- 최대 문자열 크기만큼 스택 크기가 증가하므로, 총 공간 복잡도는 O(n) 이다.

