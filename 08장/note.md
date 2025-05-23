챕터 8. "sLLM 서빙하기": **LLM을 효율적으로 서빙하기 위한 전략과 기술**

---

### **8.1 효율적인 배치 전략**

LLM의 추론을 최적화하기 위한 배치 처리 전략

#### **8.1.1 일반 배치 (정적 배치)**

* N개의 요청을 모아서 한번에 추론하고, **모든 생성이 끝날 때까지 기다림**.
* 문제점: 짧은 문장이 먼저 끝나도 긴 문장이 끝날 때까지 대기해야 함 → **낭비 발생**.
![image](https://github.com/user-attachments/assets/aef9d2b7-c563-48aa-8bd2-22be685447a4)

#### **8.1.2 동적 배치**

* 비슷한 시간대에 들어온 요청을 하나의 배치로 묶어 처리.
* 대기시간을 줄이기 위한 전략이지만, **길이 차이로 인해 GPU 효율이 떨어질 수 있음**.\
  ![image](https://github.com/user-attachments/assets/95a71d6e-15bd-4f81-8490-fcd639d04b8c)


#### **8.1.3 연속 배치 (Continuous Batching)**

* 생성이 끝난 자리에 **바로 새로운 문장을 추가**.
* 유휴 공간을 활용하여 GPU 사용률을 극대화.
* Hugging Face의 `Text Generation Inference`에서는 `waiting_served_ratio`를 조정하여 이를 지원함.
![image](https://github.com/user-attachments/assets/c443d4bd-9ce6-4433-bdbe-8bb2a01f31ee)


####
👉 입력 데이터를 추론 전에 길이 기준으로 정렬하거나 그룹화해라.
→ padding 낭비 줄고, 연산량 줄어든다.


👉vLLM, FlashAttention 같이 효율적 연산을 도와주는 프레임워크를 활용해라.
→ 별도 구현 없이 내부적으로 배치 최적화를 해준다.

👉대량 요청을 처리할 경우, batch size를 최대한 활용하라.
→ 대량 추론 작업을 병렬 처리하여 응답 시간 단축 가능


---

### **8.2 효율적인 트랜스포머 연산**

Transformer의 핵심 연산인 self-attention을 **속도와 메모리 측면에서 최적화**.

#### **8.2.1 플래시어텐션 (FlashAttention)**

* 시퀀스 길이가 길어질 때 GPU 메모리 병목을 해결하기 위한 기술.
* **블록 단위 연산 수행**으로 SRAM에서 처리 가능한 계산을 수행해 메모리 접근 병목 완화.

> 관련 그림:

![image](https://github.com/user-attachments/assets/ace89adf-25f2-4602-a675-f35356908c3d)
* **그림 8.4**: Self-attention 과정 시각화 (Q, K, V → 마스킹 → 소프트맥스 → 드롭아웃).

* **그림 8.5**: 각 단계별 연산 시간 비교.
* **그림 8.6**: GPU 메모리 계층 구조 (SRAM, HBM, DRAM).
* **그림 8.7**: FlashAttention의 블록 단위 연산 방식 구조도.


---

### **8.2.2 블록 단위 소프트맥스 연산**

* 소프트맥스를 전체 벡터가 아닌 **블록 단위로 분할하여 계산**함.
* 예시: `X = [1, 2, 3, 4, 5, 6]`을 두 벡터로 나누어 각각 소프트맥스를 수행.

> 관련 공식:

* `softmax(xᵢ) = e^(xᵢ) / Σ e^(xⱼ)`
* `softmax(X) = f(X) / I(X)`, f(X)는 지수함수 적용한 벡터.

---

이해를 더 돕기 위해 필요한 부분이 있다면, 각 섹션을 더 자세히 설명드릴 수 있어요.
예: 연속 배치 동작 방식, FlashAttention 메모리 구조, softmax 수식 연산 방식 등.
