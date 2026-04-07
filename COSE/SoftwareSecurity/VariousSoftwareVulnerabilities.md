# 🔐Various Software Vulnerabilities

3/31 수업~

## Integer Overflow / Underflow

|자료형|범위|
|-|-|
|char|0 ~ 65535|
|byte (1 byte)|-128 ~ 127|
|short (2 byte)|-32768 ~ 32767|
|int (4 byte)|-2^31 ~ 2^31 - 1|
|long (8 byte)|-2^63 ~ 2^63 - 1|
|float (4 byte)|1.4E-45 ~ 3.4E38|
|double (8 byte)|4.9E-324 ~ 1.8E308|

### 일어나는 원인
signed 자료형들은 첫 비트를 부호 표기를 위해 사용  
--> 최댓값을 넘어서면 부호가 바뀜

### 방지 방법
1. 입력값이 유효한지 검사
2. GCC 컴파일 옵션 이용 (ex. GCC -ftrapv: 덧셈/뺄셈/곱셈의 signed overflow에 대한 trap 생성)

## Command Injection

<img width="987" height="527" alt="image" src="https://github.com/user-attachments/assets/1f51cbce-96ac-4580-8b04-3f78c8a64269" />

### 예시

<img width="997" height="355" alt="image" src="https://github.com/user-attachments/assets/d7aa5abc-3da0-4b66-885e-e6bb1a4932c8" />

▶ IP 주소 외에 추가로 커맨드를 입력할 수 있음: Injection!!  
<img width="607" height="241" alt="image" src="https://github.com/user-attachments/assets/bf13df7c-ea4f-4c99-bc5e-edc02dfcf9e0" />

### 방지 방법
1. 입력값이 유효한지 검사
2. System Call 사용을 제한
3. Principle of Least Privilege  
    - Running a program with root privileges should be avoided as much as possible  
    - Necessary permissions should only be granted when required

## Code Injection
신뢰할 수 없는 입력이 코드로서 실행 가능할 때 발생

ex)
- SQL Injection
- HTML/JS Injection (XSS)
- **eval() 함수 등 사용**

### eval() 함수 사용 시 공격 예시
<img width="792" height="295" alt="image" src="https://github.com/user-attachments/assets/3984b2c5-d41b-432a-ab87-91090ea555bf" />

<img width="757" height="178" alt="image" src="https://github.com/user-attachments/assets/ae5d7c5d-4656-4fe4-af42-dd82c15c1915" />

### 방지 방법
1. 가능하면 eval(), exec() 등의 함수 사용하지 않기
2. 입력값 검사 (**zero-trust**)

## Path Traversal
파일 주소는 **../**를 통해 상위 디렉토리로 이동할 수 있음  
--> 상위 주소의 중요 파일들에 접근 가능

<img width="646" height="80" alt="image" src="https://github.com/user-attachments/assets/15c9a401-3ed0-4b03-8d6a-a482a1252e86" />

## Serialization
데이터를 파일로서 저장하는 방법  
복잡한 object를 byte stream으로 변경

데이터를 그냥 파일에 쓰면: 파싱이 어렵고 type 보증이 안 됨  
--> JSON 사용: 파일 구조화는 되지만, 기본적인 자료형에 대해서만 지원함  
--> Serialization하여 저장!

### 취약점
신뢰할 수 없는 데이터에 대한 deserialization은 위험!
- Remote Code Execution (RCE)
- Arbitrary file read/write
- Denial of Service
- Authentication bypass

공격자가 임의로 위험한 serialized object를 만들면, deserialization 과정에서 위험한 method나 constructor가 실행 가능

<img width="475" height="232" alt="image" src="https://github.com/user-attachments/assets/3467ab7e-9f30-496b-ab4c-209029d2048c" />






























