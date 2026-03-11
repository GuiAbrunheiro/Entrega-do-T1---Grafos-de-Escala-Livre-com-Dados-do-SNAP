# T1 - Grafos de Escala Livre com Dados do SNAP

## Descrição do trabalho
Este repositório contém a entrega do **Trabalho 1 da disciplina de Grafos**, com foco na análise de **grafos de escala livre** a partir de um dataset da base **SNAP (Stanford Large Network Dataset Collection)**.

A equipe utilizou o dataset **Enron Email Network**, modelando-o como um **grafo não direcionado e não ponderado**, conforme descrito na própria documentação do dataset. O objetivo foi analisar a estrutura da rede, calcular métricas básicas, observar a distribuição de graus e investigar se a rede apresenta comportamento compatível com **cauda pesada** e possível ajuste por **lei de potência**.

---

## Equipe
- Guilherme Abrunheiro de Souza
- Marcelo Kalsovik Junior
- Bernardo Batista Cavalcante

## Dataset utilizado
- **Nome:** Enron Email Network
- **Fonte:** SNAP — Stanford Large Network Dataset Collection
- **Tipo de rede:** Communication Network

### Descrição do dataset
Segundo a descrição oficial do SNAP:

> "Nodes of the network are email addresses and if an address i sent at least one email to address j, the graph contains an undirected edge from i to j."

Ou seja:
- cada **nó** representa um endereço de e-mail;
- cada **aresta** representa uma conexão entre dois endereços;
- a rede é considerada **não direcionada**.

---

## Problema encontrado na base de dados
Durante o carregamento do arquivo do dataset, foi identificado um ponto importante:  
o arquivo apresenta, em muitos casos, a mesma conexão em ambas as ordens, por exemplo:

- `1 0`
- `0 1`

Como o grafo é **não direcionado**, essas duas linhas representam **a mesma aresta**.

### Por que isso é um problema?
Se o arquivo fosse lido diretamente sem tratamento, o programa acabaria contando duas vezes a mesma conexão, o que faria o número de arestas ficar incorreto.

Isso pode ser verificado comparando com os dados oficiais do dataset:

- **Nodes:** 36692
- **Edges:** 183831

Se não houver tratamento de duplicatas, a quantidade de arestas obtida fica aproximadamente o dobro do esperado, o que indica claramente que o arquivo contém repetições da mesma aresta em sentidos invertidos.

### Como o problema foi tratado
Para corrigir isso, foi aplicado um tratamento no momento da leitura do arquivo.

Cada aresta `(v, w)` foi convertida para uma forma canônica:

- `(min(v, w), max(v, w))`

Dessa forma:
- `1 0` vira `(0, 1)`
- `0 1` também vira `(0, 1)`

Assim, ambas passam a representar exatamente a mesma aresta.

Depois disso, foi utilizado um conjunto (`set`) para garantir que cada aresta não direcionada fosse adicionada apenas uma vez ao grafo.

### Resultado desse tratamento
Com esse ajuste:
- o número de arestas passa a coincidir com o valor esperado da base;
- o grafo fica consistente com a definição oficial do dataset;
- a análise passa a refletir corretamente a estrutura real da rede.

---

## Modelagem adotada
A rede foi modelada como um:

- **grafo não direcionado**
- **grafo não ponderado**
- representado por **listas de adjacência**

Cada vértice representa um endereço de e-mail, e cada aresta representa uma conexão entre dois e-mails.

---

## Objetivos da análise
O trabalho teve como objetivos principais:

- carregar e estruturar o dataset em forma de grafo;
- tratar corretamente duplicatas e auto-laços;
- calcular métricas estruturais da rede;
- analisar a distribuição de graus;
- gerar visualizações em escala linear e log-log;
- investigar a presença de comportamento compatível com rede de escala livre.

---

## Métricas analisadas
Ao longo da análise, foram estudadas métricas como:

- número de vértices `|V|`
- número de arestas `|E|`
- densidade do grafo
- grau médio
- soma dos graus
- clustering médio
- distribuição de graus `P(k)`
- comportamento da distribuição em escala log-log
- ajuste de lei de potência na cauda da distribuição

---

## Organização do repositório

```bash
.
├── data/
│   └── email-Enron.txt.gz
├── notebooks/
│   └── enron_bag_grafo.ipynb
├── scripts/
│   └── analise_enron.py
├── images/
│   ├── degree_distribution_linear_enron.png
│   ├── loglog_enron_undirected.png
│   └── powerlaw_fit_enron_undirected.png
└── README.md
