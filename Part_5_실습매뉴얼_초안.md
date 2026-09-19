# Practical Computer Vision Study — Part V
## Model Reliability & Research Topics 실습 매뉴얼

> **Part V의 목표**  
> 좋은 AI 실습은 모델이 잘 작동하는 장면만 보여주는 것으로 끝나지 않습니다.
>
> 이번 파트의 핵심 질문은:
>
> > **“이 모델의 결과를 언제 믿을 수 있고, 언제 조심해야 하는가?”**
>
> 이를 위해 Robustness, Domain Shift, Anomaly Detection, Continual Learning, VLM Hallucination, Model Comparison을 실험합니다.

---

# 0. Part V 개요

권장 진행은 6주입니다.

| 주차 | 주제 | 기본 방법 | 결과 |
|---|---|---|---|
| Week 19 | Robustness | 기존 모델 + 변형 이미지 | 강건성 표 |
| Week 20 | Domain Shift | 서로 다른 domain 이미지 | domain별 성능 |
| Week 21 | Anomaly Detection | HF Spaces / 공개 demo | 정상/이상 비교 |
| Week 22 | Continual Learning | 강사 제공 Colab | Forgetting 관찰 |
| Week 23 | VLM Hallucination | VQA/VLM demo | 오류 taxonomy |
| Week 24 | Model Comparison | 직접 만든 benchmark | 비교 보고서 |

---

# 1. Part V 학습 목표

Part V를 마치면 다음을 할 수 있어야 합니다.

- Robustness가 무엇인지 설명할 수 있다.
- 입력 변화에 따른 모델 성능 변화를 체계적으로 비교할 수 있다.
- Domain Shift의 예를 들 수 있다.
- Anomaly Detection의 기본 문제 설정을 설명할 수 있다.
- Continual Learning과 Catastrophic Forgetting의 관계를 설명할 수 있다.
- VLM Hallucination을 유형별로 구분할 수 있다.
- 하나의 benchmark를 여러 모델에 동일하게 적용할 수 있다.
- 단순 평균 점수뿐 아니라 오류 유형과 실패 조건을 함께 분석할 수 있다.

---

# 2. Week 19 — Robustness

## 2.1 핵심 질문

> 입력 이미지가 조금 달라져도 모델 결과가 유지되는가?

예:

```text
Original
Blur
Dark
Crop
Rotate
Occlusion
```

사람에게는 같은 물체로 보이지만 모델 결과가 크게 바뀔 수 있습니다.

---

# 3. Robustness 실험의 기본 원칙

한 번에 하나의 조건만 바꿉니다.

나쁜 실험:

```text
어둡게 + 회전 + crop + blur를 한 번에 적용
```

좋은 실험:

```text
Original
↓
Brightness만 변경

Original
↓
Rotation만 변경
```

그래야 원인을 해석할 수 있습니다.

---

# 4. 대상 모델 선택

Part I~III에서 이미 사용한 모델 중 하나를 재사용합니다.

추천:

- Image Classification
- Object Detection
- Open Vocabulary Detection
- VQA

새로운 도구를 배우기보다 **기존 모델을 더 깊게 분석**하는 것이 목적입니다.

---

# 5. 실습 1 — Brightness

이미지를 세 수준으로 준비합니다.

```text
Bright
Normal
Dark
```

분류 예:

| Condition | Prediction | Confidence |
|---|---|---:|
| Normal | Cup | 0.95 |
| Dark | Cup | 0.64 |
| Very Dark | Book | 0.41 |

---

# 6. 실습 2 — Blur

다음 수준을 만듭니다.

```text
Original
Light Blur
Medium Blur
Strong Blur
```

간단한 온라인 이미지 편집기 또는 기본 사진 편집 기능을 사용해도 됩니다.

---

# 7. 실습 3 — Rotation

```text
0°
15°
45°
90°
180°
```

결과를 기록합니다.

---

# 8. 실습 4 — Crop

