# 🔐Web Security

4/9 수업

## Cross Site Scripting (XSS)
공격자가 웹 자원에 위험 스크립트를 주입하여 웹 브라우저를 통해 실행되게 하는 공격

<img width="602" height="327" alt="image" src="https://github.com/user-attachments/assets/d1bef23e-da20-44f5-b007-8aab36613593" />

<img width="768" height="597" alt="image" src="https://github.com/user-attachments/assets/27e738ea-b857-4cc8-8c62-c4946d4de0fb" />

사용자들의 세션 쿠키 등을 공격자가 얻을 수 있음

### Reflected XSS Attack
유저 요청 -> 서버 응답 (with injected script) -> 즉시 실행  
공격자가 HTTP 요청을 통해 입력한 공격이 다시 공격자에게 돌아옴

<img width="1513" height="348" alt="image" src="https://github.com/user-attachments/assets/7363cd3b-89c4-467f-ae8f-8423c31040df" />

<img width="681" height="357" alt="image" src="https://github.com/user-attachments/assets/ce054378-a0d1-46d5-ba11-c53d56d0ddfc" />

#### 공격 시나리오
1. 공격자가 XSS 취약점을 보유한 웹 사이트를 발견
2. 공격자는 유저 정보를 탈취할 수 있는 스크립트가 담긴 악성 URL을 만든 후, 이메일 등을 통해 일반 유저들에게 해당 링크를 전송
3. 일반 유저가 악성 URL로 사이트에 접속
4. 일반 유저의 웹 브라우저를 통해 스크립트가 실행되고, 이는 (스크립트에 따라) 공격자에게 전송

### Stored XSS Attack
Attacker input -> stored on server (DB) -> victim requests page -> script execution  
서버 DB나 파일에 스크립트가 저장되어 있다가 해당 데이터/파일에 접근할 때 실행

<img width="810" height="326" alt="image" src="https://github.com/user-attachments/assets/14c5229f-6f26-46b2-8a61-aee219647fb1" />

<img width="850" height="492" alt="image" src="https://github.com/user-attachments/assets/0f7ac7da-d9bc-46e2-ba47-ea7618448da1" />

#### 공격 시나리오
1. 공격자가 XSS 취약점을 보유한 웹 사이트를 발견
2. 유저 정보를 탈취할 수 있는 스크립트가 담긴 post를 게시함
3. 일반 유저가 해당 post를 조회하면, 스크립트가 실행되고 (스크립트에 따라) 공격자에게 전송됨

<img width="1325" height="273" alt="image" src="https://github.com/user-attachments/assets/fb8ae67b-690a-4a30-8240-d273f9928800" />

### 방어 방법
1. 입력값 검사
2. 입력값의 특정 문자 제거 or 치환

## SQL Injection
<img width="1452" height="571" alt="image" src="https://github.com/user-attachments/assets/0d6f079d-5962-4e13-9c69-88a61145efe1" />

<img width="1483" height="665" alt="image" src="https://github.com/user-attachments/assets/50196fa6-e98e-4113-9449-bfc7ff6f1c0c" />

### Blind SQL Injection
부정한 방법으로 데이터베이스에서 정보를 취득  
스무고개와 같은 방식

<img width="1490" height="807" alt="image" src="https://github.com/user-attachments/assets/5c03c4cb-8c42-49bc-a20c-067861070a25" />

### 방어 방법
1. 입력값 검사
2. **Parameterized Query 사용**
```
SELECT * FROM table WHERE condition=? → SELECT * FROM table WHERE condition= value
```

## 공격의 방향성
### 1. Denial of Service (DoS)
서비스의 **Availability**를 공격  
주로 네트워크 환경에서의 공격  
소프트웨어에서는 Disk Usage, Memory, CPU 자원 등에 시행 가능

#### Distributed DoS (DDoS)
인터넷 트래픽을 집중시켜 일반적인 접근 시도가 불가능하게 만듦

<img width="588" height="398" alt="image" src="https://github.com/user-attachments/assets/bf19d44f-7af6-4384-bfac-d49360080f1d" />

### Information Leakage
서비스의 **Confidenciality**를 공격  
부정한 방법을 통해 서비스의 중요 정보를 공격자에게 전송  

### Privilege Excalation (권한 상승)
시스템/네트워크/프로그램의 인증되지 않은 접근 권한을 획득  

- Vertical: multi-level 권한 구조에서 상위 권한을 취득
- Horizontal: 동일 레벨의 다른 권한을 획득 (다른 데이터나 함수에 접근)




































