# Spotify_Playlist_Mood_Classfication-for-Music_Recommendation_Chatbot

### **📅 프로젝트 진행기간**

2022.12.09 ~ 2022. 12. 22

# **🚀 프로젝트 개요**

## 💡프로젝트 진행 배경

- Youtube 에서 특정 감정을 나타내는 단어를 검색 시 **음악,노래** 등이 최상단에 노출됨을 확인
 
- 상당수의 이용자가 스트리밍 알고리즘 서비스에 이어, 감정형용사 검색만으로 손쉽게 음악을 추천 받고 싶어함을 파악

## 프로젝트 목표

→ **“감성분석을 바탕으로 Spotify Playlist의 곡 Mood 분석 기반 음악 추천 서비스 구축 및 앱 데모 개발”**

**추천 시스템 핵심가치**

- 사용자: 아티스트 및 장르 인사이트 없이 감정별 음원추천으로 다양한 음악체험 가능
- 제공자: MAU/DAU,UX 확보에 따른 서비스 개발

## 🛠 프로젝트 구조

- User Input (감정 문장) → Sentence Classification → Spotify Moods Data로 구축한 DB에서 해당 Sentence의 감성과 Mapping Emotion Songs Picking → User Recommendation

<img width="999" alt="스무디프로젝트구조" src="https://user-images.githubusercontent.com/109712433/225647507-06868c4e-e196-49c8-b55f-bcfcc428992f.png">

## 🏃‍♀️ **팀 구성 및 역할**

**유한솔**


프로젝트 기획, 데이터 분석(Spotyfi train data, AI HUB 감성 말뭉치) 및 수집, 데이터 전처리(Spotyfi train data), Spotyfi Open API, 모델 학습 (Spotify Data - TensorFlow.Keras), UI(Figma) MySQL, PPT

**여민희**


프로젝트 기획, 데이터 분석 및 수집(AI HUB 감성 말뭉치), 데이터 전처리(AI HUB 감성 말뭉치), 모델 학습(감성말뭉치 - Kobert, kober-finetuning), MySQL, Flask, UI(Figma), PPT

**윤병윤**


모델 학습(감성말뭉치- Klue-KoBert), UI(Figma), PPT

**이재엽**


데이터 증강, 모델학습(Koelectra), Flask

**전은성**


모델 학습 (Roberta, Kobert-finetuning), Flask, CSS

# **📚 데이터**

## 데이터 명세

**1) AI HUB 감성 대화 말뭉치 데이터
→ 감성분석에 사용**

- 총 데이터 수 : 74,857개
<p align="center">
<img width="400" alt="스무디 데이터 명세 감성말뭉치" src="https://user-images.githubusercontent.com/109712433/225647501-b698eb85-53cc-413c-bdb0-e9542f465c43.png">
</p>
- [감정 대분류, 감정 소분류, 사람문장1]
[감정 대분류, 감정 소분류, 사람문장2]
[감정 대분류, 감정 소분류, 사람문장3]
row로 합침
    
    → 데이터셋 199,372개
    
- 대답 컬럼은 삭제하고, 발화 컬럼만 사용
- 감정대분류종류및개수
: ‘기쁨’(12,449개), ‘당황’(11,308개),
    
    ‘슬픔’(12,708개), ‘분노’(12,503개),
    ’불안’(12,383개), ‘상처’(13,505개)
    
- 감정소분류:총58개
- 문장 속 감정을 분류할 학습데이터로 사용

**2)Spotify Moods Data**

**→ MySQL Emotion Songs DB 구축에 사용**
<p align="center">
<img width="500" alt="스포티파이 데이터명세" src="https://user-images.githubusercontent.com/109712433/225647493-18c68b60-5705-4d74-b0d6-f30088f7d95a.png">
</p>
- Spotify에서 랜덤으로 뽑은 학습용 데이터
- ID : 곡의 전체 feature에 대한 ID
- X_features : Danceability, Acousticness, Energy, Instrumentalness, Liveness, Valence,
Loudness, Speechiness, Tempo 등 곡 리듬 정보를 수치형태로 담고 있음
- 곡 리듬 정보들의 수치를 바탕으로 mood가 결정
- mood : Happy, Sad, Energetic, Calm 총 4가지의 기분이 존재
- 데이터의 곡은 팝송으로 이루어져 있음

## DB 구축에 대한 고민

**Emotion Songs DB 구축 유용 데이터 비교**

(Melon 과 Spotify 음악 데이터)

<p align="center">
<img width="400" alt="멜론디비고민" src="https://user-images.githubusercontent.com/109712433/225647478-604a9ec7-446c-4579-923c-32f2ebeedf8c.png"> <img width="400" alt="멜론 디비고민" src="https://user-images.githubusercontent.com/109712433/225647486-abe383b1-04b6-4ef8-af9f-1e2c75dc6826.png">
</p>

