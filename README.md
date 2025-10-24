# Proposta Inicial da Arquitetura do Sistema

Este documento descreve a proposta inicial da arquitetura da calculadora científica em Python, focando em uma estrutura modular, acessível e alinhada aos objetivos do projeto "Tecnologia Assistiva para Educação Inclusiva: uma calculadora científica em Python para usuários com deficiência visual".

## 1. Visão Geral da Arquitetura

A arquitetura será desenvolvida em Python e projetada para operar exclusivamente em uma interface de terminal (linha de comando). O design prioriza a total compatibilidade com leitores de tela (como NVDA e JAWS), eliminando elementos gráficos e baseando toda a interação em comandos de teclado e saída de texto claro.

A arquitetura é baseada em um conceito de **modo duplo** para equilibrar flexibilidade e acessibilidade:

1.  **Modo Padrão (Parser de Expressão):** Permite ao usuário digitar cálculos diretos em uma única linha (ex: `sen(pi/2) + 5`). É rápido e flexível para operações comuns.
2.  **Modo Guiado (Assistente de Menu):** Para operações complexas (ex: definição de matrizes, cálculos estatísticos), o sistema entra em um modo "engessado", guiando o usuário passo a passo com menus e prompts de texto. Isso reduz a chance de erro e torna processos complexos mais acessíveis.

## 2. Controlador Central (Gerenciador de Estado)

O núcleo da arquitetura é um **`io_handler` (Controlador de E/S e Estado)**. Este módulo atua como o "cérebro" da aplicação:

* **Gerencia o Estado:** Mantém o controle de qual "modo" o usuário está (Padrão ou Guiado).
* **Roteia Entradas:** Lê a entrada do teclado e a direciona para o módulo correto com base no estado atual.
* **Formata Saídas:** Recebe resultados e prompts de outros módulos e os formata como texto descritivo para o leitor de tela (ex: "Resultado: 6" ou "Menu de Matrizes: Escolha uma opção.").

## 3. Módulos de Interação (Interface do Usuário)

Estes módulos gerenciam a interação direta com o usuário:

* **`parser` (Analisador de Expressão - Modo Padrão):**
    * Opera no `ESTADO_PRINCIPAL`.
    * Recebe e analisa strings de texto (ex: `"sqrt(16) * 2"`).
    * Chama os Módulos de Lógica para executar os cálculos.
    * Identifica "palavras-chave" (ex: `"matriz"`, `"ajuda"`, `"config"`) para sinalizar ao `io_handler` a mudança de estado.

* **Módulos de Assistente (Modo Guiado):**
    * Cada módulo guiado opera em seu próprio estado (ex: `ESTADO_MATRIZ`, `ESTADO_ESTATISTICA`).
    * Apresenta menus baseados em texto (ex: "1: Criar Matriz", "2: Somar Matrizes", "S: Sair").
    * Solicita informações passo a passo (ex: "Digite o número de linhas:").
    * Chamam os Módulos de Lógica para executar as operações.

## 4. Módulos Principais (Lógica e Cálculo)

Estes módulos são as "bibliotecas" de back-end, estruturados como módulos independentes para escalabilidade. Eles apenas realizam cálculos e não interagem diretamente com o usuário. A professora solicitou a descrição dos módulos de operações básicas, trigonométricas e memória.

* **Módulo `core` (Operações Básicas):**
    * Funções: Adição, Subtração, Multiplicação, Divisão, Porcentagem, Ordem de precedência (parênteses).

* **Módulo `scientific` (Funções Científicas):**
    * **Trigonométricas:** `sen`, `cos`, `tan`, `asen`, `acos`, `atan`.
    * **Log/Exp:** `log` (base 10), `ln` (natural), `e^x`.
    * **Potência/Raiz:** `x^y`, `sqrt`, `x!`.
    * **Constantes:** `pi`, `e`.

* **Módulo `memory` (Memória de Cálculo):**
    * Funções para armazenar (`STO A`), recuperar (`RCL A`), adicionar (`M+`), e limpar registros de memória (`MC`).

* **Módulo `datascience` (Funções de IA e C. de Dados):**
    * **Álgebra Linear:** Lógica para criar, somar, multiplicar, transpor e calcular determinantes de matrizes.
    * **Estatística:** Lógica para calcular média, mediana, moda e desvio padrão de um conjunto de dados.

## 5. Definição de Estados da Aplicação

O `io_handler` gerenciará os seguintes estados para direcionar a interação:

* **`ESTADO_PRINCIPAL`:** (Padrão) Onde o `parser` está ativo.
* **`ESTADO_MATRIZ`:** (Guiado) Ativado pelo comando `"matriz"`. Apresenta o menu do módulo de álgebra linear.
* **`ESTADO_ESTATISTICA`:** (Guiado) Ativado pelo comando `"estatistica"`. Guia o usuário para inserir dados e selecionar cálculos.
* **`ESTADO_MEMORIA`:** (Guiado) Ativado por `"memoria"`. Permite ao usuário listar e gerenciar variáveis salvas.
* **`ESTADO_CONFIG`:** (Guiado) Ativado por `"config"`. Permite alterar configurações globais (ex: Graus/Radianos).
* **`ESTADO_AJUDA`:** (Guiado) Ativado por `"ajuda"`. Apresenta a documentação acessível das funções.

## 6. Interação via Teclado e Leitor de Tela

A interação é projetada para ser 100% acessível, conforme solicitado:

* **Entrada (Teclado):** O usuário usa apenas o teclado. No `ESTADO_PRINCIPAL`, ele digita expressões livres. Nos estados guiados, ele digita opções de menu (ex: "1") ou dados solicitados.
* **Saída (Leitor de Tela):** Toda resposta do sistema é em texto claro e descritivo.
    * **Exemplo (Modo Padrão):**
        * Usuário digita: `sqrt(25) + 1`
        * Saída no terminal (lida pelo NVDA): `"Resultado: 6"`
    * **Exemplo (Modo Guiado):**
        * Usuário digita: `matriz`
        * Saída no terminal: `"Menu de Matrizes. 1: Criar Matriz. 2: Somar Matrizes. S: Sair. Digite sua opção:"`
        * Usuário digita: `1`
        * Saída no terminal: `"Criar Matriz. Digite o número de linhas:"`

Esta arquitetura modular permite a escalabilidade futura (adicionando novos módulos de cálculo ou estados guiados) e cumpre os requisitos de acessibilidade.
