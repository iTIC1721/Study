# 🔐Memory Safety II

3/12 수업

---
## Out-of-bounds (OOB)

메모리는 배열 형식으로 저장됨  
배열의 크기는 `sizeof(elem) * n`  
```
array[0] array[1] array[2] ... array[k] ... array[n - 1] 
```

**배열의 특정 원소의 주소 구하기**  
▶ `&array[k] = array + sizeof(elem) * k`

### Out-of-bounds 취약점

배열 범위를 벗어난 인덱스를 사용

ex)  
array[-1]  
array[1000] (n < 1000)  

#### OOB Read
배열 범위를 벗어난 인덱스를 사용해 데이터를 읽어옴

이 경우 다른 메모리 영역에 접근하게 됨  
→ **의도하지 않은 데이터가 노출될 수 있음!**

#### OOB Write
주어진 입력 크기를 넘는 크기의 입력값을 입력하여 배열 범위를 벗어난 주소까지 write

이 경우 다른 메모리 영역에 데이터가 덮어씌워지게 됨
→ **권한을 무시한 공격 가능!**

### 방어 방법

1. 입력값이 valid한지 검증

2. 메모리 안전한 언어 사용  
ex) Java, C#, Python, Rust 등은 자동 bound check 과정이 있음

3. Secure coding: 메모리 접근 위험이 있는 함수는 사용을 피함  
ex) strcpy, gets 등...  
동적/ 정적 분석 툴 사용  

### Buffer OVerflow 공격의 근본적인 원인
1. RET 주소를 덮어쓰기 가능
2. 버퍼 주소를 공격자가 알 수 있음
3. 버퍼가 실행 가능 영역에 존재

#### 주요 방어 기술
1. 버퍼와 return address 사이에 랜덤 값 삽입: CANARY  
CANARY 값이 변경될 것을 감지하면 프로그램이 이를 감지

2. ASLR (Address Space Layout Randomization)  
프로그램 실행 시 Stack, Heap, Library 주소를 랜덤화  
이를 통해 공격자가 shellcode(버퍼) 주소를 예측 불가능

3. NX-bit  
NX-Bit(Never eXecute Bit) == XD (eXecute Disable) / DEP (Data Execution Prevention)  
현대 컴퓨터 프로세서에서 사용되는 하드웨어 기반 보안 기능
메모리 영역을 Executable 영역과 Non-Executable 영역으로 구분
▶ Stack이나 Heap의 데이터 영역을 Non-Executable하게 함


최신 Ubuntu 시스템에서는 위의 방어 기술들이 자동으로 적용되어 있음

















