# VuePractice001



## 2023-01-20

* ver1에서 발견한 문제점
  
  * 달력에 표시되는 날짜에 현재 날짜가 보이지 않는데도 넘어가지 않거나, 달력에 현재 날짜가 표시되고 있는데도 달력이 넘어가는 현상
  
  * 달력 표시를 월 기준이 아닌 주 기준으로 해서 그렇다
    
    * 첫째 주에 지난 달의 일부가 있거나 마지막 주에 다음 달의 일부가 포함된 경우

* ver2에서 고치려고 시도한 방법
  
  * Date() 객체, 'YYYY-MM-DD' 포맷을 분리한 이후에 만들면은 어떻게든 될 거라고 생각했음
  
  * 그래서 selectedDatetime 을 watch에 넣고, 달력에 표시되는 건 'YYYY-MM-DD'로 만들었다.

* ver2에서도 안 된 이유
  
  * 애초에 selectedDatetime을 기준으로 weeks display를 구성한 이상 당연한 결과다.
  
  * selectedDatetime으로부터 년, 월, 일 등의 정보를 가져와서 배열하였음...

* ver3에서는 이렇게 해보자
  
  * selectedDatetime을 기준으로 weeks를 구성하되, 그 동작의 trigger는 selectedDatetime이 아닌 다른 변수가 되어야 할 것 같다.
  
  * Date() 객체를 사용하지 말까? 몇 시인지 알 필요는 없긴 해...
  
  * 아, 아니다. 음, 아, 뷁, 이렇게 하면 될 것 같다.
    
    * selectedDatetime은 변경될 때마다 업데이트 되어야 한다.
    
    * (datetime(Date() 객체) 대신 date('YYYY-MM-DD')의 사용을 늘리자)
    
    * weeks를 그리기 위한 변수, datetimeForDisplay를 별도로 선언
    
    * datetimeForDisplay 의 default값은 selectedDatetime과 같음
    
    * datetimeForDisplay 를 기준으로 weeks 구성한다.
    
    * selectedDatetime이 weeks에서 벗어나면 그 때 다시 datetimeForDisplay를 업데이트 해주면 될 것이다.



## 2023-01-19

1. calendar
- setDate 를 사용하면 원래 값이 변경된다...
- 공식문서 - 1970 년 1 월 1 일 00:00:00 UTC와 주어진 날짜 사이의 밀리 초 (Date 개체도 변경됩니다).

Date() 객체

700101기준

YYYY-MM-DD 포맷

각각에 따라서 1일을 더하거나 빼는 방법이 달라짐

(무ㅓ야 왜 shift+Enter 안 되노)

(marktext 에서는 줄바꿈 되는데, github 에선 안 됨)

* 현재 문제점...
  
  * 다음 날짜로 넘길 때, 이번 달에 포함되어 있더라도, displayedWeeks 에 보이지 않는 날짜를 선택하면 달력이 새로고침 되어야 하는데,
    달이 바뀌기 전까지는 달력이 그대로다
  
  * 이전 날짜로 넘길 때, 저번 달에 포함되어 있더라도, displayedWeeks 에 보이는 날짜라면 달력이 새로고침 되지 않아야 하는데,
    달이 바뀌면 달력이 바로 바뀌어버린다.

달력에 현재 날짜 표시...

위에 텍스트로 뜨는 날짜와, 달력에 영향을 미치는 날짜가 뭔가 연동이 안 되고 있다?!

##### Date() 객체 안에는 GMT 정보가 있어...

![captureImg001](./%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202023-01-20%20%EC%98%A4%EC%A0%84%2012.17.24.png)