- Tags 예시
[‘추억’, ‘회상’], [‘감성’,’ 시작’, ’출발’] 등
- Tags가 임의의 단어로 되어 있기 때문에,
플레이리스트의 곡들과의 유사도를 파악하기가
어려워 주관적인 라벨링만 가능
- Tags에 있는 감정을 대변하는 단어의 길이가 짧고,
Tag 내용이 감정과 관련없는 Tag가 많았어서 Tag를
기반으로 감정을 연결하기에 적합하지 않았음

- X_features가 수치형으로 되어있으며, 이를 기반으로 각
mood별 수치를 나타냄
    
    Calm : 0.172 Energetic : 0.022
    Happy : 0.065 Sad : 0.739
    
    나타낸 수치 중 가장 큰 값을 해당 노래의 mood로 라벨링
    
- 수치를 기반으로 mood를 판단해 객관적인 라벨링 가능

 

# 💻 모델링 과정

## 1. **AI Hub 감성 대화 말뭉치 데이터를 기반으로 Emotion Sentence Classification 진행**

**Try 1**

- 소분류의경우총58개의라벨이존재하고,각대분류라벨마다약10개의소분류라벨이존재
- 대분류속소분류간문장의구분이명확하지않아서,소분류의총개수를23개로줄임

**Try1 Review**

i)성능이 두드러지게 개선 X 

ii) hidden layer와 hidden size를 각각 변경하여 모델 복잡도를 조절 → 여전히 성능 개선 X

**Try 2**

- 감정 대분류의 상처 컬럼을 제외한 5가지 감정으로 학습을 진행하고, 이를 기반으로 음악 추천

**Try2 Review**

- Spotify mood data의 경우 4개의 라벨(Happy, Sad, Energetic, Calm)이 존재하지만, 감성 대화 말뭉치의 경우 6개의 라벨이 존재하여, 당황, 슬픔에 이어 상처 또한 Sad와 연결을 지었을 경우, 3개의 감정이 하나의 mood에 연결이 되어 감정의 변별성이 약해짐
→ ‘기쁨:Happy’, ‘슬픔:Sad’, ‘당황:Sad’, ‘분노:Energetic’, ‘불안:Calm’ 으로 연결시켜줌

### Emotion Sentence Classification 결과

1) **Default Data**

- **대분류 - 라벨 5개(기쁨, 슬픔, 당황, 분노, 불안) 기준**
    - 감정분류에 많이 사용하는 Klue-kobert, Klue-roberta, Kobert-finetuning, Koelectra, Kobert을 사용하여 각각 학습
        
        
        | Model | Epoch | Max_len | Batch_size | Test_acc |
        | --- | --- | --- | --- | --- |
        | Kobert-finetuning | 15 | 64 | 128 | 0.445 |
        | koelectra | 15 | 128 | 64 | 0.579 |
        | Klue-roberta | 15 | 64 | 32 | 0.580 |
        | Klue-kobert | 15 | 64 | 64 | 0.582 |
        | Kobert | 15 | 64 | 128 | 0.605 |
    - 증강을 통해 데이터 수를 늘린 후, koelectra와 kobert에 적용하여 한번 더 성능 확인 시도

2) **Augumentation**

- 총 데이터 셋 : 290,311개
- Pororo를통한역번역(Kor→En,En→Kor)증강후학습결과
    
    
    | Model | Epoch | Max_len | Batch_size | Test_acc |
    | --- | --- | --- | --- | --- |
    | koelectra | 15 | 128 | 64 | 0.558 |
    | Kobert | 15 | 64 | 8 | 0.582 |
- **증강을했지만,모델의성능개선X**
    
    Why? 문장 자체가 짧아, 발화자의 감정이 충분히 반영되지 않았다고 추측
    

### **“학습결과 증강전 Kobert의 성능이 제일 높아, 해당 모델을 저장하여 챗봇의 문장분류에 사용 결정”**

## 2. **Spotify Moods 데이터 학습**

**TRAIN
→ 곡 별 감정 데이터 학습**