객체가 점점 잘리도록 만듭니다.

```text
100%
75%
50%
25% visible
```

---

# 9. 실습 5 — Occlusion

객체를 네모 또는 다른 물체로 일부 가립니다.

```text
0%
25%
50%
75%
```

---

# 10. Robustness Matrix

| Image | Original | Dark | Blur | Rotate | Occlusion |
|---|---|---|---|---|---|
| img01 | O | O | X | O | X |
| img02 | O | X | O | O | X |
| img03 | O | O | O | X | △ |

---

# 11. Robustness Score — 단순 버전

정교한 논문 지표가 아니어도 됩니다.

예:

```text
5개 변형 중 4개에서 정답 유지

Robustness = 4/5
```

팀 내부 비교용 지표로 사용합니다.

---

# 12. Week 20 — Domain Shift

## 12.1 개념

학습 또는 주로 경험한 데이터와 실제 사용 데이터의 분포가 달라지는 현상을 다룹니다.

예:

```text
Training / Familiar Domain:
낮에 찍은 일반 사진

New Domain:
야간 CCTV
```

---

# 13. Domain 예시

동일한 객체를 다른 domain에서 준비합니다.

```text
Real Photo
Sketch
Cartoon
Night
CCTV
Low Resolution
Rain / Fog
```

---

# 14. 실습 1 — Photo vs Drawing

분류 또는 CLIP zero-shot 모델을 사용합니다.

예:

```text
dog photo
dog sketch
dog cartoon
```

동일 class에 대한 결과를 비교합니다.

---

# 15. 실습 2 — Day vs Night

Object Detection에 권장합니다.

```text
day street
night street
```

비교:

- 사람 detection
- 자동차 detection
- confidence
- 누락 객체

---

# 16. 실습 3 — 일반 사진 vs CCTV 스타일

가능하면 공개된 예시 또는 직접 낮은 해상도/고정 카메라 느낌으로 만든 이미지를 사용합니다.

질문:

> 일상 사진에서 잘 되던 모델이 CCTV 영상에서도 같은 수준으로 작동하는가?

---

# 17. Domain 결과표

| Domain | Accuracy / Success | Common Error |
|---|---:|---|
| Photo | 90% | - |
| Sketch | 65% | class confusion |
| Cartoon | 70% | unusual shapes |
| Night | 55% | missed objects |

---

# 18. Domain Adaptation은 무엇인가?

이번 실습에서는 직접 구현하지 않아도 됩니다.

개념:

> 새로운 domain에서 성능이 떨어질 때, 그 domain에 더 잘 적응하도록 모델 또는 학습 과정을 조정하는 것.

Part V에서는 먼저 **문제를 발견하고 측정하는 능력**에 집중합니다.

---

# 19. Week 21 — Anomaly Detection

## 19.1 문제 설정

일반 classification:

```text
Normal
Scratch
Broken
Missing Part
```

모든 이상 유형을 미리 학습.

Anomaly Detection:

```text
주로 Normal을 이해
↓
Normal과 많이 다른 sample을 이상으로 표시
```

---

# 20. 응용 사례

- 제조 불량
- 표면 결함
- 구조물 이상
- 의료 영상의 이상 패턴
- 데이터 품질 검사

학부 실습에서는 산업 이미지를 주로 사용합니다.

---

# 21. 실습 데이터

간단한 자체 데이터 예:

```text
정상 컵 20장
금이 간 컵 또는 일부가 가려진 컵 5장
```

또는 공개 anomaly demo의 예제를 사용합니다.

---

# 22. Hugging Face Spaces

검색:

```text
anomaly detection
visual anomaly detection
industrial anomaly
```

업로드를 지원하는 공개 demo를 사용합니다.

---

# 23. 실습 1 — 명확한 이상

```text
Normal object
vs
Clearly damaged / changed object
```

결과를 기록합니다.

---

# 24. 실습 2 — 미세한 이상

예:

