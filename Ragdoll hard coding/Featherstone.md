# Floating-Base Inverse Dynamics (Featherstone)  
*(в контексте Momentum-Based Balance Control For Simulated Characters)*

Этот документ **строго** посвящён алгоритму *floating-base inverse dynamics*, на который ссылаются авторы статьи, и поясняет, **чем именно он отличается от Recursive Newton–Euler Algorithm (RNEA)** и почему используется для управления humanoid ragdoll.

---

## 1. Контекст из статьи

Цитата:

> *Once the optimization solves for the accelerations, a floating-base inverse dynamics algorithm described in [11] is used to convert the accelerations into actuator torques. Unlike Recursive Newton-Euler inverse dynamics algorithm, this algorithm assumes root is unactuated and generates consistent torques.*

Ключевые утверждения:

- используется **floating-base inverse dynamics**;
- **root (base) не актуирован**;
- алгоритм **не является RNEA**;
- торки **динамически согласованы** (constraint-consistent).

---

## 2. Почему RNEA неприменим напрямую

Классический Recursive Newton–Euler Algorithm предполагает:

- все обобщённые координаты либо актуированы, либо могут быть «мысленно» актуированы;
- вычисление вектора торков:
  \[
  \tau = M(q)\ddot q + C(q,\dot q) + g(q)
  \]

Для floating-base системы это приводит к проблеме:

- первые 6 DOF (base) **не имеют приводов**;
- RNEA всё равно формирует уравнения для этих DOF;
- принудительное обнуление base-torques нарушает уравнения движения.

Именно это авторы называют **inconsistent torques**.

---

## 3. Формальная постановка floating-base inverse dynamics

Разделим координаты:

- \( q_b \) — base (6 DOF, неактуированные),
- \( q_j \) — суставы (актуированные).

Уравнение движения:

\[
\begin{bmatrix}
M_{bb} & M_{bj} \\
M_{jb} & M_{jj}
\end{bmatrix}
\begin{bmatrix}
\ddot q_b \\
\ddot q_j
\end{bmatrix}
+
\begin{bmatrix}
h_b \\
h_j
\end{bmatrix}
=
\begin{bmatrix}
0 \\
\tau
\end{bmatrix}
\]

Задача inverse dynamics:

- заданы \( \ddot q_b \) и \( \ddot q_j \),
- необходимо найти **только** \( \tau \),
- при этом уравнение для base должно выполняться **без управляющих сил**.

---

## 4. Суть алгоритма Featherstone

Featherstone решает эту задачу **без явного построения матрицы масс**, используя:

- spatial algebra (6D векторы скорости и силы),
- articulated-body inertia (ABI),
- факторизацию, совместимую с ABA.

Ключевая идея:

> **Base DOF исключаются из управляющих переменных,  
но полностью участвуют в динамике.**

---

## 5. Эквивалентная матричная формула (для понимания)

Результат, который реализует алгоритм:

\[
\tau
=
M_{jj}\ddot q_j + h_j
-
M_{jb} M_{bb}^{-1}
\left(
M_{bb}\ddot q_b + h_b
\right)
\]

Интерпретация:

- второй член — это **динамическое влияние base на суставы**;
- base не требует торков, но его ускорения **корректируют** суставные торки.

---

## 6. Алгоритм Featherstone (концептуально)

### 6.1 Upward pass — Articulated Body Inertia

Для каждого звена \( i \):

- вычисляется articulated inertia \( I_i^A \);
- вычисляется bias force \( p_i^A \);
- информация агрегируется к родителю.

Это **не RNEA**, а ABI-агрегация.

---

### 6.2 Floating base resolution

Для root:

\[
a_0 = \ddot q_b
\]

\[
f_0 = I_0^A a_0 + p_0^A
\]

Важно:

- \( f_0 \) — **реакция среды / контактов**,  
- это **не управляющий torque**.

---

### 6.3 Downward pass — joint torques

Для каждого сустава \( i \):

\[
a_i = X_{i \leftarrow p} a_p + S_i \ddot q_i + c_i
\]

\[
\tau_i = S_i^T \left( I_i a_i + p_i \right)
\]

Где:

- \( S_i \) — motion subspace сустава,
- \( X \) — spatial transform,
- \( c_i \) — coriolis/centrifugal terms.

---

## 7. Принципиальное отличие от RNEA

| Характеристика | RNEA | Featherstone FB-ID |
|---------------|------|--------------------|
| Base актуирован | Да (неявно) | Нет |
| Floating base | Некорректно | Корректно |
| Constraint consistency | Нет | Да |
| Контактные реакции | Неявные | Явно вычисляемые |
| Используется в статье | ❌ | ✅ |

---

## 8. Что именно делают авторы статьи

1. Оптимизация вычисляет:
   - \( \ddot q_b \),
   - \( \ddot q_j \)

2. Floating-base inverse dynamics (Featherstone):
   - преобразует ускорения в \( \tau \),
   - **не создаёт torques для root**,
   - сохраняет динамическую согласованность.

---

## 9. Ключевой вывод

> Floating-base inverse dynamics в статье —  
> это **не Recursive Newton–Euler**,  
> а **constraint-consistent ABI-based algorithm Featherstone**.

Если при реализации:
- используется RNEA,
- а base-torques просто игнорируются —

это **не соответствует** описанию статьи.

---

## 10. Основной источник

**Roy Featherstone**  
*Robot Dynamics Algorithms*  
Kluwer Academic Publishers, 1987

Официальная страница автора и книги:  
https://royfeatherstone.org/robot-dynamics-algorithms.html

---

*Данный документ предназначен для использования в технической документации и исследовательских репозиториях.*

