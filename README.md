# 🧠 Problema do Caixeiro Viajante com QAOA

Solução desenvolvida no projeto de Iniciação Científica na **Universidade Federal do Espírito Santo (UFES)**, sob orientação do **Prof. Dr. Lúcio Souza Fassarella**.

---

## 🔍 Sobre este Projeto

Este repositório apresenta uma solução quântica para o **Problema do Caixeiro Viajante (TSP - Travelling Salesman Problem)** utilizando o algoritmo **QAOA (Quantum Approximate Optimization Algorithm)**. 

O objetivo é demonstrar como problemas de otimização combinatória podem ser representados na forma de Hamiltonianos e solucionados utilizando algoritmos de otimização quântica.

---

## 🚩 Descrição do Problema

O Problema do Caixeiro Viajante (TSP) consiste em, dado um conjunto de cidades e as distâncias entre elas, encontrar o caminho de menor custo que:

- Visite **todas as cidades exatamente uma vez**.
- Retorne à cidade de origem, formando um **ciclo hamiltoniano**.

O TSP é um problema classificado como **NP-difícil**, sendo amplamente estudado tanto na computação clássica quanto na computação quântica.

---

## ⚙️ Formulação Matemática

### 🔢 Variáveis Binárias

Cada cidade $i$ sendo visitada no instante de tempo $t$ é representada por uma variável binária:

$$
x_{i,t} \in \{0, 1\}
$$

- $x_{i,t} = 1$ ⟶ a cidade $i$ é visitada no tempo $t$.
- $x_{i,t} = 0$ ⟶ caso contrário.

O total de variáveis/qubits na formulação inicial é $n^2$, onde $n$ é o número de cidades.

---

### 🧠 Restrições de Validade

Para garantir uma solução válida (um ciclo hamiltoniano), aplicamos as seguintes restrições:

1️⃣ **Cada cidade é visitada exatamente uma vez:**

$$
\sum_t x_{i,t} = 1 \quad \forall i
$$

2️⃣ **Em cada instante de tempo, apenas uma cidade é visitada:**

$$
\sum_i x_{i,t} = 1 \quad \forall t
$$

---

### 🎯 Função de Custo

A função de custo representa a soma das distâncias entre cidades consecutivas no caminho:

$$
D(x) = \sum_{i,j} w_{ij} \sum_t x_{i,t} x_{j,t+1}
$$

Onde:

- $w_{ij}$ é a distância entre as cidades $i$ e $j$.
- O termo $x_{i,t} x_{j,t+1}$ indica a transição da cidade $i$ no tempo $t$ para a cidade $j$ no tempo $t+1$.

---

### 🚫 Função de Penalidade

Para evitar soluções inválidas, utilizamos uma penalidade quadrática:

$$
P(x) = A \left( \sum_t \left(1 - \sum_i x_{i,t} \right)^2 + \sum_i \left(1 - \sum_t x_{i,t} \right)^2 \right)
$$

- $A$ é o peso da penalização, geralmente da mesma ordem de magnitude dos pesos $w_{ij}$.

---

### 🏗️ Hamiltoniano Total

Convertendo as variáveis binárias para operadores quânticos usando:

$$
x = \frac{1}{2}(1 - Z)
$$

Obtemos:

- **Hamiltoniano de custo:**

$$
H_D = \sum_{i,j} w_{ij} \sum_t \frac{1}{4}(1 - Z_{i,t})(1 - Z_{j,t+1})
$$

- **Hamiltoniano de penalidade:**

$$
H_P = A \left( \sum_t \left(1 - \sum_i \frac{1}{2}(1 - Z_{i,t}) \right)^2 + \sum_i \left(1 - \sum_t \frac{1}{2}(1 - Z_{i,t}) \right)^2 \right)
$$

- **Hamiltoniano total:**

$$
H_C = H_D + H_P
$$

---

## 🔧 Otimização e Redução de Qubits

Uma técnica comum para otimizar o uso de qubits é **fixar uma cidade como ponto de origem e destino**, removendo-a da representação binária. Assim, o número de qubits é reduzido de $n^2$ para $(n-1)^2$.

Adicionamos um Hamiltoniano auxiliar para representar o custo de ir e voltar da cidade-base:

$$
H_H = \sum_{t \in \{0, n-1\}} \sum_i h_i \cdot \frac{1}{2}(1 - Z_{i,t})
$$

Onde $h_i$ é a distância entre a cidade $i$ e a cidade-base.

O Hamiltoniano final utilizado no QAOA é:

$$
H_C = H_D + H_P + H_H
$$

### 🧠 Conclusão

A formulação acima nos permite implementar o TSP como um problema de otimização quântica com o algoritmo QAOA. O Hamiltoniano total $H_C$ é usado para gerar o estado quântico por meio de uma sequência de operadores de fase e mistura, e os parâmetros $\beta$, $\gamma$ são otimizados para minimizar a energia esperada desse Hamiltoniano. Com isso, as melhores soluções (caminhos mínimos) aparecem com maior probabilidade na medição final.

---