```text
작은 스크래치
작은 얼룩
작은 부품 누락
```

질문:

> 이상이 작아질수록 모델 점수는 어떻게 변하는가?

---

# 25. False Positive와 False Negative

## False Positive

정상인데 이상으로 판단.

## False Negative

이상인데 정상으로 판단.

산업 현장에서는 두 오류의 비용이 다를 수 있습니다.

---

# 26. 결과표

| Image | True | Prediction | Anomaly Score | Result |
|---|---|---|---:|---|
| n01 | Normal | Normal | 0.12 | Correct |
| a01 | Anomaly | Anomaly | 0.88 | Correct |
| a02 | Anomaly | Normal | 0.31 | False Negative |

---

# 27. Week 22 — Continual Learning

## 27.1 왜 별도 방식이 필요한가?

Continual Learning은 단순 GUI demo로 핵심 현상을 보여주기 어렵습니다.

따라서 이번 주만은:

> **강사가 준비한 Colab을 학생들이 실행하고 결과를 변경하는 방식**

을 권장합니다.

학생들이 모델 코드를 작성하는 것이 목표가 아닙니다.

---

# 28. Continual Learning의 핵심 질문

> 모델이 새로운 task를 배우면서 이전 task를 잊지 않을 수 있는가?

---

# 29. Catastrophic Forgetting

예:

```text
Task A:
Cat vs Dog
   ↓
Training

Task B:
Car vs Bus
   ↓
Training

다시 Task A 평가
```

Task B를 학습한 뒤 Task A 성능이 크게 떨어지는 현상을 관찰합니다.

---

# 30. 가장 쉬운 실습 설계

강사가 사전에 Notebook을 준비합니다.

예:

```text
Task 1: MNIST digits 0,1
Task 2: digits 2,3
Task 3: digits 4,5
```

또는 작은 이미지 dataset을 사용합니다.

학생이 실행할 것은:

```text
Run All
→ Task별 accuracy 기록
→ 학습 순서 변경
→ 결과 비교
```

---

# 31. 실험표

| Stage | Task 1 Acc | Task 2 Acc | Task 3 Acc |
|---|---:|---:|---:|
| after Task 1 | 95 | - | - |
| after Task 2 | 68 | 93 | - |
| after Task 3 | 51 | 70 | 91 |

핵심:

> 새로운 task의 성능만 보지 말고 이전 task가 얼마나 유지되는지 확인합니다.

---

# 32. Continual Learning 전략 개념

구현은 선택 사항입니다.

대표 아이디어를 직관적으로만 봅니다.

### Replay

이전 데이터를 일부 다시 학습.

### Regularization

중요한 기존 파라미터가 너무 크게 바뀌지 않도록 제한.

### Prompt / Parameter-efficient Approach

새로운 task에 작은 추가 파라미터를 사용.

---

# 33. Week 22 과제

코드 작성 대신 다음을 제출합니다.

- 학습 순서
- 각 단계 accuracy
- forgetting graph
- 가장 많이 잊은 task
- 왜 이런 현상이 생겼다고 생각하는지
- replay를 넣었을 때 결과가 어떻게 달라지는지

---

# 34. Week 23 — VLM Hallucination

## 34.1 정의

VLM이 이미지에 근거하지 않은 내용을 그럴듯하게 만들어내는 현상을 실험합니다.

중요:

> 문장이 자연스럽다고 해서 이미지에 근거한 사실이라는 뜻은 아닙니다.

---

# 35. Hallucination Benchmark

Part III의 VQA dataset을 재사용할 수 있습니다.

추가로 **함정 질문**을 만듭니다.

---

# 36. 유형 1 — Non-existent Object

이미지에 자동차가 없습니다.

```text
Q: What color is the car?
```

평가:

```text
Correct refusal / correction
vs
Hallucinated color
```

---

# 37. 유형 2 — Wrong Premise

```text
Q: Why is the man holding an umbrella?
```

실제로 umbrella가 없음.

---

