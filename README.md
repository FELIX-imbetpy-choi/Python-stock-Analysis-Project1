# Python-stock-Analysis-Project1
오뚜기, 삼양, 농심 3 회사의 주가 움직임에 대해서 분석하고, 예측하는 모델을 구성해 보았습니다.
library(doBy)
library(png)
library(rJava)
library(xlsx)
PROJECT[주가 지수 현황 분석 및 예측]
PROJECT 기간 : 2019-01-11 ~ 2019-01-18
주가 DATA 출처 : http://www.krx.co.kr / 환율 DATA 출처 : http://www.smbs.biz
라면가격 DATA 출처 : http://www.price.go.kr / 재무제표 DATA 출처 : http://comp.fnguide.com
Part_01 > TOP3 기업 주가지수 및 외부 요인 현황 분석

연도별 각 기업 주가 및 환율 분석
1) 종가 히스토그램 및 분위수 그림
2) 종가 / 시가총액 추세 비교
3) 환율과 거래량 추세 비교
4) 각 기업의 주가는 서로에게 영향을 미칠까

각 기업 최근 주가 데이터 현황 분석
1) 각 기업의 최근 1년 시가 총액
2) 각 기업의 2018.12(최근) 거래량

각 기업 라면 가격과 종가 관계 확인

각 기업의 재무제표

Part_02 > TOP3 기업 재무제표 추론 및 회귀분석을 통한 예측 실시

농심 데이터 추론 및 예측
오뚜기 데이터 추론 및 예측
삼양 데이터 추론 및 예측
Part_03 > 분석 후기 및 한계점

-----------------------------------------------------------------------------------------------------------
치열한 경쟁을 하고있는 식품 업체
pic_1 <-readPNG("../Data/뉴스.PNG")
# 이미지 불러오기
pic_2 <- pic_1[,,1]
image(t(pic_2)[,nrow(pic_2):1], col=grey(seq(0,1,length.out=256)))

라면 판매율 TOP3라고 할 수 있는 농심, 오뚜기, 삼양은 현재 음식 관련 제품들로 치열한 경쟁을 펼치고 있다.
현재 후발 주자인 오뚜기와 삼양은 이색 제품으로 시장 점유율을 높이고 있는 반면, 농심은 이색 제품 개발에 실패하며 쓴맛을 보고 있다.
그렇다면 이 기업들의 주가지수 현황은 어떠할까?

--------------------------------------------------------------------------------------------------------------
1. TOP3 기업 주가지수 및 외부 요인 현황 분석
> 농심
n <- read.csv('../Data/농심.csv', fileEncoding="CP949", encoding="UTF-8")
names(n) <- c("날짜","종가","대비","거래량","거래대금","시가","고가","저가","시가총액","상장주식수")
nongshim <- orderBy(~날짜, data = n)
nongshim$연도 <- substr(nongshim$날짜, 1, 4)
nongshim$월 <- substr(nongshim$날짜, 6, 7)
nongshim$일 <- substr(nongshim$날짜, 9, 10)
nongshim <- nongshim[,c(1,11,12,13,2,3,4,5,6,7,8,9,10)] # 컬럼 순서 바꾸기
head(nongshim)
날짜	연도	월	일	종가	대비	거래량	거래대금	시가	고가	저가	시가총액	상장주식수
1226	2014-01-02	2014	01	02	251000	0	23071	5852140000	249000	256000	249000	1526743	6082642
1225	2014-01-03	2014	01	03	259000	8000	47507	12192800500	251500	260500	251500	1575404	6082642
1224	2014-01-06	2014	01	06	262500	3500	34170	8913629500	257500	262500	256000	1596694	6082642
1223	2014-01-07	2014	01	07	261000	-1500	27989	7362108000	259000	264500	259000	1587570	6082642
1222	2014-01-08	2014	01	08	264500	3500	28517	7570813000	261000	268500	260000	1608859	6082642
1221	2014-01-09	2014	01	09	264500	0	22401	5987313500	265000	268500	264500	1608859	6082642
summary(nongshim)
         날짜          연도                월                 일           
 2014-01-02:   1   Length:1226        Length:1226        Length:1226       
 2014-01-03:   1   Class :character   Class :character   Class :character  
 2014-01-06:   1   Mode  :character   Mode  :character   Mode  :character  
 2014-01-07:   1                                                           
 2014-01-08:   1                                                           
 2014-01-09:   1                                                           
 (Other)   :1220                                                           
      종가             대비               거래량          거래대금        
 Min.   :214000   Min.   :-43500.00   Min.   :  3282   Min.   :8.187e+08  
 1st Qu.:269750   1st Qu.: -3000.00   1st Qu.: 13578   1st Qu.:4.052e+09  
 Median :313000   Median :  -500.00   Median : 19195   Median :5.971e+09  
 Mean   :314553   Mean   :     2.85   Mean   : 23126   Mean   :7.390e+09  
 3rd Qu.:344000   3rd Qu.:  3000.00   3rd Qu.: 27677   3rd Qu.:9.062e+09  
 Max.   :534000   Max.   : 34000.00   Max.   :177474   Max.   :5.701e+10  
                                                                          
      시가             고가             저가           시가총액      
 Min.   :213000   Min.   :217500   Min.   :211500   Min.   :1301685  
 1st Qu.:270000   1st Qu.:273500   1st Qu.:266125   1st Qu.:1640793  
 Median :312250   Median :316000   Median :308500   Median :1903867  
 Mean   :314455   Mean   :318719   Mean   :310325   Mean   :1913313  
 3rd Qu.:344000   3rd Qu.:349000   3rd Qu.:339500   3rd Qu.:2092429  
 Max.   :540000   Max.   :540000   Max.   :500000   Max.   :3248131  
                                                                     
   상장주식수     
 Min.   :6082642  
 1st Qu.:6082642  
 Median :6082642  
 Mean   :6082642  
 3rd Qu.:6082642  
 Max.   :6082642  
                  
> 오뚜기
o <- read.csv('../Data/오뚜기.csv', fileEncoding="CP949", encoding="UTF-8")
names(o) <- c("날짜","종가","대비","거래량","거래대금","시가","고가","저가","시가총액","상장주식수")
ottugi <- orderBy(~날짜, data = o)
ottugi$연도 <- substr(ottugi$날짜, 1, 4)
ottugi$월 <- substr(ottugi$날짜, 6, 7)
ottugi$일 <- substr(ottugi$날짜, 9, 10)
ottugi <- ottugi[,c(1,11,12,13,2,3,4,5,6,7,8,9,10)] # 컬럼 순서 바꾸기
head(ottugi)
날짜	연도	월	일	종가	대비	거래량	거래대금	시가	고가	저가	시가총액	상장주식수
1226	2014-01-02	2014	01	02	407500	9500	15168	6205181000	396500	418500	386000	1401800	3440000
1225	2014-01-03	2014	01	03	386500	-21000	7560	2960196500	406500	406500	386000	1329560	3440000
1224	2014-01-06	2014	01	06	382500	-4000	4547	1751204500	386500	390500	381000	1315800	3440000
1223	2014-01-07	2014	01	07	384500	2000	3741	1454030500	382000	393500	382000	1322680	3440000
1222	2014-01-08	2014	01	08	386500	2000	4097	1604348500	383500	399000	383500	1329560	3440000
1221	2014-01-09	2014	01	09	387500	1000	4075	1590676000	386500	394500	386000	1333000	3440000
summary(ottugi)
         날짜          연도                월                 일           
 2014-01-02:   1   Length:1226        Length:1226        Length:1226       
 2014-01-03:   1   Class :character   Class :character   Class :character  
 2014-01-06:   1   Mode  :character   Mode  :character   Mode  :character  
 2014-01-07:   1                                                           
 2014-01-08:   1                                                           
 2014-01-09:   1                                                           
 (Other)   :1220                                                           
      종가              대비               거래량          거래대금        
 Min.   : 368000   Min.   :-147000.0   Min.   :   575   Min.   :3.482e+08  
 1st Qu.: 618250   1st Qu.:  -9000.0   1st Qu.:  3009   1st Qu.:1.928e+09  
 Median : 741000   Median :   -500.0   Median :  4300   Median :3.037e+09  
 Mean   : 738746   Mean   :    265.9   Mean   :  5345   Mean   :4.296e+09  
 3rd Qu.: 821750   3rd Qu.:   8000.0   3rd Qu.:  6282   3rd Qu.:5.058e+09  
 Max.   :1425000   Max.   : 138000.0   Max.   :100556   Max.   :1.053e+11  
                                                                           
      시가              고가              저가            시가총액      
 Min.   : 365500   Min.   : 373500   Min.   : 362500   Min.   :1265920  
 1st Qu.: 615750   1st Qu.: 632500   1st Qu.: 604250   1st Qu.:2126780  
 Median : 742000   Median : 750000   Median : 732000   Median :2566240  
 Mean   : 738664   Mean   : 750660   Mean   : 727435   Mean   :2545968  
 3rd Qu.: 825000   3rd Qu.: 836000   3rd Qu.: 814000   3rd Qu.:2826820  
 Max.   :1434000   Max.   :1466000   Max.   :1398000   Max.   :4902000  
                                                                        
   상장주식수     
 Min.   :3440000  
 1st Qu.:3440000  
 Median :3440000  
 Mean   :3446604  
 3rd Qu.:3440000  
 Max.   :3605237  
                  
