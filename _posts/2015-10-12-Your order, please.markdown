---
layout: post
title:  "[TIL] your order, please"
tags : codewars
---
# 문제
Description:

Your task is to sort a given string. Each word in the String will contain a single number. This number is the position the word should have in the result.

Note: Numbers can be from 1 to 9. So 1 will be the first word (not 0).

If the input String is empty, return an empty String. The words in the input String will only contain valid consecutive numbers.

For an input: "is2 Thi1s T4est 3a" the function should return "Thi1s is2 3a T4est"

## 내 해답

```
function order(words){
  if(words === "") return ""
  var list = words.split(" ");
  var numberMap = {};
  for(var i = 0; i<list.length; i++) {
    var regExp = /[0-9]/;
    var num = regExp.exec(list[i]);
    numberMap[num] = list[i];
  }
  var returnString = "";
  for(var i = 1; i<=list.length; i++) {
    returnString += numberMap[i];
    returnString += " ";
  }
  return returnString.trim();
}
```

## Best Solution

```
function order(words){
  
  return words.split(' ').sort(function(a, b){
      return a.match(/\d/) - b.match(/\d/);
   }).join(' ');
}   
```
- 나도 정규표현식을 사용할 생각은 했는데, 이렇게 우아하게 적용할 생각은 못 했었다. join을 활용할 수도 있었을 테고...궂이 문자열 뒤에 +=를 한 다음에 trim()을 사용할 필요는 없었을 거 같다.

```
var reg = /\d/;

function order(words){
  return words.split(' ').sort(comparator).join(' ');
}

function comparator(word, nextWord) {
  return +word.match(reg) - +nextWord.match(reg)
}
```
- 거의 다 비슷한 해답인 듯? 일단 공백을 기준으로 자르고, 비교자를 이용하여 정렬을 하는데 대상은 숫자를 기준으로 sort. 그 후에 join을 이용하여 붙인다.
- match는 문장에서 처음으로 만나는 숫자를 출력해주는 메서드이다. 이를 이용하면 숫자만 추려내서 그 숫자대로 정렬을 할 수 있다.