# Practical Computer Vision Study — Part III
## Vision + Language 실습 매뉴얼

> **Part III의 목표**  
> 지금까지는 이미지와 영상을 주로 시각 정보만으로 다루었습니다.  
> Part III에서는 **이미지와 자연어를 연결하는 방법**을 경험합니다.
>
> 핵심 흐름:
>
> ```text
> Image + Text
>      ↓
> Shared / Multimodal Representation
>      ↓
> Search / Detection / Caption / Question Answering
> ```

---

# 0. Part III 개요

권장 진행은 5주입니다.

| 주차 | 주제 | 주요 도구 | 핵심 결과 |
|---|---|---|---|
| Week 9 | CLIP / Image-Text Similarity | Hugging Face | Prompt별 similarity |
| Week 10 | Image-Text Retrieval | HF / 준비된 Colab / FiftyOne | 자연어 이미지 검색 |
| Week 11 | Open Vocabulary Detection | Roboflow / HF Spaces | 텍스트 기반 객체 탐지 |
| Week 12 | Image Captioning | Hugging Face Spaces | 이미지 설명 비교 |
| Week 13 | VLM / VQA | Hugging Face Spaces | 질문-응답 benchmark |

---

# 1. 학습 목표

Part III가 끝나면 다음을 할 수 있어야 합니다.

- 이미지와 텍스트를 같은 의미 공간에서 비교한다는 개념을 설명할 수 있다.
- CLIP의 zero-shot 분류 개념을 설명할 수 있다.
- 자연어 검색어로 이미지 컬렉션을 검색할 수 있다.
- 고정 class detection과 open-vocabulary detection의 차이를 설명할 수 있다.
- Image Captioning과 VQA의 차이를 설명할 수 있다.
- 동일 이미지에 대한 prompt 또는 질문 변화가 결과에 미치는 영향을 실험할 수 있다.
- VLM의 hallucination 사례를 발견하고 기록할 수 있다.
- 이미지-질문-정답 형태의 작은 benchmark를 만들 수 있다.

---

# 2. 핵심 개념 — Multimodal

Multimodal AI는 서로 다른 형태의 정보를 함께 다룹니다.

예:

```text
Image
Text
Audio
Video
```

Part III에서는 주로:

```text
Vision + Language
```

를 다룹니다.

---

# 3. Week 9 — CLIP과 Image-Text Similarity

## 3.1 핵심 질문

> 컴퓨터는 이미지와 문장이 서로 얼마나 잘 맞는지 어떻게 비교할까?

CLIP 계열 모델은 이미지와 텍스트를 각각 벡터 표현으로 변환하고 두 표현의 유사도를 비교합니다.

개념:

```text
Image ──→ Image Encoder ──→ Vector
                              ↕ similarity
Text  ──→ Text Encoder  ──→ Vector
```

---

# 4. Embedding

이미지나 문장을 많은 숫자로 표현한 것을 embedding이라고 생각할 수 있습니다.

예:

```text
dog image
→ [0.21, -0.18, 0.54, ...]
```

```text
"a photo of a dog"
→ [0.24, -0.16, 0.51, ...]
```

두 벡터가 가까우면 의미가 비슷하다고 판단할 수 있습니다.

---

# 5. 실습 1 — 같은 이미지, Prompt 변경

강아지 사진 1장을 준비합니다.

문장 후보:

```text
dog
a dog
a photo of a dog
a cute puppy
a golden retriever
an animal
a cat
a car
```

Hugging Face Spaces에서 다음 검색어를 사용합니다.

```text
CLIP
image text similarity
zero shot image classification
```

실행 가능한 demo를 선택합니다.

---

# 6. 결과 기록

| Prompt | Similarity / Score | Rank |
|---|---:|---:|
| a photo of a dog | 0.84 | 1 |
| dog | 0.80 | 2 |
| an animal | 0.71 | 3 |
| a cat | 0.23 | 7 |

점수의 절대값보다 **같은 조건 안에서의 상대적인 순위**를 중심으로 관찰합니다.

---

# 7. 실습 2 — 구체성 변화

한 자동차 사진을 준비합니다.

```text
vehicle
car
red car
red sports car
a photo of a red sports car
```

질문:

- 더 구체적인 문장이 항상 높은 점수를 받는가?
- 이미지에 실제로 보이지 않는 속성을 넣으면 어떻게 되는가?

---

# 8. 실습 3 — 문장 표현 변화

같은 의미를 다양한 문장으로 표현합니다.

```text
a dog
a photo of a dog
there is a dog
an image showing a dog
a puppy
```

결과 차이를 기록합니다.

이를 **prompt sensitivity**의 첫 실험으로 봅니다.

