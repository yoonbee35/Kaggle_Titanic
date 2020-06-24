# Kaggle_Titanic_ML-2020
실행 환경 : Local/Jupyter notebook 이용

## 문제 정의 : Kaggle의 Tatinic 탑승자 정보를 바탕으로 생존자(survived) 예측
## 학습 계획
[1] 데이터 분석 <br>
[2] 데이터 전처리<br>
[3] 모델 선택 및 비교. 결과 비교 및 <br>
[4] 모델 미세 조정<br>
[5] 결과 분석<br>
[6] 결론<br>


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
10. 상관관계파악 결과 <i>**Pclass와 Fare이 비교적 높은 상관관계를 보임, Passenger Id는 생존 여부와 거의 관계가 없음**


## 2. 데이터 전처리_Data_Preprocess.ipynb
* **데이터 분석을 통해 얻은 전처리 정보
1. 필요없는 데이터 제거 => feature로 사용하지 않음
`Ticket` : 생존 여부와 관계 없음
`Name` , `PassengerId` : 모두 다른 값을 가지고 있고 생존 여부과 관계 없음 
`Cabin` : train, test데이터 모두 너무 많은 Null값을 가지고 있음

2. Age와 Embarked는 생존여부와 관계가 있다고 생각되기 때문에 Null값을 채워넣어야 함
`Age` : 
`Embarked`

3. Age와 Fare는 너무 세세하게 값이 나누어져 있기 때문에 데이터로 사용하기 힘듦 -> 범주형 데이터로 만들어 일반화

4. `SibSp`와 `Parch`의 경우 Family(가족)이라는 feature로 묶일 수 있기 때문에 결합

5. feature로 사용할 object type의 데이터를 numeric data로 변환
`Sex`, `Embarked` : numeric data로 변환

6. 만약 feature들 사이의 scale 차이가 많이 날 경우 특성 scaling 진행 필요





reference : 
[Subinium Tutorial] Titanic (Intermediate) : https://www.kaggle.com/subinium/subinium-tutorial-titanic-intermediate
[kaggle] 타이타닉 생존자 예측, python : https://velog.io/@skyepodium/kaggle-%ED%83%80%EC%9D%B4%ED%83%80%EB%8B%89-%EC%83%9D%EC%A1%B4%EC%9E%90-%EC%98%88%EC%B8%A1
