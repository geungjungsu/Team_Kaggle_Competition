- 변수
    - Features(X)
        - ID : 순번
        - Customer ID: 각 고객의 고유 식별번호
        - Surname: 고객의 성
        - Credit Score: 고객의 신용점수
        - Geography: 고객이 거주하는 국가
        - Gender: 고객의 성별
        - Age: 고객의 나이
        - Tenure: 고객이 은행을 이용한 기간
        - Balance: 고객의 계좌 잔액
        - NumOfProducts: 고객이 이용하는 은행 상품의 수(ex. 예금,적금)
        - HasCrCard: 신용카드 보유 여부
        - IsActiveMember: 활성 회원 여부
        - EstimatedSalary: 고객의 예상 연봉
    - Target(Y)
        - Exited: 고객 이탈 여부


1. 변수 제거 후보
- Id
- Surname
- CustomerId
  - 고유값이라면 유지
  - 고유값 기능 못한다면 제거

2. 변수 전처리
- 타입 변경
  - 인코딩
      - Object : Geography(3), Gender(2)
          - int
          - category
  - float -> int
      - Age
          - ex) 36.44, 32.34(소수점 존재)
          - 소수점의 의미 불명
      - Balance, EstimatedSalary
          - 소수점 의미 명확(타입 변경 X)
      - HasCrCard, IsActiveMember
          - 범주형 변수
          - int
          - category
          
- 스케일링(이상치 기준으로 스케일링 적용)

```
  - StandardScaler : Mean = 0, std = 1
      - 데이터 정규분포에 가까운 경우
      - 변수 크기 차이가 크고, 이상치 적을 때
  
  - RobustScaler : Median, IQR 사용
      - 이상치 영향 최소화
      - 중앙값 기준으로 스케일링 하므로 분포가 비대칭적인 경우 효율
      - 데이터가 정규분포를 따르는 경우, StandarScaler만큼 효과적이지 않을 수 있다
  
  - MinMaxScaler : Min = 0 , Max = 1 사이 스케일링
      - 데이터가 특정 범위내로 제한되어야 할 때
      - 값의 분포가 균등하지 않을때
      - 딥러닝 모델에서 주로 사용
      - 해석이 쉽다
      - 값의 분포를 유지하며 스케일링
      - 이상치에 민감
```
  - StandardScaler
      - Balance
  - RobustScaler
      - Age
      - CreditScore
  - MinMaxScaler
      - Tenure
      - EstimatedSalary

- 로그변환
  - 시각화 후 그래프 대체로 정규분포
  - 따라서 로그변환 X




* 시각화 해석
![image](https://github.com/user-attachments/assets/51fabf79-f86d-4c35-b3fb-073ef23ef924)
# 은행데이터이고 나름의 다양하고 많은 사람들이 사용하는 데이터이다보니 정규분포의 형태가 나름 다 나타난다
# Exited 0이 1보다 많음 거의 8:2
# CreditScore -> low / normal / high, 나름의 정규분포
# France가 많음 거의 6:2:2 :
# Male : Female = 6:4
# 20대 - 30대 - 40대 - 50대  : 이렇게 잘랐다 데이터의 크기 -> 20대 50대가 가장 적어보이고 , 30대의 숫자가 압도적으로 많았다, 그 뒤로 40대 : 일단 저희 생각 20대는 금융상품을 구매하는 정도가 적을 것
# 경제활동을 활발히 하는 나이, 제태크를 시작하는 나이가 30대 이상부터

* 파생변수
- 저희가 생각했을 때 영향을 많이 끼칠 것 같은 것
    - 정수 : CreditScore(신용점수), Age(나이), Tenure(기간), EstimatedSalary(연봉)
    - 소희님 : NumOfProducts(상품수), HasCrCard(카드보유여부),  CreditScore(신용점수), Age(나이), Tenure(기간)

        - 30대의 수가 많다, 나이가 높을수록 연봉이 올라가고 잔고가 많아질텐데
        - 이 은행이 이미 젋은 사람을 대상으로 한 상품을 많이 보유하고 있지 않을까?
        - 그러면 자연적으로 나이가 상관이 없지 않을까?
        - Age가 상관이 없다면 이런 생각까지 갈 수 있을 것 같다.

- 저희가 생각했을 때 영향을 많이 끼칠 것 같지않은것
    - id, CustomerId, Surname, Balance(잔고), Geography(국가), HasCrCard(카드보유여부), IsActiveMember(활성 회원 여부 - 휴면계정)

- 영향 많이 끼칠 것 같은것에서 만들기
- 신용점수(CreditScore), 나이(Age), 기간(Tenure)
- 연봉(EstimatedSalary), 상품수(NumOfProducts), 카드보유여부(HasCrCard)
- Geography : 프랑스의 수가 훨씬 많은게 분석 대상 은행이 프랑스 소유가 아닌가? -> 그러면 스페인이랑 독일이 이탈율이 영향을 받지 않을까
    - 나이대 별 신용점수(CreditScore By Age)
    - 연령 별 기간 : Tenure By Age
    - 참여도 : NumOfProducts(1,2,3,4) + HasCrCard(0,1) + IsActveMember(0,1) -> 1, 2, 3, 4, 5, 6 : 낮을수록 적은 참여도
    - EstimatedSalary 별 (CreditScore * HasCrCard)
    -


- 영향 많이 끼칠 것 같은 것 + 끼칠 것 같지않은 것
- Id, CustomerId, Surname
- 정수 : Geography,HasCrCard, IsActiveMember
- 소희님 : Balance
    - 잔고 별 신용점수 : CreditScore By Balance | Balance_group
    - 연령 별 잔고 : Balance By Age
    - 잔고 별 상품 수 : NumOfProducts By Balance | Balance_group
