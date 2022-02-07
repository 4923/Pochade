## 전이 학습 기반 고정된 레이어 수에 따른 건물 전력 소비량 예측 정확도 검증


### 동기   
KPMG Ideathon에 적용할 모델링 방법론을 찾던 중, 전이학습의 필요성을 느꼈고
데스크 리서치를 진행 중 팀 fineapple의 목표 중 하나인 건물 전력 소비량 예측을 연구한 선행연구를 발견,
확보한 데이터와 현재 지식 수준으로 구현이 가능한지 검증하기 위해 논문 리뷰 및 모델링을 진행한다.  
- [modeling project repositody](https://github.com/4923/Pochade/tree/master/papers/Validation-of-Building-Power-Consumption-Prediction-Accuracy-According-to-Frozen-Number-of-Layers-based-on-Transfer-Learning)

## Description
- 스마트 그리드
    - 스마트 그리드의 필요성 == 예측이 필요한 이유
    - 예측한 양에 따라 공급한다
    - 태양광의 경우 사용하는 시간 != 발전하는 시간이므로 ESS, EMS 등이 발전함
- 즉, 전력예측이 중요한 지점인데
    - 그 전까지 예측해오던 방법?
    - 인공지능이 필요한 이유
- 인공지능의 특성상: 데이터가 필요
    - 문제?: 인공지능은 데이터가 겁나 필요한데 반해 신축건물은 데이터가 없음
    - 해결방법?: 전이학습
    - 해결방법의 문제? == 본 논문의 문제의식

> 실제 상황에 적용하기 위한 효과적인 fine tuning 방법을 도모  
> 위 목표를 성취하기 위한: 고정된 레이어 수 파악

- 어떻게? : 정확도로 판정

<hr>

## Review

- [(1) 연구주제](#-1------)
- [(2) 이 연구를 진행하는 데 필요한 배경지식](#-2-----------------------)
- [(3) 논문 내용 설명](#-3----------)
- [(4) 전체적인 결과](#-4---------)
- [(5) 이러한 결과가 나온 이유](#-5---------------)
- [(6) 이 논문에서 내 실험을 할 때](#-6------------------)

<small><i><a href='http://ecotrust-canada.github.io/markdown-toc/'>Table of contents generated with markdown-toc</a></i></small>


### (1) 연구주제  
말그대로 연구주제가 무엇인지

### (2) 이 연구를 진행하는 데 필요한 배경지식  
이 연구 전에 다른 사람들이 연구한 내용들, 이 논문에서 가장 많이 나오는 Key point 용어 설명, 왜 이 연구가 필요한지 등등

### (3) 논문 내용 설명  
논문에 따라서 내가 설명하고 싶은 부분

### (4) 전체적인 결과  
표로 만들거나 간단하게 설명

### (5) 이러한 결과가 나온 이유  
결과 해석-여기서 꼼꼼히 설명

### (6) 이 논문에서 내 실험을 할 때   
필요한 부분 & 이 부분은 실험해 볼 필요가 있는 부분이다 하는 부분 설명

```
- 무슨 연구를 한건지!
- 이 연구 전에 저자가 알아낸 배경지식은?
- 이 논문에서 중점적으로 볼 것은?
- 결론 간략하게 도식화해서 나타내보기
- METOD/EXPERIMENT 에서 내가 필요한 부분

정리해보기
- 이 논문에서 내가 해 볼만한 METHOD 
(흥미롭게 느낀 부분)

```

![transfer learning](https://user-images.githubusercontent.com/60145951/152255381-b1f976ff-73cc-4aa1-b324-ca1d0b72e8e6.png)
[image reference: IT FIND 인공지능 전이학습과 응용 분야 동향](https://mangastorytelling.tistory.com/entry/ITFIND-%EC%9D%B8%EA%B3%B5%EC%A7%80%EB%8A%A5-%EC%A0%84%EC%9D%B4%ED%95%99%EC%8A%B5Transfer-Learning%EA%B3%BC-%EC%9D%91%EC%9A%A9-%EB%B6%84%EC%95%BC-%EB%8F%99%ED%96%A5)

![strategies](https://user-images.githubusercontent.com/60145951/152263256-8ae6c2c7-5797-4e84-a613-4e121de72a9a.png)
[image reference: Transfer Learning](https://choice-life.tistory.com/40)
