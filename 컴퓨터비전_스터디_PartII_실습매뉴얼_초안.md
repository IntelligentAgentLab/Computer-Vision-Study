# Practical Computer Vision Study — Part II
## Vision in the Real World 실습 매뉴얼

> **Part II의 목표**  
> Part I에서 한 장의 이미지에 대해 Classification, Detection, Segmentation을 경험했다면,  
> Part II에서는 **사람의 자세, 시간의 흐름, 움직이는 객체, 3차원 공간 정보**로 확장합니다.
>
> 기본 원칙은 계속 동일합니다.
>
> **GUI First → Experiment → Failure Analysis → Optional Code → GitHub**

---

# 0. Part II 개요

Part II에서는 다음 네 가지 주제를 다룹니다.

1. **Pose Estimation / Pose Classification**
2. **Object Tracking**
3. **Depth Estimation**
4. **Video Understanding**

권장 진행은 4주입니다.

| 주차 | 주제 | 기본 도구 | 주요 결과물 |
|---|---|---|---|
| Week 5 | Pose Estimation | Teachable Machine | 자세 분류 결과 |
| Week 6 | Object Tracking | Roboflow Workflows | Tracking 영상 |
| Week 7 | Depth Estimation | Hugging Face Spaces | Depth map |
| Week 8 | Video Understanding | Hugging Face Spaces | Video QA 결과표 |

---

# 1. Part II 학습 목표

Part II를 마치면 다음을 할 수 있어야 합니다.

- 이미지 분류와 자세 인식의 차이를 설명할 수 있다.
- 사람의 관절 위치를 이용해 자세를 표현하는 개념을 이해할 수 있다.
- Detection과 Tracking의 차이를 설명할 수 있다.
- Tracking ID와 ID switch의 의미를 설명할 수 있다.
- 한 장의 2D 이미지에서 상대적 깊이를 추정하는 개념을 이해할 수 있다.
- 영상에서는 단일 프레임뿐 아니라 **시간 순서**가 중요하다는 점을 설명할 수 있다.
- 영상 모델의 실패 사례를 조건별로 정리할 수 있다.
- 이미지와 동영상 결과를 GitHub에 재현 가능한 형태로 기록할 수 있다.

---

# 2. 준비물

## 필수

- Chrome 또는 Edge
- Google 계정
- Teachable Machine
- Roboflow 계정
- Hugging Face 계정
- GitHub 계정
- 스마트폰 또는 웹캠

## 권장

- 5~15초 길이의 짧은 MP4 영상 여러 개
- 사람 1명이 등장하는 영상
- 사람 여러 명이 서로 교차하는 영상
- 실내/실외 사진
- 깊이 차이가 분명한 사진

---

# 3. 개인정보와 촬영 시 주의

Pose와 Tracking 실습에서는 사람이 등장하는 이미지와 영상을 사용하게 됩니다.

권장 원칙:

- 본인 또는 실습 참여에 동의한 사람만 촬영합니다.
- 불특정 다수의 얼굴이 선명하게 등장하는 영상을 공개 저장소에 업로드하지 않습니다.
- 필요하면 얼굴을 가리거나 공개 저장소에는 결과 화면만 올립니다.
- 학교나 연구실 내부 영상에 개인정보가 포함되지 않는지 확인합니다.
- GitHub에 올리기 전에 영상의 공개 가능 여부를 다시 확인합니다.

---

# 4. Week 5 — Pose Estimation / Pose Classification

## 4.1 목표

이번 주에는 컴퓨터가 사람의 자세를 어떻게 구분할 수 있는지 경험합니다.

예를 들어 다음 세 자세를 구분한다고 합시다.

```text
Standing
Arms Up
Squat
```

이미지 전체를 직접 분류하는 대신, 사람의 신체 관절 위치를 이용해 자세를 표현할 수 있습니다.

---

# 5. 핵심 개념 — Keypoint

Pose Estimation에서는 사람의 주요 관절 위치를 점으로 표현합니다.

예:

```text
머리
어깨
팔꿈치
손목
골반
무릎
발목
```

이러한 점을 **keypoint**라고 합니다.

개념적으로:

```text
Image
  ↓
Human Detection
  ↓
Keypoints
  ↓
Pose
```

