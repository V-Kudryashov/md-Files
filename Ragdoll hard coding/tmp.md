# Articulated-Body Inertia (ABI) Recursions  
(Roy Featherstone — Floating-Base Inverse Dynamics)

Данный раздел описывает **пошаговые ABI-рекурсии** (articulated-body inertia),
используемые для реализации **floating-base inverse dynamics** по Featherstone.

Изложение следует структуре книги  
Roy Featherstone — *Robot Dynamics Algorithms* (1987).

---

## 1. Пространственные величины (spatial algebra)

Каждое звено `i` описывается следующими 6D величинами:

- `v_i` — пространственная скорость
- `a_i` — пространственное ускорение
- `f_i` — пространственная сила
- `I_i` — пространственная инерция
- `p_i` — bias force (Coriolis + centrifugal + gravity)

Сустав `i` описывается:

- `S_i` — motion subspace (6×1 для 1-DOF)
- `q̇_i`, `q̈_i` — скорость и ускорение сустава

---

## 2. Пространственные преобразования

Для каждого звена `i`:

- `X_i_parent` — spatial transform от родителя к `i`
- `X_parent_i = inverse(X_i_parent)`

---

## 3. Pass 1 — вычисление bias forces (forward kinematics)

Для root:


