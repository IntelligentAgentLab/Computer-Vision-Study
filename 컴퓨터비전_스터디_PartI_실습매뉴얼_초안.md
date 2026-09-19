# Practical Computer Vision Study — Part I  
## Visual Recognition 실습 매뉴얼

> **목표**  
> 이 자료는 컴퓨터 비전 입문 학부생을 위한 실습 중심 매뉴얼입니다.  
> 코딩을 최소화하고, **GUI 도구를 먼저 사용한 뒤 필요한 경우 최소한의 Python 코드로 같은 작업을 재현**하는 방식으로 구성합니다.  
> 모든 실습 결과는 GitHub에 기록하는 것을 원칙으로 합니다.

---

# 0. Part I 개요

Part I에서는 컴퓨터 비전의 가장 기본적인 네 가지 문제를 다룹니다.

1. **Image Classification** — 이미지 전체가 어떤 범주인지 판단
2. **Generalization & Dataset Bias** — 학습한 조건과 다른 환경에서 성능이 어떻게 변하는지 관찰
3. **Object Detection** — 이미지 안에서 객체의 위치와 종류를 찾기
4. **Image Segmentation** — 객체 또는 영역을 픽셀 단위로 구분

Part I의 핵심은 이론을 많이 외우는 것이 아니라 다음 흐름을 반복해서 익히는 것입니다.

```text
문제 이해
   ↓
GUI 도구로 실행
   ↓
입력 조건 변경
   ↓
결과 비교
   ↓
실패 사례 수집
   ↓
최소 코드로 재현
   ↓
GitHub에 정리
```

---

# 1. 전체 학습 목표

Part I을 마치면 다음을 할 수 있어야 합니다.

- Classification, Detection, Segmentation의 차이를 설명할 수 있다.
- 학습 데이터의 수와 다양성이 모델 결과에 영향을 주는 이유를 설명할 수 있다.
- Confidence score가 무엇인지 설명할 수 있다.
- Bounding box가 무엇인지 설명할 수 있다.
- Segmentation mask가 무엇인지 설명할 수 있다.
- 같은 이미지에 Classification, Detection, Segmentation을 각각 적용했을 때 결과가 어떻게 다른지 비교할 수 있다.
- GUI 환경에서 모델을 실행할 수 있다.
- 모델 결과를 이미지와 표로 기록할 수 있다.
- 최소한의 코드로 inference를 실행할 수 있다.
- 실험 결과를 GitHub repository에 정리할 수 있다.

---

# 2. 준비물

## 2.1 계정

다음 계정을 준비합니다.

- Google 계정
- GitHub 계정
- Roboflow 계정

권장:

- Hugging Face 계정

---

## 2.2 프로그램

필수:

- 웹 브라우저  
  - Chrome 권장
- GitHub Desktop 또는 Git
- 이미지 편집 프로그램  
  - Windows 사진 앱 정도면 충분

선택:

- Visual Studio Code
- Python 3.10 이상
- Google Colab

---

## 2.3 실습용 이미지

직접 촬영한 이미지를 사용하는 것을 권장합니다.

예:

```text
cup/
phone/
book/
```

각 class별로 최소 20~30장의 이미지를 준비합니다.

가능하면 다음 조건을 다양하게 포함합니다.

- 다른 배경
- 다른 거리
- 다른 각도
- 밝은 환경
- 어두운 환경
- 일부가 가려진 경우
- 주변에 다른 물체가 있는 경우

---

# 3. GitHub 저장소 준비

## 3.1 Repository 생성

GitHub에서 새로운 repository를 생성합니다.

예시 이름:

```text
practical-computer-vision-study
```

권장 구조:

```text
practical-computer-vision-study/

README.md

part1/
    week01-classification/
    week02-generalization/
    week03-object-detection/
    week04-segmentation/
```

---

## 3.2 공통 폴더 구조

각 주차는 다음 형식을 권장합니다.

```text
week01-classification/

README.md

images/
    train/
    test/

results/

code/

notes/
```

의미:

| 폴더 | 내용 |
|---|---|
| images | 실험 입력 이미지 |
| results | 모델 실행 결과 |
| code | Python 또는 JavaScript 코드 |
| notes | 추가 기록 |

