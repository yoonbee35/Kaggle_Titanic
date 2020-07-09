# Kaggle_Titanic_ML-2020
실행 환경 : Local/Jupyter notebook 이용

## 문제 정의 : Kaggle의 Titanic 탑승자 정보를 바탕으로 생존자(survived) 예측
## 학습 계획
[1] 데이터 분석 <br>
[2] 데이터 전처리<br>
[3] 학습 모델 선택 및 결과 분석
[4] 결론<br>

<br><br>


## 1. 데이터 분석_Data EDA.ipynb
* **Data Shape** <br>
train : (891, 12) <br>
test : (418, 11) // train에서 survive 행이 없음 <br>

* **Feature Type** <br>
numeric data : PassengerId, Survived, Pclass, Age, SibSp, Parch, Fare<br>
object data : Name, Sex, Ticket, Cabin, Embarked<br>
Null값이 존재하는 data : Age, Cabin, Embarked(적음), Fare(test data에만)<br>

* **attribute** <br>
`Passenger Id`<br>
`Survived`(0 = 사망, 1 = 생존)<br>
`Pclass` (1 = 1st, 2 = 2nd, 3 = 3rd)<br>
`Sex` - 성별<br>
`Age` - 나이<br>
`SibSp` - 함께 탑승한 자녀 / 배우자 의 수<br>
`Parch` - 함께 탑승한 부모님 / 아이들 의 수<br>
`Ticket` - 티켓 번호<br>
`Fare` - 탑승 요금<br>
`Cabin` - 객실 번호<br>
`Embarked` - 선착장 (C = Cherbourg, Q = Queenstown, S = Southampton)<br>

