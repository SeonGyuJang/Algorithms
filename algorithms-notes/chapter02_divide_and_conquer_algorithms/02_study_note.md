# Chapter2. Divide-and-conquer algorithms

> **PDF 초안** <https://book.huihoo.com/pdf/algorithms/chap2.pdf>

---
분할 정복(divide-and-conquer) 전략은 다음의 세 단계로 문제를 해결한다. <br/>
1. 문제를 같은 종류의 더 작은 하위 문제들로 분활 <br/>
2. 이 하위 문제들을 재귀적으로 해결 <br/>
3. 부분 해답들을 결합하여 전체 해답 도출 <br/>

실제 계산은 아래와 같이 발생한다.
* (a) 문제를 하위 문제로 분활할 때
* (b) 재귀의 맨 끝에서 하위 문제가 너무 작아서 완전히 해결될 때
* (c) 부분 해를 합칠 때

---

## 2.1 Multiplication
<img width="1288" height="1036" alt="image" src="https://github.com/user-attachments/assets/ce333fc2-fbca-46fb-9ab3-64318e1f9af9" />
수학자 칼 프리드리히 가우스(1777-1855)는 두 복소수의 곱 $(a + bi)(c + di) = ac - bd + (bc + ad)i$ 이 4번의 실수 곱셈을 포함하는 것처럼 보이지만, 

$$
(a + bi)(c + di) = ac - bd + (bc + ad)i
$$

실제로는 단 3번만으로 할 수 있다는 것을 발견했다. `ac, bd, 그리고 (a+b)(c+d)`를 사용하면, 아래와 같이 가능하다.

$$
bc + ad = (a+b)(c+d) -ac -bd
$$

big-O 방식의 사고에서는 곱셈 횟수를 4회에서 3회로 줄이는 것이 쓸데없는 기발함처럼 보인다. 하지만, 이 적은 개선이 재귀적으로 적용될 때 매우 중요해진다. <br/>
복소수에서 벗어나 이것이 어떻게 일반적인 곱셈에 도움이 되는지 보자. `x`와 `y`가 두 n비트 정수라고하고, 편의상 n이 2의 거듭제곱이라고 가정하자. (더 일반적인 경우도 이와 다르지 않음). <br/>
`x`와 `y`를 곱하는 첫 번째 단계로, 각각을 n/2비트 길이인 왼쪽과 오른쪽 절반으로 나눈다.


$$
\begin{aligned}
x &= \boxed{x_L}\,\boxed{x_R} = 2^{\tfrac{n}{2}}x_L + x_R \\
y &= \boxed{y_L}\,\boxed{y_R} = 2^{\tfrac{n}{2}}y_L + y_R
\end{aligned}
$$

예를 들어, $X = 10110110_{2}$ (아래첨자 $2$는 ‘이진수’를 의미)라면 $X_{L} = 1011_{2}$, $X_{R} = 0110_{2}$이고, $X = 1011_{2} \times 2^{4} + 0110_{2}$이다. 그러면 $X$와 $Y$의 곱은 다음과 같이 다시 쓸 수 있다.

$$
\begin{aligned}
xy &= (2^{\tfrac{n}{2}}x_L + x_R)(2^{\tfrac{n}{2}}y_L + y_R) \\
   &= 2^{n}x_L y_L + 2^{\tfrac{n}{2}}\bigl(x_L y_R + x_R y_L\bigr) + x_R y_R
\end{aligned}
$$

이 오른쪽 식을 통해 `xy`	를 계산한다. 덧셈은 선형 시간이고, 2의 거듭제곱 곱셈은 단순한 비트 시프트이다. 핵심은 네 번의 `n/2` 비트 곱셈인 $x_{L}$ $\times$ $y_{L}$ , $x_{L}$ $\times$ $y_{R}$ , $x_{R}$ $\times$ $y_{L}$ , $x_{R}$ $\times$ $y_{R}$ 이다. 이들은 네 번의 재귀 호출로 처리할 수 있다.

