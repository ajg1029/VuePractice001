# VuePractice001

## 2023-02-03

달력을 그릴 때, getFullYear() 또는 getMonth() 를 사용하면 무조건 버그가 생길 수 밖에 없다는 사실을 이제야 깨달음;;

benchmarkDay에서 달력 생성에 관련된 모든 로직이 시작되어야 한다...

## 2023-01-21

#### (4)

vue 연습하려고 만들었는데 어쩌다보니 calendar project가 되어버린...

#### (3)

일단 메모 기능은 빼고

달력 표시, 주간 넘버링

일 넘기기, 달 넘기기, 연 넘기기

(달력에 표시된) 날짜 클릭 시 이동

#### (2)

1. 캘린더 스타일 설정
   
   * firstDay 선택 (6, 0, 1)
   
   * weekArrangeType 선택 (+0, +3)

2. week 번호 방식 설정
   
   * weekNumberingType (month, year)

3. targetDatetime 에 default 값 할당 (currentDatetime)

4. targetDatetime으로부터 firstDay 에 해당하는 날짜가 나올 때까지 왼쪽으로 한 칸씩 이동하면서 검증

5. firstDay 나오면 weekArrangeType에 따라서 다시 오른쪽으로 이동

6. 도착한 날짜가 drawingDatetime이 되고, 요일이 benchmarkDay가 된다.

7. drawingDatetime과 benchmarkDay를 기준으로 calendar를 그리고 month와 week를 numbering 한다.

8. weekNumberingType이 year인 경우, 한 해의 첫 번째 benchmarkDay를 확인하여 week를 numbering한다.
* 네 가지 타입의 데이터가 있다. date memo, week memo, month memo, year memo

* date memo
  
  * meta - 'YYYY-MM-DD', 'Date()'
  
  * 일간 메모, 캘린더 스타일을 변경해도 그대로 보임

* week memo
  
  * meta -firstDay, weekArrangeType, year, month, monthWeek, yearWeek
  
  * 주간 메모, 캘린더 스타일이 변경되면 보이지 않음
  
  * week 번호 방식이 바뀌는 건 상관 없음. 넘버링이 다르게 보일 뿐

* month memo, year memo
  
  * 이 두 개도 캘린더 스타일의 영향을 받는 걸로 하자.

#### (1)

* ver.3 작업 중...

* 새로운 방법으로 하려고 했는데, 이거도 해답이 안 되는 것 같다.

* targetDate가 calendarWeeks에서 벗어나는 경우에만 달력을 '새롭게' 그리는 방법...
  
  * 이전 달 마지막 주의 마지막 날 또는 다음 달 첫째 주의 첫 번째 날이 현재 월에 포함인 경우에는 weeks가 새롭게 그려지지 않는다. drawingDatetime이 업데이트 되어도 여전히 targetDate와 같기 때문에...
  
  * weeks 안에 들어있는 week들에 month에 대한 정보를 붙이면 해결할 수 있을 듯하다.
  
  * index 가 생기겟군
  
  * targetDate가 calendar를 벗어나면
  
  * !!!!
  
  * targetDate가 아니라, month 를 기준으로 그려야겠다!
  
  * 왜 이 생각을 못했지? 당연한 건데?
  
  * targetDate가 calendar를 벗어나면, month 를 +1 하는 방법으로 weeks 를 새로 그려야겠군
  
  * drawingDatetime이 아닌 drawingMonth를 쓰면 될까나

* 달력 양식에 종속된 주차별 정보는 추후에 추가하려고 했는데
  
  * month 를 기준으로 week를 그리게 되면, 자연스럽게 주차별 정보가 생길 수 밖에 없겠네

* ver.3 날리고 ver.4 로다가 새로 만드는 게 나을 듯...

* month는 두 종류가 생긴다.
  
  * date에 종속된 month
  
  * week에 종속된 month
  
  * ex) 2023년 1월 30일 월요일
    
    * date로 따지면 1월이다.
    
    * week로 따지면 '2월'의 첫째 주 월요일이다. (대한민국 표준 - 월요일 시작, 과반수)
  
  * ex) 2023년 4월 1일 토요일
    
    * date로 따지면 4월이다.
    
    * week로 따지면 '3월'의 다섯 번째 주 토요일이다. (대한민국 표준 - 월요일 시작, 과반수)
  
  * ex) 2023년 3월 3일 금요일
    
    * date로 따지면 3월이다.
    
    * week로 따지면 '3월'의 첫째 주 금요일이다. (대한민국 표준 - 월요일 시작, 과반수)
      
      * 해당 주(월~일)의 과반수가 3월이기 때문이다.
    
    * week로 따지면 '2월'의 네 번째 주 금요일이다. (일요일 시작, 전체)
      
      * 해당 주(일~토)의 전체가 3월에 있지 않기 때문이다.

* day option 을 1, 2, 3, 4, 5, 6, 0 으로 하지 말고
  
  * 6(토), 0(일), 1(월)로만 하자
  
  * benchmarkDay 도 일곱 개 다 놔두지 말고 ...
  
  * 과반수는 +3, 전체는 +0 더한 요일을 기준점으로 잡자

* '한 주의 첫 요일' & '한 달의 첫 주'
  
  * 이 둘에만 집중하면 쉬울 것 같다...

* '달력 양식' 이름 뭐로 하지? 캘린더 스타일?

* 프로젝트 이름은? 이레 사이 iresai calendar / 레사 캘린더 i-re-sa-i calender

* 서버, DB, 배포, PWA, electron 까지... 얼마나 걸릴까

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