# 38. 유형 3 — Counting

실제 6명.

```text
Q: How many people are there?
```

모델이 4명, 5명, 7명 등으로 답하는지 기록합니다.

---

# 39. 유형 4 — Spatial Relation

```text
What is to the left of the chair?
Is the cup behind the laptop?
```

---

# 40. 유형 5 — Text in Image

간판, 메뉴, 문서 등에서 실제 글자를 잘못 읽는 경우.

---

# 41. 유형 6 — Over-interpretation

단순 이미지에서 의도나 감정을 과도하게 추론하는 경우.

예:

```text
Why is the person angry?
```

표정만 보고 확정하기 어려운 상황이라면 답변이 과도한 추론인지 살펴봅니다.

---

# 42. Hallucination 결과표

| ID | Type | Question | Ground Truth | Answer | Hallucination? |
|---|---|---|---|---|---|
| 01 | absent object | car color? | no car | red | Yes |
| 02 | count | people? | 6 | 5 | Error |
| 03 | spatial | left object? | bag | bag | No |

---

# 43. Hallucination Rate — 단순 지표

```text
Hallucination Rate
=
hallucinated answers / hallucination test questions
```

모델 비교용으로 사용합니다.

단, open-ended 질문에서는 평가 기준을 먼저 정해야 합니다.

---

# 44. 질문 작성 시 주의

결과를 보고 질문을 바꾸지 않습니다.

권장:

```text
1. 질문 목록 확정
2. Ground Truth 확정
3. 모델 A 실행
4. 모델 B 실행
5. 결과 비교
```

---

# 45. Week 24 — Model Comparison

## 45.1 목표

최종 주에는 지금까지 만든 작은 benchmark를 여러 모델에 동일하게 적용합니다.

핵심은 “최고 모델 뽑기”가 아니라:

> 어떤 모델이 어떤 유형에서 강하고 약한지 관찰하는 것

입니다.

---

# 46. Benchmark 선택

다음 중 하나를 선택합니다.

### Option A — Classification Robustness

```text
20 images × 5 perturbations
```

### Option B — Detection Domain Shift

```text
day / night / sketch / CCTV
```

### Option C — VQA

```text
50 QA pairs
```

### Option D — Hallucination

```text
30 trap questions
```

---

# 47. 동일 조건 원칙

모델 비교에서 가장 중요합니다.

같게 유지:

- 입력 데이터
- 질문
- prompt
- 평가 기준
- 결과 기록 형식

모델만 변경합니다.

---

# 48. 정량 + 정성 평가

## 정량

예:

```text
Accuracy
Detection count
Hallucination rate
```

## 정성

예:

- 답변이 지나치게 장황함
- 불확실할 때도 단정함
- 작은 객체를 자주 놓침
- 공간 관계에 취약함

둘을 함께 기록합니다.

---

# 49. VQA 평가 Rubric 예시

Open-ended answer는 다음처럼 단순 rubric을 사용할 수 있습니다.

```text
2 = 핵심 사실이 정확
1 = 부분적으로 맞음
0 = 틀림 / 이미지 근거 없음
```

평가 기준을 모델 결과를 보기 **전에** 정합니다.

---

# 50. 결과표 예시

| Category | Model A | Model B | Model C |
|---|---:|---:|---:|
| Object | 90 | 88 | 92 |
| Count | 55 | 63 | 58 |
| Spatial | 61 | 72 | 60 |
| Hallucination rate | 18 | 12 | 20 |

표는 결과를 요약하는 용도이며, 반드시 실패 사례도 같이 봅니다.

---

# 51. Error Taxonomy

최종 프로젝트에서 다음과 같이 오류 유형을 정의할 수 있습니다.

```text
Recognition Error
Attribute Error
Counting Error
Spatial Error
Temporal Error
OCR Error
Hallucination
Uncertain / Ambiguous
```

각 오류의 실제 이미지 예시를 함께 보여줍니다.

---

