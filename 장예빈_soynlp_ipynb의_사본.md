
# soynlp로 자연어처리 시작하기
* https://github.com/lovit/soynlp


```
!pip install -q 'soynlp[all]'
```

    [33m  soynlp 0.0.46 does not provide the extra 'all'[0m



```
!pip show soynlp
```

    Name: soynlp
    Version: 0.0.46
    Summary: Unsupervised Korean Natural Language Processing Toolkits
    Home-page: https://github.com/lovit/soynlp
    Author: Lovit
    Author-email: soy.lovit@gmail.com
    License: UNKNOWN
    Location: /usr/local/lib/python3.6/dist-packages
    Requires: psutil, numpy
    Required-by: 



```
import pandas as pd
import numpy as np
import re
```


```
df = pd.read_csv('https://s3.ap-northeast-2.amazonaws.com/data10902/petition/petition.csv', 
                 parse_dates=['start', 'end'])
df.shape
```




    (211690, 8)



## 자신의 관심사에 맞는 단어로 데이터를 가져옵니다.


```
p = r'.*(돌봄|육아|초등|보육).*'
care = df[df['title'].str.match(p) |
           df['content'].str.match(p, flags=re.MULTILINE)]
care.shape
```




    (8382, 8)




```
care.head()
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
      <th>3</th>
      <td>24</td>
      <td>2017-08-19</td>
      <td>2017-08-26</td>
      <td>0</td>
      <td>53</td>
      <td>일자리</td>
      <td>공공기관 무조건적인 정규직전환을 반대합니다.</td>
      <td>현정부에서 정규직 일자리를 늘리는 것에 찬성합니다. 그런데 공공기관 비정규직들은 인...</td>
    </tr>
    <tr>
      <th>15</th>
      <td>36</td>
      <td>2017-08-19</td>
      <td>2017-08-26</td>
      <td>0</td>
      <td>1</td>
      <td>인권/성평등</td>
      <td>한국채식인구 100만명. 학교 급식 및 군대에서 현미채식 선택권을 보장해주십시오!</td>
      <td>문재인 대통령님과 각 정부 인사분들께 마음속 깊이 존경과 감사를 표합니다. 대한민국...</td>
    </tr>
    <tr>
      <th>23</th>
      <td>45</td>
      <td>2017-08-19</td>
      <td>2017-11-17</td>
      <td>0</td>
      <td>0</td>
      <td>육아/교육</td>
      <td>초등학교 교사 임용 시험 관련 해결방안</td>
      <td>초등학교 교사 임용 시험을 수능 시험 처럼 전국 단위로 실시하고난 후에\n1지망 2...</td>
    </tr>
    <tr>
      <th>27</th>
      <td>49</td>
      <td>2017-08-19</td>
      <td>2017-11-17</td>
      <td>0</td>
      <td>27</td>
      <td>일자리</td>
      <td>공정한사회 절차가 바른사회가 되기를 원합니다.</td>
      <td>문재인대통령과 교육부장관님!\n교사 정규직은 임용고시라는 제도를 통해 정교사가 될 ...</td>
    </tr>
    <tr>
      <th>33</th>
      <td>56</td>
      <td>2017-08-19</td>
      <td>2017-11-17</td>
      <td>0</td>
      <td>5</td>
      <td>보건복지</td>
      <td>장애아의 상태에 따른 장애아돌봄도우미 정책의 한계에 관하여...읽어주셨으면 하고 글...</td>
      <td>안녕하세요. 저는 그냥 평범한 대학생이며, 고등학생인 자폐 1급 남동생을 둔 누나입...</td>
    </tr>
  </tbody>
</table>
</div>




```
care.tail()
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
      <th>211552</th>
      <td>273198</td>
      <td>2018-06-17</td>
      <td>2018-07-17</td>
      <td>0</td>
      <td>1</td>
      <td>육아/교육</td>
      <td>초등학교 저학년(1학년~) 교과서 내용을 타인의도움이 없이도 공부가 가능도록 만들어...</td>
      <td>이제 입학을 해서 초등학교에서 ㄱㄴ을 배우는 아이들은 같은학년 특히수학이나 다른 책...</td>
    </tr>
    <tr>
      <th>211575</th>
      <td>273222</td>
      <td>2018-06-17</td>
      <td>2018-07-17</td>
      <td>0</td>
      <td>47</td>
      <td>육아/교육</td>
      <td>보육교사가 하는 일이 뭔지 알고 계신가요</td>
      <td>안녕하세요 저는 보육교사로 일하고 있는 한 여성입니다.\n보육교사로 일하면서 힘들어...</td>
    </tr>
    <tr>
      <th>211599</th>
      <td>273253</td>
      <td>2018-06-17</td>
      <td>2018-07-17</td>
      <td>0</td>
      <td>8</td>
      <td>육아/교육</td>
      <td>자유학기제 전면 수정 요청드립니다</td>
      <td>지금 중학교에서 시행하고있는 자유학기제에 대해서  더  나은  방안을 이야기하고자 ...</td>
    </tr>
    <tr>
      <th>211606</th>
      <td>273261</td>
      <td>2018-06-17</td>
      <td>2018-07-17</td>
      <td>0</td>
      <td>218</td>
      <td>보건복지</td>
      <td>현실에 맞는 보육료 지원 요청</td>
      <td>어린이집을 7시 30분부터 19시 30분까지 의무 보육을 하라 하십니다~ 교사 근무...</td>
    </tr>
    <tr>
      <th>211616</th>
      <td>273272</td>
      <td>2018-06-17</td>
      <td>2018-07-17</td>
      <td>0</td>
      <td>2</td>
      <td>육아/교육</td>
      <td>자녀장려금</td>
      <td>안녕하세요.쌍둥이 아들을 키우는 워킹맘입니다.\n맞벌이부부로 일을 하면서 자녀장려금...</td>
    </tr>
  </tbody>
</table>
</div>




```
from soynlp.tokenizer import RegexTokenizer, LTokenizer, MaxScoreTokenizer

tokenizer = RegexTokenizer()
tokenizer
```




    <soynlp.tokenizer._tokenizer.RegexTokenizer at 0x7ffa37544a20>




```
# 샘플로 보고 싶은 인덱스의 번호를 넣어주세요.
sample_index = 211049
```


```
sample_title = care['title'][sample_index]
sample_title
```




    '초중고 무상급식'




```
sample_content = care['content'][sample_index]
sample_content
```




    '전국이  초등중등  무상급식인데\\n대구경북은  올해부터 초등만 무상이고\\n중학교는  왜    유상인지요\\n나라에서  지원해주는건데\\n대구경북만 제외시킨이유는 먼가요\\n지역상관없이 무상급식지원해야하는거 아닌가요'



# 토큰화


```
tokened_title = tokenizer.tokenize(sample_title)
tokened_title
```




    ['초중고', '무상급식']




```
tokened_content = tokenizer.tokenize(sample_content)
tokened_content[:10]
```




    ['전국이', '초등중등', '무상급식인데', '\\', 'n', '대구경북은', '올해부터', '초등만', '무상이고', '\\']




```
print(len(tokened_title))
print(len(tokened_content))
```

    2
    28
    

# 텍스트 데이터 전처리 
* 개행문자 제거


```
def preprocessing(text):
    # 개행문자 제거
    text = re.sub('\\\\n', ' ', text)
    return text
```


```
# %time을 찍어주면 해당 코드를 실행할 때 걸리는 시간을 출력해 줍니다
%time sentences = care['content'].apply(preprocessing)
```

    CPU times: user 53.7 ms, sys: 119 µs, total: 53.8 ms
    Wall time: 57.2 ms
    


```
%time tokens = sentences.apply(tokenizer.tokenize)
tokens[:3]
```

    CPU times: user 15.3 s, sys: 243 ms, total: 15.5 s
    Wall time: 15.5 s
    




    3     [현정부에서, 정규직, 일자리를, 늘리는, 것에, 찬성합니다, ., 그런데, 공공기...
    15    [문재인, 대통령님과, 각, 정부, 인사분들께, 마음속, 깊이, 존경과, 감사를, ...
    23    [초등학교, 교사, 임용, 시험을, 수능, 시험, 처럼, 전국, 단위로, 실시하고난...
    Name: content, dtype: object




```
tokens[sample_index][:10]
```




    ['전국이', '초등중등', '무상급식인데', '대구경북은', '올해부터', '초등만', '무상이고', '중학교는', '왜', '유상인지요']




```
# 그래프에 retina display 적용
%config InlineBackend.figure_format = 'retina'

# 나눔고딕 설치
!apt -qq -y install fonts-nanum > /dev/null
import matplotlib.font_manager as fm
fontpath = '/usr/share/fonts/truetype/nanum/NanumBarunGothic.ttf'
font = fm.FontProperties(fname=fontpath, size=9)
```

    
    WARNING: apt does not have a stable CLI interface. Use with caution in scripts.
    



```
!pip install -q 'wordcloud[all]'
```

    [33m  wordcloud 1.4.1 does not provide the extra 'all'[0m



```
from wordcloud import WordCloud, STOPWORDS
import matplotlib.pyplot as plt
%matplotlib inline

def displayWordCloud(data = None, backgroundcolor = 'white', width=800, height=600 ):
    wordcloud = WordCloud(
                        font_path = fontpath,
                        stopwords = STOPWORDS, # 한국어는 해당이 없습니다. 한국어를 적용해 주려면 별도 처리가 필요합니다.
                        background_color = backgroundcolor, 
                         width = width, height = height).generate(data)
    plt.figure(figsize = (15 , 10))
    plt.imshow(wordcloud)
    plt.axis("off")
    plt.show() 
```


```
# 결과를 출력해 보면 불용어(STOPWORD)가 너무 많습니다.
%time displayWordCloud(' '.join(sentences))
```


![png](output_25_0.png)


    CPU times: user 19.4 s, sys: 928 ms, total: 20.3 s
    Wall time: 20.8 s
    


```
from soynlp.noun import LRNounExtractor
```


```
%%time
noun_extractor = LRNounExtractor(verbose=True)
noun_extractor.train(sentences)
nouns = noun_extractor.extract()
```

    used default noun predictor; Sejong corpus predictor
    used noun_predictor_sejong
    All 2398 r features was loaded
    scanning completed
    (L,R) has (108055, 53386) tokens
    building lr-graph completedCPU times: user 24 s, sys: 456 ms, total: 24.4 s
    Wall time: 24.5 s
    


```
# 추출된 명사를 찍어봅니다.
%time displayWordCloud(' '.join(nouns))
```


![png](output_28_0.png)


    CPU times: user 1.74 s, sys: 104 ms, total: 1.84 s
    Wall time: 1.85 s
    


```

```