---

# 4. Week 1 — Image Classification

---

## 4.1 목표

이번 실습에서는 가장 기본적인 컴퓨터 비전 문제인 **Image Classification**을 경험합니다.

질문은 다음과 같습니다.

> “이 이미지가 어떤 종류의 이미지인가?”

예를 들어 다음 사진이 있다고 합시다.

```text
[컵 사진]
```

모델은 다음처럼 답할 수 있습니다.

```text
Cup   0.96
Book  0.02
Phone 0.02
```

여기서 모델은 컵의 위치를 찾지는 않습니다.

이미지 전체를 하나의 class로 분류합니다.

---

## 4.2 핵심 개념

### Class

모델이 구분하려는 범주입니다.

예:

```text
Cup
Book
Phone
```

---

### Training

이미지를 모델에게 보여주고 패턴을 학습시키는 과정입니다.

```text
Training Images
      ↓
     Model
```

---

### Inference

학습이 끝난 모델에게 새로운 이미지를 보여주고 결과를 얻는 과정입니다.

```text
New Image
    ↓
  Model
    ↓
Prediction
```

---

### Confidence Score

모델이 각 class라고 판단한 정도입니다.

예:

```text
Cup   96%
Book   2%
Phone  2%
```

주의:

> Confidence 96%가 “96% 확률로 무조건 정답”이라는 뜻은 아닙니다.

모델 내부 출력에 기반한 상대적인 확신 정도로 이해하는 것이 좋습니다.

---

# 5. 실습 1 — Teachable Machine으로 이미지 분류

## 5.1 사이트 접속

브라우저에서 다음 사이트에 접속합니다.

```text
https://teachablemachine.withgoogle.com/
```

---

## 5.2 새 프로젝트 생성

1. **Get Started** 선택
2. **Image Project** 선택
3. 기본 이미지 모델 선택

화면에는 보통 다음과 같은 구조가 나타납니다.

```text
Class 1
Class 2
Class 3
...
```

---

## 5.3 Class 이름 변경

예:

```text
Class 1 → Cup
Class 2 → Book
Class 3 → Phone
```

가능하면 영어 이름을 권장합니다.

GitHub 파일명이나 코드에서 다루기 편하기 때문입니다.

---

## 5.4 학습 이미지 수집

두 가지 방법이 있습니다.

### 방법 A — Webcam

노트북 웹캠을 이용해서 이미지를 직접 수집합니다.

### 방법 B — Upload

미리 촬영한 이미지를 업로드합니다.

---

## 5.5 권장 이미지 수

처음 실습은 class별로 다음 정도면 충분합니다.

```text
20~30 images / class
```

예:

```text
Cup   30
Book  30
Phone 30
```

총 약 90장입니다.

---

## 5.6 좋은 데이터 수집 방법

다음처럼 동일한 물체를 다양한 조건에서 촬영합니다.

### 좋은 예

```text
Cup

- 정면
- 옆면
- 가까이
- 멀리
- 밝은 곳
- 어두운 곳
- 다른 책상 위
```

### 좋지 않은 예

```text
Cup

- 같은 위치
- 같은 배경
- 같은 각도
- 연속으로 거의 같은 사진 30장
```

---

# 6. 모델 학습

이미지 준비가 끝나면 **Train Model**을 선택합니다.

학습 동안 브라우저 창을 닫지 않습니다.

학습이 끝나면 Preview 영역에서 결과를 확인할 수 있습니다.

---

# 7. 첫 번째 테스트

학습에 사용하지 않은 새로운 사진을 준비합니다.

예:

```text
test01.jpg
test02.jpg
test03.jpg
```

각 사진을 모델에 넣고 결과를 기록합니다.

예:

| Image | True Class | Prediction | Confidence |
|---|---|---|---:|
| test01.jpg | Cup | Cup | 0.96 |
| test02.jpg | Book | Book | 0.91 |
| test03.jpg | Phone | Phone | 0.88 |

---

# 8. 실패 사례 찾기

이 실습의 핵심은 성공률을 자랑하는 것이 아닙니다.

**모델이 언제 실패하는지 찾는 것**이 더 중요합니다.

다음 조건을 일부러 만들어 봅니다.

---

