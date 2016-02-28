---
layout: post
title:  "[codewars] char_to_ascii"
tags : "codewars"
---

# 문제
Take a string and return a hash with all the ascii values of the characters in the string. Returns nil if the string is empty. The key is the character, and the value is the ascii value of the character. Repeated characters are to be ignored and non-alphebetic characters as well.

- 아스키코드로 변경하는 함수가 있을 것이라는 생각을 했다.
- 함수는 있었다.
- 그러나 문제는 정규표현식을 다루는 법을 잘 모른다는 것이었다.
# 나의 답
~~~ java
function charToAscii(string){
  if(string === "") return null;
  var removeBlank = string.replace(/\s/gi, "");
  var converted = removeBlank.replace(/[\W]/gi, "");
  var returnMap = {};
  for(var a = 0; a<converted.length; a++) {
    returnMap[converted[a]] = converted.charCodeAt(a);
  }
  return returnMap;
}
~~~
- 정규표현식을 이용하여 공백과 특수문자를 제거했다.
- 특수문자를 제거하면서 공백도 제거할 수 있음을 몰랐다. removeBlank에서 한 정규표현식은 지워도 된다.
- 정규표현식 어렵다...

# Best Solution
~~~ java
function charToAscii(string){
  if(string.length === 0) return null;
  var res = {};
  for(var i = 0; i < string.length; i++){
    var char = string.charAt(i);
    if(/[a-zA-Z]/.test(char))
      res[char] = char.charCodeAt();
  }
  return res;
}
~~~
- test를 이용해서도 문제를 풀 수 있다.
- test는 정규표현식에 매칭되는 문자열이 있는 경우 true를 리턴한다.

# 소감
- 정규표현식을 좀 더 공부해야겠다.
- 거의 모든 사람들이 test와 match를 사용했다. 나는 사고의 흐름을 알파벳이 아닌 것들을 고르겠어! 라고 했는데, 이건 결국 알파벳이랑 매칭되는 문자를 고르고 싶어. 라는 것과 똑같다. 정규표현식은 ~에 매칭되는 걸 고른다는 조건에서 좀 더 강력한 거 같다.

