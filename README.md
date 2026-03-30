# ALU 8 bits

## Vídeos Demonstrativos

Clique [aqui](https://drive.google.com/file/d/1cZZ8Wo0065HitNznaCnbhJr6h3rzmtDr/view?usp=sharing) para visualizar o vídeo de demonstração dos circuitos de somadores (1 e 8 bits) e subtrator (8 bits).

Clique [aqui](https://drive.google.com/file/d/1TRvcSZZhoU6Z6audIx-Jgsx_4Kjzwab_/view?usp=sharing) visualizar o vídeo de demonstração do circuito de multiplicador de 8 bits.

## 1. Somador Completo de 1 Bit

No Somador Completo de 1 bit, baseado na tabela verdade, soma-se  dois bits (A e B), considerando um carry de entrada (Cin), e produz uma soma (S) e um carry de saída (Cout).

**Tabela Verdade:**
| A | B | Cin | Soma | Cout |
|---|---|-----|------|------|
| 0 | 0 | 0   | 0    | 0    |
| 0 | 0 | 1   | 1    | 0    |
| 0 | 1 | 0   | 1    | 0    |
| 0 | 1 | 1   | 0    | 1    |
| 1 | 0 | 0   | 1    | 0    |
| 1 | 0 | 1   | 0    | 1    |
| 1 | 1 | 0   | 0    | 1    |
| 1 | 1 | 1   | 1    | 1    |

**Entradas:**
- `A` (1 bit): Primeiro operando
- `B` (1 bit): Segundo operando
- `Cin` (1 bit): Carry de entrada (vindo do bit anterior)

**Saídas:**
- `Soma` (1 bit): Resultado da soma (A XOR B XOR Cin)
- `Cout` (1 bit): Carry de saída para o próximo bit

**Lógica Interna:**
```
Soma = A XOR B XOR Cin
Cout = (A AND B) OR (A AND Cin) OR (B AND Cin)
```

**Funcionamento:**
- A saída Soma é 1 quando há número ÍMPAR de 1s nas entradas
- O Carry é 1 quando há PELO MENOS DOIS 1s nas entradas

**Testes Validados:**
- 0 + 0 + 0 = 0 | Cout = 0
- 1 + 1 + 0 = 0 | Cout = 1
- 1 + 1 + 1 = 1 | Cout = 1

## 2. Somador Completo de 8 Bits

No somador completo de 8 bits é implementada a soma binária para números de 8 bits encadeando 8 Somadores Completos de 1 bit.
O carry de cada bit se propaga para o próximo (Ripple Carry Adder).

**Entradas:**
- `A` (8 bits): Primeiro operando
- `B` (8 bits): Segundo operando
- `Cin` (1 bit): Carry de entrada inicial (normalmente 0)

**Saídas:**
- `S` (8 bits): Resultado da soma
- `Over` (1 bit): Carry/Overflow final

**Funcionamento:**
```
Bit 0:   A[0] + B[0] + Cin → S[0], Cout(0)
Bit 1:   A[1] + B[1] + Cout(0) → S[1], Cout(1)
Bit 2:   A[2] + B[2] + Cout(1) → S[2], Cout(2)
...
Bit 7:   A[7] + B[7] + Cout(6) → S[7], Cout(7)
```

O carry "ondula", saindo de um bit para o próximo (por isso "Ripple Carry").

**Testes Validados:**
| A   | B   | Cin | S     | Over | Descrição |
|-----|-----|-----|-------|------|-----------|
| 100 | 50  | 0   | 150   | 0    | Soma normal |
| 255 | 1   | 0   | 256   | 0    | Resultado 9 bits |
| 255 | 255 | 0   | 254   | 1    | Overflow |

Quando A + B ≥ 256, o resultado não cabe em 8 bits. O bit 9 é armazenado em `Over` (overflow).


## 3. Subtrator de 8 Bits (Complemento de 2)

Implementa subtração usando o mesmo Somador de 8 bits por meio do Complemento de 2, já que ele permite usar o **mesmo circuito** de soma para subtrair. Assim, para subtrair B de A: A - B = A + (NOT B) + 1.

**Entradas:**
- `A` (8 bits): Número que será diminuído
- `B` (8 bits): Número que diminui
- `Cin` (1 bit): Sinal de controle (0 = soma, 1 = subtração)

**Saídas:**
- `S` (8 bits): Resultado
- `Over` (1 bit): Overflow/Carry final

**Funcionamento (quando Cin = 1 para subtração):**
1. Cada bit de B é invertido via XOR (B XOR Cin)
2. O Cin = 1 é injetado no somador (adiciona o +1)
3. Resultado: A + NOT(B) + 1 = A - B

**Operações:**
- **Cin = 0 (Soma):** S = A + B
- **Cin = 1 (Subtração):** S = A - B (via complemento de 2)

**Testes Validados:**
| A   | B   | Cin | Operação | S    | Descrição |
|-----|-----|-----|----------|------|-----------|
| 20  | 5   | 0   | A + B    | 25   | Soma normal |
| 20  | 5   | 1   | A - B    | 15   | Subtração |
| 10  | 20  | 1   | A - B    | 246  | Resultado negativo (-10 em signed) |
