# regression of IBM HR data
- 기간: 2021. 03. 09. -  2021. 03. 25.
- [장혜임](https://github.com/mieyhgnaj): EDA, 데이터 전처리, 발표자료 작성, 모델링
- [정민주](https://github.com/meiren13): EDA, 발표자료 작성, 발표
- 👉🏻 [발표자료](https://drive.google.com/file/d/1qe6Aeq7voaDFavUYEWewGIJZme6GJ-BY/view?usp=sharing)

</br>

## 프로젝트 목표
IBM HR data를 바탕으로    
파이썬을 활용한 선형 회귀분석을 통해    
월수입( Monthly Income)과 통계적으로 유관한 HR변수는 무엇이 있는지 파악

</br>

## 1. 데이터 소개
![image](https://user-images.githubusercontent.com/73205057/112745575-7600a000-8fe4-11eb-828b-b446825156ac.png)
- 컬럼: 35개
- 데이터: 1470개

</br>

### 1-1 변수 설명
![image](https://user-images.githubusercontent.com/73205057/112745711-6afa3f80-8fe5-11eb-93f4-e4b6d074b539.png)

</br>

### 1-2 결측치 확인
![image](https://user-images.githubusercontent.com/73205057/112745600-a0eaf400-8fe4-11eb-96b1-fb6ef4f2d672.png)

</br>

### 1-3 독립변수 간의 상관관계 파악
![image](https://user-images.githubusercontent.com/73205057/112745605-ac3e1f80-8fe4-11eb-98d5-2e570ed43797.png)

</br>


## 2. EDA
### 2-1 독립변수 중 상관관계가 높은 변수
- 범주형 변수와 연속형 범주를 함께 비교하기 위해 pairplot 이용
- boxplot을 활용하여 이상치 및 분포 확인

  - JobLevel 기준 : 'Age', 'TotalWorkingYears', 'YearsAtCompany'
 
![image](https://user-images.githubusercontent.com/69558935/112978917-de8d7f80-9192-11eb-99ae-2a55c8e88a20.png)

![image](https://user-images.githubusercontent.com/69558935/112976696-265ed780-9190-11eb-9cb1-d4a95e8170b9.png)

![image](https://user-images.githubusercontent.com/69558935/112978758-aede7780-9192-11eb-93da-1b3dd70cad28.png)

  - year 기준 : 'YearsAtCompany', 'YearsInCurrentRole', 'YearsSinceLastPromotion', 
  'YearsWithCurrManager', 'TotalWorkingYears', 'TrainingTimesLastYear'
 
![image](https://user-images.githubusercontent.com/69558935/112926395-55545980-914e-11eb-9b95-492a0a6f8c37.png)

![image](https://user-images.githubusercontent.com/69558935/112978994-f82ec700-9192-11eb-9a3a-726725a0de80.png)

</br>

### 2-2 WorkLifeBalance & MonthlyIncome
- WorkLifeBalance unique
  - Bad    : 1    >   wlb: 1_MonthlyIncome
  - Good   : 2    >   wlb: 2_MonthlyIncome
  - Better : 3    >   wlb: 3_MonthlyIncome
  - Best   : 4    >   wlb: 4_MonthlyIncome
 
 - 두 변수간의 관계
![image](https://user-images.githubusercontent.com/69558935/112980437-da626180-9194-11eb-80bf-880e234961dc.png)
![image](https://user-images.githubusercontent.com/69558935/112980898-6f655a80-9195-11eb-8b12-11bbca12eef6.png)
![image](https://user-images.githubusercontent.com/69558935/112981151-c0754e80-9195-11eb-8ea8-525e87f9f9f6.png)

</br>

### 2-3 YearsAtCompany & MonthlyIncome
```
sns.lmplot(x='YearsAtCompany', y='MonthlyIncome', data=hr, height=6.27, aspect=8.7/6.27)
```
![image](https://user-images.githubusercontent.com/73205057/113288788-5cd05a00-932a-11eb-807c-e2888f337bf3.png)

- 회사 재직기간이 늘어날수록 월수입 증가! (당연한 건가,,)

</br>

### 2-4 Number of Employees by Department
```
sns.countplot('Department', hue='Gender', data=hr, color='brown')
```
![image](https://user-images.githubusercontent.com/73205057/113288938-84bfbd80-932a-11eb-9c34-47416a9b963f.png)

- Research & Development 부서에 가장 많은 직원이 있고, Human Resources 부서엔 가장 적은 직원이 있다.
- 모든 부서에서 남자 직원이 더 많다.

</br>

### 2-5 PercentSalaryHike & PerformanceRating
```
sns.kdeplot(hr[hr['PerformanceRating']==3]['PercentSalaryHike'], shade=True, legend=False, color='red')
sns.kdeplot(hr[hr['PerformanceRating']==4]['PercentSalaryHike'], shade=True, legend=False, color='black')
```
![image](https://user-images.githubusercontent.com/73205057/113289044-a3be4f80-932a-11eb-9ef1-1012a6c56a64.png)

- 인사고과에서 4를 받았다면, 임금인상률은 최소 20%!.!

</br>

### 2-6 average monthly income by education
  - Below College    : 1    >   edu: 1_MonthlyIncome
  - College   : 2    >   edu: 2_MonthlyIncome
  - Bachelor : 3    >   edu: 3_MonthlyIncome
  - Master   : 4    >   edu: 4_MonthlyIncome
  - Doctor   : 5    >   edu: 4_MonthlyIncome
![image](https://user-images.githubusercontent.com/73205057/113290339-7672a100-932c-11eb-99a9-40c00e083d06.png)

</br>

## 3. 데이터 기본 전처리
- 변수 제거
- 범주형 변수 인코딩
  - LabelEncoder( )
  - OneHotEncoder( )

</br>

### 3-1 변수 제거
① 모든 직원에 대해 같은 데이터이므로 제거 (EmployeeCount / Over18 / StandardHours)    
![image](https://user-images.githubusercontent.com/73205057/112745680-3be3ce00-8fe5-11eb-8718-09eb0b5ed2bd.png)    
② 사원번호이므로 제거 (EmployeeNumber)    
![image](https://user-images.githubusercontent.com/73205057/112745732-882f0e00-8fe5-11eb-9106-90fd1cad3c81.png)    
③ 불필요한 변수 제거    
- DailyRate, HourlyRate, MonthlyRate: 기본급은 종속변수로 설정한 MonthlyIncome를 예측하는 데 필요없다고 판단
- YearsWithCurrManager, Department: 타 변수와 내용 겹침
- Attrition : 해당 데이터가 이직 여부를 알아보기 위해 만들어진 데이터. MonthlyIncome를 종속변수로 선정했기 때문에 이직여부는 필요없다고 판단

</br>

### 3-2 범주형 변수 인코딩
① LabelEncoder( ): 순서가 있는 변수    
- BusinessTravel, OverTime    
![image](https://user-images.githubusercontent.com/73205057/112745750-a3018280-8fe5-11eb-9f62-3a950ad0e7e4.png)    
![image](https://user-images.githubusercontent.com/73205057/112745752-a8f76380-8fe5-11eb-871c-cb9bb817425c.png)

</br>

=> 인코딩 후    
![image](https://user-images.githubusercontent.com/73205057/112745759-b280cb80-8fe5-11eb-9590-f7c341c41408.png)

</br>

② OneHotEncoder( ) : 순서가 없는 변수    
- EducationField, Gender, JobRole, MaritalStatus    
![image](https://user-images.githubusercontent.com/73205057/112745785-d17f5d80-8fe5-11eb-8551-0f279e6a86dd.png)

</br>

=> 인코딩 후    
![image](https://user-images.githubusercontent.com/73205057/112745797-ed82ff00-8fe5-11eb-9edb-fd79dbbc829f.png)

</br>

### 3-3 최종 변수
- 인코딩 전후 비교    
![image](https://user-images.githubusercontent.com/73205057/112745810-12777200-8fe6-11eb-9962-3ebf1fd76bfa.png)



</br>

## 4. 데이터 추가 전처리 (binning)
- 범주화가 꼭 필요한 상황은 아니지만, 전후 비교를 위해 범주화 진행
- Age(연령대), DistanceFromHome(5단위), TotalWorkingYears(5단위), YearsAtCompany(5단위), YearsInCurrentRole(3단위)
- discrete data : 구간으로 나눔    
```
hr_bin['Age'] = pd.cut(x=hr_bin['Age'], bins=[-np.inf, 19, 29, 39, 49, 59, np.inf], labels=[1,2,3,4,5,6])
hr_bin['DistanceFromHome'] = pd.cut(x=hr_bin['DistanceFromHome'], bins=[-np.inf, 5, 10, 15, 20, 25, np.inf], labels=[1,2,3,4,5,6])
hr_bin['TotalWorkingYears'] = pd.cut(x=hr_bin['TotalWorkingYears'], bins=8, labels=[1,2,3,4,5,6,7,8])
hr_bin['YearsAtCompany'] = pd.cut(x=hr_bin['YearsAtCompany'], bins=8, labels=[1,2,3,4,5,6,7,8])
hr_bin['YearsInCurrentRole'] = pd.cut(x=hr_bin['YearsInCurrentRole'], bins=6, labels=[1,2,3,4,5,6])
```
- 범주화 전후 비교

</br>

![image](https://user-images.githubusercontent.com/73205057/112745831-389d1200-8fe6-11eb-855b-0a910075203a.png)



</br>

## 5. 모델 평가 비교
```
# 라벨 분리
X = hr_raw.drop(columns=['MonthlyIncome'])
y = hr_raw.MonthlyIncome

# 훈련용과 테스트용으로 나누기
from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=4)
X_train.shape, X_test.shape, y_train.shape, y_test.shape
```
```
from sklearn.linear_model import LinearRegression, Ridge, Lasso
from sklearn.ensemble import RandomForestRegressor, GradientBoostingRegressor
from xgboost import XGBRegressor
from lightgbm import LGBMRegressor

models = []
models.append(('LinearRegression', LinearRegression()))
models.append(('RandomForestRegressor', RandomForestRegressor()))
models.append(('GradientBoostingRegressor', GradientBoostingRegressor()))
models.append(('XGBRegressor', XGBRegressor()))
models.append(('LGBMRegressor', LGBMRegressor()))
models.append(('Ridge', Ridge()))
models.append(('Lasso', Lasso()))
```
```
from sklearn.metrics import mean_squared_error, mean_absolute_error, r2_score

names = []
rmse_tr = []
rmse_test = []
mae_tr = []
mae_test = []
r2_tr = []
r2_test = []

for name, model in models:
    model.fit(X_train, y_train)
    pred_tr = model.predict(X_train)
    pred_test = model.predict(X_test)
    
    names.append(name)
    rmse_tr.append((np.sqrt(mean_squared_error(y_train, pred_tr))))
    rmse_test.append((np.sqrt(mean_squared_error(y_test, pred_test))))
    mae_tr.append(mean_absolute_error(y_train, pred_tr))
    mae_test.append(mean_absolute_error(y_test, pred_test))
    r2_tr.append(r2_score(y_train, pred_tr))
    r2_test.append(r2_score(y_test, pred_test))
    
    plt.figure(figsize=(10,6))
    plt.title(name)
    plt.scatter(y_test, pred_test)
    plt.xlabel('Actual Monthly Indome')
    plt.ylabel('Predicted Monthly Income')
    plt.plot([0, 20000], [0, 20000], 'g')
    plt.show()
```

① `hr_raw.csv` 인코딩만 한 버전    
![image](https://user-images.githubusercontent.com/73205057/112745919-b52ff080-8fe6-11eb-96af-3f4891e5841f.png)    
② `hr_bin.csv` 인코딩 + 범주화까지 한 버전    
![image](https://user-images.githubusercontent.com/73205057/112745925-bcef9500-8fe6-11eb-862c-53bb42c49a07.png)    

</br>

> 🤣 범주화 여부에 따른 차이가 별로 없다...    
> &emsp; 그래서 다음 K-Fold 교차 검증은 인코딩만 한 버전으로 진행했다.

</br>

### 5-1 K-Fold
- RMSE

</br>

![image](https://user-images.githubusercontent.com/73205057/112745958-eb6d7000-8fe6-11eb-98cf-74410660a7f0.png)
![image](https://user-images.githubusercontent.com/73205057/112745970-faecb900-8fe6-11eb-87d0-53c9be466c36.png)

</br>

- R2

</br>

![image](https://user-images.githubusercontent.com/73205057/112745975-05a74e00-8fe7-11eb-819a-8666033be095.png)
![image](https://user-images.githubusercontent.com/73205057/112745977-0b049880-8fe7-11eb-99ed-8b18aa89291d.png)

</br>

### 5-2 GridSearchCV

</br>

> 🤪 위 결과에서 최적의 모델은 RMSE는 작고 R2는 1에 가까운 GradientBoostingRegressor..?

</br>

- params 확인

</br>

![image](https://user-images.githubusercontent.com/73205057/112746008-4b641680-8fe7-11eb-895d-df850e66bd73.png)

</br>

- GridSearchCV

</br>

![image](https://user-images.githubusercontent.com/73205057/112746081-abf35380-8fe7-11eb-9ed3-cb421976a0e3.png)

</br>

- feature_importances_

</br>

![image](https://user-images.githubusercontent.com/73205057/112746093-b9104280-8fe7-11eb-9040-bbb2d5cbcbfc.png)
![image](https://user-images.githubusercontent.com/73205057/112746097-c0cfe700-8fe7-11eb-902c-396e08402884.png)

</br>

## 프로젝트 회고
- 데이터를 인코딩만 했는데도 성능이 좋게 나왔다.. 이것저것 할 때마다 변화가 생기는 데이터였으면 더 재밌었을 것 같지만, 그래도 덕분에 여러 알고리즘에 적용해 볼 수 있었다.
- 각 알고리즘의 특성에 대해 더 깊은 공부가 필요하다.
- !공부!공부!

</br>

## 데이터 출처
https://www.kaggle.com/pavansubhasht/ibm-hr-analytics-attrition-dataset

