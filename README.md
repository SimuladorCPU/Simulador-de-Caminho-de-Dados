# HELP do Simulador

## Características da CPU

O simulador implementa uma CPU educacional baseada em uma arquitetura simplificada, voltada ao ensino de Arquitetura e Organização de Computadores. As principais características são:

- Conjunto de **12 instruções**, todas codificadas em **16 bits**
- **Memória de Dados** com capacidade para **256 palavras**
- **Memória de Instruções** com capacidade para **256 palavras**
- **8 registradores de uso geral** (R0 a R7)
- Suporte aos modos de execução **Monociclo** e **Multiciclo**
   
  **Modo Monociclo**
 ![Monociclo](mono.png)
  **Modo Multiciclo**
 ![Multicilo](multi.png)

---

## Operação do Simulador

A interação com o simulador é realizada por meio dos seguintes controles:

|   |   |
|---|---|
| ![](assemble.png) | **ASSEMBLE (Montar)**: traduz o código Assembly para a representação interna de instruções |
| ![](play_help.png) | **PLAY**: inicia a execução contínua do programa |
| ![](pause.png) | **PAUSE**: pausa a execução no ciclo atual |
| ![](forward.png) | **FORWARD (Passo-a-Passo)**: executa o programa de forma controlada |

---

## Configurações do Simulador

O simulador permite ajustar diferentes opções de execução e visualização:

- Exibição de valores em **Hexadecimal** ou **Decimal**
- Exibição de sinais de controle **Com Sinais**, **Sem Sinais** ou **Sinais Parciais**
- Seleção do modo de execução **Monociclo** ou **Multiciclo**

Essas configurações auxiliam na análise do comportamento da CPU sob diferentes perspectivas.

---

## Instruções Assembly

O simulador utiliza uma linguagem Assembly própria, definida especificamente para esta arquitetura.

### Instruções de Transferência de Dados

- **LDA** — *Load from Memory*  
  **Formato:** `LDA endereço, registrador`  
  Carrega o valor armazenado em um **endereço da memória de dados** para um **registrador**.  
  **Exemplo:** `LDA variavel, R0`

- **LDAi** — *Load Immediate*  
  **Formato:** `LDAi imediato, registrador`  
  Carrega um **valor imediato** diretamente em um **registrador**.  
  **Exemplo:** `LDAi 10, R1`

- **STA** — *Store to Memory*  
  **Formato:** `STA registrador, endereço`
  Armazena o conteúdo de um **registrador** em um **endereço da memória de dados**.
  **Exemplo:** `STA R0, variavel`

### Instruções Aritméticas e Lógicas

- **ADD** — *Addition*  
  **Formato:** `ADD reg_origem1, reg_origem2, reg_destino`  
  Realiza a soma entre dois **operandos de origem** (`reg_origem1 + reg_origem2`) e armazena o resultado no **operando de destino** (`reg_destino`). As flags de estado são atualizadas conforme o resultado da operação.  
  **Exemplo:** `ADD R1, R2, R0`

- **SUB** — *Subtraction*  
  **Formato:** `SUB reg_origem1, reg_origem2, reg_destino`  
  Realiza a subtração entre dois **operandos de origem** (`reg_origem1 − reg_origem2`) e armazena o resultado no **operando de destino** (`reg_destino`). As flags de estado são atualizadas conforme o resultado da operação.  
  **Exemplo:** `SUB R1, R2, R0`

- **AND** — *Logical AND*  
  **Formato:** `AND registrador_origem, registrador_destino`  
  Executa a operação lógica **AND** bit a bit entre os registradores.  
  **Exemplo:** `AND R2, R0`

- **OR** — *Logical OR*  
  **Formato:** `OR registrador_origem, registrador_destino`  
  Executa a operação lógica **OR** bit a bit entre os registradores.  
  **Exemplo:** `OR R3, R0`

- **NOT** — *Logical NOT*  
  **Formato:** `NOT registrador`  
  Inverte todos os bits do registrador especificado.  
  **Exemplo:** `NOT R0`

### Instruções de Controle de Fluxo

- **JZ** — *Jump if Zero*  
  **Formato:** `JZ endereço`  
  Realiza um **desvio condicional** para o endereço especificado se a **flag Zero (Z)** estiver ativa.  
  **Exemplo:** `JZ fim`

- **JN** — *Jump if Negative*  
  **Formato:** `JN endereço`  
  Realiza um **desvio condicional** para o endereço especificado se a **flag Negativo (N)** estiver ativa.  
  **Exemplo:** `JN negativo`

