# VuePractice001

1. calendar

setDate 를 사용하면 원래 값이 변경된다...
공식문서 - 1970 년 1 월 1 일 00:00:00 UTC와 주어진 날짜 사이의 밀리 초 (Date 개체도 변경됩니다).

Date() 객체
700101기준
YYYY-MM-DD 포맷

각각에 따라서 1일을 더하거나 빼는 방법이 달라짐

현재 문제점...
다음 날짜로 넘길 때, 이번 달에 포함되어 있더라도, displayedWeeks 에 보이지 않는 날짜를 선택하면 달력이 새로고침 되어야 하는데,
달이 바뀌기 전까지는 달력이 그대로다
이전 날짜로 넘길 때, 저번 달에 포함되어 있더라도, displayedWeeks 에 보이는 날짜라면 달력이 새로고침 되지 않아야 하는데,
달이 바뀌면 달력이 바로 바뀌어버린다.

달력에 현재 날짜 표시...

위에 텍스트로 뜨는 날짜와, 달력에 영향을 미치는 날짜가 뭔가 연동이 안 되고 있다?!

##### Date() 객체 안에는 GMT 정보가 있어...
![captureImg001](./%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202023-01-20%20%EC%98%A4%EC%A0%84%2012.17.24.png)