> 삼양
s <- read.csv('../Data/삼양.csv', fileEncoding="CP949", encoding="UTF-8")
names(s) <- c("날짜","종가","대비","거래량","거래대금","시가","고가","저가","시가총액","상장주식수")
samyang <- orderBy(~날짜, data = s)
samyang$연도 <- substr(samyang$날짜, 1, 4)
samyang$월 <- substr(samyang$날짜, 6, 7)
samyang$일 <- substr(samyang$날짜, 9, 10)
samyang <- samyang[,c(1,11,12,13,2,3,4,5,6,7,8,9,10)] # 컬럼 순서 바꾸기
head(samyang)
날짜	연도	월	일	종가	대비	거래량	거래대금	시가	고가	저가	시가총액	상장주식수
1226	2014-01-02	2014	01	02	24400	-250	28215	686700000	24650	24650	24050	183806	7533015
1225	2014-01-03	2014	01	03	25200	800	104812	2623945250	24400	25350	24350	189832	7533015
1224	2014-01-06	2014	01	06	25350	150	76733	1920138650	24750	25500	24550	190962	7533015
1223	2014-01-07	2014	01	07	25150	-200	92880	2341579100	25500	25850	24200	189455	7533015
1222	2014-01-08	2014	01	08	25150	0	30060	751421500	25150	25300	24700	189455	7533015
1221	2014-01-09	2014	01	09	26700	1550	141389	3668248700	25150	26750	25000	201132	7533015
summary(samyang)
         날짜          연도                월                 일           
 2014-01-02:   1   Length:1226        Length:1226        Length:1226       
 2014-01-03:   1   Class :character   Class :character   Class :character  
 2014-01-06:   1   Mode  :character   Mode  :character   Mode  :character  
 2014-01-07:   1                                                           
 2014-01-08:   1                                                           
 2014-01-09:   1                                                           
 (Other)   :1220                                                           
      종가             대비              거래량           거래대금        
 Min.   : 20550   Min.   :-7000.00   Min.   :   4880   Min.   :1.033e+08  
 1st Qu.: 24200   1st Qu.: -500.00   1st Qu.:  23054   1st Qu.:6.166e+08  
 Median : 29825   Median :  -50.00   Median :  40162   Median :1.626e+09  
 Mean   : 44102   Mean   :   22.31   Mean   :  68666   Mean   :3.536e+09  
 3rd Qu.: 58475   3rd Qu.:  400.00   3rd Qu.:  78528   3rd Qu.:4.359e+09  
 Max.   :113500   Max.   :11000.00   Max.   :1161831   Max.   :7.531e+10  
                                                                          
      시가             고가             저가           시가총액     
 Min.   : 20400   Min.   : 20700   Min.   : 20400   Min.   :154803  
 1st Qu.: 24200   1st Qu.: 24500   1st Qu.: 23900   1st Qu.:182299  
 Median : 29900   Median : 30425   Median : 29425   Median :224672  
 Mean   : 44151   Mean   : 45096   Mean   : 43220   Mean   :332221  
 3rd Qu.: 58600   3rd Qu.: 59650   3rd Qu.: 57300   3rd Qu.:440493  
 Max.   :113500   Max.   :117500   Max.   :109000   Max.   :854997  
                                                                    
   상장주식수     
 Min.   :7533015  
 1st Qu.:7533015  
 Median :7533015  
 Mean   :7533015  
 3rd Qu.:7533015  
 Max.   :7533015  
                  
-----------------------------------------------------------------------------------------------------------
1) 연도별 각 기업 주가 및 환율 분석
① 종가 히스토그램 및 분위수 그림
* 종가 : 장 마감 때 가격
par(mfrow = c(2,3))
hist(nongshim$종가, main = "농심", xlab = "종가", col = "red")
hist(ottugi$종가, main = "오뚜기", xlab = "종가", col = "yellow")
hist(samyang$종가, main = "삼양", xlab = "종가", col = "orange")

qqnorm(nongshim$종가, main = "농심")
qqline(nongshim$종가)

qqnorm(ottugi$종가, main = "오뚜기")
qqline(ottugi$종가)

qqnorm(samyang$종가, main = "삼양")
qqline(samyang$종가)

히스토그램으로 확인한 결과 종가 데이터가 정규분포가 아닌 것을 확인
분위수 그림 확인 결과 마찬가지로 정규분포가 아닌 것을 확인

※분위수 그림이란?
대각선(qqline())을 기준으로 산점도 점들이
가깝게 선형을 이루며 붙어 있으면 정규성을 보인다고 평가하고
그렇지 않으면 정규성을 띠지 않는다고 보는 것

shapiro.test(nongshim$종가)
shapiro.test(ottugi$종가)
shapiro.test(samyang$종가)
	Shapiro-Wilk normality test

data:  nongshim$종가
W = 0.97073, p-value = 4.846e-15
	Shapiro-Wilk normality test

data:  ottugi$종가
W = 0.95709, p-value < 2.2e-16
	Shapiro-Wilk normality test

data:  samyang$종가
W = 0.82649, p-value < 2.2e-16
shapiro.test 결과 p값이 모두 0.05보다 작아 귀무가설 기각, 즉 정규분포가 아니다.
따라서 part_02의 회귀분석을 통한 종가 예측을 시작할 때 주가의 변동을 줄이기 위해
최근 1년치 데이터를 sampling 해보는 것이 좋겠다.

② 종가 / 시가총액 추세 비교
year_nongshim <- aggregate(종가 ~ 연도, nongshim, mean)
year_ottugi <- aggregate(종가 ~ 연도, ottugi, mean)
year_samyang <- aggregate(종가 ~ 연도, samyang, mean)
year_nongshim_s <- aggregate(시가총액 ~ 연도, nongshim, mean)
year_ottugi_s <- aggregate(시가총액 ~ 연도, ottugi, mean)
year_samyang_s <- aggregate(시가총액 ~ 연도, samyang, mean)
year_nongshim_d <- aggregate(거래량 ~ 연도, nongshim, mean)
year_ottugi_d <- aggregate(거래량 ~ 연도, ottugi, mean)
year_samyang_d <- aggregate(거래량 ~ 연도, samyang, mean)
year_nongshim
year_ottugi
year_samyang
year_nongshim_s
year_ottugi_s
year_samyang_s
연도	종가
2014	278214.3
2015	307941.5
2016	364457.3
2017	333598.8
2018	288479.5
연도	종가
2014	478898.0
2015	822596.8
2016	872878.0
2017	758847.7
2018	759180.3
연도	종가
2014	26928.16
2015	23395.56
2016	30901.83
2017	56880.04
2018	82975.00
연도	시가총액
2014	1692278
2015	1873098
2016	2216863
2017	2029162
2018	1754718
연도	시가총액
2014	1647409
2015	2829733
2016	3002700
2017	2610436
2018	2635112
연도	시가총액
2014	202850.3
2015	176239.1
2016	232783.9
2017	428478.2
2018	625051.9
par(mfrow = c(2,1))
plot(year_nongshim, type = "b", col="red", lty = 1, ylim = c(0, 900000), main = "연도별 기업 종가")
lines(year_ottugi, type = "b", col="yellow", lty = 1)
lines(year_samyang, type = "b", col="orange", lty = 1)
legend(2016.5, 800000, # 좌표 위치
       c("농심","오뚜기","삼양"), # data 컬럼
       cex = 0.8, # 1을 기준으로 한 상대적 크기
       col = c("red","yellow","orange"), # 색깔 -> 데이터 컬러와 맞춰주어야함
       lty = 1
)
plot(year_nongshim_s, type = "b", col="red", lty = 1, ylim = c(0, 3500000), main = "연도별 기업 시가총액")
lines(year_ottugi_s, type = "b", col="yellow", lty = 1)
lines(year_samyang_s, type = "b", col="orange", lty = 1)
legend(2016.5, 3000000, # 좌표 위치
       c("농심","오뚜기","삼양"), # data 컬럼
       cex = 0.8, # 1을 기준으로 한 상대적 크기
       col = c("red","yellow","orange"), # 색깔 -> 데이터 컬러와 맞춰주어야함
       lty = 1
)