---

# 9. Zero-shot Classification

기존 분류:

```text
고정된 class를 학습
→ 새 이미지 분류
```

CLIP식 zero-shot:

```text
이미지
+
사용자가 만든 text labels
→ similarity 비교
→ 가장 가까운 label 선택
```

새 class를 위해 항상 별도 학습이 필요한 것은 아닙니다.

---

# 10. 실습 4 — Zero-shot Classifier 만들기

이미지 10장을 준비합니다.

Class 후보:

```text
cat
dog
car
food
building
```

각 이미지마다 가장 높은 text similarity class를 예측으로 사용합니다.

결과:

| Image | True | Predicted | Correct? |
|---|---|---|---|
| 01 | dog | dog | O |
| 02 | food | food | O |
| 03 | cat | dog | X |

---

# 11. Week 9 실패 사례

다음을 찾아봅니다.

- 모호한 사진
- 일부만 보이는 객체
- 여러 객체가 같이 등장
- 매우 추상적인 그림
- text label끼리 의미가 비슷함
- 세부 종 분류

예:

```text
dog
puppy
golden retriever
animal
```

---

# 12. 선택 코딩 — CLIP

강사가 준비한 Colab을 사용합니다.

학생이 직접 구현할 부분은 최소화합니다.

개념적인 코드:

```python
from transformers import CLIPModel, CLIPProcessor
from PIL import Image

model_name = "openai/clip-vit-base-patch32"

model = CLIPModel.from_pretrained(model_name)
processor = CLIPProcessor.from_pretrained(model_name)

image = Image.open("image.jpg")
texts = ["a dog", "a cat", "a car"]

inputs = processor(
    text=texts,
    images=image,
    return_tensors="pt",
    padding=True
)

outputs = model(**inputs)
scores = outputs.logits_per_image.softmax(dim=1)

print(scores)
```

학생은 주로:

```text
image.jpg
texts
```

만 바꿉니다.

---

# 13. Week 10 — Image-Text Retrieval

## 13.1 목표

이번에는 이미지 하나를 분류하는 것이 아니라 **많은 이미지 중 자연어와 가장 잘 맞는 이미지를 찾습니다.**

예:

```text
Query:
"a person riding a bicycle"

        ↓

Image Collection

        ↓

Top 5 matching images
```

---

# 14. Retrieval의 기본 구조

```text
Images
 ↓
Image Embeddings
 ↓
Vector Database / List

Query Text
 ↓
Text Embedding
 ↓
Similarity Search

Top-k Images
```

---

# 15. 데이터 준비

팀당 이미지 20~50장 정도를 준비합니다.

추천 주제:

- 캠퍼스
- 음식
- 교통
- 동물
- 실내 사물

예:

```text
dataset/
    img001.jpg
    img002.jpg
    ...
    img030.jpg
```

---

# 16. 쉬운 실습 방식

코딩을 최소화하기 위해 두 가지 방법 중 하나를 사용합니다.

## 방법 A — Hugging Face Space

검색:

```text
image text retrieval
CLIP search
semantic image search
```

이미지 컬렉션 업로드가 가능한 demo를 사용합니다.

## 방법 B — 강사가 준비한 Colab

학생은 폴더 업로드와 query 입력만 수행합니다.

```text
1. 이미지 폴더 업로드
2. 전체 embedding 생성
3. query 입력
4. Top 5 확인
```

---

# 17. Retrieval 실험 Query

한 데이터셋에 대해 query를 단계적으로 바꿉니다.

```text
person
person walking
person walking outside
person carrying a bag
person wearing red
```

결과 순위가 어떻게 바뀌는지 확인합니다.

---

# 18. Retrieval 결과 기록

| Query | Rank 1 | Rank 2 | Rank 3 | 만족도 |
|---|---|---|---|---|
| person | 03 | 11 | 09 | High |
| red shirt | 17 | 03 | 21 | Medium |
| carrying bag | 08 | 10 | 13 | Low |

---

# 19. Precision@K 직관

예를 들어 query에 맞는 이미지가 Top 5 중 4개라면:

```text
Precision@5 = 4 / 5
```

복잡한 평가보다 먼저:

> 상위 몇 개 결과 중 실제로 원하는 이미지가 몇 개인가?

를 봅니다.

---

# 20. FiftyOne 선택 실습

FiftyOne App은 이미지/영상 데이터셋을 시각적으로 탐색할 수 있고 embedding visualization을 이용해 비슷한 샘플의 군집을 확인할 수 있습니다.

강사가 환경을 준비한 경우 학생은 GUI에서:

- embedding scatter plot 관찰
- cluster 선택
- outlier 선택
- 선택된 점과 실제 이미지 연결

