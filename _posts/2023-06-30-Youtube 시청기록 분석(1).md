# 프로젝트 개요

이 프로젝트는 저의 유튜브 시청 기록을 데이터로 활용하여 저에 대한 더 깊은 이해를 목표로 합니다. 유튜브는 저의 일상에서 중요한 역할을 하며, 저는 다양한 이유로 유튜브 동영상을 시청합니다. 이를 통해 제가 어떤 동영상을 선호하고, 어떤 주제에 관심을 가지는지 파악하고자 합니다.

일반적인 MBTI 검사는 질문에 주관적으로 대답하여 결과를 도출합니다. 하지만 이 프로젝트에서는 유튜브 동영상 시청 기록 및 검색 기록을 통해 보다 객관적인 정보를 얻고자 합니다.

본 프로젝트에서는 추후 데이터 분석을 위해 Google Takeout을 통해 제공되는 유튜브 시청 기록 데이터를 수집하고, 데이터 전처리하여 간단한 시각화를 하여 분석하는 것을 목표로 합니다.


```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import datetime as dt
import json
import urllib
```

### 시청기록 데이터


```python
view_history = pd.read_json(r'C:\Users\hyoye\Desktop\Github.io\Project Data\Youtube 데이터 분석\json데이터\시청 기록.json')
```


