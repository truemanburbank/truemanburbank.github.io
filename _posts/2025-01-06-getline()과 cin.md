---
title: getline()과 cin()
date: 2025-01-06
categories: [문법, C++]
tags: [TIL]
---
`getline()`과 `cin`은 C++에서 입력을 처리하는 주요 방법이다. 두 함수는 입력 방식과 동작에서 차이가 있으며, 각각의 장단점이 있다.

---

## **기본 차이**

1. **`cin`**
    - 공백이나 개행 문자(엔터)를 만나면 입력을 종료한다.
    - 단어 단위로 입력을 처리하며, 문자열 입력 시 공백은 허용하지 않는다.
    
    ```cpp
    std::string input;
    std::cin >> input;  // 공백 전까지 입력받음
    ```
    
2. **`getline()`**
    - 개행 문자(엔터)까지의 모든 입력을 한 줄로 처리한다.
        - 이때 개행 문자는 저장하지 않는다.
        - 개행 문자는 널 문자로 대체된다.
    - 공백을 포함한 전체 문자열을 입력받을 수 있다.
    
    ```cpp
    std::string input;
    std::getline(std::cin, input);  // 한 줄 전체를 입력받음
    ```
    

---

## **장단점 비교**

### **1. `cin`**

### **장점**

- **빠르고 간단함**: 단어 단위 입력 처리가 간단하며, 숫자나 단일 문자열 입력에 적합하다.
- **타입 변환 자동 처리**: 정수, 실수 등 다양한 데이터 타입 입력 시 자동 변환된다.
    
    ```cpp
    int num;
    std::cin >> num;  // 정수 입력
    ```
    

### **단점**

- **공백 처리 불가**: 공백이 포함된 문자열을 처리하지 못한다.
    
    ```cpp
    std::string input;
    std::cin >> input;  // "Hello World" 입력 시 "Hello"만 저장
    ```
    
- **개행 문자 처리 문제**: 입력 후 남아 있는 개행 문자(`\n`)가 이후 입력 처리에 영향을 줄 수 있다.
    
    ```cpp
    int num;
    std::cin >> num;            // 개행 문자 남음
    std::string str;
    std::getline(std::cin, str);  // 빈 문자열 입력됨
    ```
    

---

### **2. `getline()`**

### **장점**

- **공백 포함 처리**: 공백을 포함한 한 줄의 입력을 처리할 수 있다.
    
    ```cpp
    std::string input;
    std::getline(std::cin, input);  // "Hello World" 전체를 저장
    ```
    
- **개행 문자에 특화**: 한 줄 단위로 입력을 받으므로 텍스트 파일이나 긴 문자열 처리에 유용하다.

### **단점**

- **혼합 사용 문제**: `cin`과 `getline()`을 혼용하면 개행 문자 처리로 인해 예상치 못한 동작이 발생할 수 있다.
    
    ```cpp
    int num;
    std::cin >> num;
    std::string input;
    std::getline(std::cin, input);  // 빈 문자열 저장
    ```
    
    이를 해결하려면 `std::cin.ignore()`를 사용하여 남은 개행 문자를 제거해야 한다.
    
    ```cpp
    std::cin.ignore(std::numeric_limits<std::streamsize>::max(), '\n');
    ```
    
- **느림**: `cin`에 비해 약간 느릴 수 있다. 성능이 중요한 경우에는 적합하지 않을 수 있다.

---

## **용도에 따른 선택**

1. **`cin`을 사용하는 경우**
    - 단일 단어 입력
    - 숫자, 부동소수점 등 기본 데이터 타입 입력
    - 간단한 명령형 프로그램
2. **`getline()`을 사용하는 경우**
    - 공백을 포함한 문자열 입력
    - 한 줄 전체 입력 (예: 파일 처리, 긴 문장 입력)
    - 텍스트 기반 프로그램

---

## **혼합 사용 시 주의사항**

혼합해서 사용하는 경우 `cin`으로 입력받은 뒤 반드시 남아 있는 개행 문자를 제거해야 한다.

```cpp
#include <iostream>
#include <string>
int main() 
{
    int num;
    std::cin >> num;  // 정수 입력
    std::cin.ignore(std::numeric_limits<std::streamsize>::max(), '\n');  // 남은 개행 문자 제거

    std::string line;
    std::getline(std::cin, line);  // 공백 포함 문자열 입력
    std::cout << "Number: " << num << ", Line: " << line << std::endl;

    return 0;
}
```

---

## **결론**

- `cin`은 간단한 입력과 숫자 처리에 적합하며, 빠른 입력 처리가 필요할 때 유리하다.
- `getline()`은 공백을 포함한 문자열 입력에 적합하며, 긴 텍스트나 라인 단위 입력에서 유용하다.
- 혼합해서 사용할 때는 개행 문자 처리에 주의해야 한다.

# 번외: cin.get()

- `getline()` 함수처럼 동작한다.
- 그러나 개행 문자를 읽어서 버리지 않고 입력 큐에 그대로 남겨 둔다.

```cpp
// 첫 번째 호출이 입력 큐에 개행 문자를 그대로 남겨 두기 때문에
// 두 번째 호출은 그 개행 문자를 첫 문자로 만나게 된다.
// 오류 !! 
cin.get(name, ArSize);
cin.get(dessert, ArSize);

// 해결법 - 1
cin.get(name, ArSize);
cin.get();
cin.get(dessert, ArSize);

// 해결법 - 2
cin.get(name, ArSize).get();
cin.get(dessert, ArSize).get();
```

# 빈 행과 기타 문제점

`get()`이 빈 행을 읽으면 `failbit`라는 것이 설정된다. 이 행동이 뜻하는 것은 계속되는 입력을 막고, 입력을 복원하려면 다음과 같은 명령을 사용해야 한다는 것이다.

```cpp
cin.clear();
```

잠재적으로 발생할 수 있는 또 하나의 문제는 입력 문자열이 대입된 공간보다 더 길 수 있다는 것이다. 입력 행이 지정된 문자 수보다 길면 `getline()`과 `get()`은 나머지 문자들을 입력 큐에 그대로 남겨 둔다. 그러나 부가적으로 `getline()`은 `failbit`를 설정하고 더 이상의 입력을 받지 않는다.