농심은 종가와 시가총액 모두 감소하는 추세를 보여주는 반면 오뚜기와 삼양은 증가하는 추세
위의 기사와 같이 오뚜기와 삼양은 이색제품으로 시장 점유율을 높이고 있는 반면, 농심은 주춤 하고있다.
③ 환율과 거래량 추세 비교
er <- read.csv('../Data/환율2.csv',  fileEncoding="CP949", encoding="UTF-8")
head(er)
er2 <- read.csv('../Data/위안 환율.csv',  fileEncoding="CP949", encoding="UTF-8")
head(er2)
날짜	통화명	환율
2014-01-02	미 달러화 (USD)	1055.3
2014-01-03	미 달러화 (USD)	1050.4
2014-01-06	미 달러화 (USD)	1054.1
2014-01-07	미 달러화 (USD)	1062.2
2014-01-08	미 달러화 (USD)	1067.9
2014-01-09	미 달러화 (USD)	1066.0
날짜	통화명	환율
2014.03.03	위안화 (CNH)	174.19
2014.03.04	위안화 (CNH)	174.94
2014.03.05	위안화 (CNH)	174.82
2014.03.06	위안화 (CNH)	174.82
2014.03.07	위안화 (CNH)	174.97
2014.03.10	위안화 (CNH)	173.91
er$연도 <- substr(er$날짜, 1, 4)
er$월 <- substr(er$날짜, 6, 7)
er$일 <- substr(er$날짜, 9, 10)
er <- er[,c(1,2,3,6,4,5)]

er2$연도 <- substr(er2$날짜, 1, 4)
er2$월 <- substr(er2$날짜, 6, 7)
er2$일 <- substr(er2$날짜, 9, 10)
er2 <- er2[,c(1,2,3,6,4,5)]
head(er)
head(er2)
날짜	통화명	환율	일	연도	월
2014-01-02	미 달러화 (USD)	1055.3	02	2014	01
2014-01-03	미 달러화 (USD)	1050.4	03	2014	01
2014-01-06	미 달러화 (USD)	1054.1	06	2014	01
2014-01-07	미 달러화 (USD)	1062.2	07	2014	01
2014-01-08	미 달러화 (USD)	1067.9	08	2014	01
2014-01-09	미 달러화 (USD)	1066.0	09	2014	01
날짜	통화명	환율	일	연도	월
2014.03.03	위안화 (CNH)	174.19	03	2014	03
2014.03.04	위안화 (CNH)	174.94	04	2014	03
2014.03.05	위안화 (CNH)	174.82	05	2014	03
2014.03.06	위안화 (CNH)	174.82	06	2014	03
2014.03.07	위안화 (CNH)	174.97	07	2014	03
2014.03.10	위안화 (CNH)	173.91	10	2014	03
year_er <- aggregate(환율 ~ 연도, er, mean)
year_er

year_er2 <- aggregate(환율 ~ 연도, er2, mean)
year_er2
연도	환율
2014	1053.034
2015	1131.325
2016	1160.307
2017	1131.087
2018	1100.230
연도	환율
2014	169.9194
2015	179.4756
2016	174.3950
2017	167.4595
2018	166.4151
par(mfrow = c(3,1))
plot(year_er, type = "b", col="blue",lty = 1, ylim = c(1000, 1300), main = "연도별 미 달러화 (USD) 환율")

plot(year_er2, type = "b", col="darkred",lty = 1, ylim = c(150, 200), main = "연도별 위안화 (CNH) 환율")

plot(year_nongshim_d, type = "b", col="red", lty = 1, ylim = c(0, 140000), main = "연도별 기업 거래량")
lines(year_ottugi_d, type = "b", col="yellow", lty = 1)
lines(year_samyang_d, type = "b", col="orange", lty = 1)
legend(2017.3, 100000, # 좌표 위치
       c("농심","오뚜기","삼양"), # data 컬럼
       cex = 0.9, # 1을 기준으로 한 상대적 크기
       col = c("red","yellow","orange"), # 색깔 -> 데이터 컬러와 맞춰주어야함
       lty = 1
)

환율이 증가(원화가치 약세)하게 되면 외국인 투자자들은 대부분 주식을 매도 할 것이다.
반대로 환율이 감소(원화가치 강세)하게 되면 외국인 투자자들은 주식을 매수 할 것이다.
매도/매수 가 얼마나 됐는지는 거래량을 보고 알 수 있는데,
2014 ~ 2016년간 달러화 환율이 점차 증가 -> 주식의 거래량 또한 증가 하는것을 알 수 있다.
즉 환율이 증가해 원화가치가 떨어져 많은 외국인 투자자들이 주식을 매도하기 시작 한 것이다.

하지만 환율이 하락하는 구간인 2016 ~ 2017년에 삼양의 거래량이 높게 올랐다. 이 구간은 위안화 환율과 연관이 있는데
2017년 삼양식품은 중국 업체와 중국 총판에 대한 업무협약을 맺었으며, 엄청난 양의 신제품을 쏟아내기 시작하였다.
그 신제품은 삼양의 대표 판매제품이라고 할 수 있는 불닭볶음면의 여러가지 시리즈 제품이였다.
이미 성공을 맛 본 제품이기 때문에 그에 따른 시리즈 제품들 또한 성공할 확률이 높았다고 판단 한 것이다.
이러한 소식에 많은 투자자들이 주식을 매수 한 것이다.

④ 각 기업의 주가는 서로에게 영향을 미칠까
귀무가설 : 기업 주가는 서로 관련이 없다.
대립가설 : 기업 주가는 서로 관련이 있다.

cor.test(ottugi$종가, nongshim$종가)
cor.test(ottugi$종가, samyang$종가)
cor.test(samyang$종가, nongshim$종가)
	Pearson's product-moment correlation

data:  ottugi$종가 and nongshim$종가
t = 39.718, df = 1224, p-value < 2.2e-16
alternative hypothesis: true correlation is not equal to 0
95 percent confidence interval:
 0.7248643 0.7738718
sample estimates:
      cor 
0.7503975 
	Pearson's product-moment correlation

data:  ottugi$종가 and samyang$종가
t = 2.3894, df = 1224, p-value = 0.01702
alternative hypothesis: true correlation is not equal to 0
95 percent confidence interval:
 0.01219955 0.12365356
sample estimates:
       cor 
0.06813914 
	Pearson's product-moment correlation

data:  samyang$종가 and nongshim$종가
t = -2.0247, df = 1224, p-value = 0.04312
alternative hypothesis: true correlation is not equal to 0
95 percent confidence interval:
 -0.113394677 -0.001795002
sample estimates:
        cor 
-0.05777533 
p값은 모두 0.05보다 작아 귀무가설 기각 대립가설 채택, 따라서 각 기업 주가는 서로 관련이 있다.
모두 상관분석이 가능 하지만, 가장 뚜렷한 상관관계(0.7503975)를 보여주는 오뚜기와 농심을 분석 실시

plot(ottugi$종가, type="l", col="yellow", lty=1, ylim = c(0, 1500000), ylab = "종가")
lines(nongshim$종가, type="l", col="red", lty=1)
lines(samyang$종가, type="l", col="orange", lty=1)
legend(900, 1450000, # 좌표 위치
       c("오뚜기","농심","삼양"), # data 컬럼
       cex = 1, # 1을 기준으로 한 상대적 크기
       col = c("yellow","red","orange"), # 색깔 -> 데이터 컬러와 맞춰주어야함
       lty = 1
)

추세 역시 오뚜기와 농심은 비슷한 형태의 추세를 보여주고 있다.

plot(ottugi$종가, nongshim$종가,
     ylab = "농심 종가",
     xlab = "오뚜기 종가",
     main = "오뚜기와 농심의 종가"
    )
r <- lm(ottugi$종가 ~ nongshim$종가)
abline(r)

