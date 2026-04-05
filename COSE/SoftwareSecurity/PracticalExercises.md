# 🔐Practical Exercises

3/19 수업~

---

## Basic Assembly Instructions

### Register
CPU가 사용하는 변수

(x86, 32비트 CPU 레지스터)
|이름|목적|Pointing Area|
|-|-|-|
|esp(stack pointer)|현재 스택 위치|Stack|
|ebp(stack base pointer)|스택 base 위치|Stack|
|eip(instruction pointer)|현재 실행 중인 명령어(instruction)의 위치|Text|
|eax|다목적(주로 함수 반환값 저장)|-|
|ebx|다목적|-|
|ecx|다목적(주로 루프 반복 횟수 저장)|-|
|edx|다목적|-|
|esi(source index)|다목적(주로 데이터 이동 시 source 위치)|-|
|edi(destination index)|다목적(주로 데이터 이동 시 destination 위치)|-|

|이름|크기|
|-|-|
|QWORD|8byte|
|DWORD|4byte|
|WORD|2byte|
|BYTE|1byte|


## Stack Buffer Overflow
쉬운 방법: 취약한 함수가 실행될 때 RET 주소를 변경하여 /bin/sh (또는 /bin/bash)를 실행  
```
execve("/bin//sh", ["/bin//sh", NULL], NULL)
```
```
shellcode = 
b"\x31\xc0\x50\x68\x6e\x2f\x73\x68\x68\x2f\x2f\x62\x69\
x89\xe3\x31\xc9\x31\xd2\xb0\x08\x40\x40\x40\xcd\x80"
```

<img width="321" height="261" alt="image" src="https://github.com/user-attachments/assets/f27c6d6f-1da8-4ee6-bea3-f08a524e14ed" />

<img width="833" height="250" alt="image" src="https://github.com/user-attachments/assets/69d68bd3-ec57-4c16-89fb-e141ad49e364" />

(코드 상에서 buf를 40byte를 받으므로, 그만큼의 dummy 문자를 추가함)

but 이 방법은 **\x31 등을 문자 하나가 아니라 각각 따로 4개의 문자로 읽기 때문에** 제대로 동작하지 않음  
--> Pwntools를 이용해 코드를 전송

<img width="997" height="337" alt="image" src="https://github.com/user-attachments/assets/e82ffefc-bc21-4346-8e86-7d073b622851" />

## BoF Mitigation & Bypass

### 🔵 OS 수준의 방어 방법: ASLR
Address Space Layout Randomization

기존 BoF(Buffer Overflow) 공격 방법은 Shellcode를 입력값으로 넣고, 그 주소를 RET에 넣음으로서 공격함  
--> 주소를 매번 랜덤하게 바뀌면 주소를 알 수 없지 않을까?

<img width="612" height="285" alt="image" src="https://github.com/user-attachments/assets/e800e6c1-e3a7-4e0e-994d-02c4fa3c8571" />

※ TEXT 위치가 안 바뀌는 이유: PIE(Position Independent Executable) 옵션을 켜면 TEXT 위치도 바뀜

### 🔴 ASLR 우회 방법: Memory Disclosure
ASLR은 각 섹션(Stack, Heap 등)의 **"base"** 주소만 바꾸고 offset은 바꾸지 않음  
--> base 주소 **역산 가능**

### 🔵 방어 방법: CANARY
변수와 SFP 사이에 랜덤한 값을 넣어서 기억 → 그 값이 변하면 감지!

<img width="613" height="237" alt="image" src="https://github.com/user-attachments/assets/4e41c724-af55-4890-aa27-db13077541d3" />

### 🔴 CANARY 우회 방법: Memory Disclosure
대부분의 경우에 CANARY의 첫 바이트는 NULL byte (0x00)  
▶ 이유: **string-based** 오버플로우 공격을 막기 위해

--> 이 점을 역이용해 CANARY의 값을 확인 가능, BoF 공격 시 CANARY 값을 정확히 같은 주소에 넣어 감지할 수 없게 함

### 🔵 방어 방법: NX (Page Table Entry)
스택 영역의 코드가 실행 가능한 것이 문제!!  
--> 스택 영역의 실행 권한을 아예 제거  

<img width="757" height="75" alt="image" src="https://github.com/user-attachments/assets/87942ff3-89c2-49d8-ac3f-42fb464fa853" />

### 🔴 NX 우회 방법: RTL & ROP
스택의 코드를 바로 실행하게 하는 것이 아닌, **시스템 자체 함수**를 사용하도록 RET 변경  
주로 **libc** 라이브러리 사용 (printf, scanf 등 C언어 개발의 필수 라이브러리)

#### 방법
1. ldd 명령어를 통해 libc 라이브러리의 위치 찾기

<img width="542" height="70" alt="image" src="https://github.com/user-attachments/assets/aac89040-92b5-478d-ad4e-f3fa3fdce721" />

2. 현재 함수와 시스템 함수의 주소 offset 계산

<img width="613" height="232" alt="image" src="https://github.com/user-attachments/assets/182d62e4-6d14-4eb6-b459-05b29a9530a9" />

3. 공격!

<img width="713" height="267" alt="image" src="https://github.com/user-attachments/assets/52326f42-64d8-4d9d-a716-1095908939b1" />

자세한 예시는 교재 참고 (lecture3-III.pdf)

## x64 Stack BoF

(x64, 64비트 CPU 레지스터, 32비트 레지스터에서 확장 = 32비트 레지스터도 여전히 사용 가능)
|이름|목적|Pointing Area|
|-|-|-|
|rsp(stack pointer)|현재 스택 위치|Stack|
|rbp(stack base pointer)|스택 base 위치|Stack|
|rip(instruction pointer)|현재 실행 중인 명령어(instruction)의 위치|Text|
|rax|다목적(주로 함수 반환값 저장)|-|
|rbx|다목적|-|
|rcx|다목적(주로 루프 반복 횟수 저장)|-|
|rdx|다목적|-|
|rsi(source index)|다목적(주로 데이터 이동 시 source 위치)|-|
|rdi(destination index)|다목적(주로 데이터 이동 시 destination 위치)|-|
|**r8~r15 (x64 only)**|다목적(주로 함수 매개변수로 사용)|-|

