## 8.1 배경 변경

학습:

```text
흰 책상 위 컵
```

테스트:

```text
검은 바닥 위 컵
```

---

## 8.2 조명 변경

학습:

```text
밝은 실내
```

테스트:

```text
어두운 실내
```

---

## 8.3 거리 변경

학습:

```text
가까이 촬영
```

테스트:

```text
멀리서 촬영
```

---

## 8.4 부분 가림

컵의 일부를 손이나 종이로 가려 봅니다.

---

## 8.5 여러 물체를 동시에 배치

```text
컵 + 책 + 휴대폰
```

한 이미지에 여러 class가 존재할 경우 classification 모델의 결과가 어떻게 되는지 확인합니다.

여기서 중요한 질문:

> Classification 모델은 이미지 안에 여러 객체가 있을 때 무엇을 기준으로 class를 선택할까?

---

# 9. Week 1 결과 정리

GitHub에 다음을 올립니다.

```text
week01-classification/

README.md

images/
    test01.jpg
    test02.jpg
    failure01.jpg

results/
    result01.png
    failure01.png
```

---

## 9.1 README 템플릿

```markdown
# Week 01 - Image Classification

## 1. Objective

Teachable Machine을 이용해 Cup, Book, Phone을 분류한다.

## 2. Classes

- Cup
- Book
- Phone

## 3. Dataset

각 class당 30장의 이미지를 사용하였다.

## 4. Test Results

| Image | True | Prediction | Confidence |
|---|---|---|---|
| test01 | Cup | Cup | 0.96 |
| test02 | Book | Book | 0.91 |

## 5. Failure Cases

어두운 환경에서는 Phone을 Book으로 잘못 분류하였다.

## 6. Discussion

학습 데이터가 모두 밝은 환경에서 촬영되었기 때문에
조명 변화에 대한 일반화가 충분하지 않았을 가능성이 있다.
```

---

# 10. Week 1 선택 과제 — 모델 Export

Teachable Machine에서는 학습한 모델을 export할 수 있습니다.

목표는 모델 구조를 직접 구현하는 것이 아니라,

> “GUI에서 만든 모델도 실제 프로그램에서 사용할 수 있다.”

는 것을 확인하는 것입니다.

---

# 11. Week 2 — Generalization과 Dataset Bias

---

## 11.1 목표

Week 1에서는 모델을 만드는 데 집중했습니다.

Week 2에서는 다음 질문을 다룹니다.

> “모델은 학습하지 않은 환경에서도 잘 작동하는가?”

이 능력을 **Generalization**이라고 부릅니다.

---

# 12. Generalization

예를 들어 컵 분류기를 다음 이미지로만 학습했다고 합시다.

```text
흰색 책상
밝은 조명
정면 촬영
```

테스트 환경이 다음처럼 바뀌면 어떻게 될까요?

```text
검은 책상
어두운 조명
옆면 촬영
```

성능이 떨어질 수 있습니다.

---

# 13. Dataset Bias

모델이 우리가 의도한 특징이 아니라 **우연히 같이 나타난 특징**을 학습하는 경우가 있습니다.

예:

```text
Cup 사진 → 항상 흰색 책상
Phone 사진 → 항상 검은색 책상
```

모델이 실제로는 물체보다 배경색을 학습할 수도 있습니다.

---

# 14. 실습 2 — 데이터 수에 따른 성능 변화

두 개의 모델을 만듭니다.

### Model A

```text
10 images / class
```

### Model B

```text
50 images / class
```

동일한 테스트 이미지를 사용합니다.

---

## 14.1 결과 기록

| Model | Train Images | Test Accuracy |
|---|---:|---:|
| A | 10/class | 67% |
| B | 50/class | 87% |

---

## 14.2 질문

- 데이터가 많으면 항상 성능이 좋아지는가?
- 거의 동일한 사진을 많이 추가해도 도움이 되는가?
- 데이터의 양과 다양성 중 무엇이 더 중요할까?

---

# 15. 실습 3 — 배경 다양성

두 모델을 비교합니다.

### Model A

모든 사진을 같은 책상에서 촬영

### Model B

다양한 배경에서 촬영

```text
책상
바닥
의자
창가
복도
```