![스포티파이 무드 cv](https://user-images.githubusercontent.com/109712433/225647471-797689f7-80af-451a-bc96-e6da59d180ed.png)

- 음원의 음악적 특성을 통해 감정을 예측하는 모델 개발을 위해 사용
- X_features(곡 리듬 정보, 9개)와 Y(mood)로 학습 진행
- mood를 판단할 때, score형식으로 객관적인 판단이 가능
- TensorFlow Keras Classification, Kfold(Relu, Softmax)
사용하여 감정 Classification 진행
- Confusion Matrix 사용하여 Classification 성능 측정
- 10-Kfold 결과 : 평균 79.74% (std 4.89%)
- Y_pred acc : 80.66%

**TEST**

- Test data set : Spotify open API 사용 ‘Korean Top Hit 2022’ URI 가져와 사용
- [‘Happy’], [‘Sad’], [‘Energetic’], [‘Calm’] 각 컬럼 생성 후 Predict 점수 기입

**Spotify Moods 데이터 학습 결과**

- ’Korean Top Hit 2022’에서 가져온 데이터셋의 각 mood 개수
 - Happy : 39개 Sad : 36개 Energetic : 32개 Calm : 0개
 4개의 mood 중에서 ‘Calm’에 해당하는 노래가 존재하지 않음
- Spotify API에서 Calm Korean Songs Data를 가지고 와서 학습 후 분류하여 Calm에 노래 추가
- ‘노래, 아티스트, ID(곡의 전체 feature에 대한 ID)’를 MySQL DB에 저장

# 🤖 ”SMOODY” 챗봇 제작 OVERVIEW

- **챗봇 제작을 위해 사용한 Tool**

- 사용언어 Python 

<img width="1003" alt="스무디사용툴" src="https://user-images.githubusercontent.com/109712433/225649383-2b106683-6b91-4b48-b1be-bb3049bac6d2.png">


- **UI Design Draft**
    
    <img width="869" alt="스무디 ui draft 1" src="https://user-images.githubusercontent.com/109712433/225649379-716a4328-4423-42c5-bbf3-303cc24377b3.png">
    
    Loading Page, Main Page
    
    <img width="869" alt="스무디 ui draft 2" src="https://user-images.githubusercontent.com/109712433/225649373-95e41697-54a9-43b2-8b67-dc38e2152760.png">
    
    User Input Page
    
    <img width="869" alt="스무디 ui draft 3" src="https://user-images.githubusercontent.com/109712433/225649371-a1390637-3b1c-4aae-baf8-9869fa1f7428.png">
    
    User Output Page
    

- **Flask 올리기**
- 웹 페이지가 넘어가는 것 보다는 하나의 페이지안에서 동작하는 것이 자연스럽다 생각하여 Socket.IO 를 통해서 구현
- 노래 5곡 추천과 해당 노래의 정보 확인 및 곡 듣기 가능
    
    <img width="869" alt="스무디 flask1" src="https://user-images.githubusercontent.com/109712433/225649366-25fd8b13-55ee-4e19-be38-1db633a2d2dd.png">
    
    하나의 페이지 안에서 동작 (Socket.IO를 통해 구현) 
    
    <img width="869" alt="스무디 flask 2" src="https://user-images.githubusercontent.com/109712433/225649357-84ab71b4-99f3-436d-a59b-6599c5337b2d.png">
    
    노래 5곡 추천과 해당 노래의 정보 확인 및 곡 듣기 가능
    

# 📄 결과 및 보완점

### 결과

- Sentence Detection을 통해 Sentiment Detection 을 파악하여 사용자의 기분 파악 가능.
- Spotify의 곡 리듬 정보로 계산된 객관적인 점수로 DB를 바탕으로 사용자의 기분에 따른 객관적인 곡 추천 가능
- 반응형 웹서비스로 개발 하였으나, 초기 UI의 경우 App을 목표로 디자인 하였으므로 추후 앱 으로 개발시 추가 UI에 대한 고민 축소 및 UX를 기반으로 좀 더 업데이트 된 어플 개발 용이

### 보완점

- 추천된 노래와 함께 트랙 이미지가 함께 뜨도록 DB 추가 업데이트 필요함.
- DB의 기분 별 상단 5개를 추천하도록 현재 테스트로 만들어 놓았기 때문에, 해당하는 기분의 리스트에 있는 곡을 랜덤으로 추천하도록 만들어 기분이 같아도, 다른 곡 을 추천 할 수 있도록 해야함.
- 앱으로 출시를 하게 된다면, 기존에 들었던 곡을 바탕으로 이와 유사한 노래를 추천할 수 있도록 사용자의 ID DB 저장이 필요함
- 현재 Spotify Oauth3 Authorization Authenticate 불러오는데 오류가 있어 Youtube hyperlink 연결로 대체 하였기 때문에, 이를 Spotify로 바로 연결하게 된다면 이에 대한 사항을 강구 하여야함, 또한, Spotify의 경우 유료 스트리밍 서비스 이기 때문에, 비과금 사용자를 겨냥하게 되는경우 Youtube hyperlink 역시 고려해 보아야 하는 사항
- 모든 페이지를 BootsStrap으로 통일 시켜줘야 함
