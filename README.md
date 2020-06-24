# 주가 지수 현황 분석 및 예측

주가 DATA 출처 : http://www.krx.co.kr 
환율 DATA 출처 : http://www.smbs.biz  
라면가격 DATA 출처 : http://www.price.go.kr  
재무제표 DATA 출처 : http://comp.fnguide.com¶   


## 1) 연도별 각 기업 주가 및 환율 분석
### ① 종가 히스토그램 및 분위수 그림
<img src="https://github.com/FELIX-imbetpy-choi/Python-stock-Analysis-Project1/blob/master/project-1.png?raw=true" width="500" height="500"> 

히스토그램으로 확인한 결과 종가 데이터가 정규분포가 아닌 것을 확인  
분위수 그림 확인 결과 마찬가지로 정규분포가 아닌 것을 확인  

`※분위수 그림이란?`  
대각선(qqline())을 기준으로 산점도 점들이  
가깝게 선형을 이루며 붙어 있으면 정규성을 보인다고 평가하고  
그렇지 않으면 정규성을 띠지 않는다고 보는 것  
