# study-linear-regression
study linear regression

# 요약
# 회귀분석

## 1. 회귀분석 예제
- `예측문제 prediction` : 특정한 입력변수값을 사용하여 출력변수의 값을 계산하는 것
- `회귀 regression`, `회귀분석 regression analysis` : 예측문제 중에서 출력변수의 값이 연속값인 문제
    - 보스턴 집값 예측
    - 당뇨병 진행도 예측
    - 캘리포니아 주택가격 예측
    - 가상 데이터 예측 : make_regression() 등

## 2. 선형회귀분석의 기초
- `선형회귀모형의 구조 파악`
    - 상수항 결합 : 상수항을 독립변수로 추가함
    - 최소자승법 : OLS : 잔차제곱합으로 가중치 벡터 구하기
    - 직교방정식 : 잔차 벡터의 성질, 평균 벡터의 성질
- `여기에서 나온 수학적 증명 방법이 확률론적선형모형, 분산분석, 아웃라이어, 정규화 등의 이론에서 사용된다.`

### 2-1. 선형회귀분석의 모형
- 회귀분석은 독립변수 x에 대응하는 종속변수 y와 가장 비슷한 값 \hat{y}를 출력하는 함수 f(x)를 찾는 과정과 같다.
- 선형함수 f(x)가 다음과 같을 때 **선형회귀모형 linear regression model**이라고 한다.
    - $\hat{y} = w_0 + w_1x_1 + \cdots + w_Dx_D = w_0 + w^Tx$
- 선형회귀 모형을 사용하는 회귀분석을 **선형회귀분석**이라고 한다.
- 가중치 벡터 w : 함수 f(x)의 **계수 coefficient, 모수 parameter**

### 2-2. 상수항 결합
- `상수항 결합 bias augmentation` : 회귀분석 모형 수식을 간단하게 만들기 위해 상수항을 독립변수 데이터에 추가하는 것
    - import statsmodels.api as sm
    - sm.add_constant(X)

### 2-3. 최소자승법
- `최소자승법 OLS Ordinary Least Squares` : 잔차제곱합(RSS:Residual Sum of Squares)을 최소화하는 가중치 벡터를 구하는 방법
    - 최소자승법을 통해서 선형회귀모형의 가중치 벡터를 구할 수 있다.
    - 잔차제곱합 -> 가중치 벡터로 미분(그레디언트 벡터) -> 공분산 행렬이 역행렬이 존재 할 때 가중치 벡터 w를 구할 수 있다.
    - 역행렬 존재 -> 도함수의 최저값 조건 -> 2차 도함수인 해시안 행렬이 양의 정부호(positive definite)이어야 한다.
    - 이것은 X의 독립변수, 각 행렬이 서로 독립이어야 한다는 조건을 따른다. (풀랭크)

### 2-4. 직교방정식
- 최소자승법에서 구한 그레디언트 벡터로 직교방정식을 구할 수 있다.
    - 그레디언트 벡터 = 0 이면 양변의 식을 정리
- 직교방정식을 통해서 알 수 있는 성질
    - 모형에 상수항이 있는 경우 잔차 벡터의 원소의 합은 0이다. 또한 잔차 벡터의 평균도 0이다.
    - x 데이터의 평균값 \bar{x}에 대한 예측값은 y 데이터의 평균값 \bar{y}와 같다.
- **NumPy 사용**
    - OLS의 해를 직접 이용 : np.linalg.inv(X.T @ X) @ X.T @ y
- **Scikit-learn 패키지 사용**
    - from sklearn.linear_model import LinearRegression
- **statsmodels 패키지 사용**
    - OLS 클래스 : model = sm.OLS.from_formula("y~x1 + x2", data=df)
    - 독립변수, 종속변수 df가 따로인 경우 : sm.OLS(dfX, dfy) : 상수항 결합 있어야함
    - 선형회귀모형 학습 : result = model.fit()
    - 학습 결과 보고서 : print(result.summary())
    - y값 예측 : result.predict(x)
    - 가중치 벡터 확인 : result.params (상수항과 계수에 대한 값 확인)
    - 잔차 벡터 확인 : result.resid

