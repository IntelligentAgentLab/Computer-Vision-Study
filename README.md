# Practical Computer Vision & Multimodal AI Study

학부생을 대상으로 진행하는 **실습 중심 컴퓨터 비전·멀티모달 AI 스터디**입니다.

이 스터디의 목표는 복잡한 딥러닝 모델을 처음부터 구현하는 것이 아니라,  
**현대 컴퓨터 비전 기술을 직접 실행하고, 조건을 바꾸어 실험하고, 결과를 비교·분석하는 경험을 쌓는 것**입니다.

가능한 한 사용하기 쉬운 GUI 및 웹 기반 도구를 먼저 활용하고, 필요할 경우 같은 기능을 최소한의 Python 코드로 재현합니다.  
모든 실습과 프로젝트 결과는 GitHub에 기록합니다.

---

## 🎯 Study Goals

### 1. 현대 컴퓨터 비전의 주요 문제를 직접 경험한다

다음 주제를 중심으로 실제 이미지와 영상을 사용해 모델을 실행합니다.

- Image Classification
- Object Detection
- Image Segmentation
- Pose Estimation
- Object Tracking
- Depth Estimation
- Image-Text Matching
- Open Vocabulary Detection
- Image Captioning
- Visual Question Answering (VQA)
- Video Understanding
- Document AI
- Multimodal AI

단순히 개념을 배우는 데 그치지 않고, 실제 입력에 대한 모델의 출력을 직접 확인합니다.

---

### 2. 모델을 “사용하는 것”에서 “실험하는 것”으로 발전한다

모델을 한 번 실행하고 끝내지 않습니다. 다음과 같은 조건을 바꾸어 결과를 비교합니다.

- 학습 데이터 수
- 배경
- 조명
- 이미지 회전
- Object Occlusion
- Confidence Threshold
- Prompt
- 입력 이미지
- 질문 방식

이를 통해 다음 질문에 답해 봅니다.

> 어떤 조건에서 모델이 잘 작동하는가?

> 어떤 조건에서 모델이 실패하는가?

> 입력이나 설정이 바뀌면 결과가 어떻게 달라지는가?

---

### 3. 성공 사례뿐 아니라 실패 사례를 분석한다

AI 모델은 항상 정확하지 않습니다.

따라서 다음과 같은 실패 조건을 적극적으로 찾아봅니다.

- Dataset Bias
- Domain Shift
- Small Object Detection Failure
- Occlusion
- Low-light Images
- Unusual Camera Angles
- Crowded Scenes
- VLM Hallucination
- Counting Error
- Spatial Reasoning Error

실패 사례를 통해 모델의 한계와 실제 적용 시 고려해야 할 문제를 이해합니다.

---

### 4. 코딩보다 실험 설계와 결과 해석에 집중한다

기본적인 진행 방식은 다음과 같습니다.

```text
GUI / Web Application
        ↓
모델 실행
        ↓
조건 변경
        ↓
결과 비교
        ↓
실패 사례 분석
        ↓
필요한 경우 최소 코드로 재현
        ↓
GitHub 기록
```

코딩은 모델 자체를 구현하는 목적이 아니라  
**기존 모델을 실제 프로그램에서 사용할 수 있는 수준**까지 경험하는 것을 목표로 합니다.

---

## 🧭 Study Roadmap

### Part I — Visual Recognition

- Image Classification
- Generalization
- Dataset Bias
- Object Detection
- Image Segmentation

```text
Image
  ↓
Classification
  ↓
Detection
  ↓
Segmentation
```

### Part II — Vision in the Real World

- Pose Estimation
- Object Tracking
- Video Understanding
- Depth Estimation

### Part III — Vision + Language

- CLIP
- Image-Text Similarity
- Image-Text Retrieval
- Open Vocabulary Detection
- Image Captioning
- Vision-Language Models
- Visual Question Answering

```text
Image + Text
     ↓
Vision-Language Model
     ↓
Understanding
     ↓
Question Answering
```

### Part IV — Modern Vision Applications

- Self-Supervised Representation
- Generative Vision
- Video-Language Models
- Document AI
- Multimodal Applications

### Part V — Model Reliability & Research Topics

- Robustness
- Domain Shift
- Anomaly Detection
- Continual Learning
- VLM Hallucination
- Model Comparison

---

## 🛠 Main Tools

### Google Teachable Machine

주요 용도:

- Image Classification
- Pose Classification
- 간단한 모델 학습

특징:

- 브라우저에서 실행
- 코딩 없이 데이터 수집 및 학습 가능
- 학습 결과를 즉시 테스트 가능

### Roboflow

주요 용도:

- Object Detection
- Image Segmentation
- Annotation
- Object Tracking
- Vision Workflow

데이터 준비부터 inference까지 전체 computer vision workflow를 경험하는 데 사용합니다.

### Hugging Face

주요 용도:

- CLIP
- Depth Estimation
- Image Captioning
- Visual Question Answering
- Vision-Language Models
- Document AI

처음에는 Hugging Face Spaces의 공개 데모를 사용하고, 이후 필요한 경우 Python 라이브러리로 같은 모델을 실행합니다.

### GitHub