오뚜기의 종가가 상승할때 농심의 종가도 상승하는 산점도 확인 가능
즉 투자자들은 주식을 매수할 때 오뚜기와 농심의 주가를 같이 분석할 것이고, 삼양은 별개의 주가로 분석을 한다는 것을 알 수 있다.

-----------------------------------------------------------------------------------------------------------
2) 각 기업 최근 주가 데이터 현황 분석
① 각 기업의 최근 1년 시가 총액
시가총액 : 주식시장에서 한 기업의 시장 가치를 나타내는 지표
nongshim_2018 <- nongshim[nongshim$연도 == 2018,]
ottugi_2018 <- ottugi[ottugi$연도 == 2018,]
samyang_2018 <- samyang[samyang$연도 == 2018,]
library(ggplot2)
nongshim_2018$날짜 <- factor(nongshim_2018$날짜)
ottugi_2018$날짜 <- factor(ottugi_2018$날짜)
samyang_2018$날짜 <- factor(samyang_2018$날짜)
par(mfrow = c(3,1))
plot(x = nongshim_2018$날짜, y = nongshim_2018$시가총액, xlab = paste("\n최근 시가총액 :",tail(nongshim_2018$시가총액,1),
                                                               "\n최대 :",max(nongshim_2018$시가총액),
                                                               "최소 :",min(nongshim_2018$시가총액)),
                                                                main = "농심 최근 1년 시가총액",)
plot(x = ottugi_2018$날짜, y = ottugi_2018$시가총액, xlab = paste("최근 시가총액 :",tail(ottugi_2018$시가총액,1),
                                                               "\n최대 :",max(ottugi_2018$시가총액),
                                                               "최소 :",min(ottugi_2018$시가총액)),
                                                                main = "오뚜기 최근 1년 시가총액")
plot(x = samyang_2018$날짜, y = samyang_2018$시가총액, xlab = paste("최근 시가총액 :",tail(samyang_2018$시가총액,1),
                                                               "\n최대 :",max(samyang_2018$시가총액),
                                                               "최소 :",min(samyang_2018$시가총액)),
                                                                main = "삼양 최근 1년 시가총액")

세 기업중 가장 높은 시가 총액을 보여준건 "오뚜기" 기업이며 가장 주식시장 가치가 높은 회사라고 알 수 있다.
최근 시가 총액도 마찬가지로 "오뚜기" 기업이 가장 높았으며 삼양은 시가 총액이 내려가는 추세를 보여주고 있다.

오뚜기 기업의 시가 총액이 경쟁 기업 농심과 삼양에 비해 월등히 높은 이유가 무엇일까?

다음은 http://www.ekn.kr/news/article.html?no=409218 에 실린 기사의 내용 일부이다.
[기업분석] 오뚜기, 높은 기업가치를 지속할 수 있는 요건

오뚜기는 전통 라면시장에서의 꾸준한 점유율과 프리미엄 제품 출시,
긍정적인 기업이미지 등으로 증시참여자들에게 높은 기업가치를 받아왔다.
올해는 추가적인 라면시장 점유율 상승과 지배구조 개선을 통해 수익 성장을 이어갈 수 있을지 관심이 집중된다.

◇ 프리미엄 제품 출시 여부…"미역국라면 출시 등으로 11% 매출 늘어나"
오뚜기 투자 판단에 있어 중요한 것은 증시에서 형성된 높은 기업가치를 유지할 수 있는지다.
현재 증권가에서는 프리미엄 면제품의 출시 여부를 주목하고 있다.
안정적인 매출 성장과 이익률로 시장지배력을 유지하는 동시에 프리미엄 제품이 출시되면 주가 상승에도 모멘텀이 되기 때문이다.
지난 2016년에도 프리미엄 제품인 진짬뽕의 높은 매출이 오뚜기의 기업가치를 상승시켰다.

s_sum_o <- as.matrix(aggregate(시가총액 ~ 연도, ottugi, sum))
df_s_sum_o <- data.frame(시가총액 = as.numeric(s_sum_o[,2]))
row.names(df_s_sum_o) <- c(2014,2015,2016,2017,2018)
df_s_sum_o
시가총액
2014	403615200
2015	701773760
2016	738664320
2017	634336000
2018	642967419
j_sum_o <- as.matrix(aggregate(종가 ~ 연도, ottugi, mean))
df_j_sum_o <- data.frame(종가 = as.numeric(j_sum_o[,2]))
row.names(df_j_sum_o) <- c(2014,2015,2016,2017,2018)
df_j_sum_o
종가
2014	478898.0
2015	822596.8
2016	872878.0
2017	758847.7
2018	759180.3
par(mfrow = c(1,2))

df_s_sum_o <- as.matrix(df_s_sum_o)
df_s_sum_o_b <- barplot(df_s_sum_o,
        beside = T,
        main = "연도별 오뚜기 시가총액",
        xlab = "연도",
        ylab = "시가총액",
        names=c(2014, 2015, 2016, 2017, 2018),
        border = "black",
        ylim = c(0,1000000000),
        col = c("white smoke","white smoke","yellow","white smoke","white smoke")
       )

df_j_sum_o <- as.matrix(df_j_sum_o)
df_j_sum_o_b <- barplot(df_j_sum_o,
        beside = T,
        main = "연도별 오뚜기 주가",
        xlab = "연도",
        ylab = "주가",
        names=c(2014, 2015, 2016, 2017, 2018),
        border = "black",
        ylim = c(0,1000000),
        col = c("white smoke","white smoke","yellow","white smoke","white smoke")
       )

실제로 진짬뽕이 출시된 2016년 가장 높은 시가총액 및 주가를 기록 하였다.

② 각 기업의 2018.12(최근) 거래량
거래량이 많다는것 -> 즉 거래가 활발히 이루어진다는 것은 내가 팔고 싶을 때 주식을 팔 수 있는 시장
반대로 가격이 상승하고 있다고 하더라도 거래량이 적다면 내가 원하는 가격에 매도하지 못할 수도 있다.
nongshim_2018_12 <- nongshim_2018[nongshim_2018$월 == 12,]
ottugi_2018_12 <- ottugi_2018[ottugi_2018$월 == 12,]
samyang_2018_12 <- samyang_2018[samyang_2018$월 == 12,]
nongshim_2018_12$날짜 <- factor(nongshim_2018_12$날짜)
ottugi_2018_12$날짜 <- factor(ottugi_2018_12$날짜)
samyang_2018_12$날짜 <- factor(samyang_2018_12$날짜)
#library(gridExtra)
nongshim_2018_12[nongshim_2018_12$일 == 28,7]
ottugi_2018_12[ottugi_2018_12$일 == 28,7]
samyang_2018_12[samyang_2018_12$일 == 28,7]
12268
2713
32682
vol <- data.frame(거래량 = nongshim_2018_12[nongshim_2018_12$일 == 28,7])
volume <- rbind(vol, ottugi_2018_12[ottugi_2018_12$일 == 28,7], samyang_2018_12[samyang_2018_12$일 == 28,7])
row.names(volume) <- c("농심","오뚜기","삼양")
volume
거래량
농심	12268
오뚜기	2713
삼양	32682
volume <- as.matrix(volume)
bar_volume <- barplot(volume,
        beside = T,
        main = "2018년 12월 28일 거래량",
        xlab = "기업",
        ylab = "거래량",
        names=c("농심","오뚜기","삼양"),
        border = "black",
        ylim = c(0,35000),
        col = c("red","yellow","orange")
       )
abline(h = 32682, col = "black", lty = 2, lwd = 1)
abline(h = 12268, col = "black", lty = 2, lwd = 1)
text(x = bar_volume, y = c(11268,1713,31682), labels = paste(c(12268,2713,32682)),cex = 1.5)
text(x = 2, y = 25000, labels = paste("압도적인 거래량의 삼양 ――――――――――>"),cex = 1, col = "black")

가장 최근을 기점으로 거래량이 가장 높은 기업은 "삼양"이며, 다른 기업에 비해 압도적으로 높은 거래량을 보여준다.
따라서 현재 삼양 주식 거래가 다른 기업에 비해 활발하게 진행중이라는 것을 알 수 있다.
즉, 내가 팔고 싶을 때 주식을 팔 수 있는 시장