## 3. 스케일링
- `큰 조건수의 의미 : 민감도가 커져 데이터의 변화에 따른 가중치 벡터의 오차가 커진다.`
- `조건수가 크게 나오는 경우`
    - 독립변수간 스케일의 차이 : 스케일링으로 해결
    - 다중공선선이 있는 경우(독립변수간 상관관계가 있는 경우) : 변수선택, PCA로 해결

### 3-1. 조건수
- `조건수 condition number` : 회귀분석에서는 공분산 행렬 X^TX의 가장 큰 고유값과 가장 작은 고유값의 비율
    - $\text{condition number} = \dfrac{\lambda_{max}}{\lambda_{min}}$
- OLS 분석 레포트 확인
    - condition number is large : 조건수가 크다
    - strong multicollinearity : 강한 다중공선성의 문제가 있을 수 있다.
- **조건수가 크면 계수행렬 A와 상수벡터 b에 대한 해 x의 민감도가 커진다.**
    - Ax = b
    - 따라서 계수행렬 A나 상수벡터 b에 존재하는 **오차가 해에 미치는 영향이 커진다.**
- 조건수가 가장 작은 경우
    - 계수행렬 A가 단위 행렬인 경우 : cond(I) = 1, 조건수의 최저값 = 1
- 조건수가 작은 경우 
    - 상수 벡터에 "약간의 오차" -> 연립방정식의 해에도 "동일한 수준의 오차"가 발생
- 조건수가 큰 경우 
    - 상수 벡터에 "약간의 오차" -> 연립방정식의 해의 "매우 큰 오차"가 발생
- **따라서 공분산행렬 X^TX의 조건수가 크면 회귀분석을 사용한 예측값도 오차가 커진다.**

### 3-2. 회귀분석과 조건수
- 회귀분석에서 조건수(condition number)가 커지는 경우
    - 1) 변수들의 단위 차이로 인해 숫자의 스케일이 크게 달라지는 경우 : **스케일링(scaling)**으로 해결한다.
    - 2) 다중공선성(multicollinearity), 상관관계가 큰 독립 변수들이 있는 경우 : **변수선택(VIF)**, **PCA를 사용한 차원 축소** 등으로 해결한다.
- 간단한 예 : 보스턴 집값 데이터에서 독립변수의 스케일링이 안된 상태에서 OLS 회귀분석을 하면 조건수가 매우 크게 나온다. 
    - R-squared 값도 작게 나온다.
- OLS 클래스 사용시 독립변수 스케일링
    - 모형지정 문자열 from_formula() 함수에서 **scale("x1")** 명령 사용
    - scale_feature = ["scale({})".format(name) for name in feature_names]    

## 4. 범주형 독립변수
- `범주형 독립변수가 있는 경우의 선형회귀분석 모형을 만들 수 있다.`
    - 상수항 없으면 : 풀랭크 방식
    - 상수항 있으면 : 축소랭크 방식 (기준 더미변수는 항상 1, 상수항 역할)
    - 독립변수간 상호작용

### 4-1. 범주형 변수가 하나인 경우
- 기본적인 방법은 범주형 독립변수를 갖는 독립변수를 범주형 값 별로 구분하여 각각 모형을 만들어 주는 방법
    - 범주값이 A, B인 경우 : model A와 model B 2개의 선형회귀모형을 만들어 분석한다.
- 올바른 선형회귀모형에서는 어떤 가중치가 다른 가중치의 변화에 따라서 변화하지 않아야 한다. 
    - 위의 방법은 model A와 model B에 따라서 가중치의 값이 달라지게 된다.
- 따라서 **범주형 독립변수를 더미변수로 변환해 준다.**
    - 더미변수는 상수항을 포함하고 있으므로 상수항이 없어지게 된다.