---

# 6. Pose Estimation과 Pose Classification

두 개념을 구분합니다.

## Pose Estimation

> 사람의 관절 위치를 찾는 것

결과 예:

```text
Left Shoulder : (x, y)
Right Shoulder: (x, y)
Left Elbow    : (x, y)
...
```

## Pose Classification

> 추정된 자세를 우리가 정한 class로 분류하는 것

예:

```text
Arms Up : 0.95
Standing: 0.04
Squat   : 0.01
```

Teachable Machine은 이 두 과정을 매우 쉽게 체험할 수 있게 해 줍니다.

---

# 7. 실습 1 — Teachable Machine Pose Project

접속:

```text
https://teachablemachine.withgoogle.com/
```

진행:

1. **Get Started**
2. **Pose Project**
3. 자세 class 생성
4. 웹캠 또는 파일로 데이터 수집
5. Train Model
6. 실시간 테스트

---

# 8. 추천 자세 Class

처음에는 구분이 분명한 3개 class를 권장합니다.

```text
Class 1: Standing
Class 2: Arms Up
Class 3: Squat
```

처음부터 다음처럼 미세한 차이는 피합니다.

```text
팔 10도 위
팔 20도 위
팔 30도 위
```

---

# 9. 데이터 수집 방법

각 자세마다 여러 예시를 수집합니다.

권장:

```text
50~100 frames / class
```

웹캠으로 짧게 움직이면서 여러 프레임을 수집하면 쉽게 확보할 수 있습니다.

중요:

- 완전히 같은 자세만 반복하지 않습니다.
- 몸의 위치를 조금씩 바꿉니다.
- 카메라와의 거리를 조금씩 바꿉니다.
- 좌우 위치를 바꿉니다.

---

# 10. 실습 2 — 거리 변화

같은 자세를 다음 거리에서 테스트합니다.

```text
Near
Medium
Far
```

기록 예:

| Distance | Prediction | Confidence | Correct? |
|---|---|---:|---|
| Near | Arms Up | 0.97 | O |
| Medium | Arms Up | 0.91 | O |
| Far | Standing | 0.52 | X |

질문:

> 사람이 작게 보일수록 왜 자세 인식이 어려워질 수 있을까?

---

# 11. 실습 3 — 카메라 방향 변화

학습은 정면에서만 수행합니다.

그 후:

```text
Front
45 degrees
Side
Back
```

을 테스트합니다.

결과를 비교합니다.

핵심 질문:

> 정면 데이터만 학습하면 옆모습에도 잘 작동할까?

---

# 12. 실습 4 — 사람 바꾸기

가능하면 두 명 이상이 실습합니다.

```text
Training: Student A
Testing : Student B
```

확인:

- 체형 차이
- 옷차림
- 키
- 동작 스타일

에 따라 성능이 달라지는지 관찰합니다.

---

# 13. Pose 실습의 실패 사례

다음을 일부러 테스트합니다.

- 몸 일부가 화면 밖으로 나감
- 팔이 몸에 가려짐
- 카메라와 너무 가까움
- 매우 어두운 환경
- 두 명이 동시에 등장
- 느슨하게 비슷한 자세
- 앉은 자세와 squat처럼 경계가 애매한 class

---

# 14. Week 5 GitHub 구조

```text
week05-pose/

README.md

images/
    pose_examples/

results/
    normal.png
    side_view.png
    far_distance.png

data/
    experiment.csv
```

`experiment.csv` 예:

```csv
person,distance,view,true_pose,prediction,confidence
A,near,front,arms_up,arms_up,0.97
A,far,front,arms_up,standing,0.52
B,medium,side,squat,squat,0.72
```

---

# 15. Week 6 — Object Tracking

## 15.1 목표

Detection은 한 프레임에서 객체를 찾습니다.

Tracking은 여러 프레임에 걸쳐 **같은 객체를 같은 객체로 유지**하려고 합니다.

```text
Frame 1: Person ID 1
Frame 2: Person ID 1
Frame 3: Person ID 1
```

---

# 16. Detection vs Tracking

## Detection

매 프레임 독립적으로:

```text
Person
Person
Car
```

## Tracking

시간 연결:

```text
Person #1
Person #2
Car #3
```

