# League of Legends 티어 예측 프로젝트

---

## **What is this?**

- 라이엇 open API를 통해 획득한 인 게임 데이터를 활용하여 티어 예측 모델을 개발하는 프로젝트다.

---

## **Features**

- open API를 활용한 데이터 추출 및 저장 코드는 _라이엇open API.ipynb_ 에서 확인할 수 있다.
- 최종적으로 추출된 JSON 데이터는 DataFrame 형태로 이쁘게 포장되어 탐색된 뒤, *EDA+Feature Engineering_1.ipynp*에 담겨있다.

* *EDA+Feature Engineering_1.ipynp*에는 주로 상식적인 선에서 굉장히 관대하게 변수 선택이 이루어졌다. (게임 내부 데이터가 아니거나 or 게임 형식이 다르거나 등)
* Feature Engineering을 비롯한 심도있는 변수 선택 or 파생 변수 생성, 전처리 등은 *EDA+Feature Engineering_2*에서 이루어졌다.

---

## **데이터 수집**

> Click [here](https://developer.riotgames.com/)

- 위 라이엇 개발자 공식 사이트에서 OPEN API 호출을 통해 인게임 데이터를 얻는 방식이다.

- 단, 게임 내 데이터를 뽑아내려면 아래와 같은 단계를 거쳐야만 한다.

  1. 티어별로 특정 조건을 만족하는 summoner_id와 rank(I,II,III,IV)를 추출한다.

  2. 위에서 뽑아낸 summoner_id를 통해 puuid를 추출한다.

  3. 위에서 뽑아낸 puuid를 통해 matchid를 추출한다.

  4. 위에서 뽑아낸 matchid를 통해 인 게임 내 데이터를 meta_json형식으로 추출한다.

- 데이터 수집 시, 특정 조건(승률, 게임 판 수 등)에 부합하는 데이터만을 추출했다.

  ***

## **API 이용 주의사항**

- 위 라이엇 사이트에 가입한 뒤, 발급받는 임시 API KEY가 필요하다.
- 해당 키는 유효 시간이 24시간 이므로, 만료될 때마다 계속 발급받아야한다.

- 허나, 유효시간이 만료되기 전에 계속 발급받게되면 유효 시간이 업데이트되지 않는 오류?가 존재한다.
- 이에 만약 API 호출을 통해 데이터를 불러오는 중에 API키가 만료된다면, 셀이 터진다.

- 또, 라이엇은 API 요청 제한 규정이 상당히 빡센 편이다. 내용은 다음과 같다.

  > Rate Limits

  > > 20 requests every 1 seconds(s)

  > > 100 requests every 2 minutes(s)

- 해당 트래픽을 넘기게되면 API 호출 에러가 뜨면서 역시나 셀이 터지기에 시간에 잘 유의할 필요성이 있다.

* 특정 개발 환경에서 새로운 api키를 호출하는데엔 길게는 2분까지도 소요될 수 있다.

- 이러한 문제 때문에 사실상 반수동에 가까운 노동작업으로 단계를 끊어가며 데이터를 수집해야했다.

- 또한, 이러한 문제 때문에 셀당 지속적인 파일 저장 방식을 택했음을 알린다.

- 단위 사이클(약 하루) 당 약 6만개의 데이터를 추출할 수 있다.

> 본 프로젝트는 라이엇 게임즈의 공식 API를 사용하여 개발되었으며, 이 프로젝트에서 사용된 데이터와 콘텐츠는 모두 라이엇 게임즈의 소유입니다. '리그 오브 레전드'와 관련된 모든 저작권 및 상표권은 라이엇 게임즈에 귀속됩니다. 이 프로젝트는 라이엇 게임즈와 직접적인 연관이 없으며, 그들의 승인을 받은 것이 아닙니다.

## **수집한 반정형 데이터(JSON) 내부 구조**

- 최종적으로 획득한 **meta 데이터**는 json 형식으로 크게 다음과 같은 형식을 따른다.

  - 가장 간단하게 설명하자면, 딕셔너리 안에 딕셔너리가 겹겹이 쌓여진 구조.

    - (~~다른 설명법을 못 찾겠다~~)

  - 크게 metadata, info, participants로 구성되어 있는데, 우리가 원하는 특정 인원의 인 게임 데이터는 participants 에 존재한다.

  - 여기서의 문제는 participants안에 총 10명의 participant가 존재한다는 점이다.

  - 우리는 특정 룰에 따라 데이터를 수집했고 따라서, 조건에 부합하는 단 한명의 participant 데이터만이 필요하므로 이를 추출할 전처리가 필요했다.

  - 다행이도 셀 당 저장하는 형태로 데이터수집을 진행했기에 최종 저장된 데이터에는 *summonerId*가 변수로 포함되어있고 이를 활용하여 json 데이터를 파싱한 뒤, 조건식으로 추출해냈다.

  - 우리가 원하는 데이터들을 파싱한 형태로 표현하면 아래의 예시와 같다.

    - `metajson['info']['participants'][['summonerId'] == 'summonerId']['challenges']`
    - `metajson['info']['participants']`

  - 해당 내용은 _JSON preprocessing_ 코드에 있다.