### 4-2. 풀랭크 방식
- `풀랭크 방식 full-rank` : 더미변수의 값을 원핫인코딩(one-hot-encoding) 방식으로 지정
    - 범주값에 해당하는 더미변수는 1, 범주값에 해당하지 않는 더미변수는 0이 된다.
    - 따라서 더미변수의 "가중치"는 상수항이 된다.
- 상수항만 달라지고 다른 독립변수의 가중치는 영향을 받지 않는 모형이 된다.
- **선형회귀모형에 범주형 독립변수가 있는 경우는 더미변수의 가중치 이외에 다른 상수항이 있으면 안된다.**
    - 왜냐하면 별도의 상수항 w0와 범주형 독립변수의 가중치 w1A가 구분되지 않는다.
    - 범주형 독립변수가 있는 경우 별도의 상수항 결합을 하지 않는다.
    
### 4-3. 축소랭크 방식
- `축소랭크 방식 reduced-rank` : 특정한 하나의 범주값을 기준값(reference, baseline)으로 하고 기준값에 대응하는 더미변수의 가중치는 항상 1로 놓는 방식
    - 다른 범주형 값을 갖는 경우는 기준값에 추가적인 특성이 있는 것으로 간주한다.
- 기준값이 되는 더미변수는 항상 1로 놓으므로, 이 기준 더미변수의 가중치가 상수항으로 남아있는다.
    - 여기에 다른 범주값이 선택되면 이 범주값에 해당하는 더미변수의 가중치가 기준 가중치(상수항이 된)에 더해진다.

#### 예제 : 노팅엄 지역의 월 평균 기온 데이터
- 풀랭크 방식의 OLS 모델 생성 : sm.OLS.from_formula("value ~ C(month) + 0", data=df_nottem)
    - +0 : 상수항 포함
    - 1월~12월이 범주값이 된다.
    - 데이터 임포트 : df_nottem = sm.datasets.get_rdataset("nottem").data
    - month : 범주형 독립변수
    - 모형지정 문자열에서 month를 범주형 독립변수로 지정 : C(month)
    - 각 범주값(1월~12월)에 따라서 12개의 가중치가 반환된다. 
    - 풀랭크이므로 1월인 경우 더미변수의 값의 d(1,0,0,...,0) 즉 w1만 남는다.
- 축소랭크 방식의 OLS 모델 생성 : sm.OLS.from_formula("value ~ C(month)", data=df_nottem)
    - 기본적으로 1월 평균기온을 기준 상수항이 된다.
    - 각 월의 평균 기온이 1월 평균기온 보다 얼마나 높은지를 나타내는 값이 회귀모형의 계수가 된다.

#### 예제 : 보스턴 집값 데이터의 범주형 변수
- 보스턴 집값 데이터에서 CHAS 범주형 독립변수는 0, 1 두개의 범주값을 갖는다.
    - **범주형 독립변수가 있는 경우 상수항 가중치를 갖는 모형을 만들면 축소랭크 방식으로 더미변수 변환한 것과 같은 의미이다.**
    - 이 상수항이 축소랭크 방식에서 기준 상수항의 역할을 한다.
    - 상수항 가중치 포함하지 않으면 풀랭크 방식이 된다.
    - 두 방식으로 회귀분석 한 결과 계수값은 같다.

### 4-4. 두 개 이상의 범주형 변수가 있는 경우
- **범주형 독립변수가 두 개 이상 있는 경우 : 축소형 방식 사용**
    - 상수항 결합을 해준다. : 모형지정 문자열에서 +0 포함
- 범주형 값의 조합이 많아 지므로 여러가지 모델이 발생한다.

### 4-5. 범주형 독립변수와 실수 독립변수의 상호작용
- `상호작용 interaction` : 범주형 독립변수의 값이 달라질 때(A, B) 더미변수 값에 따라서 상수항이 달라진다. 이때 상수항만 달라지는 것이 아니라 다른 독립변수들이 종속변수에 미치는 영향도 달라지는 모형을 만들때 사용한다.
    - 즉 범주값의 변화에 따라서 다른 독립변수의 가중치도 달라지는 모형