따라서 n비트 수 곱셈은 절반 크기의 네 개 부분문제를 재귀적으로 해결한 후, 위 식을 `O(n)` 시간에 계산하는 것으로 귀결된다. 전체 실행 시간을 `T(n)`이라고 하면:

$$
T(n) = 4T(\tfrac{n}{2}) + O(n)
$$

이는 $O(n^{2})$가 되는데, 전통적인 초등학교 곱셈과 같은 시간이다. 새로운 알고리즘이지만 아직 효율성 개선은 없다. 여기서 가우스의 트릭이 등장한다. `xy`식이 네 번의 `n/2`비트 곱셈을 요구하는 것 같지만, 실제로는 세 번만 있으면 된다. 왜냐하면:

$$x_{L}\times y_{L} ,\enspace	 x_{R}\times y_{R} ,\enspace	 (x_{L}+x_{R})(y_{L}+y_{R})$$
$$
x_{L}\times y_{R}\enspace+\enspace x_{R}\times y_{L}
\enspace=\enspace
(x_{L}+x_{R})(y_{L}+y_{R})
\enspace-\enspace
x_{L}\times y_{L}
\enspace-\enspace
x_{R}\times y_{R}
$$

따라서, 아래와 같이 개선된 실행 시간을 갖는다.

$$
T(n) = 3T(\tfrac{n}{2}) + O(n)
$$

핵심은 상수 개선이 4에서 3으로, 재귀의 모든 레벨에서 발생한다는 것이다. 이 복합 효과는 `O(n^1.59)`라는 극적으로 낮은 시간 복잡도를 가져온다. <br/>
이 실행 시간은 알고리즘의 재귀 호출 패턴을 트리로 분석하면 도출할 수 있다. 재귀의 각 레벨에서 부분문제들은 크기가 절반으로 줄어든다. $log_{2}n$번째 레벨에서 부분문제들이 크기가 1이 되어 재귀가 끝난다. 따라서 트리 높이는 $log_{2}n$이다. 분기 계수는 3이므로 깊이 `k`에서 $3^{k}$ 개의 부분문제가 있고, 각각의 크기는 ${\tfrac{n}{2}}^{k}$이다.

각 부분문제에서 추가 부분문제를 식별하고 답을 합치는 데 선형적인 작업이 필요하다. 따라서 깊이 `k`에서 총 시간은 아래와 같다.

$$
3^{k} \times O({\tfrac{n}{2}})^{k} = (\tfrac{3}{2})^{k} \times O(n)
$$

최상위에서 `k=0`일 때 `O(n)`이고, 최하위에서 $k=log_{2}n$일 때 $O(3^{log_{2}n}) = O(n^{log_{2}3})$이다. 이 두 끝점 사이에서 작업량은 레벨마다 $\tfrac{3}{2}$배씩 기하급수적으로 증가한다. 증가하는 기하급수의 합은 상수 배수 내에서 마지막 항과 같다. 따라서 전체 실행 시간은 $O(n^{log_{2}3}) ≈ O(n^{1.59})$ 이다.

가우스의 트릭이 없다면 분기 계수가 4가 되고, $4^{log_{2}n}) = n^2$ 개의 잎을 가져 적어도 이만큼의 실행 시간이 필요하다. 분활정복 알고리즘에서 부분문제 개수는 재귀 트리의 분기 계수가 된다. 이 계수의 작은 변화가 실행 시간에 큰 영향을 미칠 수 있다.

> 실용적 참고 : 일반적으로 1비트까지 재귀하는 것은 의미가 없다. 대부분 프로세서에서 16비트나 32비트 곱셈은 단일 연산이므로, 수가 이 범위에 도달하면 내장 프로시저를 사용하는 것이 좋다.

