
# [A Grammar of Graphics for Python — plotnine](http://plotnine.readthedocs.io/en/stable/index.html)
* [tutorial](http://plotnine.readthedocs.io/en/stable/tutorials.html)
* [ggplot2-cheatsheet](https://www.rstudio.com/wp-content/uploads/2015/03/ggplot2-cheatsheet.pdf)


```python
!pip install -q 'plotnine[all]'
```


```python
!pip show plotnine
```

    Name: plotnine
    Version: 0.3.0
    Summary: A grammar of graphics for python
    Home-page: https://github.com/has2k1/plotnine
    Author: Hassan Kibirige
    Author-email: has2k1@gmail.com
    License: GPL-2
    Location: /usr/local/lib/python3.6/dist-packages
    Requires: mizani, numpy, pandas, matplotlib, scipy, statsmodels, patsy, six
    Required-by: 



```python
import pandas as pd
import numpy as np
print(pd.__version__)
print(np.__version__)
```

    0.22.0
    1.14.5
    


```python
# 불필요한 warnings을 찍지 않기 위해 import 해왔습니다. 
import warnings
warnings.filterwarnings('ignore')

%config InlineBackend.figure_format = 'retina'

!apt -qq -y install fonts-nanum > /dev/null
import matplotlib.font_manager as fm
fontpath = '/usr/share/fonts/truetype/nanum/NanumBarunGothic.ttf'
font = fm.FontProperties(fname=fontpath, size=9)

from plotnine import *
import plotnine
```

    
    WARNING: apt does not have a stable CLI interface. Use with caution in scripts.
    


    /usr/local/lib/python3.6/dist-packages/statsmodels/compat/pandas.py:56: FutureWarning: The pandas.core.datetools module is deprecated and will be removed in a future version. Please use the pandas.tseries module instead.
      from pandas.core import datetools
    


```python
# 크롤링해 온 국민청원 데이터를 판다스를 통해 읽어온다.
df = pd.read_csv('https://s3.ap-northeast-2.amazonaws.com/data10902/petition/petition.csv', 
                 parse_dates=['start', 'end'])   
# parse할 때 dates로 해서 start, end 데이터타입이 datetime

# 데이터의 크기가 어느정도인지 본다.
df.shape
```




    (211690, 8)




```python
# 전체 데이터로 보면 너무 느리기 때문에 본인의 관심사에 맞는 데이터를 가져옵니다.
#youth_employment = df.loc[(df.title.str.contains('청년|취업', regex=True)) & \
#           (df.content.str.contains('청년|취업', regex=True))] 
#youth_employment.shape

wage = df.loc[(df.title.str.contains('최저임금|최저시급', regex=True)) & \
           (df.content.str.contains('최저임금|최저시급', regex=True))] 
wage.shape
```




    (1378, 8)




```python
#youth_employment.head(3)
wage.head(3)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>article_id</th>
      <th>start</th>
      <th>end</th>
      <th>answered</th>
      <th>votes</th>
      <th>category</th>
      <th>title</th>
      <th>content</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>446</th>
      <td>474</td>
      <td>2017-08-24</td>
      <td>2017-08-31</td>
      <td>0</td>
      <td>1</td>
      <td>인권/성평등</td>
      <td>징병군인 최저임금 보장해주세요</td>
      <td>징병군인 노동시간에 대한 최저임금 보장해주시고 나라가 일 시켰으면 일 시킨거에 대한...</td>
    </tr>
    <tr>
      <th>592</th>
      <td>620</td>
      <td>2017-08-26</td>
      <td>2017-09-02</td>
      <td>0</td>
      <td>19</td>
      <td>육아/교육</td>
      <td>어린이집 보육료 인상시기 최저임금 인상과 동일하게 적용</td>
      <td>어린이집 보육료 인상시기 문제점 및 개선(안)\n❒ 기본사항\n1) 어린이집 보육료...</td>
    </tr>
    <tr>
      <th>664</th>
      <td>692</td>
      <td>2017-08-28</td>
      <td>2017-10-27</td>
      <td>0</td>
      <td>2</td>
      <td>일자리</td>
      <td>최저임금으로 인해 실직 위기에 놓인 보육교사들..  속상합니다...</td>
      <td>안녕하세요. 저는 현재 가정 어린이집 교사입니다.\n아이들을 사랑하는 마음 하나로 ...</td>
    </tr>
  </tbody>
</table>
</div>




```python
#youth_employment.tail(3)
wage.tail(3)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>article_id</th>
      <th>start</th>
      <th>end</th>
      <th>answered</th>
      <th>votes</th>
      <th>category</th>
      <th>title</th>
      <th>content</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>211529</th>
      <td>273168</td>
      <td>2018-06-17</td>
      <td>2018-07-17</td>
      <td>0</td>
      <td>4</td>
      <td>일자리</td>
      <td>최저임금을 다시 낮춰 주세요</td>
      <td>지금 최저임금이 올라가서 소상공인들은 경기가 좋지 않아 장사는 잘 되지 않는데 임금...</td>
    </tr>
    <tr>
      <th>211549</th>
      <td>273195</td>
      <td>2018-06-17</td>
      <td>2018-07-17</td>
      <td>0</td>
      <td>41</td>
      <td>정치개혁</td>
      <td>국회의원 임금을 최저임금으로 지급해주세요</td>
      <td>국회의원 임금을 최저시급으로 지급하면 최저임금 문제 저절로 해결됩니다.</td>
    </tr>
    <tr>
      <th>211607</th>
      <td>273262</td>
      <td>2018-06-17</td>
      <td>2018-07-17</td>
      <td>0</td>
      <td>0</td>
      <td>일자리</td>
      <td>최저임금과 부동산, 경제에 대한 보완책</td>
      <td>미국에는 이런 말이 있습니다. 민주당은 경제, 공화당은 안보. 사실 그 두가지는 정...</td>
    </tr>
  </tbody>
</table>
</div>




```python
#youth_employment.info()
wage.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 1327 entries, 125 to 211360
    Data columns (total 8 columns):
    article_id    1327 non-null int64
    start         1327 non-null datetime64[ns]
    end           1327 non-null datetime64[ns]
    answered      1327 non-null int64
    votes         1327 non-null int64
    category      1327 non-null object
    title         1327 non-null object
    content       1327 non-null object
    dtypes: datetime64[ns](2), int64(3), object(3)
    memory usage: 93.3+ KB
    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 1378 entries, 446 to 211607
    Data columns (total 8 columns):
    article_id    1378 non-null int64
    start         1378 non-null datetime64[ns]
    end           1378 non-null datetime64[ns]
    answered      1378 non-null int64
    votes         1378 non-null int64
    category      1378 non-null object
    title         1378 non-null object
    content       1378 non-null object
    dtypes: datetime64[ns](2), int64(3), object(3)
    memory usage: 96.9+ KB
    


```python
#youth_employment.describe()
wage.describe()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>article_id</th>
      <th>answered</th>
      <th>votes</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>1378.000000</td>
      <td>1378.000000</td>
      <td>1378.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>161358.642961</td>
      <td>0.000726</td>
      <td>273.869376</td>
    </tr>
    <tr>
      <th>std</th>
      <td>75892.534715</td>
      <td>0.026939</td>
      <td>7556.058834</td>
    </tr>
    <tr>
      <th>min</th>
      <td>474.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>96983.000000</td>
      <td>0.000000</td>
      <td>2.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>145984.500000</td>
      <td>0.000000</td>
      <td>5.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>244125.500000</td>
      <td>0.000000</td>
      <td>13.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>273262.000000</td>
      <td>1.000000</td>
      <td>277674.000000</td>
    </tr>
  </tbody>
</table>
</div>




```python
#youth_employment.describe(include=np.object)
wage.describe(include=np.object)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>category</th>
      <th>title</th>
      <th>content</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>1378</td>
      <td>1378</td>
      <td>1378</td>
    </tr>
    <tr>
      <th>unique</th>
      <td>16</td>
      <td>1267</td>
      <td>1347</td>
    </tr>
    <tr>
      <th>top</th>
      <td>일자리</td>
      <td>최저임금</td>
      <td>여성예산 34조원 중 30%만 할당해도 군인들이 최저임금을 받을 수 있습니다. 이제...</td>
    </tr>
    <tr>
      <th>freq</th>
      <td>593</td>
      <td>33</td>
      <td>5</td>
    </tr>
  </tbody>
</table>
</div>



## 시계열 데이터 보기


```python
import warnings
warnings.filterwarnings('ignore')

wage['start_year'] = wage['start'].dt.year
wage['start_month'] = wage['start'].dt.month
wage['start_day'] = wage['start'].dt.day
wage['start_hour'] = wage['start'].dt.hour
wage['start_dow'] = wage['start'].dt.dayofweek
wage['start_wdn'] = wage['start'].dt.weekday_name

# 요일을 좀 더 간단하게 표현해 봅니다.
days = {0:'월',1:'화',2:'수',3:'목',4:'금',5:'토',6:'일'}
wage['start_dayofweek'] = wage['start_dow'].apply(lambda x: days[x])
wage.shape
```




    (1378, 15)




```python
# 월별 데이터 보기
wage['start_month'].value_counts()
```




    1     313
    5     281
    2     237
    6     179
    12    114
    3      96
    4      81
    11     40
    9      18
    10     10
    8       9
    Name: start_month, dtype: int64




```python
# 요일별 데이터 보기
wage['start_dayofweek'].value_counts()
```




    화    277
    수    239
    월    238
    목    190
    금    189
    토    130
    일    115
    Name: start_dayofweek, dtype: int64




```python
# 요일별 데이터 보기
# 육아, 보육과 관련된 청원을 내는 사람들은 보통 수요일에 청원을 많이 내고 주말에는 적게 내는 것을 알수 있습니다.
wage['start_wdn'].value_counts()
```




    Tuesday      277
    Wednesday    239
    Monday       238
    Thursday     190
    Friday       189
    Saturday     130
    Sunday       115
    Name: start_wdn, dtype: int64



## 한글폰트 사용하기
* 한글이 깨져보이는 것을 해결하기 위해 한글폰트를 사용해야 합니다.
* 여기에서는 나눔바른고딕을 사용하도록 합니다.
    * 이때 폰트가 로컬 컴퓨터에 설치되어 있어야해요.
    * 나눔고딕은 무료로 사용할 수 있는 폰트입니다.
    * 참고 : [네이버 나눔글꼴 라이선스](https://help.naver.com/support/contents/contents.nhn?serviceNo=1074&categoryNo=3497)
* 한글을 사용하기 위해서는 ggplot에서 theme에 폰트를 지정해 주면됩니다.
* 아래의 문서를 참고하면 **element_text**와 관련된 옵션을 볼 수 있습니다.
* 참고 : [plotnine.themes.element_text — plotnine 0.3.0 documentation](http://plotnine.readthedocs.io/en/stable/generated/plotnine.themes.element_text.html)


```python
# 카테고리별 청원 수
(ggplot(wage)
 + aes('category')
 + geom_bar(fill='green')
 + ggtitle('카테고리별 청원 수')
 + theme(text=element_text(fontproperties=font),
        axis_text_x=element_text(rotation=60))
)
```


![png](output_18_0.png)





    <ggplot: (-9223363294189066380)>




```python
# 카테고리별 투표수
(ggplot(wage)
 + aes(x='category', y='votes')
 + geom_col(fill='skyblue')
 + ggtitle('카테고리별 투표수')
 + theme(text=element_text(fontproperties=font),
        axis_text_x=element_text(rotation=70))
)
```


![png](output_19_0.png)





    <ggplot: (-9223363294191385642)>




```python
# coord_flip을 사용해서 x축과 y축을 바꿔본다.
(ggplot(wage)
 + aes(x='category', y='votes')
 + geom_col(fill='skyblue')
 + ggtitle('카테고리별 투표수')
 + coord_flip()
 + theme(text=element_text(fontproperties=font))
)
```


![png](output_20_0.png)





    <ggplot: (8742661767979)>




```python
(ggplot(wage) 
 + aes(x='category', y='votes', fill='answered')
 + geom_point()
 + ggtitle('카테고리별 답변 대상 청원')
 + theme(text=element_text(fontproperties=font),
        axis_text_x = element_text(rotation=70))
)
```


![png](output_21_0.png)





    <ggplot: (-9223363294193245415)>




```python
(ggplot(wage, aes(x='category', y='votes', fill='answered'))
 + geom_col()
 + ggtitle('카테고리별 답변 대상 청원')
 + theme(text=element_text(fontproperties=font),
        axis_text_x=element_text(rotation=70))
)
```


![png](output_22_0.png)





    <ggplot: (-9223363294193099411)>




```python
# 연도별 청원수는 크게 의미가 없다.
(ggplot(wage)
 + aes('start_year')
 + geom_bar(fill='green')
 + labs(y='청원수', x='연도', title='연도별 청원수')
 + theme(text=element_text(fontproperties=font))
)
```


![png](output_23_0.png)





    <ggplot: (-9223363294193315917)>




```python
(ggplot(wage)
 + aes('start_month')
 + geom_bar(fill='green')
 + labs(y='청원수', x='월', title='월별 청원수')
 + theme(text=element_text(fontproperties=font))
)
```


![png](output_24_0.png)





    <ggplot: (8742661301077)>




```python
# 9월에 청원이 특히 많은데 상위 3개 날짜만 뽑아보자
wage_1 = wage.loc[wage['start_month'] == 1]
wage_1['start_day'].value_counts()[:3]
```




    16    24
    24    18
    18    18
    Name: start_day, dtype: int64




```python
wage_1_sample = wage_1.loc[(wage_1['start_day'] == 16) | (wage_1['start_day'] == 24)]
wage_1_sample[['title', 'content', 'votes']].sort_values(by='votes', ascending=False)[:20]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>title</th>
      <th>content</th>
      <th>votes</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>86131</th>
      <td>국회의원 월급(1,166만원/연봉 1억4천), 최저임금으로!(수정)</td>
      <td>나라, 국민을 대표하고 국가를 위해서 헌신하는 마음을 갖고 일해야하는 사람이 국회의...</td>
      <td>81</td>
    </tr>
    <tr>
      <th>97150</th>
      <td>국회의원 최저임금 월급 지급</td>
      <td>안녕하세요 대한민국에서 살고 있는 20대 대학생입니다.  전 얼마전 새로운 알바를 ...</td>
      <td>74</td>
    </tr>
    <tr>
      <th>86016</th>
      <td>국회의원 월급, 최저임금으로 맞춰주십시오.</td>
      <td>나라, 국민을 위해서 봉사 헌신하는 마음을 갖고 일해야하는 사람이 국회의원이라 생각...</td>
      <td>49</td>
    </tr>
    <tr>
      <th>97212</th>
      <td>최저임금 인상후 보육교사 근무시간 마구잡이식 줄이기 막아주세요!</td>
      <td>가정어린이집 보육교사 입니다.\n최저임금 인상 후 근무시간을 1시간 줄이고, 1시간...</td>
      <td>29</td>
    </tr>
    <tr>
      <th>96312</th>
      <td>국회의원 최저임금제 및 특별법 제정</td>
      <td>☆☆☆ 국회의원 최저임금제 및 특별법 제정 ☆☆☆\n지금 국민에게 가장 큰 적폐, ...</td>
      <td>25</td>
    </tr>
    <tr>
      <th>96310</th>
      <td>국회의원 최저임금 및 국회의원 특별법</td>
      <td>☆☆☆ 국회의원 최저임금제 및 특별법 제정 ☆☆☆\n지금 국민에게 가장 큰 적폐, ...</td>
      <td>19</td>
    </tr>
    <tr>
      <th>87070</th>
      <td>최저임금 문제, 자영업자들은 어떻게 먹고 살라는거 입니까</td>
      <td>안녕하세요\n우선 자영업을 운영하는 사람 입니다.\n최저임금 상승은 해볼 필요성은 ...</td>
      <td>10</td>
    </tr>
    <tr>
      <th>96090</th>
      <td>불법 체류자로 인한 문제인 정부의 최저임금 오류 (평창의 잘못된 비자정책)</td>
      <td>문제인 정부는 좋은 일자리 창출과 좋은 환경을 만들고자 최저임금을 대폭 인상했습니다...</td>
      <td>9</td>
    </tr>
    <tr>
      <th>85982</th>
      <td>한부모 가정 2인가구 소득인정액이 최저임금보다 낮다..</td>
      <td>2018년 최저임금 7530원..\n8시간 근로기준으로 따지면 월세전 수령액 157...</td>
      <td>8</td>
    </tr>
    <tr>
      <th>96387</th>
      <td>용역업체소속 최저임금 파견근로자의 근로 실태 및 불법등을 철저히 조사 처벌 해주세요</td>
      <td>용역업체소속 최저임금 파견근로자(마트캐셔,경비,청소원,전화상담원등등)에 대한 정부의...</td>
      <td>8</td>
    </tr>
    <tr>
      <th>87222</th>
      <td>최저임금인상으로 인한 문제</td>
      <td>안녕하세요 저는 백화점에서 매니저를 하시는 부모를둔 예비고1인 학생입니다\n오늘 학...</td>
      <td>8</td>
    </tr>
    <tr>
      <th>86232</th>
      <td>소상공인은 최저임금을 줄 수 있는가?</td>
      <td>저는 현재 편의점 점주으로 일하고 있습니다.\n3교대를 기준으로 주휴수당을 포함,점...</td>
      <td>7</td>
    </tr>
    <tr>
      <th>85845</th>
      <td>남편은 최저임금을 위반했습니다..</td>
      <td>남편은 5년전부터 편의점을 경영하고 있습니다.. 다니던 회사를 퇴직후..  재취업에...</td>
      <td>6</td>
    </tr>
    <tr>
      <th>96323</th>
      <td>정부 부처 장관들은 국가 봉사직 급여는 최저임금으로. . . . .</td>
      <td>장관님들 왜 이렇게 장관을 하고싶은가 보았더니 급여가 엄청나게 나오더라구요\n국가 ...</td>
      <td>6</td>
    </tr>
    <tr>
      <th>86168</th>
      <td>최저시급 올라도 그대로인 급여.</td>
      <td>문대통령님 안녕하십니까.\n지금 제조업에 월급제로 일하고있습니다.\n최저시급이 올라...</td>
      <td>6</td>
    </tr>
    <tr>
      <th>86445</th>
      <td>물가만 올린 최저임금</td>
      <td>지금 정부에서 강력하게 추진중인 최저임금 인상의 피해자 입니다. 설익은 정책을 무리...</td>
      <td>6</td>
    </tr>
    <tr>
      <th>97217</th>
      <td>최저임금인상보다 용역업체 수수료 공개 법안 발의!</td>
      <td>저는 현재 은행에서 청원경찰로 근무하고 있는 청년입니다.\n용역업체와 무기계약을 체...</td>
      <td>6</td>
    </tr>
    <tr>
      <th>96274</th>
      <td>최저임금지키지않는 회사 실명제라 신고못하는직원</td>
      <td>대통령님께서 좋은 취지로 만든 최저임금 인상안!!\n서민들과 아르바이트생들에게는 많...</td>
      <td>5</td>
    </tr>
    <tr>
      <th>96106</th>
      <td>중소기업 폐업을결정!!!!최저임금때문에 난리나네요....</td>
      <td>한국노총이 노조원들이 일하는 193개 업체를 조사한 결과 136곳에서 휴일 근로를 ...</td>
      <td>5</td>
    </tr>
    <tr>
      <th>85791</th>
      <td>최저임금 인상 자제해주세요</td>
      <td>최저임금 인상 자제해주세요. 영세상인 다 무너집니다.\n돈이라는 것은 생성되는 것이...</td>
      <td>5</td>
    </tr>
  </tbody>
</table>
</div>




```python
(ggplot(child)
 + aes('start_day')
 + geom_bar(fill='green')
 + labs(y='청원수', x='일', title='일자별 청원수')
 + theme(text=element_text(fontproperties=font))
)
```


![png](output_27_0.png)





    <ggplot: (-9223363243667584070)>




```python
# 그래프를 그리다가 컬럼명이 떠오르지 않으면 위로 올라가지 않고 바로 컬럼명을 찍어 봅니다.
child.columns
```




    Index(['article_id', 'start', 'end', 'answered', 'votes', 'category', 'title',
           'content', 'start_year', 'start_month', 'start_day', 'start_hour',
           'start_dow', 'start_wdn', 'start_dayofweek'],
          dtype='object')




```python
(ggplot(child)
 + aes('start_dayofweek')
 + geom_bar(fill='green')
 + labs(y='청원수', x='요일', title='요일별 청원수')
 + theme(text=element_text(fontproperties=font))
)
```


![png](output_29_0.png)





    <ggplot: (8793186791039)>




```python
# 박스플롯을 그려볼까요?
(ggplot(child, aes(x='start_dayofweek', y='votes', fill='category'))
 + geom_boxplot()
 + labs(y='투표', x='요일', title='요일별 청원')
 + theme(text=element_text(fontproperties=font))
)
```


![png](output_30_0.png)





    <ggplot: (8793186753088)>




```python
# 그래프를 좀 더 자세하게 보기 위해 투표수가 특정 건 이하인 데이터만 모아본다.
child_votes_25000 = child.loc[child['votes'] < 1000]

(ggplot(child_votes_25000, aes(x='start_dayofweek', y='votes', fill='category'))
 + geom_boxplot()
 + labs(y='투표', x='요일', title='요일별 청원')
 + theme(text=element_text(fontproperties=font))
)
```


![png](output_31_0.png)





    <ggplot: (-9223363243667989297)>




```python

```