- 범주값에 따라서 다른 독립변수가 영향을 받는 경우
    - 범주형 독립변수 x_1에 의해서 다른 독립변수 x_2가 영향을 받는다는 의미
- 범주형 독립변수 x1과 x1과 상호작용을 하는 다른 독립변수(실수형) x2 둘다 종속변수에 영향을 미치는 경우
- `위의 두가지 경우를 지정 문자열로 나타내는 방법 확인 할 것`
- **독립변수가 종속변수에 미치는 영향 : 가중치 w = 계수 = 모수**

## 5. 부분회귀
- `특정 독립변수의 종속변수와의 순수한 상관관계(영향력)를 알 수 있다.`
    - 프리슈-워-로벨 정리 FWL
    - 부분회귀 플롯
    - CCPR 플롯

### 5-1. 부분회귀의 의미
- 회귀분석을 한 후에 새로운 독립변수를 추가하여 다시 회귀분석을 하는 경우
    - 예를들면 새로운 특징 벡터가 추가 되는 경우
    - 집값 예측에서 "시내버스와의 거리"라는 독립변수가 추가 되는 경우
- **새로운 독립변수 그룹 X2를 추가해서 다시 회귀분석을 하면 기존 가중치 벡터의 값이 달라지게 된다.**
    - $w^{'}_1 = w_1 - (X^T_1X_1)^{-1}X^T_1X_2w^{'}_2$
    - 기존 가중치 벡터의 값과 다르다.
- 두 회귀분석의 결과가 같은 경우
    - 1) w'2=0 인 경우 : 새로운 독립변수 그룹 X2가 종속변수 y와 상관관계가 없는 경우
    - 2) XT_1X2=0 인 경우 : 독립변수 X1과 새로운 독립변수 X2가 직교하는 경우, 두 독립변수가 서로 상관관계가 없으면 직교할 가능성이 높다.

### 5-2. 프리슈-워-로벨 정리
- `프리슈-워-로벨 정리 Frisch-Waugh-Lovell, FWL`
    - 1) 특정한 독립변수 그룹 X1으로 종속변수 y를 선형회귀분석하여 잔차 y* 를 구한다.
    - 2) X1들로 다른 독립변수 x2를 선형회귀분석하여 잔차 x2* 을 구한다.
    - 3) y* 를 종속변수, x2* 를 독립변수로 하여 선형회귀분석을 한다.
    - 여기에서 나온 가중치는 X1과 x2를 모두 사용하여 y를 선형회귀분석하였을 때 x2에 대한 가중치와 같다.
        - $x^{*^T}_2 x^{*}_2 w_2 = x^{*^T}_2 y^{*}$
- 이 식의 증명과정에서 **잔차행렬 M의 성질**을 사용하게 된다.

### 5-3. 부분회귀 플롯
- `부분회귀 플롯 Partial Regression Plot` : 독립변수의 갯수가 많을 때 특정한 하나의 독립변수의 영향력을 시각화 하는 방법
    - Added Variable Plot 이라고도 함
    - **어떤 독립변수에서 다른 독립변수들의 성분을 제거해주는 것과 같은 의미이다.**
- 부분회귀 플롯을 그리는 방법
    - **프리슈-워-로벨 정리(FWL)의 방법을 사용**
    - 여기에서 구해진 잔차들(x2*, y*)의 스캐터플롯과 회귀분석 결과를 나타낸 것과 같다.

#### 예제 : 보스턴 집값 데이터
- AGE 독립변수와 MEDV 종속변수의 관계는 상관관계가 있는 것 처럼 보인다. 
    - 그래프의 기울기가 0이 아닌 경우, 미미한 +, - 라도 상관관계 있다고 본다.
- statsmodels 패키지 사용
    - sm.graphics.plot_partregress("MEDV", "AGE", others, data=df, obs_label=False, ret_coords=True)
    - **변수 이름 : endog : 종속변수, exog : 독립변수**
