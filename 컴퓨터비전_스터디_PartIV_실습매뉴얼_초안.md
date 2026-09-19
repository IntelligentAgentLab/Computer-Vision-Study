# Practical Computer Vision Study — Part IV
## Modern Vision Applications 실습 매뉴얼

> **Part IV의 목표**  
> 앞선 파트에서 개별 비전 기술을 실습했다면, Part IV에서는 최근 컴퓨터 비전에서 많이 활용되는  
> **representation, 생성형 비전, 문서 이해, video-language, multimodal application**을 경험합니다.
>
> 이 파트에서는 “모델을 새로 학습”하기보다 **공개 모델과 인터페이스를 이용해 응용 문제를 구성하는 능력**에 초점을 둡니다.

---

# 0. Part IV 개요

권장 진행은 5주입니다.

| 주차 | 주제 | 기본 도구 | 핵심 결과 |
|---|---|---|---|
| Week 14 | Representation / Self-Supervised Learning | FiftyOne + 준비된 Colab | Embedding map |
| Week 15 | Generative Vision | Hugging Face Spaces | Prompt 비교 |
| Week 16 | Document AI | Hugging Face Spaces | OCR / DocQA 결과 |
| Week 17 | Video-Language Model | Hugging Face Spaces | Video 질문응답 |
| Week 18 | Multimodal Application | HF Spaces / Roboflow / Gradio 선택 | 작은 응용 prototype |

---

# 1. 학습 목표

Part IV가 끝나면 다음을 할 수 있어야 합니다.

- Representation과 embedding의 역할을 설명할 수 있다.
- 이미지 embedding을 2차원으로 시각화해 cluster와 outlier를 관찰할 수 있다.
- 생성형 이미지 모델에서 prompt 변화가 결과에 미치는 영향을 비교할 수 있다.
- OCR과 Document Question Answering의 차이를 설명할 수 있다.
- 문서 레이아웃 정보가 중요한 이유를 설명할 수 있다.
- Video-Language Model에 시간적 질문을 설계할 수 있다.
- 여러 비전 기능을 연결해 작은 multimodal application을 기획할 수 있다.

---

# 2. Week 14 — Representation과 Self-Supervised Learning

## 2.1 왜 Representation을 볼까?

이미지 모델은 최종 class만 만드는 것이 아니라 이미지의 특징을 내부 숫자 표현으로 바꿉니다.

```text
Image
  ↓
Encoder
  ↓
Embedding
```

비슷한 이미지가 embedding 공간에서 가까이 모이는지 관찰할 수 있습니다.

---

# 3. Self-Supervised Learning의 직관

전통적인 supervised learning:

```text
Image + Label
       ↓
Learning
```

Self-supervised learning:

```text
많은 Image
   ↓
이미지 자체에서 학습 신호 구성
   ↓
Representation 학습
```

이번 실습에서는 학습 과정을 직접 구현하지 않습니다.

이미 학습된 representation을 **관찰하고 활용**합니다.

---

# 4. 실습 데이터

이미지 50~100장을 준비합니다.

추천 구성:

```text
dogs       20
cats       20
cars       20
food       20
misc       20
```

또는 캠퍼스 사진:

```text
building
classroom
people
food
vehicle
```

---

# 5. FiftyOne 방식

FiftyOne App은 데이터셋을 시각적으로 탐색하고 embedding visualization을 통해 2차원 scatter plot에서 데이터 구조를 살펴볼 수 있습니다.

코딩을 최소화하려면 강사가 미리 Colab 또는 환경을 준비합니다.

학생은:

1. App 실행
2. Embeddings panel 열기
3. Cluster 관찰
4. Lasso로 영역 선택
5. 실제 이미지 확인
6. Outlier 찾기

에 집중합니다.

---

# 6. Embedding Plot 읽기

각 점:

```text
1 image
```

점들이 가까움:

```text
representation이 비슷함
```

멀리 떨어짐:

```text
representation이 다름
```

주의:

> 2D visualization은 고차원 embedding을 축소한 결과이므로 실제 공간을 완벽하게 보존하지는 않습니다.

---

# 7. 실습 1 — Cluster 찾기