테스트에서는 학습에 사용하지 않은 새로운 배경을 사용합니다.

---

## 15.1 결과 예시

| Model | Training Background | New Background Accuracy |
|---|---|---:|
| A | Same | 55% |
| B | Diverse | 86% |

---

# 16. 실습 4 — 조명 변화

다음 세 조건을 테스트합니다.

```text
Bright
Normal
Dark
```

예:

| Condition | Correct | Confidence |
|---|---|---:|
| Bright | Yes | 0.95 |
| Normal | Yes | 0.91 |
| Dark | No | 0.44 |

---

# 17. 실습 5 — Occlusion

물체를 일부 가립니다.

예:

```text
0% covered
25% covered
50% covered
75% covered
```

결과를 기록합니다.

---

# 18. 실습 6 — Rotation

이미지를 회전합니다.

```text
0°
45°
90°
180°
```

질문:

> 모델은 물체의 방향이 바뀌어도 같은 class로 인식할까?

---

# 19. Week 2 최종 실험표

모든 조건을 하나의 표로 정리합니다.

| Condition | Cup | Book | Phone |
|---|---|---|---|
| Normal | O | O | O |
| Dark | O | X | X |
| Different Background | O | O | X |
| 50% Occlusion | X | O | X |
| Rotated | O | X | O |

---

# 20. Week 2 핵심 결론

다음 문장을 학생 스스로 작성하도록 합니다.

> “우리 모델이 가장 취약한 조건은 ______ 이었다.”

> “그 이유는 학습 데이터에서 ______ 가 부족했기 때문이라고 생각한다.”

> “성능을 개선하려면 ______ 데이터를 추가할 필요가 있다.”

---

# 21. Week 3 — Object Detection

---

## 21.1 목표

Classification은 이미지 전체에 하나의 label을 붙입니다.

Object Detection은 이미지 안의 **객체 위치와 종류를 동시에 찾습니다.**

```text
Input Image

   ↓

Person  [x1, y1, x2, y2]
Car     [x1, y1, x2, y2]
Dog     [x1, y1, x2, y2]
```

---

# 22. Classification과 Detection 비교

## Classification

질문:

> 이 이미지의 주요 내용은 무엇인가?

결과:

```text
Dog
```

---

## Detection

질문:

> 이미지 안에 어떤 객체가 어디에 있는가?

결과:

```text
Dog      0.95
Person   0.92
Bicycle  0.88
```

각 객체에는 위치 정보가 포함됩니다.

---

# 23. Bounding Box

객체 주변을 둘러싸는 사각형입니다.

일반적으로 다음 좌표로 표현합니다.

```text
x_min
y_min
x_max
y_max
```

또는

```text
center_x
center_y
width
height
```

---

# 24. Confidence Threshold

Detection 모델은 많은 후보를 생성할 수 있습니다.

예:

```text
Dog 0.92
Cat 0.61
Chair 0.37
Bottle 0.21
```

Threshold를 0.5로 설정하면:

```text
Dog
Cat
```

만 남을 수 있습니다.

---

# 25. Roboflow 시작하기

브라우저에서:

```text
https://roboflow.com/
```

로그인합니다.

---

# 26. 실습 방법 A — 공개 모델 사용

처음 실습에서는 직접 모델을 학습하지 않습니다.

공개 dataset 또는 pretrained detection model을 사용합니다.

목표:

> “Object Detection이 어떤 결과를 생성하는지 이해한다.”

---

# 27. 입력 이미지 준비

최소 5장의 이미지를 준비합니다.

권장:

1. 사람이 많은 거리
2. 차량이 있는 도로
3. 책상 위 물건
4. 반려동물
5. 복잡한 실내

---

# 28. Detection 결과 확인

결과에는 일반적으로 다음이 포함됩니다.

```text
Class
Confidence
Bounding Box
```

예:

| Class | Confidence |
|---|---:|
| person | 0.95 |
| car | 0.91 |
| bicycle | 0.83 |

---

# 29. 실습 1 — Threshold 변경

같은 이미지에서 threshold를 변경합니다.

```text
0.2
0.5
0.8
```

---

## 29.1 관찰할 내용

Threshold가 낮을 때:

- 더 많은 객체가 나타남
- 잘못된 detection이 증가할 수 있음

Threshold가 높을 때:

- detection 수가 줄어듦
- 확신이 높은 객체만 남음
- 실제 객체도 사라질 수 있음

---

## 29.2 결과표

| Threshold | Detected Objects | False Detection |
|---:|---:|---:|
| 0.2 | 18 | 5 |
| 0.5 | 12 | 2 |
| 0.8 | 7 | 0 |

---

# 30. Precision과 Recall 직관 이해

복잡한 수식보다 상황으로 이해합니다.

---

## Precision

> 모델이 “찾았다”고 한 것 중 실제로 맞는 비율

---

## Recall

> 실제 존재하는 객체 중 모델이 찾아낸 비율

---

### 예

실제 자동차가 10대 있습니다.

모델이 8대를 찾았습니다.

그중 2개는 잘못된 detection입니다.

이 경우:

- 찾아낸 실제 자동차: 6
- 놓친 자동차: 4
- 잘못 찾은 객체: 2

Precision과 Recall이 서로 다른 의미를 갖는 이유를 이해합니다.

---

# 31. IoU

Bounding box끼리 얼마나 겹치는지를 측정합니다.

```text
IoU =
Intersection Area
-----------------
Union Area
```

직관:

```text
IoU = 1.0
→ 완전히 겹침

IoU = 0
→ 전혀 겹치지 않음
```

---

# 32. mAP

Object Detection에서 자주 사용되는 대표적인 평가 지표입니다.

초급 단계에서는 정확한 계산 공식을 외우지 않아도 됩니다.

다음 정도만 기억합니다.

> 여러 class와 confidence 조건에서 detection 품질을 종합적으로 평가하는 대표 지표

---

# 33. 실패 사례 찾기

다음 조건을 일부러 테스트합니다.

- 매우 작은 객체
- 부분적으로 가려진 객체
- 서로 겹친 객체
- 어두운 이미지
- 흐릿한 이미지
- 독특한 각도
- 사람이 매우 많은 장면

---

# 34. 실습 2 — 작은 객체

같은 객체를 가까이와 멀리서 촬영합니다.

예:

```text
Car close
Car medium
Car far
```

결과를 비교합니다.

---

# 35. 실습 3 — Occlusion

사람 또는 물체를 일부 가립니다.

```text
Full object
25% hidden
50% hidden
75% hidden
```

Detection 결과가 언제 사라지는지 기록합니다.

---

# 36. 실습 4 — Crowded Scene

사람이 많은 사진에서 다음을 확인합니다.

- 실제 사람 수
- detection된 사람 수
- 누락된 사람 수
- 중복 detection 여부

---

# 37. Week 3 GitHub 결과 구조

```text
week03-object-detection/

README.md

images/
    street.jpg
    crowded.jpg

results/
    threshold_02.jpg
    threshold_05.jpg
    threshold_08.jpg

code/
    inference.py
```

---

# 38. Week 3 최소 Python 코드

GUI 실습이 끝난 뒤 선택적으로 수행합니다.

먼저 설치:

```bash
pip install inference-sdk
```

예시:

```python
from inference_sdk import InferenceHTTPClient

client = InferenceHTTPClient(
    api_url="https://serverless.roboflow.com",
    api_key="YOUR_API_KEY"
)

result = client.infer(
    "image.jpg",
    model_id="YOUR_MODEL_ID"
)

print(result)
```

---

# 39. API Key 주의

API key는 GitHub에 올리지 않습니다.

나쁜 예:

```python
api_key="abcdef123456"
```

좋은 방법:

```text
.env
```

사용:

```python
import os
from dotenv import load_dotenv

load_dotenv()

API_KEY = os.getenv("ROBOFLOW_API_KEY")
```

`.gitignore`:

```text
.env
```

---

# 40. Week 3 README 템플릿

```markdown
# Week 03 - Object Detection

## Objective

Roboflow를 이용해 object detection을 실행하고
confidence threshold 변화에 따른 결과 차이를 분석한다.

## Input

street.jpg

## Experiment

Threshold:

- 0.2
- 0.5
- 0.8

## Results

| Threshold | Number of Detections |
|---|---:|
| 0.2 | 18 |
| 0.5 | 12 |
| 0.8 | 7 |

## Failure Cases

멀리 있는 사람과 부분적으로 가려진 사람의 detection이 자주 실패했다.

## Discussion

Threshold를 높이면 false detection이 감소했지만,
실제 객체를 놓치는 경우도 증가했다.
```