- 부분회귀 플롯을 사용하여 AGE와 MEDV 변수의 상관관계를 분석하면 상관관계가 없는 것으로 나온다. 
    - 부분회귀 플롯의 가로축 AGE는 다른 독립변수의 영향을 제거한 **순수한 독립변수의 성분**을 뜻한다.
    - AGE의 데이터가 아니고 AGE에서 다른 성분을 뺀 데이터
- 전체 독립변수에 대한 부분회귀 플롯
    - sm.graphics.plot_partregress_grid(result, fig=fig)
    
### 5-4. CCPR 플롯
- `CCPR Component-Component plus Residual` : 특정한 하나의 변수의 영향을 살펴보기 위한 방법, 부분회귀 플롯과 의미가 같다.
    - 스캐터 플롯의 일종
- statsmodels 패키지 사용
    - sm.graphics.plot_ccpr(result, "AGE")
- 전체 독립변수에 대한 CCPR
    - sm.graphics.plot_ccpr_grid(result, fig=fig)

#### 부분회귀 플롯과 CCPR 플롯을 한번에 나타내기
- sm.graphics.plot_regress_exog(result, "AGE")

## 6. 확률론적 선형 회귀모형
- `최소자승법 OLS로 구한 가중치의 신뢰도(신뢰구간)를 구한다.`
    - 부트스트래핑, 재표본화
    - 확률론적 선형 회귀모형의 4가지 가정 : 가우시안 정규분포, 잡음에 대한 가정
    - 최대가능도 방법과 OLS 방법의 결과는 같다.
    - 잡음과 잔차의 관계, 잡음과 가중치의 관계를 통한 성질 확인
    - 회귀계수의 표준오차 : std err
    - 단일계수 t-검정 : 계수 값에 대한 검정, 두 계수가 같은지 비교
    - 회귀분석 F-검정 : 같은 데이터에대한 회귀모형끼리 성능을 비교할 때 기준값

### 6-1. OLS 방법의 신뢰도는?
- OLS 방법을 사용하여 구한 최적의 가중치 벡터 w가 어느 정도 신뢰할 수 있는지 확인 해야한다.
    - 왜냐하면 표본에서 계산된 "추정치"이기 때문이다.
    - 어떤 독립변수의 x1의 가중치가 -2라고 할 때 이 값이 정확하다면 x1은 종속변수와 반비례한다고 결론을 내릴 수 있다.
    - 그러나 추정오차의 크기가 2.5라고 한다면 실제 가중치는 -2 +,- 2.5이므로 -4.5~0.5의 범위가 된다.
    - 실제 가중치는 0이 될 수도 있고 양수가 될 수도 있다. 그렇다면 반비례한다는 결론이 아닌 아무 상관 없다거나 비례한다는 결과가 나올 수 있다.
- 가중치 추정치의 신뢰도를 구하기 위해서 신뢰 구간을 구해야 한다.

### 6-2. 부트스트래핑
- `부트스트래핑 bootstrapping` : 회귀분석에 사용한 표본 데이터가 달라질 때 회귀분석의 결과는 어느 정도 영향을 받는지 확인하기 위한 방법
    - **OLS로 구한 가중치의 추정치는 표본 데이터에 따라서 달라진다.**
    - 따라서 여러가지 표본 데이터 집합을 바꾸어서 가중치의 변화 정도를 알수 있다.
    - 현실적으로 추가적인 데이터를 얻기 어렵다.
- `재표본화 re-sampling` : 기존의 데이터에서 여러가지 다양한 표본 데이터 집합을 만드는 방법
    - 새로운 데이터를 얻기 어려우므로 기존 데이터를 재표본화하여 가중치의 변화정도를 확인할 수 있다. 
    - 중복 선택도 가능하게끔하여 최대한 다양한 데이터 샘플을 만든다.
- **데이터의 분포 확인**
    - sns.distplot(x, ax=ax1)
- 추정치 평균과 분산의 관계로 신뢰구간 확인
    - 평균 + - 표준편차*2
    - 이 범위에서 추정치가 음수, 0, 양수로 바뀌는지에 따라서 독립변수에 대한 분석이 달라지게 된다.

