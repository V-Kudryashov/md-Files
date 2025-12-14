
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
[ M_bb M_bj ] [ q̈_b ] + [ h_b ] = [ 0 ]
[ M_jb M_jj ] [ q̈_j ] [ h_j ] [ τ ]

Где:

- `M_bb` — инерция base,
- `M_jj` — инерция суставов,
- `M_bj`, `M_jb` — связи base–суставы,
- `h_b`, `h_j` — кориолисовы, центробежные и гравитационные члены,
- `τ` — вектор суставных моментов.

---

## 4. Постановка задачи inverse dynamics

Задано:

- ускорение base `q̈_b`,
- ускорения суставов `q̈_j`.

Требуется:

- вычислить **только** `τ`,
- **без управляющих сил на base**,
- при полном выполнении уравнений движения.

---

## 5. Результирующая формула для суставных торков

Эквивалентное решение задачи inverse dynamics:

`τ = M_jj · q̈_j + h_j − M_jb · M_bb⁻¹ · ( M_bb · q̈_b + h_b )`

Смысл:

- первый член — стандартная инверсная динамика суставов;
- второй член — **динамическое влияние base**;
- base не управляется, но его ускорение корректирует `τ`.

---

## 6. Ключевая идея алгоритма Featherstone

Алгоритм Featherstone:

- **не строит матрицу масс явно**;
- использует:
  - spatial algebra (6D векторы скорости и силы),
  - articulated-body inertia (ABI),
  - рекурсивную факторизацию.

Принципиально:

> **Base DOF исключены из управляющих переменных,  
> но полностью участвуют в динамике системы.**

---

## 7. Структура алгоритма Featherstone (концептуально)

### 7.1 Upward pass — articulated-body inertia

Для каждого звена `i` вычисляются:

- articulated inertia `I_i^A`,
- bias force `p_i^A`.

Затем эти величины рекурсивно агрегируются к родителю.

Это **ABI-рекурсия**, а не RNEA.

---

### 7.2 Floating base resolution

Для корневого звена:

- ускорение base задаётся как `a_0 = q̈_b`,
- вычисляется пространственная сила реакции:

`f_0 = I_0^A · a_0 + p_0^A`

Важно:

- `f_0` — **реакция среды / контактов**,
- это **не управляющий момент**.

---

### 7.3 Downward pass — вычисление суставных моментов

Для каждого сустава `i`:

- вычисляется ускорение звена:

`a_i = X(i←p) · a_p + S_i · q̈_i + c_i`

- затем вычисляется момент:

`τ_i = S_iᵀ · ( I_i · a_i + p_i )`

Где:

- `S_i` — motion subspace сустава,
- `X(i←p)` — spatial transform,
- `c_i` — кориолисовы и центробежные члены.

---

## 8. Отличие от Recursive Newton–Euler Algorithm

| Характеристика | RNEA | Featherstone FB-ID |
|----------------|------|--------------------|
| Floating base | Некорректно | Корректно |
| Base актуирован | Неявно | Нет |
| Constraint consistency | Нет | Да |
| Реакции контактов | Неявные | Явно вычисляемые |
| Используется в статье | Нет | Да |

---

## 9. Что именно делают авторы статьи

1. Оптимизация вычисляет ускорения:
   - `q̈_b`,
   - `q̈_j`.

2. Floating-base inverse dynamics (Featherstone):
   - преобразует ускорения в `τ`,
   - **не создаёт torques для root**,
   - сохраняет динамическую согласованность системы.

---

## 10. Ключевой вывод

Floating-base inverse dynamics в статье — это:

- **не Recursive Newton–Euler Algorithm**;
- **не computed torque с обнулением base**;
- **constraint-consistent ABI-based алгоритм Featherstone**.

Любая реализация на базе RNEA с последующим обнулением base-torque  
**не соответствует описанию статьи**.

---

## 11. Основной источник

Roy Featherstone  
*Robot Dynamics Algorithms*  
Kluwer Academic Publishers, 1987

Официальная страница автора и книги:  
https://royfeatherstone.org/robot-dynamics-algorithms.html

---

Документ предназначен для технической документации и исследовательских репозиториев.

