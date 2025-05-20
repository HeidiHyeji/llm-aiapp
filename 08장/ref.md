## 🧠 어텐션이란?

> 주어진 입력에서 **중요한 부분에 더 집중하게 해주는 메커니즘**

예를 들어, 번역할 때 "그는 사과를 먹었다"라는 문장에서
"먹었다"에 해당하는 영어 단어를 예측할 때
→ 모델이 "사과"와 "그는" 중에서 "사과" 쪽에 더 주목하도록 **가중치를 주는 방식**이에요.

이런 "집중 정도를 계산하는 방식"이 여러 가지가 있는데,
그 중 **가장 많이 쓰이는 방식이 Dot-Product Attention**입니다.

---

## 🔍 Dot-Product Attention이란?

다음 수식으로 정의됩니다:

$$
\text{Attention}(Q, K, V) = \text{softmax}\left(\frac{QK^T}{\sqrt{d_k}}\right)V
$$

* **Q**: Query (현재 주목하려는 정보)
* **K**: Key (각 입력 토큰에 대한 대표값)
* **V**: Value (실제 정보)

즉,

1. **Q와 K의 점곱(Dot Product)** → 유사도 계산
2. **Softmax** → 유사도를 확률처럼 정규화
3. **가중합(weighted sum)** → V를 이 유사도로 가중합

➡️ 이 계산을 통해 **어떤 V에 얼마나 집중할지 결정**하는 게 dot-product attention이에요.

---

## 🔄 다른 어텐션 방식도 있어?

네, Dot-Product 말고도 다음과 같은 방식들이 있습니다:

| 이름                                | 설명                                     |
| --------------------------------- | -------------------------------------- |
| **Additive Attention** (Bahdanau) | Q와 K를 합쳐서 MLP로 유사도 계산                  |
| **Scaled Dot-Product Attention**  | Dot Product 계산 후 $\sqrt{d_k}$로 scaling |
| **Multi-Head Attention**          | 여러 개의 Dot-Product Attention을 병렬로 수행    |
| **Sparse Attention**              | 일부 토큰만 참조하는 희소 어텐션                     |
| **FlashAttention**                | 계산 효율성과 메모리 최적화를 강화한 dot-product 변형    |

---
