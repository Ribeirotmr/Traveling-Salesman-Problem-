# Solução do problema que eu desenvolvi no projeto de iniciação científica da Universidade Federal do Espirito Santo com o Prof Dr Lúcio Souza Fassarella.









# Problema do Caixeiro Viajante com QAOA

## Introdução

O Problema do Caixeiro Viajante (TSP – *Travelling Salesman Problem*) é classificado como **NP-difícil**. Ele consiste em, dada uma lista de cidades e as distâncias entre elas, encontrar o menor percurso possível que passe por **todas as cidades exatamente uma vez** e retorne à cidade de origem.

Matematicamente, esse problema pode ser representado por um grafo $G = (V, E)$, onde os vértices $( i, j \in V)$ representam cidades e as arestas $E$ possuem pesos $w_{ij}$, representando as distâncias entre as cidades.

---

## Formulação Binária

Para representar o TSP em termos de computação quântica, utilizamos variáveis binárias:

$$
x_{i,t} \in \{0, 1\}
$$

Essa variável indica se a cidade $i$ é visitada no instante de tempo $t$. Como temos $n$cidades, o problema requer inicialmente $n^2$ qubits. A configuração completa dos qubits pode ser expressa como:

$$
x = x_{0,0}x_{1,0}x_{2,0} \dots x_{n-1,n-1}
$$

Entretanto, nem toda cadeia de bits representa uma solução válida. Para garantir que a solução seja um **ciclo Hamiltoniano**, duas restrições devem ser satisfeitas:

1. Cada cidade deve ser visitada uma única vez:
   $$
   \sum_t x_{i,t} = 1 \quad \forall i
   $$
2. Em cada instante de tempo, apenas uma cidade deve ser visitada:
   $$
   \sum_i x_{i,t} = 1 \quad \forall t
   $$

---

## Função de Custo

O custo total do caminho pode ser determinado observando as transições entre as cidades ao longo do tempo. Para cada par $(i, j \in E$), com peso $w_{ij}$, sempre que a cidade $i$ é visitada no tempo $t$ e a cidade $j$ é visitada no tempo $t+1$, temos:

$$
x_{i,t} = x_{j,t+1} = 1
$$

Assim, a função de custo $D(x)$ pode ser escrita como:

$$
D(x) = \sum_{i,j} w_{ij} \sum_t x_{i,t} x_{j,t+1}
$$

---

## Penalidade

Como muitas cadeias de bits representam soluções inválidas (que violam as restrições de ciclo Hamiltoniano), introduzimos uma função de penalidade $P(x)$:

$$
P(x) = A \left( \sum_t \left(1 - \sum_i x_{i,t} \right)^2 + \sum_i \left(1 - \sum_t x_{i,t} \right)^2 \right)
$$

O parâmetro $A$ representa a penalização e é escolhido na mesma ordem de magnitude dos pesos $w_{ij}$. A função de custo total a ser minimizada é então:

$$
C(x) = D(x) + P(x)
$$

---

## Quantização

Como estamos lidando com variáveis binárias, é necessário convertê-las em operadores quânticos. Isso é feito por meio da correspondência:

$$
x = \frac{1}{2}(1 - Z)
$$

Onde $Z$ é o operador de Pauli-Z. Aplicando essa transformação, temos o Hamiltoniano da função de custo:

$$
H_D = \sum_{i,j} w_{ij} \sum_t \frac{1}{4}(1 - Z_{i,t})(1 - Z_{j,t+1})
$$

E o Hamiltoniano da penalidade:

$$
H_P = A \left( \sum_t \left(1 - \sum_i \frac{1}{2}(1 - Z_{i,t}) \right)^2 + \sum_i \left(1 - \sum_t \frac{1}{2}(1 - Z_{i,t}) \right)^2 \right)
$$

Assim, o **Hamiltoniano total** do problema é:

$$
H_C = H_D + H_P
$$

---

## Otimização e Redução de Qubits

Uma otimização importante consiste em **fixar uma cidade como base** (inicial e final). Como o QAOA busca ciclos fechados, não é necessário representá-la com qubits, reduzindo o número total de variáveis de $n^2$ para $(n-1)^2$.

Para compensar a remoção da cidade-base, adiciona-se um Hamiltoniano auxiliar $H_H$, que incorpora os custos de ida e volta:

$$
H_H = \sum_{t \in \{0, n-1\}} \sum_i h_i \cdot \frac{1}{2}(1 - Z_{i,t})
$$

Onde $h_i$ representa a distância entre a cidade $i$ e a cidade-base. O Hamiltoniano final usado no QAOA será:

$$
H_C = H_D + H_P + H_H
$$

---

## Conclusão

A formulação acima nos permite implementar o TSP como um problema de otimização quântica com o algoritmo QAOA. O Hamiltoniano total $H_C$ é usado para gerar o estado quântico por meio de uma sequência de operadores de fase e mistura, e os parâmetros $\beta, \gamma são otimizados para minimizar a energia esperada desse Hamiltoniano. Com isso, as melhores soluções (caminhos mínimos) aparecem com maior probabilidade na medição final.