- **JMP** — *Unconditional Jump*  
  **Formato:** `JMP endereço`  
  Realiza um **desvio incondicional** para o endereço especificado.  
  **Exemplo:** `JMP inicio`

### Instrução de Controle de Execução

- **HLT** — *Halt*  
  **Formato:** `HLT`  
  Encerra a execução do programa, interrompendo o ciclo de busca e execução da CPU.

---

### Declaração de Variáveis
  
- As variáveis devem ser declaradas utilizando **valores inteiros em formato decimal**
- O uso de **labels** é permitido para identificação de endereços no código:
  - Devem iniciar com **letras**
  - Podem conter **números**, desde que não sejam o primeiro caractere
  - Não são permitidos caracteres especiais ou espaços

---

## Formato das Instruções (ISA)

As instruções seguem os formatos definidos pela **ISA (Instruction Set Architecture)** do simulador.  
Cada formato especifica a posição dos campos como **opcode**, registradores e valores imediatos.

Os formatos serão apresentados por uma **figura**, facilitando a visualização da organização dos 16 bits e o entendimento do processo de decodificação das instruções.

![Instruções](ISA_instrucoes.png)

---

## Sinais de Controle

Os sinais de controle são responsáveis por coordenar o funcionamento dos principais componentes do caminho de dados, definindo quais operações devem ser realizadas em cada ciclo de execução da instrução.

### `wrReg` — Write Register

Controla a **escrita no Banco de Registradores**.  
Quando ativado, permite que o valor presente na entrada do banco seja armazenado no **registrador de destino**.

Este sinal é ativado nas instruções que produzem um resultado a ser escrito em um registrador, como:  
`LDA`, `LDAi`, `ADD`, `SUB`, `AND`, `OR` e `NOT`.

Permanece desativado nas instruções que não realizam escrita em registradores, como:  
`STA`, `JZ`, `JN` e `JMP`.

### `rdMem` — Read Memory

Controla a **leitura da Memória de Dados**.  
Quando ativado, o valor armazenado no endereço especificado é disponibilizado na saída da memória.

Este sinal é ativado exclusivamente na instrução `LDA`, pois essa instrução requer a leitura de um valor da memória para posterior escrita em um registrador.  
Em todas as demais instruções, o sinal permanece desativado.

### `wrMem` — Write Memory

Controla a **escrita na Memória de Dados**.  
Quando ativado, o valor proveniente de um registrador é armazenado no endereço de memória especificado pela instrução.

Este sinal é ativado apenas na instrução `STA`, que realiza a operação de armazenamento de dados da CPU para a memória.

### `muxPC` — Seleção da Fonte do Program Counter

Define a **origem do próximo valor do PC (Program Counter)**.

- Quando configurado como `0`, o PC recebe o valor presente nos **bits 00–07 da instrução**, sendo utilizado em instruções de **desvio (jump)**.
- Quando configurado como `1`, o PC é atualizado com a saída do somador (`PC + 1`), representando o **avanço sequencial da execução do programa**.

### `muxAReg` — Seleção do Registrador de Destino

Seleciona qual campo da instrução será utilizado para identificar o **registrador de destino** no Banco de Registradores.

- Valor `0`: utiliza os **bits 08–10** da instrução (instruções `LDA` e `LDAi`).
- Valor `1`: utiliza os **bits 00–02** da instrução (instruções aritméticas e lógicas).

### `muxDReg` — Seleção da Origem do Dado para Escrita em Registrador

Define a **origem do dado** que será escrito no registrador de destino. Os valores possíveis são:

- `00`: valor imediato presente nos **bits 00–07 da instrução** (`LDAi`);
- `01`: valor lido da **Memória de Dados** (`LDA`);
- `10`: resultado produzido pela **ULA** (instruções aritméticas e lógicas).

### `opULA` — Código de Operação da ULA

Define qual operação será executada pela **Unidade Lógica e Aritmética (ULA)**.  
Os códigos de operação suportados são:

- `000` — `ADD` (adição)
- `001` — `SUB` (subtração)
- `100` — `AND` (operação lógica AND)
- `110` — `OR`  (operação lógica OR)
- `111` — `NOT` (operação lógica NOT)

---

## Contato e Sugestões

Sugestões, relatos de erros e contribuições podem ser enviados por meio do **repositório GitHub do projeto**.  
As mensagens enviadas pelo sistema de issues ou contato do Git são encaminhadas para os e-mails dos desenvolvedores responsáveis.