-----------------------------------------------------------------------------------------------------------
3) 각 기업 라면 가격과 종가 관계 확인
noodle <- read.csv('../Data/라면가격.csv')
rownames(noodle) <- c("2017-10","2017-11","2017-12","2018-01","2018-02","2018-03","2018-04","2018-05","2018-06",
                               "2018-07","2018-08","2018-09","2018-10","2018-11","2018-12")
noodle_j <- cbind(noodle,
      data.frame(농심종가 = c(343718.8,351250,355236.8,329568.2,305722.2,306809.25,316261.9,321575,345000,297750,270295.5,245352.9,226881.0,238568.2,257026.3)),
      data.frame(오뚜기종가 = c(763875,780181.8,801157.9,785909.1,713444.4,716223.8,773381,768300,855894.7,842772.7,804000,719411.8,683190.5,700227.3,735842.1)),
      data.frame(삼양종가 = c(59993.75,62077.27,86115.79,87722.73,92816.67,84652.38,85076.19,97515,106763.16,95477.27,84204.55,79841.18,66319.05,60272.73,56484.21))
     )
noodle_j
신라면	삼양라면	진라면	농심종가	오뚜기종가	삼양종가
2017-10	3407	3285	2790	343718.8	763875.0	59993.75
2017-11	3415	3225	2776	351250.0	780181.8	62077.27
2017-12	3416	3219	2769	355236.8	801157.9	86115.79
2018-01	3389	3127	2720	329568.2	785909.1	87722.73
2018-02	3399	3261	2760	305722.2	713444.4	92816.67
2018-03	3394	3281	2771	306809.2	716223.8	84652.38
2018-04	3387	3101	2810	316261.9	773381.0	85076.19
2018-05	3392	3146	2743	321575.0	768300.0	97515.00
2018-06	3386	3184	2778	345000.0	855894.7	106763.16
2018-07	3391	3242	2818	297750.0	842772.7	95477.27
2018-08	3397	3169	2763	270295.5	804000.0	84204.55
2018-09	3621	3464	2967	245352.9	719411.8	79841.18
2018-10	3640	3492	2939	226881.0	683190.5	66319.05
2018-11	3630	3478	3000	238568.2	700227.3	60272.73
2018-12	3638	3479	2961	257026.3	735842.1	56484.21
par(mfrow = c(2,3))
barplot(noodle_j$신라면, main = "신라면 가격 변화", las = 2, col = "red", ylim = c(0,4000),
        names = c("2017-10","2017-11","2017-12","2018-01","2018-02","2018-03","2018-04",
                  "2018-05","2018-06","2018-07","2018-08","2018-09","2018-10","2018-11","2018-12"))

barplot(noodle_j$진라면, main = "진라면 가격 변화", las = 2, col = "yellow", ylim = c(0,3500),
        names = c("2017-10","2017-11","2017-12","2018-01","2018-02","2018-03","2018-04",
                  "2018-05","2018-06","2018-07","2018-08","2018-09","2018-10","2018-11","2018-12"))

barplot(noodle_j$삼양라면, main = "삼양라면 가격 변화", las = 2, col = "orange", ylim = c(0, 3500),
        names = c("2017-10","2017-11","2017-12","2018-01","2018-02","2018-03","2018-04",
                  "2018-05","2018-06","2018-07","2018-08","2018-09","2018-10","2018-11","2018-12"))

barplot(noodle_j$농심종가, main = "농심 종가 변화", las = 2, col = "red", ylim = c(0,400000),
        names = c("2017-10","2017-11","2017-12","2018-01","2018-02","2018-03","2018-04",
                  "2018-05","2018-06","2018-07","2018-08","2018-09","2018-10","2018-11","2018-12"))

barplot(noodle_j$오뚜기종가, main = "오뚜기 종가 변화", las = 2, col = "yellow", ylim = c(0,950000),
        names = c("2017-10","2017-11","2017-12","2018-01","2018-02","2018-03","2018-04",
                  "2018-05","2018-06","2018-07","2018-08","2018-09","2018-10","2018-11","2018-12"))

barplot(noodle_j$삼양종가, main = "삼양 종가 변화", las = 2, col = "orange", ylim = c(0,120000),
        names = c("2017-10","2017-11","2017-12","2018-01","2018-02","2018-03","2018-04",
                  "2018-05","2018-06","2018-07","2018-08","2018-09","2018-10","2018-11","2018-12"))

세 기업 모두 라면 가격이 증가하고 있으며, 주가는 감소하는 추세

cor.test(noodle_j$농심종가, noodle_j$신라면)
cor.test(noodle_j$오뚜기종가, noodle_j$진라면)
cor.test(noodle_j$삼양종가, noodle_j$삼양라면)
	Pearson's product-moment correlation

data:  noodle_j$농심종가 and noodle_j$신라면
t = -5.3034, df = 13, p-value = 0.000143
alternative hypothesis: true correlation is not equal to 0
95 percent confidence interval:
 -0.9407246 -0.5460426
sample estimates:
       cor 
-0.8269837 
	Pearson's product-moment correlation

data:  noodle_j$오뚜기종가 and noodle_j$진라면
t = -2.4853, df = 13, p-value = 0.02733
alternative hypothesis: true correlation is not equal to 0
95 percent confidence interval:
 -0.83658346 -0.07793585
sample estimates:
       cor 
-0.5675404 
	Pearson's product-moment correlation

data:  noodle_j$삼양종가 and noodle_j$삼양라면
t = -3.0341, df = 13, p-value = 0.00959
alternative hypothesis: true correlation is not equal to 0
95 percent confidence interval:
 -0.8693809 -0.1963686
sample estimates:
       cor 
-0.6438637 
귀무가설 : 각 기업 라면 가격과 종가는 관련이 없다.
대립가설 : 각 기업 라면 가격과 종가는 관련이 있다.

농심 종가와 신라면 가격의 상관관계 : -0.8269837 / p-value = 0.000143
오뚜기 종가와 진라면 가격의 상관관계 : -0.5675404 / p-value = 0.02733
삼양 종가와 삼양라면 가격의 상관관계 : -0.6438637 / p-value = 0.00959

세 기업 모두 음의 상관관계이며, p-value < 0.05 ====> 즉, 각 기업의 라면 가격은 종가와 관련이 있다.

par(mfrow = c(3,1))
# 산점도
plot(noodle_j$농심종가 ~ noodle_j$신라면,
    main = "<농심> \n농심 종가와 신라면 가격",
    xlab = paste("신라면 가격", "\n상관계수 :",cor(noodle_j$농심종가,noodle_j$신라면)),
    ylab = "종가") # ~ 연산자 기준으로 뒤에 쓰는게 종속변수, 앞에 쓰는게 독립변수
# 회귀분석 : lm(종속변수 ~ 독립변수)
n_n <- lm(noodle_j$농심종가 ~ noodle_j$신라면)
# 회귀선 그리기
abline(n_n)

plot(noodle_j$오뚜기종가 ~ noodle_j$진라면,
    main = "<오뚜기> \n오뚜기 종가와 진라면 가격",
    xlab = paste("진라면 가격", "\n상관계수 :",cor(noodle_j$오뚜기종가, noodle_j$진라면)),
    ylab = "종가") # ~ 연산자 기준으로 뒤에 쓰는게 종속변수, 앞에 쓰는게 독립변수
# 회귀분석 : lm(종속변수 ~ 독립변수)
n_o <- lm(noodle_j$오뚜기종가 ~ noodle_j$진라면)
# 회귀선 그리기
abline(n_o)

plot(noodle_j$삼양종가 ~ noodle_j$삼양라면,
    main = "<삼양> \n삼양 종가와 삼양라면 가격",
    xlab = paste("삼양라면 가격", "\n상관계수 :",cor(noodle_j$삼양종가,noodle_j$삼양라면)),
    ylab = "종가") # ~ 연산자 기준으로 뒤에 쓰는게 종속변수, 앞에 쓰는게 독립변수
# 회귀분석 : lm(종속변수 ~ 독립변수)
n_s <- lm(noodle_j$삼양종가 ~ noodle_j$삼양라면)
# 회귀선 그리기
abline(n_s)

※ 라면 가격과 종가(주가)가 음의 상관관계(?)

물가가 상승하는 인플레이션은 두 가지의 종류로 나눌 수 있다.

1) 수요 견인 인플레이션
수요가 좋아져서 경기가 확장되는 현상

2) 비용 상승 인플레이션
유가나 원자재가가 올라 자연스레 완제품의 가격이 상승하는 현상