다음 프레임에서도 같은 ID를 유지하려고 합니다.

---

# 17. Tracking ID

Tracking 모델은 객체마다 임시 ID를 부여합니다.

예:

```text
Person 12
Person 18
Car 23
```

이 ID는 주민번호 같은 영구 식별자가 아닙니다.

단지 해당 영상 안에서 객체의 이동 경로를 연결하기 위한 번호입니다.

---

# 18. ID Switch

Tracking 중 같은 사람이 갑자기 다른 ID를 받는 경우가 있습니다.

예:

```text
Frame 1: Person #3
Frame 2: Person #3
Frame 3: Person #7
```

이를 **ID switch**라고 부릅니다.

---

# 19. Roboflow Workflows 기본 흐름

Roboflow의 Workflow 환경에서는 여러 vision 단계를 블록 형태로 연결할 수 있습니다.

개념적으로:

```text
Video Input
    ↓
Object Detection
    ↓
Tracker
    ↓
Visualization
    ↓
Output
```

UI 이름은 서비스 업데이트에 따라 조금 달라질 수 있으므로, 핵심은 **Detection 결과를 Tracker에 연결**하는 것입니다.

---

# 20. 실습용 영상 준비

각 팀이 다음 영상을 준비합니다.

### Video A — Easy

- 사람 1명
- 5~10초
- 가림 없음

### Video B — Crossing

- 사람 2명 이상
- 서로 교차

### Video C — Occlusion

- 사람이 잠시 물체 뒤로 숨음

### Video D — Crowded

- 가능한 경우 여러 사람이 등장

---

# 21. 실습 1 — 기본 Tracking

Roboflow에서 다음 파이프라인을 구성합니다.

```text
Input Video
  ↓
Person Detection
  ↓
Object Tracker
  ↓
Bounding Box + Track ID
```

실행 후 결과 영상을 저장합니다.

---

# 22. Tracking 관찰표

| Video | Objects | ID Switch | Lost Track | Comment |
|---|---:|---:|---:|---|
| Easy | 1 | 0 | 0 | stable |
| Crossing | 2 | 2 | 0 | IDs changed |
| Occlusion | 1 | 1 | 1 | lost behind object |

정확한 표준 평가 지표가 아니라도 좋습니다.

초급 실습에서는 **무엇이 일어났는지를 눈으로 관찰해 기록**하는 것이 목적입니다.

---

# 23. 실습 2 — 교차 상황

두 사람이 서로 지나가도록 촬영합니다.

확인:

- ID가 유지되는가?
- 서로 ID가 바뀌는가?
- 한 사람을 놓치는가?
- 두 사람을 하나로 합쳐서 추적하는가?

---

# 24. 실습 3 — Occlusion

사람이 기둥이나 문 뒤로 1~2초 가려졌다가 다시 나오도록 합니다.

질문:

> 다시 등장했을 때 기존 ID를 유지하는가?

---

# 25. 실습 4 — 빠른 움직임

다음 두 영상을 비교합니다.

```text
Slow Walking
Fast Running
```

관찰:

- Bounding box 안정성
- Track 유지 시간
- ID switch 발생 여부

---

# 26. Tracking 결과를 GitHub에 올릴 때

대용량 MP4를 그대로 Git에 올리는 것은 피하는 것이 좋습니다.

권장:

```text
results/
    tracking_easy.gif
    tracking_crossing.gif
```

또는

```text
README.md
→ 짧은 결과 GIF
→ 전체 영상은 별도 링크
```

GitHub 저장소 용량을 고려합니다.

---

# 27. Week 6 README 질문

다음 질문에 답하도록 합니다.

1. 가장 tracking이 쉬웠던 영상은?
2. 가장 어려웠던 영상은?
3. ID switch는 어떤 상황에서 발생했는가?
4. Detection 실패와 Tracking 실패는 어떻게 구분할 수 있는가?
5. 실제 서비스에서 ID switch가 문제가 되는 사례는?

---

# 28. Week 7 — Depth Estimation

## 28.1 목표

사진은 기본적으로 2차원입니다.

Depth Estimation은 한 장의 이미지에서 각 위치가 카메라로부터 얼마나 가깝거나 먼지 추정합니다.