질문:

- 같은 class가 모이는가?
- class가 달라도 시각적으로 비슷하면 가까운가?
- 배경이 비슷한 이미지끼리 모이는가?

스크린샷을 저장합니다.

---

# 8. 실습 2 — Outlier

Cluster에서 혼자 떨어진 이미지를 찾습니다.

가능한 이유:

- 잘못된 label
- 독특한 배경
- 흐릿한 이미지
- class와 다른 내용
- crop 문제

---

# 9. 실습 3 — CLIP vs Visual Representation

가능하면 강사가 두 종류의 embedding visualization을 준비합니다.

예:

```text
Visual-only representation
CLIP representation
```

질문:

> 텍스트 의미와 연결된 representation에서는 cluster 구조가 어떻게 달라지는가?

정확한 우열을 가리기보다 차이를 관찰합니다.

---

# 10. Week 14 결과물

```text
week14-representation/

README.md
dataset_summary.csv
embedding_plot.png
selected_cluster.png
outliers/
```

README에:

```text
가장 뚜렷한 cluster
예상 밖의 cluster
outlier 3개
가능한 이유
```

를 기록합니다.

---

# 11. Week 15 — Generative Vision

## 11.1 목표

이번 주에는 이미지를 분석하는 모델이 아니라 **이미지를 생성하거나 편집하는 모델**을 경험합니다.

주요 task:

```text
Text-to-Image
Image-to-Image
Image Editing
```

---

# 12. 분석형 Vision과 생성형 Vision

분석:

```text
Image → Label / Box / Caption
```

생성:

```text
Text → Image
Image + Instruction → Edited Image
```

---

# 13. 실습 도구

Hugging Face Spaces:

```text
image generation
text to image
image editing
```

가능하면 동일한 model/demo를 팀 전체가 사용합니다.

공개 Space는 운영 상태가 바뀔 수 있으므로 수업 직전에 실행 여부를 확인합니다.

---

# 14. Prompt 실험 설계

처음 prompt:

```text
a robot in a classroom
```

요소를 하나씩 추가합니다.

```text
a small robot in a classroom
a small robot in a university classroom
a small robot in a university classroom, realistic photo
a small robot in a university classroom, realistic photo, wide angle
```

한 번에 하나의 요소만 바꾸는 것이 좋습니다.

---

# 15. 실습 1 — Prompt 구체성

| Prompt | Subject | Background | Style | Overall Match |
|---|---|---|---|---|
| short | O | △ | - | Medium |
| detailed | O | O | O | High |

---

# 16. 실습 2 — 공간 관계

Prompt:

```text
a red cup to the left of a blue book
```

다음으로 변경:

```text
a blue book to the left of a red cup
```

관찰:

> 모델이 left/right 관계를 정확하게 반영하는가?

---

# 17. 실습 3 — Counting

```text
three red apples on a table
five cups on a desk
```

실제 생성된 개수를 확인합니다.

생성 모델도 counting에서 오류를 만들 수 있습니다.

---

# 18. 실습 4 — Image Editing

가능한 Space에서 입력 이미지를 업로드하고 지시합니다.

예:

```text
Remove the cup.
Change the background to a library.
Make the car blue.
```

평가:

- 요청한 부분만 바뀌었는가?
- 원래 보존되어야 할 영역까지 바뀌었는가?
- 새로운 artifact가 생겼는가?

---

# 19. 생성형 실습 기록

생성 이미지만 저장하지 말고 반드시 prompt도 저장합니다.

```csv
id,prompt,seed_or_setting,observation
01,"a robot in a classroom",,basic
02,"a small robot ...",,better background
```

가능한 경우 사용한 모델 이름과 설정도 기록합니다.

---

# 20. Week 16 — Document AI

## 20.1 목표

문서는 단순한 사진과 다릅니다.

문서에는:

- 글자
- 위치
- 표
- 제목
- 문단
- 레이아웃

정보가 함께 존재합니다.

---

# 21. OCR

OCR의 기본 목표:

```text
Image
  ↓
Text Recognition
  ↓
Extracted Text
```

예:

```text
영수증 이미지
→ "Total 15,000"
```