# 52. Part V 최종 프로젝트

## A. VLM Hallucination Benchmark

가장 추천하는 연구형 프로젝트입니다.

```text
50 Images
×
5 Questions
=
250 QA items
```

2~3개 모델을 비교합니다.

---

## B. Robustness Benchmark

원본 이미지와 변형 이미지를 비교합니다.

```text
Original
Brightness
Blur
Rotation
Crop
Occlusion
```

---

## C. Domain Shift Study

같은 class/object를 여러 domain에서 테스트합니다.

```text
Photo
Sketch
Night
CCTV
```

---

## D. Continual Learning Demo

강사 제공 Notebook을 기반으로:

```text
Sequential Training
vs
Replay
```

를 비교합니다.

---

# 53. 연구형 README 구조

```markdown
# Project Title

## 1. Research Question

## 2. Model / Tool

## 3. Dataset

## 4. Experimental Design

## 5. Evaluation Criteria

## 6. Results

## 7. Failure Cases

## 8. Error Taxonomy

## 9. Discussion

## 10. Limitations

## 11. Future Work
```

---

# 54. 좋은 결론과 나쁜 결론

## 나쁜 결론

> Model A가 최고다.

작은 실험만으로 일반적인 우열을 단정하기 어렵습니다.

## 좋은 결론

> 본 실험의 50개 이미지에서는 Model A가 counting 질문보다 object recognition 질문에서 더 안정적인 결과를 보였다.

실험 범위를 명확히 제한합니다.

---

# 55. Reproducibility Checklist

GitHub에 다음을 남깁니다.

- [ ] 사용 모델 이름
- [ ] 실행 날짜
- [ ] 입력 이미지
- [ ] 질문/prompt
- [ ] 설정값
- [ ] 결과 CSV
- [ ] 평가 기준
- [ ] 오류 유형
- [ ] 대표 실패 이미지
- [ ] 코드 또는 사용 방법

공개 서비스는 시간이 지나면서 모델이 변경될 수 있으므로 실행 날짜를 기록하는 것이 좋습니다.

---

# 56. Part V GitHub 구조

```text
part5/

week19-robustness/
week20-domain-shift/
week21-anomaly/
week22-continual-learning/
week23-hallucination/
week24-model-comparison/

final-project/
```

---

# 57. Part V 체크리스트

- [ ] 입력 변형에 따른 robustness를 측정했다.
- [ ] Domain Shift 사례를 구성했다.
- [ ] domain별 오류 차이를 기록했다.
- [ ] anomaly detection을 실행했다.
- [ ] false positive와 false negative를 구분했다.
- [ ] catastrophic forgetting을 관찰했다.
- [ ] VLM hallucination 질문을 설계했다.
- [ ] ground truth를 모델 실행 전에 작성했다.
- [ ] 동일 benchmark로 여러 모델을 비교했다.
- [ ] 정량 결과와 정성 오류 분석을 함께 작성했다.
- [ ] 실험 한계를 명확하게 기록했다.
- [ ] 재현 가능한 형태로 GitHub에 업로드했다.

---

# 58. 전체 스터디의 최종 메시지

이 스터디의 목적은 특정 라이브러리를 외우는 것이 아닙니다.

전체 과정에서 반복한 패턴은 다음입니다.

```text
Problem
   ↓
Use a Model
   ↓
Design an Experiment
   ↓
Collect Results
   ↓
Find Failures
   ↓
Explain
   ↓
Try Again
```

가장 중요한 질문은 항상 같습니다.

> **“모델이 답을 냈는가?”가 아니라 “왜 이런 답을 냈고, 언제 이 답을 믿을 수 있는가?”**

---

## 참고 공식 사이트

- Hugging Face Spaces: https://huggingface.co/spaces
- Hugging Face Visual Question Answering: https://huggingface.co/docs/transformers/tasks/visual_question_answering
- FiftyOne: https://docs.voxel51.com/
- Roboflow: https://roboflow.com/