---

# 41. Week 4 — Image Segmentation

---

## 41.1 목표

Object Detection은 객체를 사각형으로 찾습니다.

Segmentation은 객체의 **실제 형태를 픽셀 단위로 구분**합니다.

---

# 42. Detection과 Segmentation 비교

## Detection

```text
[      Dog      ]
```

객체를 bounding box로 표시합니다.

---

## Segmentation

```text
Dog에 해당하는 픽셀만 mask
```

즉 객체의 윤곽을 더 정확하게 표현합니다.

---

# 43. Semantic Segmentation

각 픽셀을 class로 분류합니다.

예:

```text
Road
Sky
Building
Person
Car
```

자율주행 장면에서 자주 사용됩니다.

---

# 44. Instance Segmentation

같은 class의 서로 다른 객체를 각각 구분합니다.

예:

```text
Person 1
Person 2
Person 3
```

세 사람 모두 class는 Person이지만 서로 다른 instance입니다.

---

# 45. Segmentation Mask

각 픽셀이 특정 객체 또는 class에 속하는지 표현한 이미지입니다.

예:

```text
Original Image
      ↓
Segmentation Model
      ↓
Mask
```

---

# 46. Roboflow에서 Segmentation 실습

Week 3과 마찬가지로 처음에는 공개 dataset/model을 사용합니다.

준비 이미지:

- 사람
- 자동차
- 동물
- 제품
- 도로 장면

---

# 47. 실습 1 — Detection과 Segmentation 비교

같은 이미지를 두 방식으로 처리합니다.

```text
Image
 ├── Detection
 └── Segmentation
```

---

## 결과 비교표

| 항목 | Detection | Segmentation |
|---|---|---|
| 위치 | 가능 | 가능 |
| 객체 윤곽 | 대략적 | 정확 |
| 픽셀 수준 정보 | 없음 | 있음 |
| 계산량 | 상대적으로 적음 | 상대적으로 큼 |

---

# 48. 어떤 문제에 무엇을 사용할까?

다음 문제를 보고 적절한 방법을 선택합니다.

---

### 문제 1

> 사진에 자동차가 몇 대 있는가?

권장:

```text
Object Detection
```

---

### 문제 2

> 도로 전체 영역 중 자동차가 차지하는 면적은 얼마인가?

권장:

```text
Segmentation
```

---

### 문제 3

> 사람 각각의 정확한 외곽선을 분리하고 싶다.

권장:

```text
Instance Segmentation
```

---

### 문제 4

> 자율주행 이미지에서 도로, 보도, 하늘을 구분하고 싶다.

권장:

```text
Semantic Segmentation
```

---

# 49. 실습 2 — 겹친 객체

서로 겹쳐 있는 두 객체를 테스트합니다.

예:

```text
두 사람
겹친 차량
여러 과일
```

Detection과 Segmentation 결과를 비교합니다.

---

# 50. 실습 3 — 복잡한 윤곽

다음처럼 모양이 사각형으로 표현하기 어려운 물체를 선택합니다.

- 자전거
- 나뭇가지
- 사람
- 동물
- 의자

질문:

> Bounding box가 실제 객체 영역을 얼마나 많이 포함하고 있는가?

---

# 51. IoU in Segmentation

Segmentation에서도 IoU를 사용할 수 있습니다.

이번에는 box가 아니라 **mask 영역의 겹침**을 측정합니다.

```text
Prediction Mask
∩
Ground Truth Mask
```

---

# 52. Dice Score

Segmentation에서 자주 사용되는 또 다른 평가 지표입니다.

초급 단계에서는 다음 정도로 이해합니다.

> 예측 mask와 실제 mask가 얼마나 비슷한지를 측정하는 지표

---

# 53. Week 4 GitHub 결과 구조

```text
week04-segmentation/

README.md

images/
    person.jpg
    car.jpg
    bicycle.jpg

results/
    detection_person.jpg
    segmentation_person.jpg

    detection_car.jpg
    segmentation_car.jpg

code/
    inference.py
```

---

# 54. Part I 통합 비교

Part I이 끝난 뒤 반드시 다음 표를 작성합니다.

| Task | Input | Output | 대표 질문 |
|---|---|---|---|
| Classification | Image | Class | 무엇인가? |
| Detection | Image | Box + Class | 무엇이 어디 있는가? |
| Segmentation | Image | Pixel Mask | 정확히 어느 영역인가? |

---

# 55. 통합 실습

하나의 이미지를 선택합니다.

예:

```text
사람 + 자전거 + 자동차가 포함된 거리 사진
```

다음 세 가지 방법으로 처리합니다.

1. Classification
2. Detection
3. Segmentation

---

## 비교 질문

### Classification

> 이 이미지 전체의 대표적인 내용은 무엇이라고 판단하는가?

### Detection

> 어떤 객체들을 찾았는가?

### Segmentation

> 객체들의 실제 영역을 어느 정도 정확하게 구분했는가?

---

# 56. Part I 최종 미니 프로젝트

학생 2~3명이 한 팀이 되어 작은 실험을 수행합니다.

다음 중 하나를 선택합니다.

---

## 프로젝트 A — 환경 변화에 강한 분류기

목표:

```text
Cup / Book / Phone
```

분류 모델을 만들고 다음 조건을 테스트합니다.

- 밝기
- 배경
- 거리
- 각도
- 가림

---

## 프로젝트 B — 거리 이미지 Object Detection

거리 이미지 20장을 수집하고 다음을 분석합니다.

```text
Person
Car
Bus
Bicycle
```

조건별 detection 성능을 비교합니다.

---

## 프로젝트 C — Detection vs Segmentation

동일 이미지 10장에 대해

```text
Detection
vs
Segmentation
```

을 비교합니다.

---

## 프로젝트 D — 실패 사례 수집

목표:

> 모델을 잘 작동시키는 것이 아니라 의도적으로 실패 사례를 찾는다.

최소 20개의 실패 사례를 수집합니다.

분류:

```text
Lighting
Occlusion
Small Object
Unusual Angle
Background
Crowded Scene
```

---

# 57. 최종 제출물

각 팀은 다음을 제출합니다.

```text
README.md

images/

results/

code/

presentation/
```

README에는 최소한 다음 항목을 포함합니다.

---

## 1. Problem

어떤 문제를 해결하려 했는가?

---

## 2. Tool

어떤 도구를 사용했는가?

---

## 3. Dataset

어떤 데이터를 사용했는가?

---

## 4. Experiment

무엇을 바꾸면서 비교했는가?

---

## 5. Results

결과를 표와 이미지로 제시합니다.

---

## 6. Failure Cases

모델이 실패한 예를 최소 3개 포함합니다.

---

## 7. Discussion

왜 이런 결과가 나왔는지 설명합니다.

---

# 58. 좋은 GitHub 프로젝트의 조건

좋은 repository는 코드가 많다고 좋은 것이 아닙니다.

다음이 더 중요합니다.

```text
문제가 명확한가?

실험 조건이 명확한가?

결과가 재현 가능한가?

실패 사례가 포함되어 있는가?

결과를 해석했는가?
```

---

# 59. 권장 GitHub README 예시

```markdown
# Robust Image Classification Experiment

## Problem

배경과 조명 변화가 이미지 분류 성능에 미치는 영향을 분석한다.

## Tool

Google Teachable Machine

## Classes

- Cup
- Book
- Phone

## Experiment

다음 조건을 비교하였다.

1. Normal
2. Dark
3. Different Background
4. Occlusion

## Results

| Condition | Accuracy |
|---|---:|
| Normal | 93% |
| Dark | 68% |
| Background Change | 72% |
| Occlusion | 61% |

## Main Finding

조명보다 occlusion 상황에서 성능 감소가 가장 컸다.

## Possible Explanation

학습 이미지에는 가려진 물체가 거의 포함되어 있지 않았기 때문이라고 판단하였다.

## Future Work

부분 가림이 포함된 학습 데이터를 추가해 다시 실험한다.
```

---

# 60. Part I에서 반드시 기억할 핵심