즉, 수요 견인 인플레이션은 주가에 긍정적인 현상을 가져다 주지만 비용상승 인플레이션은 주가에 달갑지만은 않은 상황이다.
인플레이션으로 인해 물가가 상승하게 되어 실질 소득이 감소하면 자연히 소비 역시 감소되어 기업의 실적이 나빠지게 된다.
결론적으로 상관관계와 막대그래프를 미루어 보았을 때, 비용 상승 인플레이션과 관련이 있다

-----------------------------------------------------------------------------------------------------------
4) 각 기업의 재무제표
매출액, 영업이익, 순이익, 자산, 부채, 자본과 종가(주가)와의 관계 확인
2017.12 / 2018.03 / 2018.06 / 2018.09

주가 변동 요인 : 매출액, 영업이익, 순이익, 자산, 부채, 자본

농심
매출액 : 4615, 4693, 4442, 4678
영업이익 : 105, 283, 29, 142
순이익 : 126, 283, 83, 167
자산 : 23174, 23628, 23731, 23882
부채 : 4702, 5107, 5123, 5104
자본 : 18472, 18522, 18607, 18778

오뚜기
매출액 : 4897, 5483, 5136, 5455
영업이익 : 263, 360, 342, 362
순이익 : 137, 319, 263, 619
자산 : 14058, 14975, 15146, 16249
부채 : 4404, 5223, 5141, 5450
자본 : 9654, 9752, 10005, 10799

삼양
매출액 : 1271, 1249, 1226, 1086
영업이익 : 120, 172, 123, 110
순이익 : 43, 127, 136, 85
자산 : 3815, 3767, 3973, 3819
부채 : 1885, 1733, 1808, 1570
자본 : 1929, 2034, 2166, 2250

n_m <- c(4615, 4693, 4442, 4678)
n_y <- c(105, 283, 29, 142)
n_s <- c(126, 283, 83, 167)
n_js <- c(23174, 23628, 23731, 23882)
n_b <- c(4702, 5107, 5123, 5104)
n_jb <- c(18472, 18522, 18607, 18778)

o_m <- c(4897, 5483, 5136, 5455)
o_y <- c(263, 360, 342, 362)
o_s <- c(137, 319, 263, 619)
o_js <- c(14058, 14975, 15146, 16249)
o_b <- c(4404, 5223, 5141, 5450)
o_jb <- c(9654, 9752, 10005, 10799)

s_m <- c(1271, 1249, 1226, 1086)
s_y <- c(120, 172, 123, 110)
s_s <- c(43, 127, 136, 85)
s_js <- c(3815, 3767, 3973, 3819)
s_b <- c(1885, 1733, 1808, 1570)
s_jb <- c(1929, 2034, 2166, 2250)
aggregate(종가 ~ 연도 == 2017 & 월 == 12, nongshim, mean)
aggregate(종가 ~ 연도 == 2018 & 월 == '03', nongshim, mean)
aggregate(종가 ~ 연도 == 2018 & 월 == '06', nongshim, mean)
aggregate(종가 ~ 연도 == 2018 & 월 == '09', nongshim, mean)

aggregate(종가 ~ 연도 == 2017 & 월 == 12, ottugi, mean)
aggregate(종가 ~ 연도 == 2018 & 월 == '03', ottugi, mean)
aggregate(종가 ~ 연도 == 2018 & 월 == '06', ottugi, mean)
aggregate(종가 ~ 연도 == 2018 & 월 == '09', ottugi, mean)

aggregate(종가 ~ 연도 == 2017 & 월 == 12, samyang, mean)
aggregate(종가 ~ 연도 == 2018 & 월 == '03', samyang, mean)
aggregate(종가 ~ 연도 == 2018 & 월 == '06', samyang, mean)
aggregate(종가 ~ 연도 == 2018 & 월 == '09', samyang, mean)

n_j <- c(355236.8, 306809.5, 345000, 245352.9)
o_j <- c(801157.9, 716523.8, 855894.7, 719411.8)
s_j <- c(86115.79, 84652.38, 106763.16, 79841.18)
연도 == 2017 & 월 == 12	종가
FALSE	313912.6
TRUE	355236.8
연도 == 2018 & 월 == "03"	종가
FALSE	314688.0
TRUE	306809.5
연도 == 2018 & 월 == "06"	종가
FALSE	314073.7
TRUE	345000.0
연도 == 2018 & 월 == "09"	종가
FALSE	315526.1
TRUE	245352.9
연도 == 2017 & 월 == 12	종가
FALSE	737763.0
TRUE	801157.9
연도 == 2018 & 월 == "03"	종가
FALSE	739132.8
TRUE	716523.8
연도 == 2018 & 월 == "06"	종가
FALSE	736901.4
TRUE	855894.7
연도 == 2018 & 월 == "09"	종가
FALSE	739017.4
TRUE	719411.8
연도 == 2017 & 월 == 12	종가
FALSE	43440.68
TRUE	86115.79
연도 == 2018 & 월 == "03"	종가
FALSE	43395.35
TRUE	84652.38
연도 == 2018 & 월 == "06"	종가
FALSE	43115.66
TRUE	106763.16
연도 == 2018 & 월 == "09"	종가
FALSE	43599.50
TRUE	79841.18
data_nongshim <- data.frame(매출액 = n_m, 영업이익 = n_y, 순이익 = n_s, 자산 = n_js, 부채 = n_b, 자본 = n_jb, 종가 = n_j)
row.names(data_nongshim) <- c("2017.12", "2018.03", "2018.06", "2018.09")

data_ottugi <- data.frame(매출액 = o_m, 영업이익 = o_y, 순이익 = o_s, 자산 = o_js, 부채 = o_b, 자본 = o_jb, 종가 = o_j)
row.names(data_ottugi) <- c("2017.12", "2018.03", "2018.06", "2018.09")

data_samyang <- data.frame(매출액 = s_m, 영업이익 = s_y, 순이익 = s_s, 자산 = s_js, 부채 = s_b, 자본 = s_jb, 종가 = s_j)
row.names(data_samyang) <- c("2017.12", "2018.03", "2018.06", "2018.09")
data_nongshim
data_ottugi
data_samyang
매출액	영업이익	순이익	자산	부채	자본	종가
2017.12	4615	105	126	23174	4702	18472	355236.8
2018.03	4693	283	283	23628	5107	18522	306809.5
2018.06	4442	29	83	23731	5123	18607	345000.0
2018.09	4678	142	167	23882	5104	18778	245352.9
매출액	영업이익	순이익	자산	부채	자본	종가
2017.12	4897	263	137	14058	4404	9654	801157.9
2018.03	5483	360	319	14975	5223	9752	716523.8
2018.06	5136	342	263	15146	5141	10005	855894.7
2018.09	5455	362	619	16249	5450	10799	719411.8
매출액	영업이익	순이익	자산	부채	자본	종가
2017.12	1271	120	43	3815	1885	1929	86115.79
2018.03	1249	172	127	3767	1733	2034	84652.38
2018.06	1226	123	136	3973	1808	2166	106763.16
2018.09	1086	110	85	3819	1570	2250	79841.18
cor(data_nongshim)
cor(data_ottugi)
cor(data_samyang)
매출액	영업이익	순이익	자산	부채	자본	종가
매출액	1.00000000	0.82922965	0.7920790	-0.02463483	-0.08616398	0.08091856	-0.5985166
영업이익	0.82922965	1.00000000	0.9959389	0.05073947	0.18794425	-0.16556962	-0.3808946
순이익	0.79207897	0.99593891	1.0000000	0.12390843	0.27217666	-0.12806983	-0.4004249
자산	-0.02463483	0.05073947	0.1239084	1.00000000	0.93535078	0.84297236	-0.7264634
부채	-0.08616398	0.18794425	0.2721767	0.93535078	1.00000000	0.59819893	-0.5349678
자본	0.08091856	-0.16556962	-0.1280698	0.84297236	0.59819893	1.00000000	-0.8353819
종가	-0.59851663	-0.38089464	-0.4004249	-0.72646340	-0.53496779	-0.83538189	1.0000000
매출액	영업이익	순이익	자산	부채	자본	종가
매출액	1.0000000	0.9175163	0.7747562	0.7563256	0.8967657	0.5270089	-0.7685279
영업이익	0.9175163	1.0000000	0.7338006	0.8204998	0.9821400	0.5636264	-0.4539729
순이익	0.7747562	0.7338006	1.0000000	0.9642095	0.8361577	0.9397749	-0.6235465
자산	0.7563256	0.8204998	0.9642095	1.0000000	0.9131095	0.9346020	-0.4388977
부채	0.8967657	0.9821400	0.8361577	0.9131095	1.0000000	0.7083719	-0.4554153
자본	0.5270089	0.5636264	0.9397749	0.9346020	0.7083719	1.0000000	-0.3625141
종가	-0.7685279	-0.4539729	-0.6235465	-0.4388977	-0.4554153	-0.3625141	1.0000000
매출액	영업이익	순이익	자산	부채	자본	종가
매출액	1.000000000	0.49103002	-0.000561273	0.01732899	0.9157726	-0.8622730	0.36128832
영업이익	0.491030024	1.00000000	0.495989961	-0.44916807	0.1014378	-0.3812260	-0.09746876
순이익	-0.000561273	0.49598996	1.000000000	0.39985997	-0.1957209	0.4423900	0.54119639
자산	0.017328993	-0.44916807	0.399859972	1.00000000	0.2595416	0.3884773	0.91847069
부채	0.915772594	0.10143777	-0.195720862	0.25954160	1.0000000	-0.7890545	0.49170423
자본	-0.862273012	-0.38122599	0.442389971	0.38847733	-0.7890545	1.0000000	0.11553631
종가	0.361288324	-0.09746876	0.541196386	0.91847069	0.4917042	0.1155363	1.00000000
cor.test(data_nongshim$종가, data_nongshim$매출액)
cor.test(data_nongshim$종가, data_nongshim$영업이익)
cor.test(data_nongshim$종가, data_nongshim$순이익)
cor.test(data_nongshim$종가, data_nongshim$자산)
cor.test(data_nongshim$종가, data_nongshim$부채)
cor.test(data_nongshim$종가, data_nongshim$자본)