### 정수 곱셈을 위한 분할정복 알고리즘 코드(pseudocode)
```pseudocode
function multiply(x, y)
Input:  Positive integers x and y, in binary
Output: Their product

n = max(size of x, size of y)
if n = 1: return x y

x_L, x_R = leftmost ⌈n/2⌉, rightmost ⌊n/2⌋ bits of x
y_L, y_R = leftmost ⌈n/2⌉, rightmost ⌊n/2⌋ bits of y

P1 = multiply(x_L, y_L)
P2 = multiply(x_R, y_R)
P3 = multiply(x_L + x_R, y_L + y_R)

return P1 × 2^n + (P3 − P1 − P2) × 2^{n/2} + P2

```
> 02_study_code.py를 통해 실습 가능

---

## 2.2 점화식
<img width="1288" height="862" alt="image" src="https://github.com/user-attachments/assets/68ccb5ee-5697-4e10-8c15-1ca4787f1328" />
분할정복 알고리즘은 종종 일반적인 패턴을 따른다. 크기 `n`인 문제를 `a > 0`, `b > 1`, `d ≥ 0` 인 상수들에 대해 크기 $\tfrac{n}{b}$인 `a`개의 부분문제로 재귀적으로 해결하고, 이 답들을 $O(n^{d})$ 시간에 합친다. (곱셈 알고리즘에서는 `a = 3`, `b = 2`, `d = 1`). 따라서 실행 시간은 $T(n) = aT([\tfrac{n}{b}]) + O(n^{d})$로 나타낼 수 있다.

이제 이 일반적인 점화식의 해를 구해보면, `마스터 정리` $T(n) = aT([\tfrac{n}{b}]) + O(n^{d})$가 상수 `a > 0`, `b > 1`, `d ≥ 0`에 대해 성립하면:

$$
T(n)=
\begin{cases}
\mathcal{O}(n^{d})              & \text{if } d > \log_{b} a,\\
\mathcal{O}(n^{d}\log n)        & \text{if } d = \log_{b} a,\\
\mathcal{O}(n^{\log_{b} a})     & \text{if } d < \log_{b} a.
\end{cases}
$$



이 단일 정리가 우리가 사용할 대부분의 분할정복 알고리즘의 실행 시간을 알려준다.

증명: 편의상 `n`이 `b`의 거듭제곱이라고 하자. 부분문제 크기가 각 레벨에서 `b`배씩 감소하므로 $log_{b}n$레벨 후 기본 경우에 도달한다. 이것이 재귀 트리의 높이다. 분기 계수는 `a` 이므로 `k`번째 레벨에 각각 크기 $(\tfrac{n}{b})^{k}$인 $a^{k}$개의 부분문제가 있다.

이 레벨에서 총 작업량은:

$$
a^{k} \times \mathcal{O} \left(\left(\frac{n}{b^{k}}\right)^{d}\right)
= \mathcal{O}(n^{d})\left(\frac{a}{b^{d}}\right)^{k}
$$

`k`가 O부터 $log_{b}n$까지 변할 때, 비율 $\tfrac({a}{b})^{d}$인 기하급수를 형성한다. 비율이 1보다 작으면 첫 항이 지배적이고, 1보다 크면 마지막 항이 지배적이며, 정확히 1이면 모든 항이 같다.

### 이진 탐색
궁극적인 분할정복 알고리즘은 이진 탐색이다. 정렬된 배열에서 키를 찾기 위해 중간 원소와 비교하고, 결과에 따라 절반에서 재귀한다. 점화식은 $T(n) = T([\tfrac{n}{2}] + O(1)$ 이고, 이는 `a = 1`, `b = 2`, `d = 0`인 경우다. 마스터 정리를 적용하면 친숙한 $O(logn)$ 실행 시간을 얻는다.

---

## 2.3 Mergesort
<img width="1288" height="678" alt="image" src="https://github.com/user-attachments/assets/5f85c951-7a8b-4368-956b-5b6b535ec8bd" />

---

## 2.4 Medians

---

## 2.5 Matrix multiplication

---

## 2.6 The fast Fourier transform



