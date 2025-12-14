
# Floating-Base Inverse Dynamics (Featherstone)

Данный документ описывает **floating-base inverse dynamics**, используемую в статье  
*Momentum-Based Balance Control For Simulated Characters*, и объясняет,  
почему **алгоритм Featherstone принципиально отличается от Recursive Newton–Euler Algorithm (RNEA)**.

Документ **сфокусирован исключительно на инверсной динамике**.

---

## 1. Контекст из статьи

Цитата из статьи:

> Once the optimization solves for the accelerations, a floating-base inverse dynamics algorithm described in [11] is used to convert the accelerations into actuator torques.  
> Unlike Recursive Newton-Euler inverse dynamics algorithm, this algorithm assumes root is unactuated and generates consistent torques.

Ключевые моменты:

- используется **floating-base inverse dynamics**;
- корень (root, base) **не актуирован**;
- используется **не RNEA**;
- вычисляемые торки **динамически согласованы** (*constraint-consistent*).

---

## 2. Почему Recursive Newton–Euler Algorithm не подходит

Классический RNEA вычисляет торки по формуле:

`τ = M(q) · q̈ + C(q, q̇) + g(q)`

Проблема для floating-base системы:

- первые 6 DOF (base) **не имеют приводов**;
- RNEA формирует уравнения и для base;
- принудительное обнуление base-torque нарушает уравнения движения.

В результате получаются **inconsistent torques**, о которых прямо говорится в статье.

---

## 3. Уравнение движения floating-base системы

Разделим координаты:

- `q_b` — base (6 DOF, неактуированные),
- `q_j` — актуированные суставы.

Уравнение движения в блочной форме:

