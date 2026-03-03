# 3D-Occupancy-Prediction-KUBIG
NuScenes 데이터를 사용한 3D 점유 예측

# 3D Occupancy Prediction using VoxelNet (NuScenes)

> **KUBIG AI Society CV Project** > **핵심 역할:** VoxelNet 베이스라인 구축, Custom Head 설계 및 Loss Function 최적화

## 1. Project Overview
자율주행 차량의 안전한 주행을 위해, 주변 환경을 3차원 격자(Voxel)로 나누고 각 공간의 점유 여부(Occupied/Empty)를 예측하는 프로젝트입니다.

- **Dataset:** NuScenes (LiDAR & Camera)
- **Task:** 3D Binary Classification

## 2. Model Architecture: VoxelNet with Custom Head
기존의 Object Detection용 VoxelNet 구조를 Occupancy Prediction에 맞게 수정했습니다.

- **VFE (Voxel Feature Encoder):** LiDAR 포인트 클라우드에서 지역적 통계 특징 추출
- **Custom Classification Head:** $K \times 128$ 특징 벡터를 $200 \times 200 \times 16$ 격자 공간으로 매핑하는 이진 분류 헤드 직접 구현

## 3. Statistical Approach (핵심 강점)
통계학 전공자로서 **Class Imbalance(데이터 불균형)** 문제를 해결하기 위해 다음과 같은 전략을 사용했습니다.

### Hybrid Loss Function
단순 BCE의 한계를 극복하기 위해 IoU와 상관관계가 높은 **Dice Loss**를 결합했습니다.
$$L = L_{BCE} + \lambda(1 - Dice)$$

### Performance Monitoring
- **$pos$ (정답 비율) vs $pred$ (예측 비율):** 모델이 특정 공간을 과하게 채우는 편향(Bias)이 있는지 통계적으로 모니터링하며 학습 진행

## 4. Results
- **Metric:** Mean IoU 상승 확인
- **Visualization:** LiDAR의 정밀한 지오메트리를 바탕으로 근거리 객체 형태 복원 성공

---

## 🔗 External Links
- [Notion: 상세 연구 노트](여기에_노션_링크_넣기)
- [Project Presentation (PDF)](./docs/presentation.pdf) # 2번에서 만든 docs 폴더 안에 PPT를 PDF로 넣어두면 좋아!