을 실습합니다.

코딩 설치가 부담되면 이 단계는 시연으로 대체합니다.

---

# 21. Week 11 — Open Vocabulary Detection

## 21.1 목표

전통적인 detector는 미리 정의된 class를 찾습니다.

예:

```text
person
car
dog
bicycle
```

Open Vocabulary Detection은 텍스트로 찾고 싶은 대상을 지정합니다.

```text
red backpack
person wearing black
yellow vehicle
coffee cup
```

---

# 22. Fixed Vocabulary vs Open Vocabulary

| 항목 | Fixed | Open Vocabulary |
|---|---|---|
| class | 미리 정함 | text prompt로 지정 |
| 새 class | 재학습 필요 가능 | prompt로 시도 가능 |
| 유연성 | 낮음 | 높음 |
| prompt 영향 | 없음/적음 | 큼 |

---

# 23. 실습 준비

복잡한 거리 또는 실내 이미지 5장을 준비합니다.

한 이미지에 여러 대상이 포함되도록 합니다.

예:

```text
person
bag
car
sign
chair
cup
```

---

# 24. 실행 방법

## Roboflow

Workflow 또는 사용 가능한 foundation / zero-shot detection 기능을 활용합니다.

## Hugging Face Spaces

검색:

```text
open vocabulary detection
zero shot object detection
Grounding DINO
text guided detection
```

현재 실행 가능한 공개 demo를 선택합니다.

---

# 25. 실습 1 — 일반명사 → 구체 표현

동일 이미지를 사용합니다.

```text
person
woman
woman wearing black
person carrying a bag
```

결과를 저장합니다.

---

# 26. 실습 2 — 속성 추가

```text
car
red car
small red car
```

질문:

> 자연어 표현이 구체적이 될수록 항상 detection이 좋아지는가?

---

# 27. 실습 3 — 존재하지 않는 대상

이미지에 없는 객체를 prompt로 입력합니다.

```text
elephant
airplane
guitar
```

관찰:

- 아무것도 찾지 않는가?
- 다른 객체를 잘못 찾는가?
- confidence가 낮아지는가?

---

# 28. 결과 표

| Prompt | Ground Truth Count | Detected | False Positive |
|---|---:|---:|---:|
| person | 5 | 5 | 0 |
| black shirt | 2 | 3 | 1 |
| red bag | 1 | 0 | 0 |
| guitar | 0 | 1 | 1 |

---

# 29. Week 12 — Image Captioning

## 29.1 목표

Image Captioning은 이미지 전체를 자연어 문장으로 설명합니다.

```text
Image
  ↓
Vision-Language Model
  ↓
"A person is riding a bicycle on a street."
```

---

# 30. Captioning 실습 도구

Hugging Face Spaces:

```text
image captioning
image to text
vision language
```

업로드가 가능한 demo를 선택합니다.

---

# 31. 이미지 유형 6종

1. 단일 객체
2. 여러 객체
3. 사람 행동
4. 복잡한 거리
5. 텍스트가 포함된 이미지
6. 애매하거나 이상한 장면

---

# 32. Caption 평가 항목

문장이 그럴듯하다는 이유만으로 정답으로 보지 않습니다.

다음을 확인합니다.

- 주요 객체가 맞는가?
- 행동이 맞는가?
- 객체 간 관계가 맞는가?
- 중요한 정보를 빠뜨렸는가?
- 이미지에 없는 내용을 만들었는가?

---

# 33. Caption 비교표

| Image | Major Object | Action | Relation | Hallucination |
|---|---|---|---|---|
| img01 | O | O | O | X |
| img02 | O | X | △ | X |
| img03 | O | O | X | O |

Hallucination이 있으면 구체적으로 적습니다.

---

# 34. 실습 2 — 모델 비교

가능하면 같은 이미지 5장을 서로 다른 2개 이상의 공개 Space 또는 모델에서 실행합니다.

평가표:

| Image | Model A | Model B | Better factual detail? |
|---|---|---|---|
| 01 | ... | ... | A |
| 02 | ... | ... | B |

목표는 순위를 정하는 것이 아니라:

> **모델마다 어떤 유형의 차이가 나타나는지 관찰하는 것**

입니다.

---

# 35. Week 13 — Vision-Language Model과 VQA

## 35.1 목표

Visual Question Answering은 이미지와 질문을 함께 입력합니다.

```text
Image
+
Question
  ↓
VLM
  ↓
Answer
```

예:

```text
Q: What color is the car?
A: Red.
```

---

# 36. 질문 난이도 설계

## Level 1 — Object

```text
Is there a dog?
What object is on the table?
```

## Level 2 — Attribute