```python
print(view_history.shape)
view_history.head(10)
```

    (37000, 9)
    




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
      <th>header</th>
      <th>title</th>
      <th>titleUrl</th>
      <th>subtitles</th>
      <th>time</th>
      <th>products</th>
      <th>activityControls</th>
      <th>description</th>
      <th>details</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>YouTube</td>
      <td>What is an Edge-Induced Subgraph? | Graph Theo...</td>
      <td>https://www.youtube.com/watch?v=bk08JzEWeC4</td>
      <td>[{'name': 'Wrath of Math', 'url': 'https://www...</td>
      <td>2023-06-16T07:59:02.786Z</td>
      <td>[YouTube]</td>
      <td>[YouTube 시청 기록]</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>YouTube</td>
      <td>Vertex Covers and Vertex Covering Numbers | Gr...</td>
      <td>https://www.youtube.com/watch?v=1KkT7y8nxH0</td>
      <td>[{'name': 'Wrath of Math', 'url': 'https://www...</td>
      <td>2023-06-16T07:57:33.757Z</td>
      <td>[YouTube]</td>
      <td>[YouTube 시청 기록]</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>YouTube</td>
      <td>P vs. NP and the Computational Complexity Zoo ...</td>
      <td>https://www.youtube.com/watch?v=YX40hbAHx3s</td>
      <td>[{'name': 'hackerdashery', 'url': 'https://www...</td>
      <td>2023-06-16T07:56:58.579Z</td>
      <td>[YouTube]</td>
      <td>[YouTube 시청 기록]</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>YouTube</td>
      <td>[HR컨퍼런스 | 황성현 대표와 해답을 더하다] 정량화가 어려운 목표를 수치화하고,...</td>
      <td>https://www.youtube.com/watch?v=zsgds5ZGuqw</td>
      <td>[{'name': 'Lemonbase', 'url': 'https://www.you...</td>
      <td>2023-06-16T07:46:51.005Z</td>
      <td>[YouTube]</td>
      <td>[YouTube 시청 기록]</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>YouTube</td>
      <td>실제 일하면서 알게된 컨설팅 현실 / 연봉, 하는 일, 애로 사항 / 개발자의 삶과...</td>
      <td>https://www.youtube.com/watch?v=Nsz-DFOqWqU</td>
      <td>[{'name': '기술노트with 알렉', 'url': 'https://www.y...</td>
      <td>2023-06-16T02:47:37.045Z</td>
      <td>[YouTube]</td>
      <td>[YouTube 시청 기록]</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>5</th>
      <td>YouTube</td>
      <td>진로고민) IT 컨설팅 하려면 IT 개발 잘 알아야 할까요? 공부는 준비는 어떻게 ...</td>
      <td>https://www.youtube.com/watch?v=yXNzN57C4Vo</td>
      <td>[{'name': '기술노트with 알렉', 'url': 'https://www.y...</td>
      <td>2023-06-16T02:38:28.846Z</td>
      <td>[YouTube]</td>
      <td>[YouTube 시청 기록]</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>6</th>
      <td>YouTube</td>
      <td>[알터릭스와 함께 하는 태블로] 10월 온라인 무료 실습 세션 안내 (↓↓ 신청은 ...</td>
      <td>https://www.youtube.com/watch?v=nFlX4Rm1e-w</td>
      <td>[{'name': 'VizLab', 'url': 'https://www.youtub...</td>
      <td>2023-06-16T01:17:52.163Z</td>
      <td>[YouTube]</td>
      <td>[YouTube 시청 기록]</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>7</th>
      <td>YouTube</td>
      <td>태블로 베이직 시리즈 - 첫걸음 ① 데이터 연결하기 을(를) 시청했습니다.</td>
      <td>https://www.youtube.com/watch?v=ZC1l9Xba4vk</td>
      <td>[{'name': 'VizLab', 'url': 'https://www.youtub...</td>
      <td>2023-06-16T00:49:22.395Z</td>
      <td>[YouTube]</td>
      <td>[YouTube 시청 기록]</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>8</th>
      <td>YouTube</td>
      <td>태블로(Tableau) Full 기초 교육 영상 Part.1 태...</td>
      <td>https://www.youtube.com/watch?v=qT38CVgKIfw</td>
      <td>[{'name': 'PLANIT DATAV', 'url': 'https://www....</td>
      <td>2023-06-16T00:49:09.341Z</td>
      <td>[YouTube]</td>
      <td>[YouTube 시청 기록]</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>9</th>
      <td>YouTube</td>
      <td>(이코테 2021 강의 몰아보기) 1. 코딩 테스트 출제 경향 분석 및 파이썬 문법...</td>
      <td>https://www.youtube.com/watch?v=m-9pAwq1o3w</td>
      <td>[{'name': '동빈나', 'url': 'https://www.youtube.c...</td>
      <td>2023-06-16T00:30:18.664Z</td>
      <td>[YouTube]</td>
      <td>[YouTube 시청 기록]</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>



### 검색 기록 데이터


```python
search_history = pd.read_json(r'C:\Users\hyoye\Desktop\Github.io\Project Data\Youtube 데이터 분석\json데이터\검색 기록.json')
```


```python
print(search_history.shape)
search_history.head(10)
```

    (14025, 9)
    




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
      <th>header</th>
      <th>title</th>
      <th>titleUrl</th>
      <th>time</th>
      <th>products</th>
      <th>activityControls</th>
      <th>description</th>
      <th>details</th>
      <th>subtitles</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>YouTube</td>
      <td>https://www.youtube.com/watch?v=1KkT7y8nxH0 을(...</td>
      <td>https://www.youtube.com/results?search_query=h...</td>
      <td>2023-06-16T07:57:24.169Z</td>
      <td>[YouTube]</td>
      <td>[YouTube 검색 기록]</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>YouTube</td>
      <td>it 컨설턴트 을(를) 검색했습니다.</td>
      <td>https://www.youtube.com/results?search_query=i...</td>
      <td>2023-06-16T02:38:21.289Z</td>
      <td>[YouTube]</td>
      <td>[YouTube 검색 기록]</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>YouTube</td>
      <td>테블로 베이직 시리즈 을(를) 검색했습니다.</td>
      <td>https://www.youtube.com/results?search_query=%...</td>
      <td>2023-06-16T01:17:33.908Z</td>
      <td>[YouTube]</td>
      <td>[YouTube 검색 기록]</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>YouTube</td>
      <td>태블로  을(를) 검색했습니다.</td>
      <td>https://www.youtube.com/results?search_query=%...</td>
      <td>2023-06-16T00:49:07.302Z</td>
      <td>[YouTube]</td>
      <td>[YouTube 검색 기록]</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>YouTube</td>
      <td>동빈나 코딩테스트 을(를) 검색했습니다.</td>
      <td>https://www.youtube.com/results?search_query=%...</td>
      <td>2023-06-16T00:30:01.072Z</td>
      <td>[YouTube]</td>
      <td>[YouTube 검색 기록]</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>5</th>
      <td>YouTube</td>
      <td>주간 마케칭 을(를) 검색했습니다.</td>
      <td>https://www.youtube.com/results?search_query=%...</td>
      <td>2023-06-15T23:03:27.986Z</td>
      <td>[YouTube]</td>
      <td>[YouTube 검색 기록]</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>6</th>
      <td>YouTube</td>
      <td>ab태스트 을(를) 검색했습니다.</td>
      <td>https://www.youtube.com/results?search_query=a...</td>
      <td>2023-06-15T23:03:00.448Z</td>
      <td>[YouTube]</td>
      <td>[YouTube 검색 기록]</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>7</th>
      <td>YouTube</td>
      <td>그로스해킹 을(를) 검색했습니다.</td>
      <td>https://www.youtube.com/results?search_query=%...</td>
      <td>2023-06-15T23:02:45.972Z</td>
      <td>[YouTube]</td>
      <td>[YouTube 검색 기록]</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>8</th>
      <td>YouTube</td>
      <td>정량화 을(를) 검색했습니다.</td>
      <td>https://www.youtube.com/results?search_query=%...</td>
      <td>2023-06-15T22:52:46.258Z</td>
      <td>[YouTube]</td>
      <td>[YouTube 검색 기록]</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>9</th>
      <td>YouTube</td>
      <td>킬라그램 식보이 을(를) 검색했습니다.</td>
      <td>https://www.youtube.com/results?search_query=%...</td>
      <td>2023-06-15T16:36:13.274Z</td>
      <td>[YouTube]</td>
      <td>[YouTube 검색 기록]</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>



# 데이터 전처리

### 시청기록 데이터 전처리


```python
# 광고 제거 함수 (광고 X => detail = NaN)
def remove_ads(df):
  return df[df.details.isnull()]

# time -> date format
def date_format(x):
  yyyy_mm_dd = x.split('T')[0]
  yyyy = int(yyyy_mm_dd.split('-')[0])
  mm = int(yyyy_mm_dd.split('-')[1])
  dd = int(yyyy_mm_dd.split('-')[2])
  return dt.date(yyyy, mm, dd)

# Drop missing videos
def drop_missing_videos(df):
  return df[df['subtitles'].isnull() == False]

# Creator col
def creator_col(x):
  dictionary = x[0]
  return dictionary['name']

# 검색어 전처리
def title(x):
  word = x.split('(를)')[0]
  return word[:-2]

# 검색어 전처리
def keyword(x):
  word = x.split('(를)')[0]
  return word[:-2]
```


```python
def view_preprocessing(df):
  df = remove_ads(df)
  df = df[['title', 'subtitles', 'time', 'activityControls']]
  df['time'] = df['time'].apply(date_format)
  df = drop_missing_videos(df)
  df['subtitles'] = df['subtitles'].apply(creator_col)
  df.rename(columns = {'subtitles' : 'creator'}, inplace = True)
  df['activityControls'] = '시청'
  df['title'] = df['title'].apply(title)
  return df
```


```python
view_history = view_preprocessing(view_history)
```


```python
view_history.head(10)
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
      <th>creator</th>
      <th>time</th>
      <th>activityControls</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>What is an Edge-Induced Subgraph? | Graph Theory</td>
      <td>Wrath of Math</td>
      <td>2023-06-16</td>
      <td>시청</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Vertex Covers and Vertex Covering Numbers | Gr...</td>
      <td>Wrath of Math</td>
      <td>2023-06-16</td>
      <td>시청</td>
    </tr>
    <tr>
      <th>2</th>
      <td>P vs. NP and the Computational Complexity Zoo</td>
      <td>hackerdashery</td>
      <td>2023-06-16</td>
      <td>시청</td>
    </tr>
    <tr>
      <th>3</th>
      <td>[HR컨퍼런스 | 황성현 대표와 해답을 더하다] 정량화가 어려운 목표를 수치화하고,...</td>
      <td>Lemonbase</td>
      <td>2023-06-16</td>
      <td>시청</td>
    </tr>
    <tr>
      <th>4</th>
      <td>실제 일하면서 알게된 컨설팅 현실 / 연봉, 하는 일, 애로 사항 / 개발자의 삶과...</td>
      <td>기술노트with 알렉</td>
      <td>2023-06-16</td>
      <td>시청</td>
    </tr>
    <tr>
      <th>5</th>
      <td>진로고민) IT 컨설팅 하려면 IT 개발 잘 알아야 할까요? 공부는 준비는 어떻게 ...</td>
      <td>기술노트with 알렉</td>
      <td>2023-06-16</td>
      <td>시청</td>
    </tr>
    <tr>
      <th>6</th>
      <td>[알터릭스와 함께 하는 태블로] 10월 온라인 무료 실습 세션 안내 (↓↓ 신청은 ...</td>
      <td>VizLab</td>
      <td>2023-06-16</td>
      <td>시청</td>
    </tr>
    <tr>
      <th>7</th>
      <td>태블로 베이직 시리즈 - 첫걸음 ① 데이터 연결하기</td>
      <td>VizLab</td>
      <td>2023-06-16</td>
      <td>시청</td>
    </tr>
    <tr>
      <th>8</th>
      <td>태블로(Tableau) Full 기초 교육 영상 Part.1 태...</td>
      <td>PLANIT DATAV</td>
      <td>2023-06-16</td>
      <td>시청</td>
    </tr>
    <tr>
      <th>9</th>
      <td>(이코테 2021 강의 몰아보기) 1. 코딩 테스트 출제 경향 분석 및 파이썬 문법...</td>
      <td>동빈나</td>
      <td>2023-06-16</td>
      <td>시청</td>
    </tr>
  </tbody>
</table>
</div>



### 검색기록 데이터 전처리


```python
def search_preprocessing(df):
  df = remove_ads(df)
  df = df[['title', 'time']]
  df['time'] = df['time'].apply(date_format)
  df['title'] = df['title'].apply(keyword)
  df.rename(columns = {'title' : 'keyword'}, inplace = True)
  df['activityControls'] = '검색'
  return df
```


```python
search_history = search_preprocessing(search_history)
```


```python
search_history.head(10)
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
      <th>keyword</th>
      <th>time</th>
      <th>activityControls</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>https://www.youtube.com/watch?v=1KkT7y8nxH0</td>
      <td>2023-06-16</td>
      <td>검색</td>
    </tr>
    <tr>
      <th>1</th>
      <td>it 컨설턴트</td>
      <td>2023-06-16</td>
      <td>검색</td>
    </tr>
    <tr>
      <th>2</th>
      <td>테블로 베이직 시리즈</td>
      <td>2023-06-16</td>
      <td>검색</td>
    </tr>
    <tr>
      <th>3</th>
      <td>태블로</td>
      <td>2023-06-16</td>
      <td>검색</td>
    </tr>
    <tr>
      <th>4</th>
      <td>동빈나 코딩테스트</td>
      <td>2023-06-16</td>
      <td>검색</td>
    </tr>
    <tr>
      <th>5</th>
      <td>주간 마케칭</td>
      <td>2023-06-15</td>
      <td>검색</td>
    </tr>
    <tr>
      <th>6</th>
      <td>ab태스트</td>
      <td>2023-06-15</td>
      <td>검색</td>
    </tr>
    <tr>
      <th>7</th>
      <td>그로스해킹</td>
      <td>2023-06-15</td>
      <td>검색</td>
    </tr>
    <tr>
      <th>8</th>
      <td>정량화</td>
      <td>2023-06-15</td>
      <td>검색</td>
    </tr>
    <tr>
      <th>9</th>
      <td>킬라그램 식보이</td>
      <td>2023-06-15</td>
      <td>검색</td>
    </tr>
  </tbody>
</table>
</div>



# 유튜브 API

### 유튜브 API를 활용해 videoId, categoryId, tags를 가져옵니다

시청기록의 동영상 제목을 기반으로 유튜브 API에서 videoId를 가져오고, videoId를 통해서 categoryId와 tags를 가져옵니다


```python
view_history['videoId'] = True
view_history['categoryId'] = True
view_history['tags'] = True
```


```python
# Youtube API
from googleapiclient.discovery import build
from googleapiclient.errors import HttpError

# Replace 'YOUR_API_KEY' with your actual API key
api_key = 'AIzaSyAOn9Kfbc31B2WV2GQnzPkXKagUIiVqvBE'
youtube = build('youtube', 'v3', developerKey=api_key)
```


```python
def get_videos_id(x):
  search_query = x
  response = youtube.search().list(part='snippet', q=search_query, maxResults = 1).execute()
  video_id =response['items'][0]['id']['videoId']
  return video_id

def get_videos_tags(x):
  video_response = youtube.videos().list(part='snippet,contentDetails', id = x).execute()
  tags = video_response['items'][0]['snippet']['tags']
  return tags

def get_videos_categoryId(x):
  video_response = youtube.videos().list(part='snippet,contentDetails', id = x).execute()
  categoryId = video_response['items'][0]['snippet']['categoryId']
  return categoryId
```

유튜브 API는 할당량이 정해져 있기 때문에 37,000건의 시청 기록에서 videoId, categoryId, tags를 가져오는 작업은 대략 한 달이 소요될 것으로 예상됩니다. 따라서, 저는 일주일 동안 API를 사용하여 데이터를 수집하였고, 약 2,000건의 데이터에서 videoId, categoryId, tags를 성공적으로 찾아낼 수 있었습니다.

2,000건은 2023/06/07일 부터 2023/06/16일 까지 약 10일간의 데이터 입니다.


```python
sample_view_history = pd.read_csv(r'C:\Users\hyoye\Desktop\Github.io\Project Data\Youtube 데이터 분석\raw_sample_view_history.csv')
print(sample_view_history.shape)
sample_view_history
```

    (2005, 7)
    




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
      <th>creator</th>
      <th>time</th>
      <th>activityControls</th>
      <th>videoId</th>
      <th>categoryId</th>
      <th>tags</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>What is an Edge-Induced Subgraph? | Graph Theory</td>
      <td>Wrath of Math</td>
      <td>2023-06-16</td>
      <td>시청</td>
      <td>bk08JzEWeC4</td>
      <td>27</td>
      <td>['wrath of math', 'math lessons', 'math', 'edu...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Vertex Covers and Vertex Covering Numbers | Gr...</td>
      <td>Wrath of Math</td>
      <td>2023-06-16</td>
      <td>시청</td>
      <td>1KkT7y8nxH0</td>
      <td>27</td>
      <td>['wrath of math', 'math lessons', 'math', 'edu...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>P vs. NP and the Computational Complexity Zoo</td>
      <td>hackerdashery</td>
      <td>2023-06-16</td>
      <td>시청</td>
      <td>YX40hbAHx3s</td>
      <td>27</td>
      <td>['computational complexity', 'computer science...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>[HR컨퍼런스 | 황성현 대표와 해답을 더하다] 정량화가 어려운 목표를 수치화하고,...</td>
      <td>Lemonbase</td>
      <td>2023-06-16</td>
      <td>시청</td>
      <td>zsgds5ZGuqw</td>
      <td>27</td>
      <td>['레몬베이스', 'lemonbase', '황성현', '퀀텀인사이트', 'HR컨퍼런...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>실제 일하면서 알게된 컨설팅 현실 / 연봉, 하는 일, 애로 사항 / 개발자의 삶과...</td>
      <td>기술노트with 알렉</td>
      <td>2023-06-16</td>
      <td>시청</td>
      <td>Nsz-DFOqWqU</td>
      <td>27</td>
      <td>['IT', '컨설팅', '컨설턴트', '하는 일', '현실', '진로']</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>2000</th>
      <td>금태양 멸치 #헬린이 #헬스 #금태양 #운동하는직장인 #다이어트중</td>
      <td>벤쿠버헬린이</td>
      <td>2023-06-07</td>
      <td>시청</td>
      <td>uf9Tbfqx2dE</td>
      <td>20</td>
      <td>True</td>
    </tr>
    <tr>
      <th>2001</th>
      <td>성수 핫플 젠틀몬스터 팝업 갔다 옴</td>
      <td>내하루</td>
      <td>2023-06-07</td>
      <td>시청</td>
      <td>-okLAne-Ec0</td>
      <td>22</td>
      <td>True</td>
    </tr>
    <tr>
      <th>2002</th>
      <td>My top 4 funniest videos of the day</td>
      <td>Chris Colditz</td>
      <td>2023-06-07</td>
      <td>시청</td>
      <td>FwBV3VITQaY</td>
      <td>24</td>
      <td>True</td>
    </tr>
    <tr>
      <th>2003</th>
      <td>쥬브 땡길 때(?) 흰띠 검은띠 차이🤣</td>
      <td>용용용 YYY</td>
      <td>2023-06-07</td>
      <td>시청</td>
      <td>Qdu9Lawc35o</td>
      <td>22</td>
      <td>True</td>
    </tr>
    <tr>
      <th>2004</th>
      <td>킹받는 박연진 하도영 광고</td>
      <td>뫄옹</td>
      <td>2023-06-07</td>
      <td>시청</td>
      <td>z9KAXX0lcL4</td>
      <td>1</td>
      <td>True</td>
    </tr>
  </tbody>
</table>
<p>2005 rows × 7 columns</p>
</div>



YouTube의 categoryId를 기반으로 카테고리를 분류하는 작업을 수행합니다. <br>
저는 dictionary 형태의 데이터를 활용하여 map 함수를 사용하여 'Category'라는 새로운 컬럼을 생성할 것입니다.


```python
youtube_category_dict = {
    2 : "Autos & Vehicles",
    1 :  "Film & Animation",
    10 : "Music",
    15 : 'Pets & Animals',
    17 : 'Sports',
    18 : 'Short Movies',
    19 : 'Travel & Events',
    20 : 'Gaming',
    21 : 'Videoblogging',
    22 : 'People & Blogs',
    23 : 'Comedy',
    24 : 'Entertainment',
    25 : 'News & Politics',
    26 : 'Howto & Style',
    27 : 'Education',
    28 : 'Science & Technology',
    29 : 'Nonprofits & Activism',
    30 : 'Movies',
    31 : 'Anime/Animation',
    32 : 'Action/Adventure',
    33 : 'Classics',
    34 : 'Comedy',
    35 : 'Documentary',
    36 : 'Drama',
    37 : 'Family',
    38 : 'Foreign',
    39 : 'Horror',
    40 : 'Sci-Fi/Fantasy',
    41 : 'Thriller',
    42 : 'Shorts',
    43 : 'Shows',
    44 : 'Trailers'}
```

categoryId가 없는 row들을 드랍하겠습니다


```python
sample_view_history[sample_view_history['categoryId'] == 'True']
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
      <th>creator</th>
      <th>time</th>
      <th>activityControls</th>
      <th>videoId</th>
      <th>categoryId</th>
      <th>tags</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>371</th>
      <td>햇살이 뜨거우니 피부보호는 필수. 우산으로 빛을 차단하는 슈퍼신인 김민별프로의 단아...</td>
      <td>골까튜브 - 골프의 모든 것!!</td>
      <td>2023-06-13</td>
      <td>시청</td>
      <td>H9fp7lvjpzM</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>521</th>
      <td>아빠와 아버지의 차이 #shorts</td>
      <td>1번국도 :사단의전설</td>
      <td>2023-06-13</td>
      <td>시청</td>
      <td>q0KeazMsawM</td>
      <td>True</td>
      <td>True</td>
    </tr>
  </tbody>
</table>
</div>




```python
sample_view_history = sample_view_history.drop([371, 521])
```

sample_view_history 데이터 프레임에 'categoryId'를 int형으로 변환하겠습니다


```python
sample_view_history['categoryId'] = sample_view_history['categoryId'].astype(int)
```

sample_view_history의 'categoryId'에 youtube_category_dict을 적용하겠습니다


```python
sample_view_history['Category'] = sample_view_history['categoryId'].map(youtube_category_dict)
```


```python
sample_view_history
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
      <th>creator</th>
      <th>time</th>
      <th>activityControls</th>
      <th>videoId</th>
      <th>categoryId</th>
      <th>tags</th>
      <th>Category</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>What is an Edge-Induced Subgraph? | Graph Theory</td>
      <td>Wrath of Math</td>
      <td>2023-06-16</td>
      <td>시청</td>
      <td>bk08JzEWeC4</td>
      <td>27</td>
      <td>['wrath of math', 'math lessons', 'math', 'edu...</td>
      <td>Education</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Vertex Covers and Vertex Covering Numbers | Gr...</td>
      <td>Wrath of Math</td>
      <td>2023-06-16</td>
      <td>시청</td>
      <td>1KkT7y8nxH0</td>
      <td>27</td>
      <td>['wrath of math', 'math lessons', 'math', 'edu...</td>
      <td>Education</td>
    </tr>
    <tr>
      <th>2</th>
      <td>P vs. NP and the Computational Complexity Zoo</td>
      <td>hackerdashery</td>
      <td>2023-06-16</td>
      <td>시청</td>
      <td>YX40hbAHx3s</td>
      <td>27</td>
      <td>['computational complexity', 'computer science...</td>
      <td>Education</td>
    </tr>
    <tr>
      <th>3</th>
      <td>[HR컨퍼런스 | 황성현 대표와 해답을 더하다] 정량화가 어려운 목표를 수치화하고,...</td>
      <td>Lemonbase</td>
      <td>2023-06-16</td>
      <td>시청</td>
      <td>zsgds5ZGuqw</td>
      <td>27</td>
      <td>['레몬베이스', 'lemonbase', '황성현', '퀀텀인사이트', 'HR컨퍼런...</td>
      <td>Education</td>
    </tr>
    <tr>
      <th>4</th>
      <td>실제 일하면서 알게된 컨설팅 현실 / 연봉, 하는 일, 애로 사항 / 개발자의 삶과...</td>
      <td>기술노트with 알렉</td>
      <td>2023-06-16</td>
      <td>시청</td>
      <td>Nsz-DFOqWqU</td>
      <td>27</td>
      <td>['IT', '컨설팅', '컨설턴트', '하는 일', '현실', '진로']</td>
      <td>Education</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>2000</th>
      <td>금태양 멸치 #헬린이 #헬스 #금태양 #운동하는직장인 #다이어트중</td>
      <td>벤쿠버헬린이</td>
      <td>2023-06-07</td>
      <td>시청</td>
      <td>uf9Tbfqx2dE</td>
      <td>20</td>
      <td>True</td>
      <td>Gaming</td>
    </tr>
    <tr>
      <th>2001</th>
      <td>성수 핫플 젠틀몬스터 팝업 갔다 옴</td>
      <td>내하루</td>
      <td>2023-06-07</td>
      <td>시청</td>
      <td>-okLAne-Ec0</td>
      <td>22</td>
      <td>True</td>
      <td>People &amp; Blogs</td>
    </tr>
    <tr>
      <th>2002</th>
      <td>My top 4 funniest videos of the day</td>
      <td>Chris Colditz</td>
      <td>2023-06-07</td>
      <td>시청</td>
      <td>FwBV3VITQaY</td>
      <td>24</td>
      <td>True</td>
      <td>Entertainment</td>
    </tr>
    <tr>
      <th>2003</th>
      <td>쥬브 땡길 때(?) 흰띠 검은띠 차이🤣</td>
      <td>용용용 YYY</td>
      <td>2023-06-07</td>
      <td>시청</td>
      <td>Qdu9Lawc35o</td>
      <td>22</td>
      <td>True</td>
      <td>People &amp; Blogs</td>
    </tr>
    <tr>
      <th>2004</th>
      <td>킹받는 박연진 하도영 광고</td>
      <td>뫄옹</td>
      <td>2023-06-07</td>
      <td>시청</td>
      <td>z9KAXX0lcL4</td>
      <td>1</td>
      <td>True</td>
      <td>Film &amp; Animation</td>
    </tr>
  </tbody>
</table>
<p>2003 rows × 8 columns</p>
</div>



# 데이터 EDA

**상위 10개의 많이 시청한 유튜브 creator 확인**


```python
creator_size = sample_view_history.groupby('creator').size().sort_values(ascending = False)
```


```python
creator_size.reset_index(name = 'Count')[:10]
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
      <th>creator</th>
      <th>Count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>흑자헬스 SurplusHealth</td>
      <td>77</td>
    </tr>
    <tr>
      <th>1</th>
      <td>박승현</td>
      <td>70</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1분요리 뚝딱이형</td>
      <td>61</td>
    </tr>
    <tr>
      <th>3</th>
      <td>한빛미디어</td>
      <td>27</td>
    </tr>
    <tr>
      <th>4</th>
      <td>윤루카스</td>
      <td>25</td>
    </tr>
    <tr>
      <th>5</th>
      <td>스완SWAN_현실남자뷰티</td>
      <td>25</td>
    </tr>
    <tr>
      <th>6</th>
      <td>ASMR Münür Önkan</td>
      <td>24</td>
    </tr>
    <tr>
      <th>7</th>
      <td>똘킹</td>
      <td>17</td>
    </tr>
    <tr>
      <th>8</th>
      <td>뫄옹</td>
      <td>17</td>
    </tr>
    <tr>
      <th>9</th>
      <td>댓글맛집</td>
      <td>15</td>
    </tr>
  </tbody>
</table>
</div>



운동 유튜버인 흑자헬스와 박승현의 시청횟수가 높은 것을 확인할 수 있습니다. 두 유튜버는 입담이 좋아 자주 시청하기도 합니다. <br>

그리고 박승현, 1분 요리 뚝딱이형, 윤루카스, 뫄옹, 댓글맛집 같은 경우는 Youtube Shorts에서 많이 보이는 유튜버입니다. Shorts는 1분 내외로 짧은 영상이여서 시청 횟수로만 보면 굉장히 높은 것을 알 수 있습니다.

해당 기간 동안 한빛미디어 채널을 통해 SQL를 공부했었습니다. 

**10일간 시청 횟수**


```python
time_size = sample_view_history.groupby('time').size()
```


```python
time_size_df = time_size.reset_index(name = 'Count')
```


```python
sns.barplot(x='Count', y='time', data=time_size_df)
plt.title('Date')
plt.xlabel('Counts')
plt.ylabel('Date')
plt.show()
```


    
![png](output_45_0.png)
    


주말은 예상외로 시청 횟수가 적은 것을 확인할 수 있었습니다. 주말에는 친구들과의 약속이 많아 유튜브 시청이 적은것은 합리적입니다.

**많이 시청한 카테고리 시각화**


```python
category_size = sample_view_history.groupby('Category').size().sort_values(ascending = False)
```


```python
category_size_df = category_size.reset_index(name = 'Count')
```


```python
sns.barplot(x='Count', y='Category', data=category_size_df)
plt.title('Category Counts')
plt.xlabel('Counts')
plt.ylabel('Categories')
plt.show()
```


    
![png](output_50_0.png)
    


저는 유튜브에서 주로 People & Blogs, Entertainment, Sports, Howto & Style, Music 카테고리의 영상을 시청하고 있다는 것을 알 수 있었습니다. 예상과는 달리, 교육 영상보다는 킬링 타임 영상들을 더 자주 시청하고 있는 것을 발견했습니다. 그러나 영상의 길이를 고려한다면 교육 영상이 더 많이 시청되는 것으로 예상됩니다. 보통 교육 영상은 30-40분 정도의 길이를 가지기 때문입니다. 

또한, 이동 중에는 YouTube Music을 통해 음악을 듣는데, 이로 인해 Music이 상위 5개 카테고리에 포함되어 있는 것은 합리적입니다. 

이번 프로젝트를 통해 간단한 유튜브 시청 기록 분석을 마무리하였습니다. 다음에는 태그와 검색 기록을 활용하여 더 세부적으로 제 관심사에 대해 조사해보도록 하겠습니다.