#### OLS 레포트에서 확인가능
- `OLS 레포트의 값은 부트스트래핑이 아닌 확률론적 선형 회귀모형을 사용하여 구한 값이다.`
- **std err** : 추정치의 표준편차
- **[0.025  0.975]** : 신뢰구간

### 6-3. 확률론적 선형 회귀모형
- OLS 레포트에서 가중치의 추정치에 대한 표준편차(std err), 신뢰구간의 값은 부트스트래핑이 아닌 확률론적 선형 회귀모형으로 구한 값이다.
- 확률론적 선형 회귀모형을 사용하면 부트스트래핑 처럼 많은 계산을 하지 않고도 빠르고 안정적으로 가중치 추정값의 오차를 구할 수 있다.
- 확률론적 선형 회귀모형은 데이터가 확률변수로 부터 생성된 표본이라고 가정한다.

### 확률론적 선형 회귀모형의 4가지 가정
- 1) `선형정규 분포 가정`
    - **종속변수 y는 가우시안 정규 분포를 따른다.**
    - 기댓값 : x의 선형조합 w^Tx 
    - 분산 : \sigma^2
    - 잡음 \epsilon 으로 선형회귀모형을 나타낼 수 있다. : $\epsilon = y-w^Tw$
- 2) `외생성 가정 Exogeneity`
    - **잡음 \epsilon의 조건부 기대값은 독립변수 x에 상관없이 항상 0이라고 가정한다.** : $\text{E}[\epsilon | x] = 0$
    - 무조건부 기댓값 = 0
    - 잡음 \epsilon과 독립변수 x가 상관관계가 없다.
- 3) `조건부 독립 가정`
    - **i번째 표본의 잡음과 j번째 표본의 잡음의 공분산 값이 x와 상관없이 항상 0이라고 가정한다.** : $\text{Cov}[\epsilon_i, \epsilon_j] = 0$
    - \ep_i와 \ep_j는 서로 독립이다.
    - 잡음 벡터 \ep의 공분산 행렬은 대각행렬이 되어야한다. : $\text{Cov}[\epsilon] = \text{E}[\epsilon \epsilon^T] = \text{diagonal matrix}$
- 4) `등분산성 가정`
    - **\ep_i와 ep_j의 분산 값이 표본과 상관없이 항상 같다고 가정한다.**
    - 잡음 벡터 \ep의 공분산 행렬이 항등핼렬이 되어야 한다. : $\text{Cov}[\epsilon] = \text{E}[\epsilon \epsilon^T] = \sigma^2 I$
    
### 6-4. 최대 가능도 방법을 사용한 선형 회귀분석
- 확률론적 선형 회귀모형의 가정과 최대 가능도 방법(MLE : Maximum Likelihood Estimation)을 사용하여 가중치 벡터 w의 값을 구한다.
    - 최대가능도방법 : MLE : 변화하는 값을 모수로, 고정된 상수를 독립변수로 바꾸어 확률분포를 계산하는 방법
    - 누적 계산을 통해 모수를 추정하기 위한 방법으로 다양한 확률분포에서 최적의 모수를 찾기 위해 사용된다.
- 가능도 방법에 로그를 적용하면 곱하기를 더하기로 바꿀 수 있어 식이 간단해진다.
- **로그 가능도 방법으로 구한 식을 최적화하면(w벡터로 미분하여 그레디언트 벡터를 구하고 이 벡터가 0이라고 하면) OLS 방법으로 구한 것과 동일한 결과를 얻을 수 있다.**
    - $\hat{w} = (X^TX)^{-1}X^Ty$
    - 또한 직교방정식도 같다.
    
### 6-5. 잔차의 분포
- `확률론적 선형 회귀모형에 따르면 잔차는 정규분포를 따른다.`
    - $e = y - \hat{y} = y - Hy = (I-H)y = My = M(Xw + \epsilon)$
    - $e = M\epsilon$
