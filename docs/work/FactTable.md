

첫번째..
요건 
 - DB 리소스 가용률 확대? 
   운영 디비에 대량 작업(트렌젝션)발생으로 AWS 환경에 구성된 DB에 부하가 증가함. 
   짧은 주기로 프로시저를 이용하여 집계테이블(Fact)생성시 DB  가용률 문제가 발생. 

 * 정말 이것때문이라면 Event Invoker를 lamda에서 airflow 로 변경하고
   airflow에서 DB 리소스 사용량을 확인하여 집계 작업 여부를 동적으로 관리 하면 된다고 생각됨.
   ?? DB리소스 점유를 어떻게 연동 할수 있을까? 

선행 요건
 - Raw, Hist 결과를 S3 Or File로 생성이 선행되어야 함.
  - 기존 Lamda Invoke 주기가 1분인데 Disk I/O 를 발생하면서 기준과 유사하게 Fact 결과를 생성할수 있는가? 
 

두번째.. 
요건 
 - 메타버스 Request에 따른 늦은 응답속도
   BI 내에서 검색기간에 범위가 넓어지면 화면 응답이 길어짐. Raw Level 엑세스에 따른 집계에 많은 시간이 소비됨

선행 요건
 - Fact 테이블을 이용한 데이터 마트 구축


세번째.. 
요건 
 - BI사용확대에 따른 데이터 거버넌스 확대
   전사에 BI지표 항목및 분석항목을 공유하여 데이터 거버넌스를 확대 하는게 빠른 속도가 답보 되어야 함. 

선행 요건
 - 분석항목 관련 BI 지표 관련 브레이밍 스톤 필요 




---
























개요 : 퀵사이트 전환 계획 일정 재수립
 

배경 : AD FS 접속시스템 구성 및 신규 인력 (2) 충원에 따른 전환 일정 재 수립
 

내용  :  ‘2021년 10월까지 전환 완료 목표’
       - 일정은 NCIS 전체 장표에 대해 금년 10월 말까지 퀵사이트 전환 완료를 목표
       - 순서는 각 사업부 순차적 전환 → 인력 충원에 따라 2개 사업부 병행 작업을 통한 일정 단축
       - 방식은 기존 NCIS 조회 조건으로 적용하되, 신규 BI ML 기능 및 UI / UX 특성을 활용하여 구성






AI 팀 관련 개발 Work-Flow 
Enge 노드로 접속하여 ipynb 파일에 개발 진행 
Spark-submit 을 통해 ipynb 파일 실행 

AI 팀 관련 소스 운영에 있어 문제 
  - 소스 이원화 
    : ipynb를 이용하여 개발 완료시 Production 코드는 별도에 팀에서 생성하고 있음
      운영중 요건 변경및 기타 사유로 인해 코드 변경시 커뮤니 케이션 비용 증가발생  


  - 중복 소스 발생 
    : ipytnb 파일에 코드 개발시 사용자에 따른 중복 소스가 지속적으로 생성 



개선안
  - Enge 노드에서 Gitlab 접속 가능 여부?
    지라 티켓을 기준으로 브랜치 생성 (Edge 노드에 사용자별 폴더로 Branch)
      기능 테스트는 ipytnb를 이용해여 진행하고 진행
      로컬에서 작업한 내용을 기준으로 Gitlab을 통한 형상 관리

  - 로컬 환경에서 AWS S3 까지만 접근 할수 있을까? 이건 I AM 계정을 사용한다해도 불가능 해보인다


  - 

pip install jupyter_contrib_nbextensions

jupyter contrib nbextension install --user

{
  "version": 1,
  "Exporter": {
    "template_path": [
      ".",
      "/opt/conda/lib/python3.7/site-packages/jupyter_contrib_nbextensions/templates"
    ],
    "preprocessors": [
      "jupyter_contrib_nbextensions.nbconvert_support.CodeFoldingPreprocessor",
      "jupyter_contrib_nbextensions.nbconvert_support.PyMarkdownPreprocessor"
    ]
  }
}

