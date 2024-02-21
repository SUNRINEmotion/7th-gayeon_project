# 자동차 리콜 데이터 분석

프로젝트 설명 
자동차 리콜제도란 자동차가 안전기준에 부적합하거나 안전운행에 지장을 주는 결함이 있는 경우 안전과 관련된 사고와 소비자 피해를 사전에 예방하고 재발을 방지하는 제도를 말한다.
이 프로젝트의 목표는 한국교통안전공단 자동차 결함 리콜 데이터를 분석해 정보를 도출하는 것이다.

<br/>
[이미지1]
<img width="1230" alt="1" src="https://github.com/shrkdus14/-/assets/123463057/73b78462-d141-40cf-8f0c-2ef1a3d94f76">
‘한국교통안전공단_자동차결함 리콜현황’의 데이터를 불러와 dataframe을 확인해주고 이를 정규화 시켜 주었다. 결측치는 isnull.sum으로 확인해주었고, 중복값은 duplicated로 삭제해주었다.

```py
df = pd.read_csv('/한국교통안전공단_자동차결함 리콜현황_20221231.csv', encoding = 'cp949')
    df.isnull().sum()
df[df.duplicated(keep=False)]
df = df.drop_duplicates()
```

그 후는 기초적인 데이터 변형이다. 열들이 문자형 데이터로 이루어져 있기에 분석을 위해 정수형으로 변환해준 후 편리를 위해 열을 영어로 변경한다. 
'생산기간(부터)', '생간기간(까지)', '리콜개시일'이라는 열을 뽑아내 새로운 열을 만든다. 이는 날짜 및 시간 데이터를 효율적으로 처리하고 분석하기 위함이다.

<br/>
[이미지2,3]
<div>
  <img width="863" alt="2" src="https://github.com/shrkdus14/-/assets/123463057/2ca5cb0b-c742-4c71-8293-2bb7304b4356">
  <img width="847" alt="3" src="https://github.com/shrkdus14/-/assets/123463057/a1735a53-1012-461c-9ff8-9daf1cead376">
</div>
그래프를 깔끔하게 그리기 위해 제조사별 내용을 sort를 통해 정렬해준 후 막대그래프를 그려주었다. 
모델별 내용도 같은 방식으로 진행하였다. 

```py
plt.figure(figsize=(10,5))
sns.set(font="NanumBarunGothic",rc={"axes.unicode_minus":False},style='darkgrid')
ax = sns.countplot(x="manufacturer", data=df, palette="Set2", order=tmp.index)
plt.xticks(rotation=80)
plt.show()
```
<br/>
[이미지4]
<img width="857" alt="4" src="https://github.com/shrkdus14/-/assets/123463057/9310da4f-92e0-47ee-bf2e-452c703ca361">
월별 리콜 건수는 건수에 맞추어 정렬한 것이 아니라 날짜 순서대로 맞추어 그래프를 그려주었다. 
데이터 값으로 정렬을 하게 되면 달이 뒤죽박죽으로 섞이기 때문에 달마다 있는 리콜 건수를 쉽게 확인하기 위해서 1월부터 12월까지의 달을 정렬하여 준 것이다. 

```py
plt.figure(figsize=(10,5))
sns.set(style="darkgrid")
ax = sns.countplot(x="recall_month", data=df, palette="Set2")
plt.show()
```

이렇게 막대 그래프를 그리면 한눈에 정보를 파악하기가 쉽다. 
하지만 처음 막대 그래프와 같이 데이터 간의 격차가 크면 다른 값들이 잘 나타나지 않는다는 단점도 있다. 
그래서 데이터의 값들이 서로 비슷할 때 막대그래프를 그리는 것이 좋다.

<br/>
[이미지5]
<img width="859" alt="5" src="https://github.com/shrkdus14/-/assets/123463057/64eea5c6-a690-4cac-87af-d7e27aa36d7d">
생산년도별 리콜 현황은꺽은선 그래프로 확인을 해주었다.
꺽은선 그래프는 년도 마다의 변화를 파악하기 쉬움으로 막대그래프 대신 그려주었다.

```py
tmp = pd.DataFrame(df.groupby("start_year").count()["model"]).rename(columns={"model": "count"}).reset_index()
tmp = tmp.astype(int)
plt.figure(figsize=(10,5))
sns.set(style="darkgrid")
sns.lineplot(data=tmp, x="start_year", y="count")
plt.xticks(tmp["start_year"], rotation=45)
plt.show()
```
<br/>
[이미지6]
<div>
  <img width="549" alt="6" src="https://github.com/SUNRINEmotion/7th-gayeon_project/assets/123463057/57327e07-f360-4ff7-b70f-cdf7b27dcfe1">
  <img width="709" alt="7" src="https://github.com/SUNRINEmotion/7th-gayeon_project/assets/123463057/66406ace-a229-498f-8858-b10f40ee8111">
</div>
마지막으로 히트맵이다. 히트맵은 색상으로 표현할 수 있는 다양한 정보를 일정한 이미지 위에 열분포 형태의 그래픽으로 출력하는 것이다. 

[이미지6]에서 보이는 첫 번째 히트맵은 위에서 그렸던 달마다의 리콜 건수를 히트맵으로 나타낸 것이다. 다만 색을 정렬하기 위해 달을 정렬하지 못한 것이 아쉽다. 

```py
new = pd.DataFrame(new.sort_values(by='start_year', ascending=False))
ax = sns.heatmap(new, annot=True, fmt='d', cmap='YlGnBu')
plt.show()
```

두 번째 히트맵은 리콜은 진행한 첫 달과 끝나는 달을 이용하여 그려주었는데 이렇게 하나의 데이터만을 이용하지 않고 여러 데이터를 이용하여 나타낼 수 있는 것은 장점인 것 같다.

```py
new_df = pd.DataFrame(new_df.sort_values(by='start_month'))
ax = sns.heatmap(new_df, annot=True, fmt='d', cmap='YlGnBu')
plt.show()
```