- 잔차는 잡음의 선형변환(linear transform)이다. 정규분포의 선형변환은 정규분포이므로 잔차 e도 정규분포를 따른다.
    - 잡음 \epsilon은 정규분포를 따른다.
    
#### 잔차의 정규성 검정
- `정규성 검정 normality test` : 확률분포가 가우시안 정규분포를 따르는지 확인
    - 옴니버스 검정 : sm.stats.omni_normtest(result.resid)
    - 자크-베라 검정 : sm.stats.jaque_bera(result.resid)
    - 콜모고로프-스미르노프 검정 : sm.stats.kstest_normla(result.resid)
- `잔차의 기댓값도 x와 상관없이 0`이다.
    - 잡음(=오차, disturbans)의 기댓값이 x와 상관없이 0이다. (외생성 가정)

### 6-5. 회귀 계수의 표준 오차
- `회귀 계수의 표준오차 standard error of regression coefficient` : 가중치 벡터 w의 표준편차
    - OLS 방법으로 구한 가중치 벡터의 신뢰도, 추정오차에 대한 값
    - OLS 레포트에서 "std err"에 해당하는 값

#### 구하는 방법
- `가중치 벡터 w의 예측치의 공분산 행렬을 구하고, 공분산 행렬의 대각성분이 분산이므로, 이 분산으로부터 표준편차를 구한다.`
- 가중치의 예측치 \hat{w}도 정규분포확률변수인 \epsilon의 선형변환이므로 정규분포를 따른다. 
    - $\hat{w} = w + (X^TX)^{-1}X^T\epsilon$
- \hat{w}의 기댓값은 w와 같다.
    - $\text{E}[\hat{w}] = w$
    - \hat{w}는 w의 비편향 추정값이다.      
- \hat{w}의 공분산
    - $\text{Cov}[\hat{w}] = \sigma^2(X^TX)^{-1}$
- 잔차의 분산으로부터 잡음의 분산을 추정
    - $\text{E}[e^2] = \sigma^2(N-K)$
- 잡음에 대한 비편향 표본분산
    - $s^2 = \dfrac{e^Te}{N-k} = \dfrac{RSS}{N-K}$
- \hat{w}(가중치의 예측치)의 공분산 추정값
    - $\text{Cov}[\hat{w}] \approx s^2(X^TX)^{-1}$
- w_i의 분산은 공분산 행렬의 대각성분
    - $\text{Var}[\hat{w}_i] = (\text{Cov}[\hat{w}])_{ii}$
- w 예측치의 표준오차 : 표준편차
    - $\sqrt{\text{Var}[\hat{w}_i]} \approx se_i = \sqrt{s^2((X^TX)^{-1})_{ii}}$
- **정규화된 모수오차는 자유도가 N-K인 표준스튜던트 t분포를 따른다.** 

### 6-6. 단일 계수 t-검정

#### 정규화된 모수오차로 검정
- 정규화된 모수오차를 검정통계량을 사용하여 wi가 0 인지 아닌지에 대한 검정을 할 수 있다.
    - $H_0 : w_i = 0 \; (i=0,\cdots,K-1)$
    - 유의확률이 0이면 귀무가설 기각 : wi = 0 일 가능성이 작다.
    - 유의확률이 유의수준보다 크면 귀무가설 채택 : wi = 0 일 가능성이 크다.
    - **즉 wi, 독립변수 xi 들의 가중치가 종속변수와 상관관계가 있는지 없는지를 검정한다.**
- OLS 레포트에서 이 검정에 대한 유의확률 : P>|t|
    - 즉 기본적으로 OLS 레포트는 wi=0인지에 대한 검정을 계산해준다.

#### `단일 계수-t 검정 single coefficient t-test`
- **단일 계수-t검정을 사용하면 wi=0 이 아닌 경우도 검정할 수 있다.**
    - $H_0 : w_1 = 60$
    - w1의 값은 50이다라는 귀무가설을 검정할 수 있다.
    - 유의확률을 확인하여 판단할 수 있다.
- statsmodels
    - print(result.t_test("x1=60")) : 독립변수의 이름과 가중치 값을 넣어준다.
