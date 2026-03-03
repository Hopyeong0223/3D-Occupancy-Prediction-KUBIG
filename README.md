# 3D-Occupancy-Prediction-KUBIG
NuScenes 데이터를 사용한 3D 점유 예측

# 3D Occupancy Prediction using VoxelNet (NuScenes)

> **KUBIG AI Society CV Project** > **핵심 역할:** VoxelNet 베이스라인 구축, Custom Head 설계 및 Loss Function 최적화

## 1. Project Overview
자율주행 차량의 안전한 주행을 위해, 주변 환경을 3차원 격자(Voxel)로 나누고 각 공간의 점유 여부(Occupied/Empty)를 예측하는 프로젝트입니다.

- **Dataset:** NuScenes (LiDAR & Camera)
- **Task:** 3D Binary Classification
<img width="1162" height="650" alt="image" src="https://github.com/user-attachments/assets/03c105f6-94a7-4f2c-902a-42b751b57e84" />

## 2. Model Architecture: VoxelNet with Custom Head
기존의 Object Detection용 VoxelNet 구조를 Occupancy Prediction에 맞게 수정했습니다.

- **VFE (Voxel Feature Encoder):** LiDAR 포인트 클라우드에서 지역적 통계 특징 추출
- **Custom Classification Head:** $K \times 128$ 특징 벡터를 $200 \times 200 \times 16$ 격자 공간으로 매핑하는 이진 분류 헤드 직접 구현

<img width="1157" height="621" alt="image" src="https://github.com/user-attachments/assets/e83f5e85-24d5-4fb4-ae2c-b601a5d9588a" />
<img width="1165" height="659" alt="image" src="https://github.com/user-attachments/assets/b1f4f8cb-2d95-45a0-8c96-fc1bd66a8d8f" />


## 3. Statistical Approach & Loss Function
본 프로젝트에서는 가장 표준적이면서도 강력한 **Binary Cross Entropy (BCE)**를 사용하여 학습을 진행했습니다.

### Loss Function: BCE
$$BCE(z_i, y_i) = -y_i \log \sigma(z_i) - (1-y_i) \log (1-\sigma(z_i))$$
- **선택 이유:** 확률적 학습에 가장 안정적이며, 개별 Voxel의 점유 확률을 정확하게 모델링하기에 적합합니다.

### Statistical Monitoring (핵심 분석 포인트)
단순히 Loss 수치에만 의존하지 않고, 통계학 전공자로서 **데이터 불균형(Class Imbalance)** 문제를 해결하기 위해 다음 지표들을 실시간으로 분석했습니다.

1. **$pos$ (정답 비율) vs $pred$ (예측 비율) 분석:**
   - 전체 공간 중 실제 물체가 차지하는 비율($pos$)과 모델이 예측한 비율($pred$)을 대조하였습니다.
   - 특정 에포크에서 $pred$가 급상승하며 **FP(False Positive)가 폭발하는 구간**을 포착하고, 이를 통해 모델이 빈 공간(Empty)을 과하게 점유(Occupied)로 판단하는 편향(Bias)을 모니터링했습니다.

2. **Precision-Recall Trade-off:**
   - 자율주행의 안전성을 위해 빈 공간을 물체로 착각하는 것보다, **물체를 빈 공간으로 착각하는 것(FN)**이 더 위험하다는 점에 착안하여 지표의 밸런스를 분석했습니다.

3. **IoU (Intersection over Union) 중심 평가:**
   - 3D 공간의 희소성(Sparsity) 때문에 발생하는 Accuracy의 역설을 피하기 위해, 실제 정답 영역과 예측 영역의 겹침 정도를 나타내는 IoU를 주력 지표로 설정했습니다.




## 4. Results
- **Metric:** Mean IoU 상승 확인
- **Visualization:** LiDAR의 정밀한 지오메트리를 바탕으로 근거리 객체 형태 복원 성공

<img width="1062" height="370" alt="image" src="https://github.com/user-attachments/assets/465cafdb-8538-46c8-928d-f28040a56e07" />
<img width="1045" height="308" alt="image" src="https://github.com/user-attachments/assets/29496947-1ba4-41ec-acf5-ff1ed3091631" />



---

## 🔗 External Links
- [Notion: 상세 연구 노트](https://www.notion.so/KUBIG-Project-2f43fd1d8fdd803daf8fcb5846e14268?source=copy_link)
- [Project Presentation (PDF)](./docs/presentation.pdf) # 2번에서 만든 docs 폴더 안에 PPT를 PDF로 넣어두면 좋아!