```text
Image
  ↓
Depth Model
  ↓
Depth Map
```

---

# 29. Depth Map

Depth map은 이미지의 각 위치에 깊이 값을 부여합니다.

시각화할 때는 색 또는 밝기로 표현합니다.

주의:

> 색이 밝다고 반드시 가까운 것은 아닙니다.

사용한 데모의 색상 규칙을 확인해야 합니다.

---

# 30. Metric Depth vs Relative Depth

초급 실습에서 중요한 구분입니다.

## Metric Depth

```text
2.1 meters
5.4 meters
```

처럼 실제 거리 단위 추정.

## Relative Depth

```text
A가 B보다 가깝다.
C가 가장 멀다.
```

처럼 상대적인 깊이를 추정.

많은 일반 데모에서는 상대 깊이를 이해하는 것부터 시작하면 됩니다.

---

# 31. Hugging Face Spaces 이용

접속:

```text
https://huggingface.co/spaces
```

검색 예:

```text
depth estimation
monocular depth
depth map
```

실행 가능한 Space를 선택합니다.

Space는 변경되거나 종료될 수 있으므로 특정 앱 하나에 의존하지 않습니다.

---

# 32. 실습용 이미지 6종

다음 이미지를 준비합니다.

1. 실내 복도
2. 야외 도로
3. 풍경
4. 사람과 배경
5. 유리/거울이 있는 장면
6. 그림 또는 만화

---

# 33. 실습 1 — 명확한 원근

복도 사진을 사용합니다.

예:

```text
foreground chair
middle person
far door
```

질문:

> 모델의 depth map이 우리가 느끼는 원근과 일치하는가?

---

# 34. 실습 2 — 반사체

다음 대상을 포함합니다.

- 거울
- 유리
- 반짝이는 금속

질문:

> 반사된 공간을 실제 공간처럼 해석하는가?

---

# 35. 실습 3 — 그림과 사진

실제 거리 사진과 거리 그림을 비교합니다.

```text
Real Photo
vs
Drawing / Illustration
```

질문:

> 모델은 물리적인 3D 정보를 직접 측정하는 것일까, 아니면 이미지 패턴으로부터 추정하는 것일까?

---

# 36. 실습 4 — 동일 장면 Crop

같은 이미지를 다음처럼 만듭니다.

```text
Original
Center Crop
Zoomed
```

Depth map이 얼마나 달라지는지 확인합니다.

---

# 37. Depth 실험표

| Image | Near Object Correct? | Far Object Correct? | Major Error | Notes |
|---|---|---|---|---|
| hallway | O | O | - | good |
| mirror | X | O | reflection | mirror confused |
| drawing | O | △ | flat region | uncertain |

---

# 38. 선택 과제 — 최소 Python 코드

강사가 준비한 Colab 또는 로컬 환경에서 Hugging Face의 depth-estimation pipeline을 사용합니다.

개념적인 형태:

```python
from transformers import pipeline

depth = pipeline(
    task="depth-estimation",
    model="MODEL_NAME"
)

result = depth("image.jpg")
result["depth"].save("depth.png")
```

학생은 모델 구현이 아니라 다음만 수정합니다.

```text
MODEL_NAME
image.jpg
output filename
```

---

# 39. Week 8 — Video Understanding

## 39.1 목표

영상에서는 단순히 “무엇이 있는가?”뿐 아니라 다음이 중요합니다.

```text
무엇이 먼저 일어났는가?
무엇이 나중에 일어났는가?
객체가 어떻게 움직였는가?
상태가 어떻게 변했는가?
```

즉 **Temporal Understanding**이 필요합니다.

---

# 40. Image Understanding vs Video Understanding

### Image

```text
What is in the scene?
```

### Video

```text
What happened?
What happened before?
What happened after?
How did the object move?
```

---

# 41. 실습 영상 만들기

각 팀이 5~15초 영상 3개를 만듭니다.

### Video 1 — Simple Action

```text
사람이 책을 집어 든다.
```

### Video 2 — Sequence

```text
문을 연다
→ 방에 들어간다
→ 의자에 앉는다
```

### Video 3 — Object Movement

```text
공이 왼쪽에서 오른쪽으로 이동
```

---

# 42. Video VLM / Video QA 실행