- **두 독립변수의 계수값 비교**
    - 두 독립변수의 계수값을 비교하는 검정을 할 수 있다.
    - print(result.t_test("x1=x2"))
    - 유의확률을 확인하여 귀무가설이 기각되면 두 독립변수의 계수값은 다르다는 의미
    - 귀무가설이 채택되면 두 독립변수의 계수값은 거의 같다는 의미
    - 두 독립변수의 계수값이 같다고 하면 하나는 제거해도 모형의 성능에 변화가 없다.

### 6-7. 회귀분석 F-검정
- `회귀분석 F-검정 regression F-test` : 여러 회귀모형의 유의확률을 비교하고 어떤 모형이 더 성능이 좋은지 비교한다.
- 전체 회귀 계수(모수, 가중치 벡터)가 모두 의미가 있는가에 대한 귀무가설
    - 즉 $H_0 : w_1=w_2=...=w_{k-1}=0$
    - 모든 독립변수의 가중치가 0이냐 아니냐에 대한 검정으로, 이 모형의 성능이 어느정도인지 알 수 있다.
- 대부분의 경우 이 귀무가설은 기각된다.
    - 유의확률의 값이 작을 수록 귀무가설의 기각이 강하므로 더 의미가 있는 모형이다.
    - 유의확률의 값이 클 수록 귀무가설의 채택이 강하므로 성능이 떨어지는 모형이다.
- **OLS 레포트**
    - F-검정 통계량 : F-statistic    
    - 유의확률 : Prob(F-statistic)

## 7. 회귀분석의 기하학

### 7-1. 회귀 벡터공간
- 예측값 \hat{y}를 X의 각 열 c1,...,cM의 선형조합으로 표현 할 수 있다.
    - $\hat{y} = Xw = w_1c_1 + w_2c_2 + \cdots + w_Mc_M$
    - ci는 X의 열벡터
- 모든 열 ci가 선형독립(풀랭크)이면 예측값 \hat{y}는 X의 각 열을 **기저벡터(basis vector) 로 하는 벡터공간 위에 존재한다.**

#### 회귀모형의 각 상수항을 벡터공간에 나타내면
- 예측값 \hat{y}
    - 종속변수와 예측값의 차이인 잔차 벡터를 가장 작게 만드는 최적의 예측값
    - 벡터공간 내에 존재
    - y와 가장 가까운 벡터
    - 종속변수 y를 X의 각 열 ci를 기저벡터로 하는 벡터공간에 **투영한 벡터**
- 잔차 벡터 e
    - 실제값(종속변수)과 예측값의 차이
    - 벡터공간에 직교
    - 종속변수 y를 벡터공간에 투영하고 남은 **직교 벡터**

### 7-2. 잔차행렬과 투영행렬
- `변형행렬 transform matrix` : 벡터 a에서 벡터 b를 변형하는 데 쓰이는 행렬 T
    - $b = Ta$
- `잔차행렬 residual matrix` : 종속값 벡터 y를 잔차 벡터 e로 변환하는 행렬 M
    - $e = My$
    - $M = I-H = I - X(X^TX)^{-1}X^T$
- `투영행렬 projection matrix` : 종속값 벡터 y를 예측값 벡터 \hat{y}로 변환하는 행렬 H
    - $\hat{y} = Hy$
    - $H = X(X^TX)^{-1}X^T$
    - 햇(hat)행렬, **영향도 행렬(influence matrix)**

#### 잔차행렬 M과 투영행렬 H의 성질
- 1) 대칭행렬이다.
    - $M^T=M,\;\; H^T=H$
- 2) 멱등행렬(idempotent)이다. : 자기자신을 몇번을 곱하든 자기자신이 되는 행렬
    - $M^2=M^5=M,\;\; H^2=H^7=H$
- 3) M과 H는 서로 직교한다.
    - $MH = HM = 0$
- 4) M은 X와 직교한다.
    - $MX=0$
- 5) X에 H를 곱해도 변하지 않는다.
    - $HX=H$

































































































































































































