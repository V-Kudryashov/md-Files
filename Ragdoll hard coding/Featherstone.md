# Floating-Base Inverse Dynamics (Featherstone)
*(в контексте Momentum-Based Balance Control For Simulated Characters)*

Документ **строго** посвящён алгоритму *floating-base inverse dynamics*, используемому в статье, и поясняет, **чем он отличается от Recursive Newton–Euler Algorithm (RNEA)** и почему применяется для управления humanoid ragdoll.

---

## 1. Контекст из статьи

Цитата:

> Once the optimization solves for the accelerations, a floating-base inverse dynamics algorithm described in [11] is used to convert the accelerations into actuator torques. Unlike Recursive Newton-Euler inverse dynamics algorithm, this algorithm assumes root is unactuated and generates consistent torques.

Ключевые утверждения:

- используется **floating-base inverse dynamics**;
- **root (base) не актуирован**;
- алгоритм **не является RNEA**;
- торки **динамически согласованы** (*constraint-consistent*).

---

## 2. Почему RNEA неприменим напрямую

Классический Recursive Newton–Euler Algorithm предполагает вычисление

$\boldsymbol{\tau} = \mathbf{M(q)\ddot q + C(q,\dot q) + g(q)}$

Для floating-base системы это приводит к проблеме:

- первые 6 DOF (base) **не имеют приводов**;
- RNEA формирует уравнения и для них;
- принудительное обнуление base-torque нарушает уравнения движения.

Именно это авторы называют **inconsistent torques**.

---

## 3. Формальная постановка floating-base inverse dynamics

Разделим координаты:

- $q_b$ — base (6 DOF, неактуированные),
- $q_j$ — суставы (актуированные).

Уравнение движения:

$\begin{bmatrix}
\mathbf{M_{bb}} & \mathbf{M_{bj}} \\
\mathbf{M_{jb}} & \mathbf{M_{jj}}
\end{bmatrix}
\begin{bmatrix}
\ddot q_b \\
\ddot q_j
\end{bmatrix}
+
\begin{bmatrix}
\mathbf{h_b} \\
\mathbf{h_j}
\end{bmatrix}
=
\begin{bmatrix}
\mathbf{0} \\
\boldsymbol{\tau}
\end{bmatrix}$

Задача inverse dynamics:

- заданы $\ddot q_b$ и $\ddot q_j$;
- требуется найти **только** $\boldsymbol{\tau}$;
- уравнение для base должно выполняться **без управляющих сил**.

---

## 4. Суть алгоритма Featherstone

Алгоритм Featherstone решает задачу **без явного построения матрицы масс**, используя:

- spatial algebra (6D векторы),
- articulated-body inertia (ABI),
- факторизацию, совместимую с ABA.

Ключевая идея:

> **Base DOF исключаются из управляющих переменных,  
> но полностью участвуют в динамике.**

---

## 5. Эквивалентная матричная формула (для понимания)

Результат, реализуемый алгоритмом:

$\boldsymbol{\tau}
=
\mathbf{M_{jj}}\ddot q_j + \mathbf{h_j}
-
\mathbf{M_{jb} M_{bb}^{-1}}
\left(
\mathbf{M_{bb}}\ddot q_b + \mathbf{h_b}
\right)$

Интерпретация:

- второй член — динамическое влияние base на суставы;
- base не управляется, но его ускорения **корректируют** суставные торки.

---

## 6. Алгоритм Featherstone (концептуально)

### 6.1 Upward pass — Articulated Body Inertia

Для каждого звена $i$ вычисляются:

- articulated inertia $\mathbf{I_i^A}$;
- bias force $\mathbf{p_i^A}$;

после чего они агрегируются к родительскому звену.

Это **ABI-рекурсия**, а не RNEA.

---

### 6.2 Floating base resolution

Для root:

$a_0 = \ddot q_b$

$\mathbf{f_0} = \mathbf{I_0^A} a_0 + \mathbf{p_0^A}$

Важно:

- $\mathbf{f_0}$ — **реакция среды / контактов**;
- это **не управляющий torque**.

---

### 6.3 Downward pass — вычисление суставных торков

Для каждого сустава $i$:

$a_i = \mathbf{X_{i \leftarrow p}} a_p + \mathbf{S_i}\ddot q_i + \mathbf{c_i}$

$\tau_i = \mathbf{S_i^T} \left( \mathbf{I_i} a_i + \mathbf{p_i} \right)$

Где:

- $\mathbf{S_i}$ — motion subspace сустава,
- $\mathbf{X}$ — spatial transform,
- $\mathbf{c_i}$ — кориолисовы и центробежные члены.

---

## 7. Принципиальное отличие от RNEA

| Характеристика | RNEA | Featherstone FB-ID |
|----------------|------|--------------------|
| Base актуирован | Да (неявно) | Нет |
| Floating base | Некорректно | Корректно |
| Constraint consistency | Нет | Да |
| Контактные реакции | Неявные | Явно вычисляемые |
| Используется в статье | ❌ | ✅ |

---

## 8. Что именно делают авторы статьи

1. Оптимизация вычисляет ускорения:
   - $\ddot q_b$,
   - $\ddot q_j$.

2. Floating-base inverse dynamics (Featherstone):
   - преобразует ускорения в $\boldsymbol{\tau}$;
   - **не создаёт torques для root**;
   - сохраняет динамическую согласованность.

---

## 9. Ключевой вывод

> Floating-base inverse dynamics в статье —  
> это **не Recursive Newton–Euler**,  
> а **constraint-consistent ABI-based алгоритм Featherstone**.

Если используется RNEA с последующим обнулением base-torque —  
это **не соответствует** статье.

---

## 10. Основной источник

**Roy Featherstone**  
*Robot Dynamics Algorithms*  
Kluwer Academic Publishers, 1987

Официальная страница автора и книги:  
https://royfeatherstone.org/robot-dynamics-algorithms.html

---

*Документ предназначен для технической документации и исследовательских репозиториев.*
