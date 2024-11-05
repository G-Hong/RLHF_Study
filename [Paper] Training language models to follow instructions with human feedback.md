# Training language models to follow instructions with human feedback

[Paper Link](https://arxiv.org/abs/2203.02155)

## abstract
![image](https://github.com/user-attachments/assets/53f79251-f5db-42c6-9eb6-7c9a6eb4532e)

언어 모델을 크게 키우는 것이 반드시 사용자의 의도를 더 잘 따르는 것은 아니다. 대형 언어 모델은 거짓되거나, 유해하거나, 사용자에게 도움이 되지 않는 출력을 생성할 수 있다. 즉, 이러한 모델들은 사용자와 '정렬'되어 있지 않다. 본 논문은 
<span style="background-color:#fff5b1">**인간 피드백을 통한 미세조정으로 다양한 작업에서 언어 모델을 사용자 의도와 정렬하는 방법**</span>
을 보여준다.


# Sample Markdown File

This is a sample Markdown file created to demonstrate how Markdown formatting works on GitHub.

## Table of Contents
1. [Introduction](#introduction)
2. [Usage](#usage)
3. [Conclusion](#conclusion)

## Introduction
Markdown is a lightweight markup language that you can use to add formatting elements to plaintext text documents. 

## Usage
Here are some examples of Markdown syntax:
- **Bold Text**: Use `**bold text**` for bold.
- *Italic Text*: Use `*italic text*` for italics.
- `Inline Code`: Use backticks for inline code snippets.

### Code Block Example
```python
print("Hello, World!")
```

## Conclusion
Markdown makes it easy to style your text in a visually appealing way. It’s widely used in README files and documentation.




# 언어 모델의 확장과 사용자의 의도

언어 모델을 크게 키우는 것이 반드시 사용자의 의도를 더 잘 따르는 것은 아니다. 대형 언어 모델은 거짓되거나, 유해하거나, 사용자에게 도움이 되지 않는 출력을 생성할 수 있다. 즉, 이러한 모델들은 사용자와 '정렬'되어 있지 않다. 본 논문은 **인간 피드백을 통한 미세조정으로 다양한 작업에서 언어 모델을 사용자 의도와 정렬하는 방법**을 보여준다.

## 연구 과정

연구 과정은 다음과 같다:

1. 레이블러가 작성한 프롬프트와 OpenAI API 제출 프롬프트로 시작하여 레이블러 시연 데이터셋을 수집
2. 이를 사용하여 지도 학습으로 GPT-3를 fine tuning
3. 모델 출력의 순위 데이터셋을 수집하여 인간 피드백 기반 강화학습으로 추가 fine tuning

이렇게 만들어진 모델은 'InstructGPT'이다. 프롬프트 분포에 대한 인간 평가에서, 1.3B 파라미터의 InstructGPT 모델 출력이 175B GPT-3(파라미터 100배 많음)의 출력보다 더 선호되었다. 또한 InstructGPT 모델은 공개 NLP 데이터셋의 성능 저하를 최소화하면서도 진실성이 향상되고 유해 출력이 감소하였다. InstructGPT가 여전히 단순한 실수를 하지만, 이 연구 결과는 인간 피드백을 통한 미세조정이 언어 모델을 인간의 의도와 정렬하는 데 있어 유망한 방향임을 보여주는 것이다.

### 레이블러(데이터 라벨링 작업자) 시연 데이터
모델이 어떻게 응답해야 하는지 직접 시연하여 작성한 예시 데이터

### 인간 피드백
- 지도 학습 단계에서 레이블러들이 만든 시연 데이터로 모델 학습
- 강화 학습 단계에서 모델이 생성한 여러 답변들의 순위를 매기는 방식으로 피드백

## 전체 과정

1. 레이블러가 프롬프트에 대한 이상적인 응답 작성
2. 이 데이터로 GPT-3 fine tuning
3. fine tuned 모델의 여러 출력들에 대해 인간이 순위 매김
4. 이 순위 데이터를 사용해 강화학습으로 다시 fine tuning

## Introduction

대규모 언어 모델들(LMs)은 작업에 대한 몇 가지 예시를 입력으로 제공받으면 다양한 자연어 처리(NLP) 작업을 수행하도록 "프롬프팅" 될 수 있다. 하지만 이러한 모델들은 종종 사실을 날조하거나, 편향되거나 유해한 텍스트를 생성하거나, 단순히 사용자의 지시를 따르지 않는 등의 의도치 않은 행동을 보인다(Bender 외., 2021; Bommasani 외., 2021; Kenton 외., 2021; Weidinger 외., 2021; Tamkin 외., 2021; Gehman 외., 2020). 이는 최근 많은 대규모 LM에 사용되는 **언어 모델링 목표(인터넷에서 웹 페이지의 다음 토큰 예측)가 “사용자의 지침을 유용하고 안전하게 따르기”라는 목표와 다르기 때문**이다(Radford 외., 2019; Brown 외., 2020; Fedus 외., 2021; Rae 외., 2021; Thoppilan 외., 2022). 따라서 우리는 이러한 언어 모델링 목적이 '잘못 정렬되어 있다'고 말한다. 이러한 의도치 않은 행동들을 방지하는 것은 수백 개의 애플리케이션에서 배포되고 사용되는 언어 모델들에게 특히 중요한 것이다.

우리는 사용자의 의도에 따라 행동하도록 언어 모델을 훈련시켜 모델 정렬에서 진전을 이루었다(Leike 외., 2018). 이는 지시사항을 따르는 것과 같은 명시적 의도와, 진실성 유지, 편향되지 않음, 유해하지 않음과 같은 암묵적 의도 모두를 포함한다. Askell 외.(2021)의 용어를 사용하면, 우리는 언어 모델이 '도움이 되고'(사용자가 과제를 해결하도록 도와야 함), '정직하며'(정보를 조작하거나 사용자를 오도해서는 안 됨), '무해한'(사람이나 환경에 물리적, 심리적, 사회적 해를 끼치지 않아야 함) 것을 원한다. 이러한 기준의 평가에 대해서는 3.6절에서 자세히 설명한다.

## RM(Reward Model, 보상 모델)

- 인간이 모델 출력들의 품질을 비교하여 순위를 매긴 데이터로 학습된 모델
- 새로운 모델 출력이 들어오면 그것이 얼마나 "좋은" 출력인지 점수를 매기는 역할

## PPO(Proximal Policy Optimization) 알고리즘

- 강화학습에서 사용되는 알고리즘
- 모델이 RM에서 높은 점수를 받을 수 있는 출력을 더 자주 생성하도록 학습

## 전체 과정 요약

- 기존의 지도학습 모델이 출력을 생성한다
- RM이 이 출력의 품질을 평가하여 점수를 매긴다
- PPO 알고리즘이 이 점수를 바탕으로 모델을 조금씩 수정한다
- 이 과정을 반복하여 점점 더 좋은 출력을 생성하는 모델로 발전시킨다

⇒ 인간이 좋다고 평가할 만한 출력을 더 많이 생성하도록 모델을 지속적으로 개선하는 과정

레이블러들은 InstructGPT의 출력을 GPT-3의 출력보다 크게 선호한다. 테스트 세트에서, 1.3B 파라미터를 가진 InstructGPT 모델의 출력이 100배 이상 많은 파라미터를 가진 175B GPT-3의 출력보다 더 선호되었다. 이 모델들은 같은 구조를 가지며, 단지 InstructGPT가 우리의 인간 데이터로 미세조정되었다는 점만이 다르다. 이러한 결과는 GPT-3에 few-shot 프롬프트를 추가하여 지시사항을 더 잘 따르게 만들었을 때도 유지되었다. 175B InstructGPT의 출력은 175B GPT-3의 출력보다 85 ± 3% 더 선호되었고, few-shot 175B GPT-3보다는 71 ± 4% 더 선호되었다. 또한 InstructGPT 모델들은 레이블러들의 평가에 따르면 더 적절한 출력을 생성하고, 지시사항의 명시적 제약을 더 안정적으로 따른다.

InstructGPT 모델들은 GPT-3보다 진실성이 향상되었다. TruthfulQA 벤치마크에서 InstructGPT는 GPT-3보다 약 두 배 더 자주 진실되고 유익한 답변을 생성했다. 이러한 결과는 GPT-3에 대해 적대적으로 선택되지 않은 질문들의 하위 집합에서도 똑같이 강력했다. API 프롬프트 분포에서 "closed-domain" 작업(입력에 없는 정보를 출력에 포함하면 안 되는 요약 및 closed-domain QA 등)에서, InstructGPT 모델들은 GPT-3의 절반 수준으로 입력에 없는 정보를 만들어냈다(환각 비율이 각각 21% vs. 41%).

InstructGPT는 GPT-3보다 유해성은 약간 개선되었으나, 편향성은 개선되지 않았다. 유해성을 측정하기 위해, 우리는 RealToxicityPrompts 데이터셋(Gehman 외., 2020)을 사용하여 자동 및 인간 평가를 수행했다. InstructGPT 모델들은 존중하는 태도를 요구받았을 때 GPT-3보다 약 25% 적은 유해한 출력을 생성했다. 하지만 InstructGPT는 Winogender(Rudinger 외., 2018)와 CrowSPairs(Nangia 외., 2020) 데이터셋에서 GPT-3보다 유의미한 개선을 보이지 않았다.