```text
What color is the bag?
```

## Level 3 — Count

```text
How many people are visible?
```

## Level 4 — Spatial

```text
What is to the left of the car?
```

## Level 5 — Action

```text
What is the person doing?
```

## Level 6 — Reasoning

```text
Why might the person be holding an umbrella?
```

Reasoning 질문은 단순 사실 질문보다 평가가 어려우므로 구분해서 기록합니다.

---

# 37. VQA Benchmark 만들기

이미지 10장을 선정합니다.

각 이미지마다 5개의 질문을 만듭니다.

```text
10 images × 5 questions = 50 QA pairs
```

CSV 예:

```csv
image,question,type,ground_truth
img01.jpg,How many people are visible?,count,3
img01.jpg,Is there a bicycle?,yes_no,yes
img01.jpg,What color is the car?,attribute,red
```

---

# 38. Ground Truth

가능하면 질문을 만들 때 사람이 정답을 먼저 기록합니다.

나쁜 방법:

```text
모델 답을 본 뒤 정답을 결정
```

좋은 방법:

```text
사람이 이미지 확인
→ 정답 기록
→ 모델 실행
→ 비교
```

---

# 39. 실습 — 질문 유형별 성능

| Type | Questions | Correct | Accuracy |
|---|---:|---:|---:|
| yes/no | 10 | 9 | 90% |
| count | 10 | 5 | 50% |
| attribute | 10 | 8 | 80% |
| spatial | 10 | 6 | 60% |
| action | 10 | 8 | 80% |

이 표가 이후 Part V의 모델 신뢰성 분석으로 연결됩니다.

---

# 40. VQA Hallucination 사전 실험

이미지에 없는 대상을 전제로 질문합니다.

예:

```text
자동차가 없는 이미지

Q: What color is the car?
```

좋은 응답:

```text
There is no car visible.
```

문제가 있는 응답:

```text
The car is red.
```

---

# 41. Leading Question

질문 자체가 모델을 특정 답으로 유도할 수 있습니다.

비교:

```text
What is the person holding?
```

vs

```text
Why is the person holding a phone?
```

실제로 phone이 없다면 두 번째 질문이 hallucination을 유발할 가능성이 있습니다.

---

# 42. Part III 통합 프로젝트

다음 중 하나를 선택합니다.

## A. Campus Image Search

캠퍼스 사진 50장에 대해 자연어 검색 시스템을 구성합니다.

## B. Open Vocabulary Object Search

한 장면에서 자연어로 여러 객체를 찾습니다.

## C. VQA Benchmark

직접 만든 50~100개의 VQA pair로 모델을 비교합니다.

## D. Captioning Error Analysis

50장 이미지의 caption 오류 유형을 분석합니다.

---

# 43. GitHub 구조

```text
part3/

week09-clip/
    README.md
    images/
    results.csv

week10-retrieval/
    README.md
    dataset/
    queries.csv
    results/

week11-open-vocabulary/
    README.md
    images/
    results/

week12-captioning/
    README.md
    captions.csv

week13-vqa/
    README.md
    questions.csv
    results.csv

project/
```

---

# 44. README 필수 항목

```markdown
## Objective
## Model / Tool
## Dataset
## Prompt or Questions
## Experiment Design
## Results
## Failure Cases
## Discussion
## Next Experiment
```

---

# 45. Part III 체크리스트

- [ ] CLIP의 image/text embedding 개념을 설명할 수 있다.
- [ ] prompt를 바꾸어 similarity를 비교했다.
- [ ] zero-shot classification을 실험했다.
- [ ] 자연어 기반 image retrieval을 수행했다.
- [ ] retrieval query를 구체화해 결과 변화를 관찰했다.
- [ ] open vocabulary detection을 실행했다.
- [ ] 존재하지 않는 객체 prompt를 테스트했다.
- [ ] image captioning을 실행했다.
- [ ] caption hallucination 사례를 기록했다.
- [ ] VQA benchmark를 만들었다.
- [ ] 질문 유형별 결과를 비교했다.
- [ ] 결과를 GitHub에 정리했다.

---

# 46. 다음 Part

Part IV에서는 이러한 모델을 실제 응용 형태로 확장합니다.

```text
Representation Learning
Generative Vision
Document AI
Video-Language
Multimodal Application
```

---

## 참고 공식 사이트

- Hugging Face Spaces: https://huggingface.co/spaces
- Hugging Face CLIP: https://huggingface.co/docs/transformers/model_doc/clip
- Hugging Face VQA: https://huggingface.co/docs/transformers/tasks/visual_question_answering
- Roboflow: https://roboflow.com/
- FiftyOne: https://docs.voxel51.com/
