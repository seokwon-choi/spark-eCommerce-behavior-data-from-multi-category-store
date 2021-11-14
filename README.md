# spark-eCommerce-behavior-data-from-multi-category-store


### 사용 데이터 링크 : https://www.kaggle.com/mkechinov/ecommerce-behavior-data-from-multi-category-store
###

## 1. 해당 전체 기간에서, KST 기준으로 active user 수가 제일 큰 날짜를 구하세요
### 문제 이해 : 날짜 별 user_id의 개수가 가장 큰 날짜를 구한다. 이때 같은 날짜에 중복된 user_id는 1개만 count한다. data_format은 KST기준으로 수행됨
#### [주석]
#### nov = csv파일 불러오기
#### df_first = 날짜, 유저 추출
#### df_g = 날짜로 group화, 유저 중복 제거 카운트
#### df_d = count기준 내림차순 정렬
#### df_last = count가 제일 큰것 하나만 남기기
#### answer = 결과(active user 수가 제일 큰 날짜)
###

## 2. 1의 날짜에서, 세션이 가장 긴 사용자 10명에 대해 "user_id, session_id, 세션시간"를 구하세요
### 문제 이해 : 세션은 한번 접속에 있는 시간으로 정의, 같은 user_id일 경우 user_session이 같으면 계속 접속 중을 의미함, 세션시간은 한개의 세션의 시작시간과 끝나는 시간의 차이
#### [주석]
#### df_s = 날짜, 유저, 세션, 시간 select
#### df_fil = 1번 과제 날짜(2019-11-17)만 추출
#### df_ma_mi = 유저와 세션을 기준으로 group화 하고 같은 group에서 최대 시간과 최소 시간을 추출
#### df_sts = 최대 시간과 최소 시간의 차이를 초(sec)단위로 구함, 이 시간이 세션 시간이 됨
#### df_so = 세션 시간을 기준으로 내림차순 정렬
#### df_li = 유저, 세션, 세션 시간을 10개만 추출
###


## 3. 1의 날짜의 15분 단위로 active user 수를 구하세요
### 문제 이해 : 00:00:00부터 23:59:59 까지 15분 단위로 각 단위에서의 유저 수를 중복 제거하고 구한다. 구간은 start_time이상 finish_time이하이다.
#### [주석] 
#### df_th = 날짜, 유저, 시간 추출
#### df_thf = 1번 과제 날짜(2019-11-17)만 추출
#### df_hm = '시', '분'을 추출하고 '분'을 15로 나눈 몫(소수점 내림)*15를 추출한다. '분'을 15로 나눈 몫(소수점 내림)*15는 15분 단위 구간의 시작 시간(분)이 된다.
#### df_temp = '시', '분'을 15로 나눈 몫(소수점 내림)*15로 group화 하면 15분 단위로 나눌 수 있다. 이때 유저 수를 중복 제거하고 count한다.
#### df_tso = '시'단위로 오름차순 정렬 후 '시'가 같을 때  "'분'을 15로 나눈 몫(소수점 내림)*15"단위로 오름차순 정렬 한다.
#### df_tem = "'분'을 추출하고 '분'을 15로 나눈 몫(소수점 내림)*15"+14를 구한다. 이것은 15분 단위 구간의 끝나는 시간(분)이 된다.
#### df_check = 시작 시간은 시:분:00, 끝나는 시간은 시:분:59 형식으로 만든다
#### df_new = 시작 시간과 끝나는 시간을 timestamp 형식으로 바꿔준다
#### df_ans = 시작 시간과 끝나는 시간 중 시간값만 추출하고 유저 수를 select
###

## 4. 1의 날짜에서 view → cart → purchase 이벤트 진행에 따른 funnel 수치를 구하세요
### 문제 이해 : 제품을 구매하는 과정을 제품을 보고(view) 장바구니에 담고(cart) 장바구니에 담은 제품만 구매(purchase)한다고 이해, view → cart의 funnel은 (cart 수 / view 수)*100 [단위:%], cart → purchase의 funnel은 (purchase 수 / cart 수)*100 [단위:%]로 이해
#### [주석]
#### df_fo = 날짜, 이벤트 타입(view, cart, purchase), 유저 추출
#### df_fofi = 1번 과제 날짜(2019-11-17)만 추출
#### df_fogr = 이벤트 타입을 기준으로 group화, 이벤트 타입 개수 카운트
#### df_fote = 이벤트 타입을 하나의 group으로 묶을 수 있도록 임시 열(temp_group)을 같은 값 '1'을 갖도록 생성
#### df_fopi = temp_group로 group화를 하면서 이벤트 타입이 column이 될 수 있도록 pivot 사용, 값은 '각 이벤트 타입 개수' 이다.
#### df_fofu =  view → cart의 funnel은 (cart 수 / view 수)x100 [단위:%], cart → purchase의 funnel은 (purchase 수 / cart 수)x100 [단위:%], 소수 3번째 자리에서 반올림