cor.test(data_ottugi$종가, data_ottugi$매출액)
cor.test(data_ottugi$종가, data_ottugi$영업이익)
cor.test(data_ottugi$종가, data_ottugi$순이익)
cor.test(data_ottugi$종가, data_ottugi$자산)
cor.test(data_ottugi$종가, data_ottugi$부채)
cor.test(data_ottugi$종가, data_ottugi$자본)


cor.test(data_samyang$종가, data_samyang$매출액)
cor.test(data_samyang$종가, data_samyang$영업이익)
cor.test(data_samyang$종가, data_samyang$순이익)
cor.test(data_samyang$종가, data_samyang$자산)
cor.test(data_samyang$종가, data_samyang$부채)
cor.test(data_samyang$종가, data_samyang$자본)
	Pearson's product-moment correlation

data:  data_nongshim$종가 and data_nongshim$매출액
t = -1.0566, df = 2, p-value = 0.4015
alternative hypothesis: true correlation is not equal to 0
95 percent confidence interval:
 -0.9900821  0.8535621
sample estimates:
       cor 
-0.5985166 
	Pearson's product-moment correlation

data:  data_nongshim$종가 and data_nongshim$영업이익
t = -0.58258, df = 2, p-value = 0.6191
alternative hypothesis: true correlation is not equal to 0
95 percent confidence interval:
 -0.9823646  0.9152353
sample estimates:
       cor 
-0.3808946 
	Pearson's product-moment correlation

data:  data_nongshim$종가 and data_nongshim$순이익
t = -0.61799, df = 2, p-value = 0.5996
alternative hypothesis: true correlation is not equal to 0
95 percent confidence interval:
 -0.9831524  0.9114135
sample estimates:
       cor 
-0.4004249 
	Pearson's product-moment correlation

data:  data_nongshim$종가 and data_nongshim$자산
t = -1.495, df = 2, p-value = 0.2735
alternative hypothesis: true correlation is not equal to 0
95 percent confidence interval:
 -0.9937321  0.7774005
sample estimates:
       cor 
-0.7264634 
	Pearson's product-moment correlation

data:  data_nongshim$종가 and data_nongshim$부채
t = -0.89547, df = 2, p-value = 0.465
alternative hypothesis: true correlation is not equal to 0
95 percent confidence interval:
 -0.9880489  0.8770605
sample estimates:
       cor 
-0.5349678 
	Pearson's product-moment correlation

data:  data_nongshim$종가 and data_nongshim$자본
t = -2.1493, df = 2, p-value = 0.1646
alternative hypothesis: true correlation is not equal to 0
95 percent confidence interval:
 -0.9964469  0.6376920
sample estimates:
       cor 
-0.8353819 
	Pearson's product-moment correlation

data:  data_ottugi$종가 and data_ottugi$매출액
t = -1.6987, df = 2, p-value = 0.2315
alternative hypothesis: true correlation is not equal to 0
95 percent confidence interval:
 -0.9948193  0.7367084
sample estimates:
       cor 
-0.7685279 
	Pearson's product-moment correlation

data:  data_ottugi$종가 and data_ottugi$영업이익
t = -0.72054, df = 2, p-value = 0.546
alternative hypothesis: true correlation is not equal to 0
95 percent confidence interval:
 -0.9852069  0.8996291
sample estimates:
       cor 
-0.4539729 
	Pearson's product-moment correlation

data:  data_ottugi$종가 and data_ottugi$순이익
t = -1.128, df = 2, p-value = 0.3765
alternative hypothesis: true correlation is not equal to 0
95 percent confidence interval:
 -0.9908403  0.8423405
sample estimates:
       cor 
-0.6235465 
	Pearson's product-moment correlation

data:  data_ottugi$종가 and data_ottugi$자산
t = -0.69078, df = 2, p-value = 0.5611
alternative hypothesis: true correlation is not equal to 0
95 percent confidence interval:
 -0.9846435  0.9031589
sample estimates:
       cor 
-0.4388977 
	Pearson's product-moment correlation

data:  data_ottugi$종가 and data_ottugi$부채
t = -0.72343, df = 2, p-value = 0.5446
alternative hypothesis: true correlation is not equal to 0
95 percent confidence interval:
 -0.9852602  0.8992818
sample estimates:
       cor 
-0.4554153 
	Pearson's product-moment correlation

data:  data_ottugi$종가 and data_ottugi$자본
t = -0.55009, df = 2, p-value = 0.6375
alternative hypothesis: true correlation is not equal to 0
95 percent confidence interval:
 -0.9816032  0.9186310
sample estimates:
       cor 
-0.3625141 
	Pearson's product-moment correlation

data:  data_samyang$종가 and data_samyang$매출액
t = 0.54795, df = 2, p-value = 0.6387
alternative hypothesis: true correlation is not equal to 0
95 percent confidence interval:
 -0.9188510  0.9815517
sample estimates:
      cor 
0.3612883 
	Pearson's product-moment correlation

data:  data_samyang$종가 and data_samyang$영업이익
t = -0.1385, df = 2, p-value = 0.9025
alternative hypothesis: true correlation is not equal to 0
95 percent confidence interval:
 -0.9678880  0.9528803
sample estimates:
        cor 
-0.09746876 
	Pearson's product-moment correlation

data:  data_samyang$종가 and data_samyang$순이익
t = 0.91018, df = 2, p-value = 0.4588
alternative hypothesis: true correlation is not equal to 0
95 percent confidence interval:
 -0.8750218  0.9882554
sample estimates:
      cor 
0.5411964 
	Pearson's product-moment correlation

data:  data_samyang$종가 and data_samyang$자산
t = 3.2843, df = 2, p-value = 0.08153
alternative hypothesis: true correlation is not equal to 0
95 percent confidence interval:
 -0.3634050  0.9983149
sample estimates:
      cor 
0.9184707 
	Pearson's product-moment correlation

data:  data_samyang$종가 and data_samyang$부채
t = 0.79858, df = 2, p-value = 0.5083
alternative hypothesis: true correlation is not equal to 0
95 percent confidence interval:
 -0.8899444  0.9865682
sample estimates:
      cor 
0.4917042 
	Pearson's product-moment correlation

data:  data_samyang$종가 and data_samyang$자본
t = 0.16449, df = 2, p-value = 0.8845
alternative hypothesis: true correlation is not equal to 0
95 percent confidence interval:
 -0.9511690  0.9690227
sample estimates:
      cor 