---

# 22. Document AI

Document AI는 OCR보다 더 넓습니다.

```text
Document Image
      ↓
Text + Layout + Visual Information
      ↓
Understanding
```

질문 예:

```text
What is the total amount?
Who issued this receipt?
What is the date?
Which item is most expensive?
```

---

# 23. 실습 문서 준비

개인정보가 없는 문서를 사용합니다.

추천:

- 직접 만든 영수증
- 공개 메뉴판
- 포스터
- 강의 슬라이드
- 논문 첫 페이지
- 공개 표/양식

사용 금지:

- 주민등록번호
- 학생 개인정보
- 금융정보
- 실제 민감 영수증

---

# 24. 실습 1 — OCR

Hugging Face Spaces 검색:

```text
OCR
document OCR
text recognition
```

문서 5개를 실행합니다.

결과:

| Document | Text Accuracy | Layout Preserved? | Error |
|---|---|---|---|
| simple | High | No | - |
| table | Medium | Partial | column order |
| poster | Medium | No | stylized text |

---

# 25. 실습 2 — Document Question Answering

검색:

```text
document question answering
document analysis
DocVQA
```

질문:

```text
What is the date?
What is the total?
Who is the author?
What is the title?
```

---

# 26. OCR vs Document QA

OCR:

> 문서에 어떤 글자가 있는가?

Document QA:

> 문서 내용과 구조를 바탕으로 질문에 답할 수 있는가?

---

# 27. 실습 3 — Layout 중요성

표 또는 2단 문서처럼 위치가 중요한 문서를 선택합니다.

질문:

```text
What value corresponds to item A?
```

단순 OCR text만 읽었을 때와 문서 QA 결과를 비교합니다.

---

# 28. 실습 4 — 문서 품질

같은 문서를 다음 형태로 만듭니다.

```text
Original
Blurred
Rotated
Low Contrast
Perspective Distorted
```

OCR/QA 결과 변화를 확인합니다.

---

# 29. Week 16 결과물

```text
week16-document-ai/

documents/
results/
ocr_results.csv
docqa_results.csv
README.md
```

---

# 30. Week 17 — Video-Language Model

## 30.1 목표

Part II의 Video Understanding을 한 단계 확장합니다.

이번에는 영상에 대해 자연어로 자유롭게 질문하고 설명을 생성하는 **Video-Language Model**을 중심으로 실습합니다.

---

# 31. 질문 유형

### Recognition

```text
What objects appear?
```

### Action

```text
What is the person doing?
```

### Temporal

```text
What happens before the person sits?
```

### Change

```text
What changes between the beginning and the end?
```

### Summary

```text
Summarize the video.
```

---

# 32. 실습용 영상

10~20초 이내로 준비합니다.

너무 긴 영상은 공개 demo의 처리 제한에 걸릴 수 있습니다.

추천:

- 물건 이동
- 간단한 요리 동작
- 사람이 방에 들어오고 나가는 장면
- 공 던지기
- 2~3단계 행동 sequence

---

# 33. 실습 1 — 질문 유형별 비교

영상 5개 × 질문 5개를 구성합니다.

```text
25 Video-QA pairs
```

결과:

| Type | Correct | Partially | Wrong |
|---|---:|---:|---:|
| Recognition | 5 | 0 | 0 |
| Action | 4 | 1 | 0 |
| Temporal | 2 | 2 | 1 |

---

# 34. 실습 2 — 중요 순간이 짧은 영상

아주 짧은 이벤트를 넣습니다.

예:

```text
5초 영상 중 0.5초 동안만 손을 흔듦
```

질문:

```text
Did the person wave?
```

모델이 놓치는지 확인합니다.

---

# 35. 실습 3 — Video Hallucination

없는 사건을 질문합니다.

```text
Q: What did the dog do?
```

영상에 개가 없다면:

좋은 답:

```text
No dog is visible.
```

문제 답:

```text
The dog ran across the room.
```

---

# 36. Week 18 — Multimodal Application

## 36.1 목표

이전까지는 각각의 모델 기능을 독립적으로 사용했습니다.

이번에는 **하나의 응용 문제를 정의하고 기존 도구를 조합**합니다.