## Classification

```text
What is this?
```

---

## Detection

```text
What objects are here and where are they?
```

---

## Segmentation

```text
Which pixels belong to each object?
```

---

# 61. 가장 중요한 연구 습관

모델을 한 번 실행한 결과만 보고 결론을 내리지 않습니다.

다음 순서를 습관화합니다.

```text
Baseline
   ↓
조건 변경
   ↓
결과 비교
   ↓
실패 분석
   ↓
가설
   ↓
다시 실험
```

---

# 62. 체크리스트

Part I을 끝낸 뒤 스스로 확인합니다.

- [ ] Teachable Machine에서 이미지 분류 모델을 만들 수 있다.
- [ ] 학습 데이터의 수와 다양성을 바꾸어 실험할 수 있다.
- [ ] Generalization의 의미를 설명할 수 있다.
- [ ] Dataset Bias의 간단한 사례를 설명할 수 있다.
- [ ] Roboflow에서 Object Detection 결과를 확인할 수 있다.
- [ ] Confidence threshold를 변경하고 결과 차이를 설명할 수 있다.
- [ ] Precision과 Recall의 차이를 직관적으로 설명할 수 있다.
- [ ] IoU가 무엇인지 설명할 수 있다.
- [ ] Segmentation과 Detection의 차이를 설명할 수 있다.
- [ ] Semantic Segmentation과 Instance Segmentation의 차이를 설명할 수 있다.
- [ ] 결과 이미지를 GitHub에 정리할 수 있다.
- [ ] API key를 GitHub에 공개하면 안 된다는 것을 알고 있다.
- [ ] 성공 사례뿐 아니라 실패 사례도 기록할 수 있다.

---

# 63. Part I 이후

Part II에서는 정적인 단일 이미지에서 한 단계 확장하여 다음 주제를 다룹니다.

```text
Pose Estimation
Object Tracking
Video Understanding
Depth Estimation
```

그리고 이후 Part III에서는

```text
CLIP
Image-Text Retrieval
Open Vocabulary Detection
VLM
VQA
```

로 확장합니다.

즉 전체 흐름은 다음과 같습니다.

```text
Image Recognition
      ↓
Spatial Understanding
      ↓
Video Understanding
      ↓
Vision + Language
      ↓
Multimodal Reasoning
```

---

# Appendix A. 추천 폴더 전체 구조

```text
practical-computer-vision-study/

README.md

part1/

    week01-classification/
        README.md
        images/
        results/
        code/

    week02-generalization/
        README.md
        images/
        results/

    week03-object-detection/
        README.md
        images/
        results/
        code/
            inference.py

    week04-segmentation/
        README.md
        images/
        results/
        code/

projects/

    team01/
    team02/
```

---

# Appendix B. `.gitignore` 예시

```text
.env
__pycache__/
*.pyc
.venv/
venv/

# large files
*.mp4
*.zip
```

영상 파일은 GitHub 용량 제한을 고려해 필요하면 링크로 제공하는 것이 좋습니다.

---

# Appendix C. `requirements.txt` 예시

Object Detection 실습에서 필요한 경우:

```text
inference-sdk
python-dotenv
```

---

# Appendix D. 실험 기록용 CSV 예시

```csv
image,true_class,prediction,confidence,condition
cup01.jpg,Cup,Cup,0.96,normal
cup02.jpg,Cup,Book,0.44,dark
phone01.jpg,Phone,Phone,0.91,normal
phone02.jpg,Phone,Book,0.51,occlusion
```

---

# Appendix E. 실습 보고서 평가 기준 예시

| 항목 | 배점 |
|---|---:|
| 문제 정의 | 10 |
| 실험 설계 | 20 |
| 데이터 구성 | 15 |
| 결과 정리 | 20 |
| 실패 사례 분석 | 20 |
| GitHub 정리 | 15 |
| 합계 | 100 |

실습 중심 스터디에서는 **코드 복잡도 자체는 평가하지 않는 것**을 권장합니다.

가장 중요한 것은 다음입니다.

> 무엇을 시험했는가?  
> 무엇이 달라졌는가?  
> 왜 그렇게 되었는가?  
> 다음 실험은 무엇인가?
