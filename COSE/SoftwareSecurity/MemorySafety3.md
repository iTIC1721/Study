# 🔐Memory Safety III

3/17 수업

---
## 메모리 관련 취약점

C/C++에서는 직접 메모리를 관리해야 함:  
`malloc()` → 메모리 할당  
`free()` → 메모리 해제  

1. **Memory Leak**

메모리를 할당했는데 제때 해제하지 않으면 불필요한 메모리가 계속 쌓여 시스템 성능이 저하됨

2. **Double Free**

이미 해제한 메모리를 다시 해제함
- 메모리 관리 구조가 깨짐
- 공격자가 구조를 조작 가능

3. **Use After Free**

이미 해제한 메모리를 사용
- 공격자가 그 자리에 데이터를 넣으면 → 프로그램이 그걸 "정상 데이터"로 착각할 수 있음  

- 예시) 리눅스 시스템은 ptmalloc2으로 메모리 할당 → free해도 바로 지워지지 않고 잠시 남아 있음  
  => A를 free하고 같은 크기의 B를 다시 할당하면 A의 메모리를 재사용함으로서 A 데이터를 읽어올 수 있음

> malloc / free를 잘 하면 해결되는 문제?
> > 조건 분기가 많고 에러 처리가 복잡하며, 휴먼 에러가 발생할 가능성 매우 높음

## 레지스터
| 레지스터    | 역할        |
| ------- | --------- |
| esp (stack pointer)     | 스택 top    |
| ebp (stack base pointer)    | 스택 기준     |
| eip (instruction pointer)    | 실행할 코드 위치 |
| eax     | 자유 사용 (주로 반환값)       |
| ecx     | 자유 사용 (주로 반복 카운터)    |
| esi/edi | 자유 사용 (주로 데이터 이동)    |

### Stack Frame
각 함수의 스택 공간을 구별하기 위한 공간  
ebp부터 esp까지의 공간을 사용
```
Stack Frame of function:  
[지역 변수 a]  ← esp
[지역 변수 b]
[SFP]          ← ebp
[Return Address]
```

### Assembly 핵심 명령어

1. **MOV**  
src을 dst에 복사
```
mov dst, src
```

ex)
```
<Register>      <Memory>
edi: 0x0        0xff00 -> 0x11223344
esi: 0xff00     0xff04 -> 0x55667788
ecx: 0x1
```
```
mov edi, esi                    => 값 복사                 => edi: 0xff00
mov edi, DWORD PTR[esi]         => 메모리 참조             => edi: 0x11223344
mov edi, DWORD PTR[esi + 4*ecx] => 메모리 참조(+4*ecx의값) => edi: 0x55667788
```

2. **LEA**  
주소값을 계산
```
lea edi, [esi + 4*ecx]  => 메모리 주소를 계산 => edi: 0xff04
```

3. **ADD / SUB**  
산술 연산
```
<Register>      <Memory>
edi: 0x5        0xff00 -> 0x1
esi: 0xff00     0xff04 -> 0x2
```
```
add edi, 0x5            => EDI += 0x5           => edi: 0xA
add edi, DWORD PTR[esi] => EDI += *(DWORD *)esi => edi: 0x6

sub edi, 0x3            => EDI -= 0x5           => edi: 0x2
sub edi, DWORD PTR[esi] => EDI -= *(DWORD *)esi => edi: 0x4
```

4. **INC / DEC**  
+1 / -1

5. **AND / OR / XOR / NOT**  
논리 연산
```
and dst, src => DST = DST & SRC
or dst, src  => DST = DST | SRC
xor dst, src => DST = DST ^ SRC
not dst      => DST = !DST
```

6. **PUSH / POP**  
스택 입/출력 연산
```
push eax => esp(스택 top)이 4 감소하고, 값이 스택에 저장 (스택은 위 → 아래)
pop eax  => 스택에서 값을 꺼내고 esp(스택 top)이 4 증가
```

7. **LEAVE**  
스택 정리
```
leave  => mov esp, ebp; pop ebp;
=> 스택 top을 스택 기준 위치로 옮김으로서 스택에 데이터기 없는 것처럼 처리
```

