* **Train data 분석 결과** <br>
![image](https://user-images.githubusercontent.com/55186800/85547142-8d2aef80-b658-11ea-97ee-8ea3b0dca570.png) 
1. 대부분의 승객(>75%)은 부모 또는 자식과 함께 타지 않음.<br>
2. 30%의 승객은 형제자매나 배우자와 함께 탑승<br>
3. 나이가 많은 승객(65-80)의 수는 1% 미만<br>
4. Name은 모두 다름 (count=unique)<br>
5. Ticket은 중복이 적음<br>
6. 성별(Sex)은 577/891이 남자로 약 65%를 차지<br>
7. Cabin은 양도 적고 중복도 많음 ==> 일부 손님들이 호실 공유<br>
8. 3등석 탑승 인원이 가장 많음<br>
9. 대부분의 탑승 인원이 100달러 이하의 요금을 지불 ($512 이상의 요금을 지불한 사람은 없음)<br>
10. 상관관계파악 결과 **Pclass와 Fare이 비교적 높은 상관관계를 보임, Passenger Id는 생존 여부와 거의 관계가 없음** <br>
<Correlation>
  
  
**Feature** | **Correlation Coefficient**  
:----: | :----:
Survived | 1.000000 
Fare | 0.257307      
Parch |  0.081629   
PassengerId | -0.005007 
SibSp | 0.035322  
Age | -0.077221  
Pclass | -0.338481
             

<br><br>


## 2. 데이터 전처리_Data_Preprocess.ipynb
* **필요없는 데이터 제거 => feature로 사용 X**<br>
`Ticket` : 생존 여부와 관계 없음<br>
`Name` , `PassengerId` : 모두 다른 값을 가지고 있고 생존 여부과 관계 없음 <br>
`Cabin` : train, test데이터 모두 너무 많은 Null값을 가지고 있음<br>

* **Null값 채우기 (Age, Embarked,Fare)**<br>
  1. `Age` : `Sex(0,1)`와 `Pcalss(1,2,3)`를 이용하여 `Age`를 예측한 뒤 median값을 이용하여 결측치 채우기<br>
  `Pclass`가 survived와 가장 높은 상관관계를 가지고 있음<br>  
  `Sex`와 `Pclass`를 연관지어 `Age`를 예측하는 것이 가장 합리적이라 생각<br>
 
  2. `Embarked` : 최빈값으로 결측치 채우기<br>

  3. `Fare` : test 파일 내 모든 `Fare`값의 mdeidan 값으로 결측치 채우기<br>


* **데이터 범주화 (Age, Fare)**<br>
1. 데이터가 너무 세세하게 나누어져 있어 feature로 사용이 어려움<br>
2. `Survive`와의 관계를 바탕으로 범주형 데이터로 만들어 일반화<br>
3. 범주화를 통해 특성 스케일링 가능 <br>
Age : 

AgeBand | Survived
-- | --
(-0.08, 16.0] | 0.550000
(16.0, 32.0] | 0.337374
(32.0, 48.0] | 0.412037
(48.0, 64.0] | 0.434783
(64.0, 80.0] | 0.090909

 Fare :

FareBand | Survived
-- | --
(-0.001, 7.91] | 0.197309
(7.91, 14.454] | 0.303571
(14.454, 31.0] | 0.454955
(31.0, 512.329] | 0.581081

* **비슷한 feature들 묶기**<br>
`SibSp`와 `Parch`의 경우 Family(가족)이라는 feature로 묶일 수 있기 때문에 결합<br>

* **object type의 데이터를 numeric data로 (Sex, Embarked)**<br>
`Sex`, `Embarked` : numeric data로 변환<br>


### 최종 Train_dataset sample

  | **Survived** | **Pclass** | **Sex** | **Age** | **SibSp** | **Parch** | **Fare** | **Embarked** | **Family**
-- | -- | -- | -- | -- | -- | -- | -- | -- | --
0 | 0 | 3 | 0 | 1 | 1 | 0 | 0 | 0 | 2
1 | 1 | 1 | 1 | 2 | 1 | 0 | 3 | 1 | 2
2 | 1 | 3 | 1 | 1 | 0 | 0 | 1 | 0 | 1
3 | 1 | 1 | 1 | 2 | 1 | 0 | 3 | 0 | 2
4 | 0 | 3 | 0 | 2 | 0 | 0 | 1 | 0 | 1
5 | 0 | 3 | 0 | 1 | 0 | 0 | 1 | 2 | 1
6 | 0 | 1 | 0 | 3 | 0 | 0 | 3 | 0 | 1
7 | 0 | 3 | 0 | 0 | 3 | 1 | 2 | 0 | 5
8 | 1 | 3 | 1 | 1 | 0 | 2 | 1 | 0 | 3
9 | 1 | 2 | 1 | 0 | 1 | 0 | 2 | 1 | 2


<br><br>

## 3. 학습 모델 선택 및 결과 비교_train_Example.ipynb
* 다양한 모델을 학습해보고 cross_validation 결과로 최종 선정 모델 결정==> **SVM**<br>
![image](https://user-images.githubusercontent.com/55186800/86536554-8ab37a00-bf23-11ea-9875-820e5214b064.png)<br>

* SVM 
<model 선택 이유>
 1. data preprocessing을 통해 feature들의 scaling을 완료한 상태 (모든 feature가 0과100사이)
 2. 891개의 많지 않은 데이터이기 때문에 적은 데이터에도 효율적으로 동작할 수 있는 Support Vector Machine 선택
 3. sklearn.svm의 SVM() 사용하고 parameter는 기본값으로 설정 (C=1.0, kernel='rbf'등)

* Model ensemble : 높은 정확도를 보였던 3개의 model을 ensemble (SVM, Random Forest, Logistic Regression)
1. `VotingClassifier` 사용 : SVM과 Random Forest, Logistic Regression 등의 model을 사용하여 soft Voting 사용
2. 각각의 model의 class를 예측하는 probability를 출력하고 각각의 확률을 평균내어 가장 높은 확률을 보인 값으로 클래스를 분류함

<br><br>


## 4. 결론
<test data 생성 결과>
![image](https://user-images.githubusercontent.com/55186800/86795134-38e82c80-c0a8-11ea-95b4-e5bdaac66992.png)

conclusion) Model Ensemble을 이용한 경우 Single Model을 이용한 정확도보다 좋은 성능을 보임
특히, SVM는 scaling에 민감하기 때문에 data preprocessing 과정에서 데이터 범주화와 null값 채우기를 통해 적절한 scaling을 진행해줘야 함







<br><br><br><br>
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
**reference**<br>
[Subinium Tutorial] Titanic (Intermediate) : https://www.kaggle.com/subinium/subinium-tutorial-titanic-intermediate
[kaggle] 타이타닉 생존자 예측, python : https://velog.io/@skyepodium/kaggle-%ED%83%80%EC%9D%B4%ED%83%80%EB%8B%89-%EC%83%9D%EC%A1%B4%EC%9E%90-%EC%98%88%EC%B8%A1
