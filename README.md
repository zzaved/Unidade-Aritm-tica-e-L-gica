# Unidade Aritmética e Lógica

## [Acesse o vídeo explicativo clicando aqui](#) 
<!-- Adicione o link para o seu vídeo explicativo aqui -->

## Sumário
1. [Introdução](#introdução)
2. [Visão Geral do Projeto](#visão-geral-do-projeto)
3. [Componentes da ULA](#componentes-da-ula)
   - [Operações Aritméticas](#operações-aritméticas)
     - [Somador de 8 bits](#somador-de-8-bits)
     - [Subtrator de 8 bits](#subtrator-de-8-bits)
     - [Multiplicador de 4 bits](#multiplicador-de-4-bits)
   - [Operações de Deslocamento](#operações-de-deslocamento)
     - [Shift Right](#shift-right)
     - [Shift Left](#shift-left)
   - [Operações Lógicas](#operações-lógicas)
     - [Operação AND](#operação-and)
     - [Operação OR](#operação-or)
     - [Operação XOR](#operação-xor)
4. [Circuito Seletor](#circuito-seletor)
5. [Detecção de Overflow](#detecção-de-overflow)
6. [Display Hexadecimal](#display-hexadecimal)
7. [Testes e Validação](#testes-e-validação)
8. [Melhorias e Otimizações](#melhorias-e-otimizações)
9. [Conclusão](#conclusão)

## Introdução

Este documento descreve o projeto e implementação de uma Unidade Lógica e Aritmética (ULA) completa usando o simulador Digital. A ULA é um componente fundamental em sistemas computacionais, responsável por realizar operações aritméticas e lógicas.

Uma ULA é o coração de qualquer CPU, processando dados e executando cálculos essenciais para o funcionamento do computador. Neste projeto, implementamos uma ULA com 8 operações diferentes, sistema de seleção de operações, detecção de overflow e exibição em displays de 7 segmentos.

![Visão Geral da ULA](imagem-ula-completa.png)
<!-- Adicione aqui uma imagem de visão geral do circuito completo -->

## Visão Geral do Projeto

Nossa ULA implementa as seguintes operações:
- **Operações Aritméticas**:
  - Somador de 8 bits
  - Subtrator de 8 bits
  - Multiplicador de 4 bits (com saída de 8 bits)
- **Operações de Deslocamento**:
  - Shift Right (deslocamento à direita)
  - Shift Left (deslocamento à esquerda)
- **Operações Lógicas**:
  - AND bit a bit
  - OR bit a bit
  - XOR bit a bit

Para selecionar entre essas 8 operações, utilizamos 3 bits de controle (SEL0, SEL1, SEL2) que permitem 2³ = 8 combinações possíveis. O resultado de cada operação é exibido em displays de 7 segmentos hexadecimais, e um LED indica situações de overflow.

O diagrama de blocos a seguir ilustra a arquitetura geral da ULA:

![Diagrama de Blocos](diagrama-blocos-ula.png)
<!-- Adicione um diagrama de blocos mostrando o fluxo de dados na ULA -->

## Componentes da ULA

### Operações Aritméticas

#### Somador de 8 bits

O somador de 8 bits recebe dois operandos A e B de 8 bits cada e retorna a soma de ambos. Este componente é a base para várias outras operações da ULA.

**Funcionamento detalhado**: 

O somador de 8 bits é construído a partir de blocos básicos: half adders e full adders. Um full adder soma três bits: A, B e um carry de entrada (Cin), produzindo um bit de soma (S) e um carry de saída (Cout).

Para um somador de 8 bits, precisamos de:
- 1 half adder para o bit menos significativo (que não tem carry de entrada)
- 7 full adders para os bits restantes, cada um recebendo o carry do estágio anterior

A estrutura básica de um full adder pode ser expressa através de sua tabela-verdade:

| A | B | Cin | S | Cout |
|---|---|-----|---|------|
| 0 | 0 | 0   | 0 | 0    |
| 0 | 0 | 1   | 1 | 0    |
| 0 | 1 | 0   | 1 | 0    |
| 0 | 1 | 1   | 0 | 1    |
| 1 | 0 | 0   | 1 | 0    |
| 1 | 0 | 1   | 0 | 1    |
| 1 | 1 | 0   | 0 | 1    |
| 1 | 1 | 1   | 1 | 1    |

Para ilustrar, considere a adição de A = 01101001 e B = 00111011:

```
    Carry:  0 1 1 1 0 1 1 0
       A:   0 1 1 0 1 0 0 1
       B:   0 0 1 1 1 0 1 1
    ――――――――――――――――――――――――
    Soma:   0 1 0 0 0 1 0 0
```

Bit a bit:
- Bit 0: 1+1 = 0 com carry 1
- Bit 1: 0+1+1 = 0 com carry 1
- Bit 2: 0+0+1 = 1 com carry 0
- Bit 3: 1+1+0 = 0 com carry 1
- Bit 4: 1+1+1 = 1 com carry 1
- Bit 5: 1+0+1 = 0 com carry 1
- Bit 6: 1+0+1 = 0 com carry 1
- Bit 7: 0+0+1 = 1 com carry 0

O resultado final é 01000100 (decimal 68), que é a soma correta de 01101001 (decimal 105) e 00111011 (decimal 59).

O somador é uma parte crucial da ULA, pois além da adição direta, ele serve como base para outras operações como subtração e multiplicação.

![Somador de 8 bits](somador-8bits.png)
<!-- Adicione uma imagem do circuito do somador -->

#### Subtrator de 8 bits

O subtrator de 8 bits implementa a operação A - B utilizando o método do complemento de 2.

**Funcionamento detalhado**: 

Em computação digital, a forma mais eficiente de realizar subtração é usando a técnica do complemento de 2. Em vez de criar um circuito separado para subtração, adaptamos o somador de 8 bits existente através de uma modificação inteligente.

Para calcular A - B usando complemento de 2, precisamos:
1. Inverter todos os bits de B (complemento de 1)
2. Adicionar 1 ao resultado da inversão
3. Somar esse valor ao operando A

Assim: A - B = A + (~B + 1) = A + complemento de 2 de B

Em nosso circuito, isso é implementado com:
- Portas XOR para inverter condicionalmente os bits de B (usando o sinal SUB)
- Conexão do sinal SUB ao Carry In do somador para adicionar 1 quando necessário

Quando o sinal SUB é 0, as portas XOR não alteram B (0 ⊕ B = B) e Cin=0, realizando a adição normal.
Quando o sinal SUB é 1, as portas XOR invertem B (1 ⊕ B = ~B) e Cin=1, realizando a subtração.

Exemplo de subtração A - B, onde A = 01100100 (100 decimal) e B = 00001010 (10 decimal):

1. Passo 1: Inverter todos os bits de B (complemento de 1)
   - B = 00001010
   - ~B = 11110101

2. Passo 2: Adicionar 1 (formando o complemento de 2)
   - ~B + 1 = 11110101 + 1 = 11110110

3. Passo 3: Somar A com o complemento de 2 de B
   ```
       A:   0 1 1 0 0 1 0 0
       -B:  1 1 1 1 0 1 1 0 (complemento de 2 de B)
   ――――――――――――――――――――――――
   Resultado: 0 1 0 1 1 0 1 0 (90 decimal)
   ```

Neste exemplo, o carry out final é descartado, e o resultado 01011010 (90 decimal) é o valor correto de 100 - 10 = 90.

Este sistema elegante permite que uma única unidade de hardware execute tanto adição quanto subtração com apenas algumas portas extras, economizando componentes e espaço.

![Subtrator de 8 bits](subtrator-8bits.png)
<!-- Adicione uma imagem do circuito do subtrator -->

#### Multiplicador de 4 bits

O multiplicador de 4 bits recebe dois operandos de 4 bits e produz um resultado de 8 bits.

**Funcionamento detalhado**: 

A multiplicação binária segue os mesmos princípios da multiplicação decimal, mas com apenas dois dígitos (0 e 1). O processo é simplificado porque multiplicar por 0 sempre resulta em 0, e multiplicar por 1 mantém o valor original.

Nossa implementação utiliza três etapas principais:

##### 1. Geração de Produtos Parciais

Cada bit do multiplicador (B) é ANDed com cada bit do multiplicando (A). Como estamos trabalhando com 4 bits, isso cria uma matriz de 16 produtos parciais (4×4):

Exemplo com A = 1101 (13 decimal) e B = 1011 (11 decimal):

```
     1 1 0 1  (A = 13)
   × 1 0 1 1  (B = 11)
   ―――――――――
```

Cada bit de B é multiplicado por todos os bits de A:
- B0=1: AND com A → 1101
- B1=1: AND com A → 1101
- B2=0: AND com A → 0000
- B3=1: AND com A → 1101

##### 2. Deslocamento dos Produtos Parciais

Cada produto parcial é deslocado com base na posição do bit do multiplicador:

```
     1 1 0 1  (A = 13)
   × 1 0 1 1  (B = 11)
   ―――――――――
     1 1 0 1  (A × B0, sem deslocamento)
   1 1 0 1    (A × B1, deslocado 1 posição)
  0 0 0 0     (A × B2, deslocado 2 posições)
 1 1 0 1      (A × B3, deslocado 3 posições)
```

Isso resulta em:
```
     1 1 0 1
   1 1 0 1
  0 0 0 0
 1 1 0 1
―――――――――――――
 1 0 0 0 1 1 1 1  (143 decimal, o resultado correto de 13 × 11)
```

##### 3. Soma dos Produtos Parciais

Este é o estágio mais complexo. Usamos uma combinação de half adders e full adders organizados em uma estrutura de árvore ou matriz para somar eficientemente todos os produtos parciais. A estrutura geral é:

```
Bit 0: Diretamente do AND(A0,B0)
Bit 1: Half Adder somando AND(A1,B0) e AND(A0,B1)
Bit 2: Full Adder para AND(A2,B0), AND(A1,B1), e um carry anterior
...e assim por diante
```

No circuito, implementamos isso usando:
- 16 portas AND para gerar todos os produtos parciais
- 4 half adders e 8 full adders organizados em cascata para somar progressivamente os produtos parciais

A matriz completa de adders forma um padrão triangular que processa cada bit do resultado final, considerando todos os produtos parciais relevantes e os carries acumulados.

**Exemplo Detalhado**:

Para A = 0110 (6) e B = 0111 (7):

1. **Produtos Parciais**:
   ```
   AND_00 = A0 × B0 = 0 × 1 = 0
   AND_01 = A1 × B0 = 1 × 1 = 1
   AND_02 = A2 × B0 = 1 × 1 = 1
   AND_03 = A3 × B0 = 0 × 1 = 0
   
   AND_10 = A0 × B1 = 0 × 1 = 0
   AND_11 = A1 × B1 = 1 × 1 = 1
   AND_12 = A2 × B1 = 1 × 1 = 1
   AND_13 = A3 × B1 = 0 × 1 = 0
   
   AND_20 = A0 × B2 = 0 × 1 = 0
   AND_21 = A1 × B2 = 1 × 1 = 1
   AND_22 = A2 × B2 = 1 × 1 = 1
   AND_23 = A3 × B2 = 0 × 1 = 0
   
   AND_30 = A0 × B3 = 0 × 0 = 0
   AND_31 = A1 × B3 = 1 × 0 = 0
   AND_32 = A2 × B3 = 1 × 0 = 0
   AND_33 = A3 × B3 = 0 × 0 = 0
   ```

2. **Deslocamento e soma**:
   ```
       0 1 1 0  (A = 6)
     × 0 1 1 1  (B = 7)
     ―――――――――
       0 1 1 0  (A × B0 = 6)
      0 1 1 0   (A × B1 = 6, deslocado 1 posição)
     0 1 1 0    (A × B2 = 6, deslocado 2 posições)
    0 0 0 0     (A × B3 = 0, deslocado 3 posições)
    ―――――――――
    0 0 1 0 1 0 1 0  (42 decimal, o resultado correto de 6 × 7)
   ```

Esta implementação de multiplicador é mais complexa que os outros componentes, mas demonstra como operações aritméticas complexas podem ser construídas usando blocos lógicos simples.

![Processo de Multiplicação Binária](multiplicacao-binaria.png)
<!-- Adicione uma imagem ilustrando a multiplicação binária -->

O circuito completo do multiplicador:

![Multiplicador de 4 bits](multiplicador-4bits.png)
<!-- Adicione uma imagem do circuito do multiplicador -->

### Operações de Deslocamento

#### Shift Right

A operação Shift Right desloca todos os bits de A uma posição para a direita, o que é equivalente a dividir o número por 2 para números positivos.

**Funcionamento detalhado**: 

No shift right (deslocamento à direita), cada bit é movido uma posição para a direita, o bit menos significativo é perdido, e um novo bit (geralmente 0) é inserido na posição mais significativa.

A implementação é surpreendentemente simples e direta, pois não requer cálculos - apenas conexões específicas:

- Bit 7 do resultado recebe 0 (ou o valor do bit de sinal, para shift aritmético)
- Bit 6 do resultado recebe Bit 7 de A
- Bit 5 do resultado recebe Bit 6 de A
- Bit 4 do resultado recebe Bit 5 de A
- Bit 3 do resultado recebe Bit 4 de A
- Bit 2 do resultado recebe Bit 3 de A
- Bit 1 do resultado recebe Bit 2 de A
- Bit 0 do resultado recebe Bit 1 de A
- O Bit 0 de A é descartado completamente

Considere o exemplo de A = 10101010 (170 decimal):

```
Entrada A:       1 0 1 0 1 0 1 0
                 ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓
Shift Right:   0 1 0 1 0 1 0 1 (com 0 inserido à esquerda)
```

Resultado: 01010101 (85 decimal), que é exatamente a metade de 170.

Esta operação é matematicamente equivalente à divisão por 2 (com arredondamento para baixo) em números positivos. Note que para números negativos em complemento de 2, o resultado depende se o shift é lógico (insere 0) ou aritmético (preserva o bit de sinal).

**Aplicações práticas**:
- Divisão rápida por potências de 2
- Acesso a bits específicos em manipulação de baixo nível
- Algoritmos de processamento de imagem e sinal
- Algoritmos de multiplicação/divisão otimizados

Em nossa ULA, implementamos um shift right lógico (que sempre insere 0 no bit mais significativo), que é a variante mais comum em operações de manipulação de bits.

A tabela abaixo mostra o mapeamento direto entre os bits de entrada e saída:

| Bit de entrada | Bit de saída |
|----------------|--------------|
| A7             | SR_BIT6      |
| A6             | SR_BIT5      |
| A5             | SR_BIT4      |
| A4             | SR_BIT3      |
| A3             | SR_BIT2      |
| A2             | SR_BIT1      |
| A1             | SR_BIT0      |
| A0             | (descartado) |
| 0 (constante)  | SR_BIT7      |

![Shift Right](shift-right.png)
<!-- Adicione uma imagem do circuito do Shift Right -->

#### Shift Left

A operação Shift Left desloca todos os bits de A uma posição para a esquerda, o que é equivalente a multiplicar o número por 2 para números positivos.

**Funcionamento detalhado**: 

No shift left (deslocamento à esquerda), cada bit é movido uma posição para a esquerda, o bit mais significativo é perdido (potencialmente causando overflow), e um novo bit (sempre 0) é inserido na posição menos significativa.

Assim como o shift right, a implementação é direta, usando apenas conexões específicas:

- Bit 0 do resultado recebe 0 (sempre)
- Bit 1 do resultado recebe Bit 0 de A
- Bit 2 do resultado recebe Bit 1 de A
- Bit 3 do resultado recebe Bit 2 de A
- Bit 4 do resultado recebe Bit 3 de A
- Bit 5 do resultado recebe Bit 4 de A
- Bit 6 do resultado recebe Bit 5 de A
- Bit 7 do resultado recebe Bit 6 de A
- O Bit 7 de A é descartado (e pode ser usado para determinar overflow)

Considere o exemplo de A = 01010101 (85 decimal):

```
Entrada A:       0 1 0 1 0 1 0 1
                 ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓
Shift Left:    1 0 1 0 1 0 1 0 (com 0 inserido à direita)
```

Resultado: 10101010 (170 decimal), que é exatamente o dobro de 85.

Esta operação é matematicamente equivalente à multiplicação por 2 em números binários. Quando o bit mais significativo original é 1, ocorre overflow, pois esse bit é "empurrado para fora" do resultado de 8 bits.

**Aplicações práticas**:
- Multiplicação rápida por potências de 2
- Cálculos aritméticos otimizados
- Algoritmos de codificação/compressão
- Alocação de bits em flags e máscaras de bits

Em nossa ULA, monitoramos o bit A7 original para determinar se ocorre overflow na operação de shift left. Se A7=1, então ocorre overflow, pois uma informação significativa foi perdida.

A tabela abaixo mostra o mapeamento direto entre os bits de entrada e saída:

| Bit de entrada | Bit de saída |
|----------------|--------------|
| A0             | SL_BIT1      |
| A1             | SL_BIT2      |
| A2             | SL_BIT3      |
| A3             | SL_BIT4      |
| A4             | SL_BIT5      |
| A5             | SL_BIT6      |
| A6             | SL_BIT7      |
| A7             | (para overflow)|
| 0 (constante)  | SL_BIT0      |

Esta operação é particularmente útil em algoritmos de multiplicação rápida, pois deslocar à esquerda n posições é equivalente a multiplicar por 2^n.

![Shift Left](shift-left.png)
<!-- Adicione uma imagem do circuito do Shift Left -->

### Operações Lógicas

#### Operação AND

A operação lógica AND realiza a operação AND bit a bit entre os operandos A e B, sendo fundamental em manipulações de bits e mascaramento.

**Funcionamento detalhado**: 

A operação AND bit a bit compara cada posição de bit em A com a posição correspondente em B. O resultado é 1 apenas se ambos os bits forem 1; caso contrário, o resultado é 0.

A tabela-verdade da operação AND é:

| A | B | A AND B |
|---|---|---------|
| 0 | 0 | 0       |
| 0 | 1 | 0       |
| 1 | 0 | 0       |
| 1 | 1 | 1       |

Para implementar esta operação na ULA, usamos 8 portas AND independentes (uma para cada par de bits). Cada porta AND recebe os bits correspondentes dos operandos A e B e produz um bit do resultado.

Considere o exemplo de A = 11110000 (240 decimal) e B = 00111100 (60 decimal):

```
     A: 1 1 1 1 0 0 0 0
     B: 0 0 1 1 1 1 0 0
   ――――――――――――――――――――――
A AND B: 0 0 1 1 0 0 0 0 (48 decimal)
```

Bit por bit:
- Bit 7: 1 AND 0 = 0
- Bit 6: 1 AND 0 = 0
- Bit 5: 1 AND 1 = 1
- Bit 4: 1 AND 1 = 1
- Bit 3: 0 AND 1 = 0
- Bit 2: 0 AND 1 = 0
- Bit 1: 0 AND 0 = 0
- Bit 0: 0 AND 0 = 0

**Aplicações da operação AND**:
1. **Mascaramento de bits**: Isolar bits específicos em um valor
   - Exemplo: `valor & 0x0F` extrai os 4 bits menos significativos
2. **Verificação de bits**: Testar se bits específicos estão ativados
   - Exemplo: `(flags & 0x80) != 0` verifica se o bit 7 está ativado
3. **Desativação de bits**: Forçar bits específicos para 0
   - Exemplo: `valor & 0xFE` força o bit 0 para 0, mantendo outros bits inalterados
4. **Implementação de lógica condicional**: Realizar decisões baseadas em múltiplas condições

A operação AND é uma das operações fundamentais em computação digital e geralmente é implementada diretamente no hardware como uma instrução de processador básica. Em nossa ULA, ela é implementada da forma mais direta possível, usando 8 portas AND individuais.

O circuito para esta operação é consideravelmente mais simples que os componentes aritméticos, pois não há propagação de carry ou lógica complexa - apenas 8 portas AND independentes.

![Operação AND](operacao-and.png)
<!-- Adicione uma imagem do circuito da operação AND -->

#### Operação OR

A operação lógica OR realiza a operação OR bit a bit entre os operandos A e B, sendo essencial para combinar flags e ativar bits específicos.

**Funcionamento detalhado**: 

A operação OR bit a bit compara cada posição de bit em A com a posição correspondente em B. O resultado é 1 se pelo menos um dos bits for 1; o resultado é 0 apenas se ambos os bits forem 0.

A tabela-verdade da operação OR é:

| A | B | A OR B |
|---|---|--------|
| 0 | 0 | 0      |
| 0 | 1 | 1      |
| 1 | 0 | 1      |
| 1 | 1 | 1      |

Na ULA, implementamos esta operação usando 8 portas OR independentes (uma para cada par de bits). Cada porta OR recebe os bits correspondentes dos operandos A e B e produz um bit do resultado.

Considere o exemplo de A = 10101010 (170 decimal) e B = 01010101 (85 decimal):

```
    A: 1 0 1 0 1 0 1 0
    B: 0 1 0 1 0 1 0 1
  ――――――――――――――――――――
A OR B: 1 1 1 1 1 1 1 1 (255 decimal)
```

Bit por bit:
- Bit 7: 1 OR 0 = 1
- Bit 6: 0 OR 1 = 1
- Bit 5: 1 OR 0 = 1
- Bit 4: 0 OR 1 = 1
- Bit 3: 1 OR 0 = 1
- Bit 2: 0 OR 1 = 1
- Bit 1: 1 OR 0 = 1
- Bit 0: 0 OR 1 = 1

**Aplicações da operação OR**:
1. **Ativação de bits**: Forçar bits específicos para 1
   - Exemplo: `valor | 0x01` força o bit 0 para 1, mantendo outros bits inalterados
2. **Combinação de flags**: Combinar múltiplas flags em um único valor
   - Exemplo: `flags = FLAG_READ | FLAG_WRITE | FLAG_EXECUTE`
3. **União de conjuntos**: Implementar a operação de união em conjuntos representados por bits
4. **Configuração de registradores**: Ativar recursos específicos em hardware mantendo configurações existentes

Assim como a operação AND, o circuito do OR é muito direto e simples: oito portas OR independentes, sem propagação de informação entre os bits.

![Operação OR](operacao-or.png)
<!-- Adicione uma imagem do circuito da operação OR -->

#### Operação XOR

A operação lógica XOR (OU exclusivo) realiza a operação XOR bit a bit entre os operandos A e B, sendo crucial para operações de paridade, detecção de mudanças e criptografia.

**Funcionamento detalhado**: 

A operação XOR (OU exclusivo) bit a bit compara cada posição de bit em A com a posição correspondente em B. O resultado é 1 se os bits forem diferentes (um 0 e um 1); o resultado é 0 se os bits forem iguais (ambos 0 ou ambos 1).

A tabela-verdade da operação XOR é:

| A | B | A XOR B |
|---|---|---------|
| 0 | 0 | 0       |
| 0 | 1 | 1       |
| 1 | 0 | 1       |
| 1 | 1 | 0       |

Na ULA, implementamos esta operação usando 8 portas XOR independentes (uma para cada par de bits). Cada porta XOR recebe os bits correspondentes dos operandos A e B e produz um bit do resultado.

Considere o exemplo de A = 11001100 (204 decimal) e B = 10101010 (170 decimal):

```
     A: 1 1 0 0 1 1 0 0
     B: 1 0 1 0 1 0 1 0
   ――――――――――――――――――――――
A XOR B: 0 1 1 0 0 1 1 0 (102 decimal)
```

Bit por bit:
- Bit 7: 1 XOR 1 = 0 (bits iguais → 0)
- Bit 6: 1 XOR 0 = 1 (bits diferentes → 1)
- Bit 5: 0 XOR 0 = 0 (bits iguais → 0)

**Propriedades matemáticas importantes da XOR**:
1. **Comutatividade**: A XOR B = B XOR A
2. **Associatividade**: (A XOR B) XOR C = A XOR (B XOR C)
3. **Auto-inversa com dupla aplicação**: A XOR B XOR B = A (cancelamento)
4. **Identidade com zero**: A XOR 0 = A
5. **Resultado zero com valores iguais**: A XOR A = 0

**Aplicações da operação XOR**:
1. **Inversão seletiva de bits**: Inverter bits específicos sem afetar outros
   - Exemplo: `valor XOR 0xFF` inverte todos os bits
2. **Detecção de diferenças**: Identificar bits que mudaram entre dois valores
3. **Cálculo de paridade**: Verificação de erros em transmissão de dados
4. **Operações criptográficas**: Base para muitos algoritmos de criptografia
5. **Troca de valores sem variável temporária**:
   ```
   a = a XOR b
   b = a XOR b
   a = a XOR b
   ```

A operação XOR é particularmente interessante por sua propriedade de auto-inversão. Isso a torna muito útil em criptografia, pois a mesma operação pode ser usada tanto para codificar quanto para decodificar.

Em nossa ULA, o circuito XOR é similar aos circuitos AND e OR em termos de simplicidade: oito portas XOR independentes, sem propagação entre bits.

![Operação XOR](operacao-xor.png)
<!-- Adicione uma imagem do circuito da operação XOR --> 1 = 1 (bits diferentes → 1)
- Bit 4: 0 XOR 0 = 0 (bits iguais → 0)
- Bit 3: 1 XOR 1 = 0 (bits iguais → 0)
- Bit 2: 1 XOR 0 = 1 (bits diferentes → 1)
- Bit 1: 0 XOR 1 = 1 (bits diferentes → 1)

## Melhorias e Otimizações

Durante o desenvolvimento, realizamos diversas otimizações para tornar o circuito mais eficiente, compacto e fácil de entender. Esta seção detalha as principais melhorias implementadas e possíveis futuras expansões.

**Otimizações implementadas**: 

### 1. Uso de Barramentos e Distribuidores

Uma das otimizações mais significativas foi a utilização de barramentos para organizar as conexões:

- **Problema inicial**: Trabalhar com 64 fios individuais (8 bits × 8 operações) resultava em um circuito confuso e difícil de depurar
- **Solução**: Implementamos distribuidores para criar barramentos de 8 bits, reduzindo drasticamente o número de conexões visíveis
- **Benefícios**:
  - Redução significativa na complexidade visual do circuito
  - Menor probabilidade de erros de conexão
  - Facilidade de manutenção e modificação
  - Melhor organização lógica dos componentes

#### Detalhes técnicos:
Cada operação produz 8 bits de saída que são agrupados por um distribuidor em um único barramento de 8 bits. Isso reduziu o número de conexões visíveis de 64 para apenas 8 (um barramento por operação).

### 2. Multiplexador Unificado

Em vez de usar 8 multiplexadores separados (um para cada bit):

- **Problema inicial**: A abordagem convencional exigiria 8 multiplexadores 8:1, um para cada bit de saída
- **Solução**: Utilizamos um único multiplexador que trabalha com barramentos completos de 8 bits
- **Benefícios**:
  - Redução drástica no número de componentes
  - Simplificação do circuito de seleção
  - Menor probabilidade de erros de configuração
  - Redução no espaço ocupado pelo circuito

#### Implementação:
Configuramos o multiplexador para aceitar entradas de 8 bits e processá-las como uma unidade, em vez de processar cada bit individualmente.

### 3. Reutilização de Componentes

Implementamos o subtrator reutilizando o somador existente:

- **Problema inicial**: Criar um circuito separado para subtração duplicaria vários componentes
- **Solução**: Adaptamos o somador para realizar subtração adicionando apenas algumas portas XOR e conectando o sinal SUB ao Carry In
- **Benefícios**:
  - Redução do número total de componentes
  - Menor área ocupada pelo circuito
  - Simplificação da manutenção (apenas um circuito principal para depurar)
  - Design mais elegante e eficiente

#### Detalhamento técnico:
A implementação requer apenas 8 portas XOR adicionais e uma conexão extra para o sinal SUB, economizando dezenas de portas lógicas que seriam necessárias para um subtrator independente.

### 4. Detectores de Overflow Específicos

Implementamos métodos específicos de detecção de overflow para cada operação:

- **Problema inicial**: Diferentes operações têm diferentes condições de overflow, o que poderia exigir circuitos complexos
- **Solução**: Projetamos um sistema de detecção de overflow específico para cada operação, selecionado pelo mesmo multiplexador que escolhe a operação
- **Benefícios**:
  - Maior precisão na detecção de overflow
  - Feedback visual mais confiável
  - Melhor adaptação às características de cada operação

#### Detalhes de implementação:
Para o somador e subtrator, usamos a XOR dos carries de entrada e saída do bit mais significativo. Para o multiplicador, verificamos os 4 bits superiores usando uma porta OR. Para o shift left, monitoramos o bit A7.

### 5. Organização Hierárquica do Circuito

Estruturamos o circuito de forma hierárquica, agrupando componentes relacionados:

- **Problema inicial**: Um circuito "plano" seria difícil de entender e modificar
- **Solução**: Agrupamos componentes relacionados e organizamos o circuito em blocos funcionais distintos
- **Benefícios**:
  - Melhor compreensão do funcionamento global
  - Facilidade de depuração (problemas podem ser isolados em blocos específicos)
  - Possibilidade de reutilização de blocos em outros projetos
  - Documentação mais clara e estruturada

#### Abordagem:
Cada operação foi implementada como um bloco distinto com entradas e saídas bem definidas. O sistema de seleção e o display hexadecimal foram igualmente modularizados.

### Sugestões para Melhorias Futuras

1. **Implementação de mais operações**:
   - Divisão
   - Operação NOT
   - Rotação circular (em vez de shift)
   - Operações com sinal (aritméticas com complemento de 2)

2. **Expansão para 16 ou 32 bits**:
   - Aumentar a precisão dos cálculos
   - Implementar operações com números maiores
   - Manter a mesma arquitetura básica, apenas expandindo o tamanho dos barramentos

3. **Interface com memória**:
   - Adicionar registradores para armazenar resultados
   - Implementar leitura/escrita em uma pequena RAM
   - Criar uma ULA mais semelhante à de um processador real

4. **Melhoria no feedback visual**:
   - Adicionar displays para mostrar os valores de entrada
   - Implementar LEDs para indicar qual operação está selecionada
   - Adicionar indicadores de status para flags como zero, negativo, etc.

Estas otimizações e sugestões demonstram que mesmo um circuito aparentemente simples como uma ULA básica pode se beneficiar significativamente de boas práticas de design. A estruturação cuidadosa e a reutilização de componentes resultaram em um circuito mais compacto, fácil de entender e modificar, e com menor probabilidade de erros.

![Comparação de versões](comparacao-versoes.png)
<!-- Adicione uma imagem comparando versões inicial e otimizada do circuito --># Documentação da Unidade Lógica e Aritmética (ULA)