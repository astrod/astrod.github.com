---
layout: post
title:  "[Codewars] Vasya - Clerk"
tags : "codewars"
---

# 문제
The new "Avengers" movie has just been released! There are a lot of people at the cinema box office standing in a huge line. Each of them has a single 100, 50 or 25 dollars bill. A "Avengers" ticket costs 25 dollars.

Vasya is currently working as a clerk. He wants to sell a ticket to every single person in this line.

Can Vasya sell a ticket to each person and give the change if he initially has no money and sells the tickets strictly in the order people follow in the line?

Return YES, if Vasya can sell a ticket to each person and give the change. Otherwise return NO.

- 어려운 문제라고 1도 생각 안했는데 상당히 고생했다.
- 문제의 스케일이 커지게 되면 어떻게 해야 하지? 이 방법으로는 노답;;; 이 될거 같은데.


# 내 해답
```
function tickets(peopleInLine){
  var moneyPool = {25 : 0, 50: 0, 100 : 0};
  var flag = true;
  peopleInLine.every(function(value) {
    moneyPool[value] += 1;
    if(value === 50) {
      if(moneyPool[25] > 0) {
            moneyPool[25] -= 1;
            return true;
      }
      flag = false;
      return false;
    } else if(value === 100) {
        if(moneyPool[25] > 0 && moneyPool[50] > 0) {
            moneyPool[25] -= 1;
            moneyPool[50] -= 1;
            return true;
        }
        if(moneyPool[25] > 2) {
            moneyPool[25] -= 3;
            return true;
        }
        
        flag = false;
        return false;
      
    }
    return true;
  });
  if(flag) return "YES";
  else return "NO";
}
```
- 모든 경우의 수를 따져서 문제가 생기면 break를 하고 에러를 리턴한다.
- 경우의 수를 잘 못 따져서 고생했다. 좀 더 아름다운 방법이 있을 거 같은데...

# Best Solution
```
function tickets(peopleInLine) {
  var bills = [0, 0, 0]
  for (var i = 0; i < peopleInLine.length; i++) {
    switch (peopleInLine[i]) {
      case 25:
        bills[0]++
        break
        
      case 50:
        bills[0]--
        bills[1]++
        break
        
      case 100:
        bills[1] ? bills[1]-- : bills[0] -= 2
        bills[0]--
        break
    }
    
    if (bills[0] < 0) {
      return 'NO'
    }
  }
  
  return 'YES'
}
```
- switch-case를 사용해서 코드를 좀 더 깔끔하게 다듬었다.
- 내가 한 방법과 크게 다르지 않음.
- every를 사용한 한계인 거 같기도 하다.

# 결론
Best Solution을 살펴봤는데 아름다운 해답은 없는 거 같았다.
이 문제는 추상화시키면 결국 25, 50을 가지고 25 / 75를 만들건데 어떻게 만들꺼야? 라는 문제랑 일맥상통한 건데...어떻게 할 수 있을지 고민해봐야겠다.