GitHub는 단순한 코드 저장소가 아니라  
**실험 과정과 결과를 축적하는 실험 노트**로 사용합니다.

---

## 🔄 Standard Study Workflow

각 주차는 가능한 한 같은 패턴으로 진행합니다.

1. **Understand**  
   이번 주 기술이 어떤 문제를 해결하는지 이해한다.

2. **Use**  
   GUI 또는 공개 애플리케이션에서 모델을 직접 실행한다.

3. **Experiment**  
   입력, prompt, threshold, 데이터 등의 조건을 변경한다.

4. **Compare**  
   결과를 이미지, 영상 또는 표로 비교한다.

5. **Analyze**  
   무엇이 달라졌고 왜 달라졌는지 분석한다.

6. **Code**  
   가능하면 간단한 코드로 같은 기능을 재현한다.

7. **Document**  
   결과를 GitHub에 정리한다.

---

## 💻 Coding Policy

이 스터디에서는 코딩을 학습의 진입장벽으로 만들지 않습니다.

> **GUI First, Code Later**

예:

```text
Roboflow에서 Object Detection 실행
        ↓
결과와 동작 방식 이해
        ↓
Python API로 동일한 inference 실행
```

또는

```text
Hugging Face Space에서 VQA 실행
        ↓
질문과 결과 비교
        ↓
Python pipeline으로 재현
```

---

## 📁 Repository Structure

```text
practical-computer-vision-study/

README.md

part1/
    week01-classification/
    week02-generalization/
    week03-object-detection/
    week04-segmentation/

part2/
    pose-estimation/
    object-tracking/
    depth-estimation/
    video-understanding/

part3/
    clip/
    open-vocabulary-detection/
    image-captioning/
    vqa/

part4/
    document-ai/
    video-language/
    multimodal-applications/

part5/
    robustness/
    hallucination/
    model-comparison/

projects/
    team01/
    team02/
```

---

## 📝 Weekly Deliverables

매주 가능한 범위에서 다음 결과를 남깁니다.

- 입력 이미지 또는 영상
- 모델 실행 결과
- 비교 실험 결과
- 실패 사례
- 간단한 결과 해석
- 필요한 경우 실행 코드

각 실습 폴더의 기본 구조:

```text
README.md
images/
results/
code/
```

---

## 📊 Recommended Experiment Format

가능하면 결과를 표로 정리합니다.

| Condition | Prediction | Confidence | Result |
|---|---|---:|---|
| Normal | Cup | 0.96 | Correct |
| Dark | Book | 0.44 | Wrong |
| Occlusion | Cup | 0.61 | Correct |
| New Background | Phone | 0.38 | Wrong |

각 실험에는 다음 내용을 포함합니다.

> **Observation**  
> 무엇이 달라졌는가?

> **Possible Explanation**  
> 왜 이런 결과가 나타났다고 생각하는가?

> **Next Experiment**  
> 다음에는 무엇을 확인해 보고 싶은가?

---

## 🔬 Study Philosophy

이 스터디에서 가장 중요한 것은 높은 성능의 모델을 만드는 것이 아닙니다.

다음과 같은 연구 습관을 익히는 것이 더 중요합니다.

```text
Baseline
   ↓
Experiment
   ↓
Comparison
   ↓
Failure Analysis
   ↓
Hypothesis
   ↓
New Experiment
```

계속해서 다음 질문을 반복합니다.

- 무엇을 바꾸었는가?
- 결과는 어떻게 달라졌는가?
- 왜 달라졌는가?
- 어떤 조건에서 실패했는가?
- 더 확인하려면 어떤 실험을 해야 하는가?

---

## 🚀 Final Team Project

스터디 후반에는 2~3명이 한 팀이 되어 작은 프로젝트를 진행합니다.

예시:

- 캠퍼스 이미지 VQA
- 교통 영상 객체 탐지 및 추적
- 운동 자세 분석
- Document AI Assistant
- Image Retrieval System
- Open Vocabulary Object Search
- VLM Hallucination Benchmark
- Detection vs Segmentation Comparison
- Domain Shift Experiment
- Vision Model Robustness Test

새로운 딥러닝 모델을 처음부터 만들 필요는 없습니다.

기존 모델을 활용하여

> **문제 정의 → 실험 → 결과 → 분석**

의 전체 과정을 경험하는 것이 목표입니다.

---

## ✅ Expected Outcomes

스터디를 완료하면 다음을 경험하게 됩니다.

- 컴퓨터 비전의 주요 문제 이해
- 실제 AI 모델 사용 경험
- Dataset과 Generalization에 대한 이해
- 기본적인 모델 평가 방법 이해
- 모델 실패 사례 분석
- Vision-Language Model 사용
- VQA 및 Multimodal AI 실습
- 간단한 AI inference 코드 작성
- Git/GitHub 기반 협업
- AI 실험 결과 문서화
- 소규모 팀 프로젝트 경험

---

## ⭐ Most Important Principle

> **Don't just run the model. Experiment with it.**

모델을 실행하는 것 자체보다  
**조건을 바꾸고, 결과를 비교하고, 실패 이유를 분석하는 것**이 이 스터디의 핵심입니다.