새 모델을 학습할 필요는 없습니다.

---

# 37. Prototype 수준 정의

이 스터디에서 prototype은 다음 정도면 충분합니다.

```text
Input
  ↓
AI Model / Workflow
  ↓
Result
  ↓
사용자가 이해할 수 있는 Output
```

완전한 상용 서비스일 필요는 없습니다.

---

# 38. 선택 A — Roboflow Workflow

예:

```text
Video
 ↓
Detection
 ↓
Tracking
 ↓
Count
 ↓
Visualization
```

GUI 중심으로 연결합니다.

---

# 39. 선택 B — Hugging Face Space Duplicate

공개 Space의 구조와 라이선스를 확인한 후, 허용되는 경우 복제하여:

- 제목 변경
- 설명 변경
- 입력 예시 추가
- 출력 표시 개선

등의 작은 변형을 수행합니다.

---

# 40. 선택 C — Gradio 최소 코드

강사가 기본 template을 제공합니다.

학생은 모델 호출 부분과 설명만 바꿉니다.

개념:

```python
import gradio as gr

def predict(image, question):
    # prepared model call
    return answer

gr.Interface(
    fn=predict,
    inputs=["image", "text"],
    outputs="text"
).launch()
```

학생에게 중요한 것은 UI 코딩이 아니라:

```text
입력은 무엇인가?
모델은 무엇을 하는가?
출력은 사용자에게 어떻게 보여줄 것인가?
```

입니다.

---

# 41. 추천 응용 프로젝트

## A. Campus Visual Assistant

```text
Campus Image
+
Question
→ Answer
```

## B. Receipt Assistant

```text
Receipt Image
→ OCR / Document QA
→ Key Information
```

## C. Video Event Assistant

```text
Short Video
→ Summary
→ Questions
```

## D. Visual Search

```text
Image Collection
+
Natural Language Query
→ Top Images
```

## E. Detection + Description

```text
Image
→ Objects
→ VLM Description
```

---

# 42. Application Design Sheet

개발 전에 README에 작성합니다.

```markdown
## Problem

누가 어떤 상황에서 사용하는가?

## Input

이미지 / 영상 / 문서 / 질문

## AI Function

Detection / Retrieval / VQA / OCR 등

## Output

사용자에게 무엇을 보여주는가?

## Success Criteria

무엇이 되면 성공이라고 볼 것인가?

## Failure Cases

예상되는 실패 상황은?
```

---

# 43. Part IV GitHub 구조

```text
part4/

week14-representation/
week15-generative-vision/
week16-document-ai/
week17-video-language/
week18-multimodal-app/

project/
```

---

# 44. Part IV 체크리스트

- [ ] Embedding plot을 읽을 수 있다.
- [ ] Cluster와 outlier를 찾았다.
- [ ] 생성형 이미지 prompt를 단계적으로 바꿔 보았다.
- [ ] 생성 이미지의 counting/spatial 오류를 확인했다.
- [ ] OCR을 실행했다.
- [ ] Document QA를 실행했다.
- [ ] 문서 품질을 변화시켜 결과를 비교했다.
- [ ] Video-Language Model에 temporal question을 사용했다.
- [ ] 작은 multimodal application을 설계했다.
- [ ] 모델과 데이터의 라이선스/개인정보를 확인했다.
- [ ] 결과를 GitHub에 기록했다.

---

# 45. 다음 Part

Part V에서는 질문이 바뀝니다.

지금까지:

> 모델이 무엇을 할 수 있는가?

Part V:

> **이 결과를 얼마나 믿을 수 있는가?**

다음 주제를 다룹니다.

```text
Robustness
Domain Shift
Anomaly Detection
Continual Learning
Hallucination
Model Comparison
```

---

## 참고 공식 사이트

- Hugging Face Spaces: https://huggingface.co/spaces
- Hugging Face Image Captioning: https://huggingface.co/docs/transformers/tasks/image_captioning
- Hugging Face Document QA: https://huggingface.co/docs/transformers/tasks/document_question_answering
- FiftyOne: https://docs.voxel51.com/
- Roboflow: https://roboflow.com/
