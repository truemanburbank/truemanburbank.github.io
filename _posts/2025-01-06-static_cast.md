---
title: static_casttypeName(value)
date: 2025-01-06
categories: [문법, C++]
tags: [TIL]
---
# static_cast<typeName>(value)

`static_cast`는 C++에서 제공하는 **형 변환 연산자** 중 하나로, 특정 타입으로 변환을 수행할 때 사용된다. 다른 캐스트 연산자(`dynamic_cast`, `reinterpret_cast`, `const_cast`)와 달리, **컴파일 타임에 타입 검사**를 수행하므로 안전성이 높은 편이다.

---

## **`static_cast`의 특징**

1. **컴파일 타임에 작동**:
    - 변환이 가능한지 여부를 컴파일러가 확인한다.
    - 잘못된 변환 시 컴파일 오류가 발생하여 프로그램의 안전성을 높인다.
2. **명시적인 변환**:
    - 암시적인 변환이 가능한 경우에도 사용하여 **의도를 명확히 표현**할 수 있다.
    - 가독성을 향상시키고, 실수를 줄이는 데 도움을 준다.
3. **런타임 검사 없음**:
    - `static_cast`는 런타임 타입 검사를 수행하지 않으므로, 변환이 가능한지 컴파일러가 보장하지 않는 경우 잘못된 변환이 발생할 수 있다.

---

## **`static_cast`의 주요 사용 사례**

### 1. **기본 타입 간 변환**

- 정수, 부동소수점, 열거형 등 기본 데이터 타입 간의 변환을 수행한다.

```cpp
double d = 3.14;
int i = static_cast<int>(d);  // double → int로 변환 (소수점 버림)
```

### 2. **상속 관계에서의 업캐스팅 (Upcasting)**

- 파생 클래스 객체를 기반 클래스 포인터 또는 참조로 변환할 때 사용한다.
- 이는 암시적으로도 가능하지만, `static_cast`를 사용하면 의도를 명확히 표현할 수 있다.

```cpp
class Base {};
class Derived : public Base {};

Derived derived;
Base* base = static_cast<Base*>(&derived);  // Derived → Base
```

### 3. **상속 관계에서의 다운캐스팅 (Downcasting)**

- 기반 클래스 객체를 파생 클래스 포인터 또는 참조로 변환할 때 사용한다.
- **런타임 검사가 없기 때문에 안전하지 않을 수 있다**. 따라서, 다운캐스팅에는 `dynamic_cast`가 더 권장된다.

```cpp
Base* base = new Derived();
Derived* derived = static_cast<Derived*>(base);  // Base → Deriv
```

### 4. **void*와 다른 포인터 타입 간 변환**

- `void*`는 모든 포인터 타입과 호환되므로, `static_cast`를 사용하여 원하는 타입으로 명시적으로 변환할 수 있다.

```cpp
void* ptr = &d;
double* d_ptr = static_cast<double*>(ptr);
```

### 5. **열거형 타입과 정수 간 변환**

- 열거형 값을 정수로 변환하거나, 반대로 정수를 열거형 값으로 변환할 수 있다.

```cpp
enum Color { RED, GREEN, BLUE };

Color c = static_cast<Color>(1);  // int → enum
int n = static_cast<int>(c);     // enum → in
```

---

## **`static_cast`와 C 스타일 캐스트 비교**

C 스타일 캐스트는 `(type)value` 형태로 사용되며, 변환 가능 여부를 제한하지 않고 무조건 변환을 시도한다. 이는 실수로 인한 문제를 발생시킬 가능성이 높다.

`static_cast`는 변환 가능한 경우에만 허용되므로, C 스타일 캐스트보다 더 안전하고, 의도를 명확히 나타낸다.

```cpp
double d = 3.14;
int i1 = (int)d;               // C 스타일 캐스트
int i2 = static_cast<int>(d);  // static_cast
```

---

## **주의점**

1. **런타임 타입 정보 (RTTI)가 없는 경우 위험**
    - 다운캐스팅처럼 타입 정보가 런타임에 확인되어야 하는 상황에서는 `static_cast`가 안전하지 않을 수 있다.
2. **데이터 손실 가능**
    - 타입 변환 과정에서 데이터 손실이 발생할 수 있다. 예를 들어, `double`을 `int`로 변환하면 소수점 이하의 값이 손실된다.
3. **코드 가독성**
    - 명확한 의도를 표현할 수 있지만, 잘못 사용하면 오히려 가독성을 해칠 수 있다. 적절한 주석과 함께 사용하는 것이 좋다.