
# 새로그 추가할 때 요청을 어떻게 관리해야 대 참사가 일어나지 않을까? 

## 로그 추가 
 - 회사에서 가장 많이 받게 되는 요청이 
 - 새 로그 추가해주세요
 - 그런데 시간이 지나면 이게 다 `상하`게 된다. 
 - 이게 뭐지 왜지? 이거 빼도 되나? 아 무서워서 못빼겠다. 
 - 나중에 앱을 개변하게 될 때도, 이게 있어야 되지는 없어도 되는지 다들 갈등 

 - 또한가지 문제는 정의와 관련된 부분이다 
 - 컨벤션 
 - 예를 들어서 재방문 유저
    - 재 방문의 정의가, 기획팀, 개발팀, 마케팅팀 모두 달러서 
    - 세 부서 각각 기준으로 뽑아주는 회사도 보았다.
    - 그래고 더 큰 문제는 여기서 맨날 물어본다. 저희 정의가 어떻게 되었죠? 



## 직면하는 문제 결국 관리 없이 발생하는 문제는 아래와 같다.  
- 로그 상함 
- 로그 상싱
- 정의 공유 안됨
- 맥락 공유 안됨

## 최송한에 관리 항목 
 - 이럴 때 많이 나오는 시도가 , 사내 공통의 공용문서를 이용하는 것이다. 
 - 모두하 하나에 문서를 공유한다. 

## 쫌더 나은 방법은 
 - 별도의 소스를 훑어보는 프로그램이 
 - 소스를 뒤지며, 일정 규칙에 따라, 로그를 남기는 코드를 확인하고, 
 - 그 코드가 사내 로그 규칙 쉬트에 있는지 확인한다. 
 - 코드는 있는데, 쉬트에 없거나 
 - 쉬트에 있는데, 코드에 없거나 같은 경우 
 - 데이터가 있었다가 값자기 출현하지 않게 되는 경우 
 - 데이터 불륨 모니터링 도구가 워닝을 보낸다. 

## User <UnSync>
 - 대부분의 로그 코드는 개발자가 추가한다. 
 - 대부분의 실제 로그는 분석가가 읽는다. 
 - 설달 지나서 분석가가 읽을 때쯤 발견한다. 
 - 로그가 전부 똑같은 값 111로 남겨지고 있다. 
 - 지난 석달간 데이터 날아갔다. 
 - 새로 추가된 로그는 반드시 데이터 검수 프로세스가 있어야 한다. 
 - 추가된 로그에 대한 정합성 검사가 자동화 되어야 한다. (카디날리티: 로그 속성 정보 다채로움)

## 정리하면 
 - 사내 기획, 개발, 분석팀이 모두가 공용으로 보는 로그와 개념 정의문서가 필요하다. 
 - 소스코드를 읽어 연동되는 데이터 모니터링 도구가 필요하다. 
 - 이걸 만들어 두지 않으면 , 장기적으로 큰 화를 입게 된다. 
 - 나무 아미타불 

## 

 

