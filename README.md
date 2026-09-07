# 한국어 다화자 대화 요약 (NLP)

다화자 한국어 대화를 요약하는 경진대회 프로젝트입니다. Qwen3-8B 기반 추론 전략 최적화로 ROUGE 기반 평가에서 상위권을 달성했습니다.

**결과: 52.5876, Mid LB 2위 / 16팀**

## 접근 전략

```
Qwen3-8B (Strong Baseline 52.47)
    ↓
MBR (Minimum Bayes Risk) 디코딩  → 52.41 (소폭 하락)
    ↓
Routing 전략 (문장 길이/복잡도 기반 분기)  → 51.90 (하락)
    ↓
Selective Swap (앙상블 후 consensus 기반 교체)  → 52.5876 (최고점)
```

### 핵심 전략: Selective Swap

- 여러 디코딩 전략 결과물을 앙상블한 뒤, 문장 단위로 consensus를 확인
- consensus가 낮은 문장(불확실한 부분)만 선택적으로 다른 후보로 교체
- 전체 교체 시 발생하는 과교정 문제를 회피

## 파일 구성

```
nlp-dialogue-summary/
└── notebooks/
    ├── 01_mbr_strategy.ipynb           # MBR 디코딩 전략
    ├── 02_routing_strategy.ipynb       # Routing 전략 (복잡도 기반 분기)
    ├── 03_selective_swap_final.ipynb   # Selective Swap (최종 제출 전략)
    └── 04_best_submission.ipynb        # 최고점 제출 후처리
```

## 실패 기록

- **Qwen3-8B LoRA 파인튜닝**: 50시간 풀 학습 후 49.85로 베이스라인(52.47) 미달. 소량 사전 검증 없이 풀 학습을 시작한 것이 원인으로 분석 — 이후 "소량 검증 → 확인 → 풀 학습" 순서를 원칙으로 삼음
- **Routing 전략**: 문장 복잡도 기반 분기가 오히려 일관성을 깨뜨려 성능 하락

> **26-09-07 정정**: 이전 버전에는 LoRA 실패 원인으로 "negatives 오탐 226개"가 적혀 있었지만, 이 수치는 별개 프로젝트(RAG 검색 시스템의 Reranker 파인튜닝 실패 분석)에서 나온 것이 잘못 섞여 들어간 것으로 보입니다. 이 노트북들에는 해당 수치의 근거가 없어 제거했습니다.

## 기술 스택

| 구분 | 내용 |
|------|------|
| 모델 | Qwen3-8B (vLLM 추론) |
| 전략 | MBR 디코딩, Selective Swap |
| 평가 | ROUGE-1/2/L (custom weighted) |
