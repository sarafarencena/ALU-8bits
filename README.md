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

## 4. Multiplicador de 8 bits
 
O multiplicador recebe dois operandos de 8 bits, sendo eles:
-  **AC** (multiplicando)
- **N** (multiplicador)

E produz um resultado de 16 bits dividido em duas saídas:
- **AC_out** (8 bits menos significativos)
- **MQ_out** (8 bits mais significativos).

O circuito implementa multiplicação binária combinacional pelo método shift-and-add, que replica o processo de multiplicação no papel: para cada bit de N, soma-se AC deslocado para a esquerda pela posição daquele bit.
 
### Separação de N
 
A entrada N é dividida em 8 bits individuais (N0 a N7) por um Distribuidor.
 
### Produtos parciais via AND
 
Para cada bit Ni, um broadcast replica esse bit 8 vezes, formando um barramento de 8 bits. Esse barramento entra em uma porta AND de 8 bits junto com AC:
 
- Se Ni = 1, a AND retorna AC inteiro
- Se Ni = 0, a AND retorna zeros
 
Isso gera 8 produtos parciais de 8 bits, um por bit de N.
 
### Posicionamento em 16 bits (shift left)
 
Cada produto parcial passa por um Distribuidor que o insere na posição correta dentro de um barramento de 16 bits, preenchendo o restante com zeros. A configuração muda a cada estágio para simular o deslocamento:
 
| Produto | Configuração do Distribuidor | Efeito |
|---------|------------------------------|--------|
| PP0 | 8,8 → 16 | sem deslocamento |
| PP1 | 1,8,7 → 16 | shift left 1 |
| PP2 | 2,8,6 → 16 | shift left 2 |
| PP3 | 3,8,5 → 16 | shift left 3 |
| PP4 | 4,8,4 → 16 | shift left 4 |
| PP5 | 5,8,3 → 16 | shift left 5 |
| PP6 | 6,8,2 → 16 | shift left 6 |
| PP7 | 7,8,1 → 16 | shift left 7 |
 
O shift left acontece pela posição do fio dentro do barramento — sem nenhum componente adicional de deslocamento.
 
### Soma em cascata
 
Os 8 produtos parciais de 16 bits são somados em sequência por 7 instâncias do `soma_16bits`:
 
```
SOM1: PP0 + PP1 → R1
SOM2: R1  + PP2 → R2
SOM3: R2  + PP3 → R3
SOM4: R3  + PP4 → R4
SOM5: R4  + PP5 → R5
SOM6: R5  + PP6 → R6
SOM7: R6  + PP7 → Resultado final (16 bits)
```
 
### Divisão do resultado
 
Um Distribuidor final separa o resultado de 16 bits:
 
- Bits 0–7 → **AC_out**
- Bits 8–15 → **MQ_out**
 
 
## Casos de teste
 
| AC | N | Resultado | AC_out esperado | MQ_out esperado |
|----|---|-----------|-----------------|-----------------|
| 5  | 3 | 15        | 15              | 0               |
| 16 | 16 | 256      | 0               | 1               |
 
O segundo caso (16 × 16 = 256) é especialmente útil pois o resultado cruza a fronteira entre AC_out e MQ_out, validando que a divisão final está correta. Como 256 = 2⁸, o único bit 1 fica exatamente na posição 8 — que pertence a MQ_out.

---

Acesse [aqui](https://drive.google.com/drive/folders/1XJji9Yzx_nmVaWa0aK6dnY-uWFvmWiFX?usp=sharing) o drive do projeto.