0.1155363 
귀무가설 : 각 요인 증감과 종가와는 관련이 없다.
대립가설 : 각 요인 증감과 종가와는 관련이 있다.
p-value = x > 0.05므로 귀무가설 채택 대립가설 기각 따라서 각 요인 증가와 종가와는 관계가 없다.
하지만 데이터의 수가 너무 작다보니 생길 수 있는 오류라고 판단 하여, 구할 수 없는 데이터는 추론하여 대입 후 분석을 실시 하겠다.
추론은 2017년 10, 11월 / 2018년 1, 2, 4, 5, 7, 8, 10, 11, 12월을 추론 하겠다.

--------------------------------------------------------------------------------------------------------------
2. TOP3 기업 재무제표 추론 및 회귀분석을 통한 예측 실시
추론 방법
1) ex) 1월 2월 3월의 일수를 합해 1분기의 매출액 4693을 나눔
2) 1월의 값을 구하고 싶다면 나눈 값에 1월의 일수를 곱하고 2월의 값을 구하고 싶다면 2월의 일수를 곱한다.
1) 농심 데이터 추론 및 예측
# 각 월의 일수를 확인 해보자.
nrow(nongshim[(nongshim$연도 == 2017) & (nongshim$월 == 10),])
nrow(nongshim[(nongshim$연도 == 2017) & (nongshim$월 == 11),])
nrow(nongshim[(nongshim$연도 == 2017) & (nongshim$월 == 12),])
cat('-------------------------------------------------------------------------------------------------------------------')
nrow(nongshim[(nongshim$연도 == 2018) & (nongshim$월 == "01"),])
nrow(nongshim[(nongshim$연도 == 2018) & (nongshim$월 == "02"),])
nrow(nongshim[(nongshim$연도 == 2018) & (nongshim$월 == "03"),])
nrow(nongshim[(nongshim$연도 == 2018) & (nongshim$월 == "04"),])
nrow(nongshim[(nongshim$연도 == 2018) & (nongshim$월 == "05"),])
nrow(nongshim[(nongshim$연도 == 2018) & (nongshim$월 == "06"),])
nrow(nongshim[(nongshim$연도 == 2018) & (nongshim$월 == "07"),])
nrow(nongshim[(nongshim$연도 == 2018) & (nongshim$월 == "08"),])
nrow(nongshim[(nongshim$연도 == 2018) & (nongshim$월 == "09"),])
nrow(nongshim[(nongshim$연도 == 2018) & (nongshim$월 == 10),])
nrow(nongshim[(nongshim$연도 == 2018) & (nongshim$월 == 11),])
nrow(nongshim[(nongshim$연도 == 2018) & (nongshim$월 == 12),])
16
22
19
-------------------------------------------------------------------------------------------------------------------
22
18
21
21
20
19
22
22
17
21
22
19
data_nongshim
매출액	영업이익	순이익	자산	부채	자본	종가
2017.12	4615	105	126	23174	4702	18472	355236.8
2018.03	4693	283	283	23628	5107	18522	306809.5
2018.06	4442	29	83	23731	5123	18607	345000.0
2018.09	4678	142	167	23882	5104	18778	245352.9
2017년 10월 : 16, 1295, 29, 35, 6504, 1319, 5185, 343718.8
2017년 11월 : 22, 1781, 40, 48, 8944, 1814, 7129, 351250
2017년 12월 : 19, 1538, 35, 42, 7724, 1567, 6157, 355236.8

2018년
1월 : 22, 1692, 102, 45, 8521, 1841, 6680, 329568.2
2월 : 18, 1384, 83, 37, 6972, 1506, 5465, 305722.2
3월 : 21, 1615, 97, 43, 8134, 1758, 6376, 306809.25
4월 : 21, 1554, 10, 29, 8305, 1793, 6512, 316261.9
5월 : 20, 1480, 9, 27, 7910, 1707, 6202, 321575
6월 : 19, 1406, 9, 26, 7514, 1622, 5892, 345000
7월 : 22, 1687, 51, 60, 8613, 1840, 6772, 297750
8월 : 22, 1687, 51, 60, 8613, 1840, 6772, 270295.5
9월 : 17, 1303, 39, 46, 6655, 1722, 5233, 245352.9

n_2017 <- nongshim[nongshim$연도==2017,]
n_2018 <- nongshim[nongshim$연도==2018,]
aggregate(종가 ~ 월, n_2017, mean)
월	종가
01	322050.0
02	338825.0
03	299954.5
04	318550.0
05	337131.6
06	352476.2
07	322928.6
08	326000.0
09	339976.2
10	343718.8
11	351250.0
12	355236.8
aggregate(종가 ~ 월, n_2018, mean)
월	종가
01	329568.2
02	305722.2
03	306809.5
04	316261.9
05	321575.0
06	345000.0
07	297750.0
08	270295.5
09	245352.9
10	226881.0
11	238568.2
12	257026.3
data_nongshim2 <- data.frame(매출액 = c(1295,1781,1538,1692,1384,1615,1554,1480,1406,1687,1687,1303),
           영업이익 = c(29,40,35,102,83,97,10,9,9,51,51,39),
           순이익 = c(35,48,42,45,37,43,29,27,26,60,60,46),
           자산 = c(6504,8944,7724,8521,6972,8134,8305,7910,7514,8613,8613,6655),
           부채 = c(1319,1814,1567,1841,1506,1758,1793,1707,1622,1840,1840,1722),
           자본 = c(5185,7129,6157,6680,5465,6376,6512,6202,5892,6772,6772,5233),
           종가 = c(343718.8,351250,355236.8,329568.2,305722.2,306809.25,316261.9,321575,345000,297750,270295.5,245352.9)
          )
row.names(data_nongshim2) <- c("2017-10","2017-11","2017-12","2018-01","2018-02","2018-03","2018-04","2018-05","2018-06",
                               "2018-07","2018-08","2018-09")
data_nongshim2
매출액	영업이익	순이익	자산	부채	자본	종가
2017-10	1295	29	35	6504	1319	5185	343718.8
2017-11	1781	40	48	8944	1814	7129	351250.0
2017-12	1538	35	42	7724	1567	6157	355236.8
2018-01	1692	102	45	8521	1841	6680	329568.2
2018-02	1384	83	37	6972	1506	5465	305722.2
2018-03	1615	97	43	8134	1758	6376	306809.2
2018-04	1554	10	29	8305	1793	6512	316261.9
2018-05	1480	9	27	7910	1707	6202	321575.0
2018-06	1406	9	26	7514	1622	5892	345000.0
2018-07	1687	51	60	8613	1840	6772	297750.0
2018-08	1687	51	60	8613	1840	6772	270295.5
2018-09	1303	39	46	6655	1722	5233	245352.9
shapiro.test(data_nongshim2$종가)
	Shapiro-Wilk normality test

data:  data_nongshim2$종가
W = 0.92834, p-value = 0.3628
shapiro.test 결과 p-value = 0.3628로 알파값 0.05보다 크기 때문에 정규분포를 이룬다는 것을 확인 가능
즉, 회귀분석을 통한 예측이 신빙성이 있다.

par(mfrow = c(2,3))
barplot(data_nongshim2$매출액,
        main = "농심 2017-10 - 2018-09 매출액",
        ylab = "매출액",
        border = "black",
        names = c("2017-10","2017-11","2017-12","2018-01","2018-02","2018-03","2018-04",
                 "2018-05","2018-06","2018-07","2018-08","2018-09"),
        ylim = c(0,2500),
        col = "grey",
        las=2
       )
barplot(data_nongshim2$영업이익,
        main = "농심 2017-10 - 2018-09 영업이익",
        ylab = "영업이익",
        border = "black",
        names = c("2017-10","2017-11","2017-12","2018-01","2018-02","2018-03","2018-04",
                 "2018-05","2018-06","2018-07","2018-08","2018-09"),
        ylim = c(0,120),
        col = "grey",
        las=2
       )
barplot(data_nongshim2$순이익,
        main = "농심 2017-10 - 2018-09 순이익",
        ylab = "순이익",
        border = "black",
        names = c("2017-10","2017-11","2017-12","2018-01","2018-02","2018-03","2018-04",
                 "2018-05","2018-06","2018-07","2018-08","2018-09"),
        ylim = c(0,100),
        col = "grey",
        las=2
       )
barplot(data_nongshim2$자산,
        main = "농심 2017-10 - 2018-09 자산",
        ylab = "자산",
        border = "black",
        names = c("2017-10","2017-11","2017-12","2018-01","2018-02","2018-03","2018-04",
                 "2018-05","2018-06","2018-07","2018-08","2018-09"),
        ylim = c(0,12000),
        col = "grey",
        las=2
       )
barplot(data_nongshim2$부채,