Hugging Face Spaces에서 검색:

```text
video question answering
video language model
video text to text
video understanding
```

업로드 가능한 공개 demo를 선택합니다.

---

# 43. 질문 설계

질문을 세 종류로 나눕니다.

## Level 1 — Recognition

```text
What objects are visible?
How many people appear?
```

## Level 2 — Action

```text
What is the person doing?
```

## Level 3 — Temporal Reasoning

```text
What happened before the person sat down?
What happened after the door opened?
```

---

# 44. 실습 1 — 같은 영상, 질문 난이도 변경

| Level | Question | Answer | Correct? |
|---|---|---|---|
| Recognition | Is there a chair? | Yes | O |
| Action | What is the person doing? | Sitting | O |
| Temporal | What happened before sitting? | Opened door | △ |

목표:

> 질문이 복잡해질수록 어떤 오류가 나타나는가?

---

# 45. 실습 2 — 영상 길이 변경

같은 행동을 다음 길이로 준비합니다.

```text
5 sec
10 sec
20 sec
```

모델이 긴 영상에서 더 잘하는지, 더 어려워하는지 관찰합니다.

---

# 46. 실습 3 — 사건 순서 바꾸기

두 영상을 만듭니다.

### A

```text
책을 집음 → 책을 펼침
```

### B

```text
책을 펼침 → 책을 내려놓음
```

질문:

```text
What happened first?
```

---

# 47. Video Understanding의 대표 실패

- 매우 짧은 순간을 놓침
- 비슷한 행동을 혼동
- 사건 순서를 바꿈
- 등장 인원 수를 잘못 셈
- 영상에 없는 행동을 설명
- 프레임 사이의 인과관계를 과도하게 추론

---

# 48. Part II 통합 실습

하나의 사람 행동 영상을 다음 관점으로 분석합니다.

```text
Pose
  ↓
Detection
  ↓
Tracking
  ↓
Video Understanding
```

예:

> 한 사람이 스쿼트를 3번 하는 영상

확인:

- Pose: squat 자세를 인식하는가?
- Tracking: 사람 ID가 유지되는가?
- Video QA: 무엇을 하고 있다고 설명하는가?
- Counting: 반복 횟수를 정확히 세는가?

---

# 49. Part II Mini Project

## A. Pose Coach

```text
Webcam
  ↓
Pose Classification
  ↓
Correct / Incorrect Pose
```

## B. People Tracking Test

서로 교차하는 사람 영상을 만들어 tracking 실패 사례를 수집합니다.

## C. Depth Robustness

실내/실외/거울/그림에서 depth estimation을 비교합니다.

## D. Video QA Benchmark

10개 짧은 영상 × 5개 질문을 만들어 50개 QA benchmark를 구성합니다.

---

# 50. Part II 최종 산출물

```text
part2/

week05-pose/
week06-tracking/
week07-depth/
week08-video-understanding/

project/
```

각 README에 반드시 포함:

1. Objective
2. Tool
3. Input
4. Experiment
5. Results
6. Failure Cases
7. Discussion
8. Next Experiment

---

# 51. Part II 체크리스트

- [ ] Pose와 Image Classification의 차이를 설명할 수 있다.
- [ ] Keypoint의 의미를 설명할 수 있다.
- [ ] 다른 사람/거리/각도에서 pose 성능을 비교했다.
- [ ] Detection과 Tracking의 차이를 설명할 수 있다.
- [ ] ID switch 사례를 찾았다.
- [ ] Depth map을 생성했다.
- [ ] Relative depth를 이해했다.
- [ ] Depth estimation의 실패 사례를 찾았다.
- [ ] Video QA를 실행했다.
- [ ] 시간 순서 질문을 실험했다.
- [ ] 결과와 실패 사례를 GitHub에 기록했다.

---

# 52. 다음 Part

Part III에서는 비전에 **언어**가 결합됩니다.

```text
Image
  +
Text
  ↓
CLIP / Vision-Language Model
  ↓
Retrieval
Detection
Captioning
VQA
```

---

## 참고 공식 사이트

- Teachable Machine: https://teachablemachine.withgoogle.com/
- Roboflow: https://roboflow.com/
- Hugging Face Spaces: https://huggingface.co/spaces
