# T1 - Caracterização de Grafos de Escala Livre com Dados do SNAP

## Visão geral

Este repositório contém a entrega do **Trabalho 1 da disciplina de Resolução de Problemas em Grafos**, cujo tema é a **caracterização estrutural de redes reais** e a verificação da hipótese de **comportamento de escala livre** em dados da coleção **SNAP (Stanford Large Network Dataset Collection)**.

A equipe trabalhou com o dataset **Enron Email Network**, pertencente à categoria **Communication Network**. O estudo foi desenvolvido com foco em:

- construir o grafo a partir de um dataset real;
- documentar as decisões de modelagem;
- calcular métricas estruturais fundamentais;
- analisar a distribuição de graus em escala linear e log-log;
- ajustar uma lei de potência na cauda da distribuição;
- discutir, de forma crítica, em que medida a rede pode ou não ser considerada de escala livre.

---

## Equipe

- Guilherme Abrunheiro de Souza  
- Marcelo Kalsovik Junior  
- Bernardo Batista Cavalcante  

---

## Dataset utilizado

- **Nome:** Enron Email Network  
- **Categoria no SNAP:** Communication Network  
- **Fonte geral:** SNAP — Stanford Large Network Dataset Collection  
- **Página do dataset:** https://snap.stanford.edu/data/email-Enron.html  

### Descrição oficial do dataset

Segundo a descrição oficial do SNAP:

> "Nodes of the network are email addresses and if an address i sent at least one email to address j, the graph contains an undirected edge from i to j."

Ou seja:

- cada **nó** representa um endereço de e-mail;
- cada **aresta** representa uma conexão entre dois endereços;
- a rede é tratada como **não direcionada** no dataset disponibilizado;
- a base resume a comunicação observada entre endereços de e-mail da Enron e contatos relacionados.

---

## Objetivo do trabalho

O objetivo central deste projeto foi investigar se a rede de e-mails da Enron apresenta propriedades compatíveis com **grafos de escala livre**, especialmente por meio da análise da **distribuição de graus**.

Mais especificamente, buscamos:

- representar corretamente a base como grafo;
- calcular **ordem** `|V|`, **tamanho** `|E|`, **densidade**, **grau médio** e **clustering médio`;
- estudar a distribuição de graus `P(k)`;
- observar o comportamento da distribuição em **escala linear** e **escala log-log`;
- realizar um **ajuste por lei de potência** com justificativa metodológica;
- interpretar os resultados com cuidado, sem forçar a classificação da rede como escala livre.

---

## Modelagem adotada

A rede foi modelada como um:

- **grafo não direcionado**;
- **grafo não ponderado**;
- representado por **listas de adjacência**;
- implementado com a estrutura **Bag**, no estilo `algs4`.

### Interpretação dessa modelagem

- Cada vértice representa um endereço de e-mail.
- Cada aresta representa uma conexão entre dois endereços.
- Como o dataset do SNAP já descreve a rede como **undirected**, a análise foi feita nesse formato.
- Não foram utilizados pesos nas arestas, pois a base usada aqui indica apenas a existência da conexão, e não a intensidade da troca de mensagens.

---

## Problema encontrado na base de dados

Durante o carregamento do arquivo, foi identificado um ponto importante no dataset.

Em muitos casos, a mesma conexão aparece nas duas ordens, por exemplo:

- `1 0`
- `0 1`

Como o grafo é **não direcionado**, essas duas linhas representam a **mesma aresta**.

### Por que isso é relevante?

Se o arquivo for lido diretamente, sem tratamento, o programa pode contabilizar duas vezes uma mesma conexão. Isso compromete:

- o número de arestas;
- a soma dos graus;
- o grau médio;
- a densidade;
- e, por consequência, toda a análise estrutural.

### Evidência do problema

Os dados oficiais do SNAP informam:

- **Nodes:** 36692  
- **Edges:** 183831  

Quando a leitura é feita sem tratamento das duplicatas invertidas, a quantidade de arestas tende a aparecer aproximadamente em dobro, indicando claramente a presença de repetições da mesma conexão em sentidos trocados.

### Como o problema foi tratado

Para resolver isso, cada aresta `(v, w)` foi convertida para uma forma canônica:

- `(min(v, w), max(v, w))`

Assim:

- `1 0` vira `(0, 1)`
- `0 1` também vira `(0, 1)`

Depois disso, foi utilizado um `set` para garantir que cada aresta não direcionada fosse adicionada apenas uma vez.

### Resultado do tratamento

Com esse ajuste:

- o número de arestas passa a coincidir com o valor esperado da base;
- o grafo fica consistente com a definição oficial do dataset;
- a análise passa a refletir melhor a estrutura real da rede.

Além disso, **auto-laços** também foram ignorados na leitura, por não fazerem parte da modelagem adotada para o grafo simples analisado neste trabalho.

---

## Métricas analisadas

Ao longo da análise, foram calculadas e discutidas as seguintes métricas:

- número de vértices `|V|`;
- número de arestas `|E|`;
- densidade do grafo;
- grau médio;
- soma dos graus;
- verificação do lema do aperto de mãos;
- clustering médio;
- distribuição de graus `P(k)`;
- visualização da distribuição em escala linear;
- visualização da distribuição em escala log-log;
- ajuste de lei de potência na cauda da distribuição.

---

## Principais resultados obtidos

A execução atual do projeto apresenta os seguintes valores principais:

- **|V| = 36692**
- **|E| = 183831**
- **densidade = 0.00027310**
- **grau médio = 10.0202**
- **clustering médio = 0.496983**

No ajuste de lei de potência, foram reportados:

- **gamma = 1.9725**
- **xmin = 4.0**
- **sigma = 0.0076**
- **KS = 0.0155**
- **n_cauda = 16514**

Esses valores indicam que a distribuição de graus apresenta **cauda pesada** e que a região de cauda pode ser analisada com um ajuste por **lei de potência**.

---

## Interpretação dos resultados

A análise sugere que a rede possui uma distribuição de graus bastante desigual, com muitos vértices de baixo grau e uma parcela menor de vértices muito conectados, o que é coerente com redes reais de comunicação.

Ao mesmo tempo, a equipe adotou uma interpretação crítica:

- um gráfico log-log com aparência aproximadamente linear **não basta** para afirmar que toda a rede é escala livre;
- o ajuste deve ser analisado junto com parâmetros como `gamma`, `xmin`, `KS` e `n_cauda`;
- por isso, a conclusão mais adequada não é afirmar que a rede inteira segue perfeitamente uma lei de potência, mas sim que **há um ajuste plausível de power law na cauda da distribuição de graus**.

Essa leitura está alinhada com a observação do próprio enunciado da atividade: **nem toda rede real segue lei de potência de forma robusta**, e parte importante do trabalho está justamente na análise crítica.

---

## Comparação conceitual com outras categorias de rede

Mesmo sem executar aqui uma segunda base, é possível fazer uma comparação conceitual com outras categorias do SNAP.

Por exemplo:

- em **redes de comunicação**, como Enron, é comum haver concentração de conexões em certos nós muito ativos, como contas centrais ou indivíduos com papel organizacional relevante;
- em **redes de estradas**, por outro lado, a distribuição de graus costuma ser mais limitada por restrições físicas e geográficas, o que reduz a presença de hubs extremos;
- em **redes sociais**, pode haver hubs também, mas os mecanismos de formação de arestas podem ser diferentes dos observados em redes corporativas de e-mail.

Assim, a rede Enron tende a ser mais compatível com a ideia de cauda pesada do que redes estruturadas por restrições físicas rígidas, como redes viárias.

---

## Estrutura do repositório

```bash
.
├── data/
│   └── email-Enron.txt.gz
├── notebooks/
│   └── codigo2.ipynb
├── scripts/
│   └── codigo1.py
├── Imagens/
│   ├── Gráfico 1.png
│   ├── Gráfico 2.png
│   └── Gráfico 3.png
└── README.md
```

> Observação: em alguns sistemas, os nomes dos arquivos de imagem podem aparecer com variações de codificação de caracteres. Isso não altera o conteúdo da análise.

---

## Como reproduzir a análise

### 1. Instalar as dependências

Recomenda-se usar Python 3.10+.

```bash
pip install numpy matplotlib powerlaw algs4
```

### 2. Estrutura esperada

Mantenha a estrutura de pastas do repositório, especialmente:

- `data/email-Enron.txt.gz`
- `scripts/codigo1.py`
- `notebooks/codigo2.ipynb`

### 3. Executar o script

A partir da pasta raiz do repositório:

```bash
python scripts/codigo1.py
```

### 4. Executar o notebook

Abra o notebook em Jupyter Notebook, JupyterLab ou Google Colab e ajuste o caminho do arquivo, se necessário.

No notebook reorganizado para esta entrega, o caminho recomendado é:

```python
dataset_path = "../data/email-Enron.txt.gz"
```

---

## Organização metodológica do notebook

O notebook do projeto foi estruturado para atender ao roteiro do trabalho, contendo:

- contextualização do problema;
- definição formal do grafo;
- justificativa da modelagem;
- leitura e limpeza do dataset;
- cálculo das métricas básicas;
- análise da distribuição de graus;
- gráficos em escala linear e log-log;
- ajuste de lei de potência;
- interpretação dos parâmetros do ajuste;
- conclusão crítica sobre a hipótese de escala livre.

---

## Observações sobre hardware, tempo e limitações

A análise foi feita sobre uma rede real relativamente grande, o que exige certo cuidado de desempenho, especialmente em rotinas como cálculo de clustering e ajuste estatístico.

Também vale destacar que:

- o uso de `Bag` foi mantido por aderência à proposta da disciplina e ao estilo `algs4`;
- como `Bag` não impede duplicatas, o tratamento correto precisou ser feito na leitura do arquivo;
- a análise de lei de potência foi feita sobre a **cauda** da distribuição, e não sobre toda a rede indistintamente;
- portanto, a classificação da rede como “escala livre” deve ser tratada com senso crítico.

---

## Entregáveis contemplados

Este repositório contempla os itens mínimos esperados pela atividade:

- script da análise;
- notebook da análise;
- dataset utilizado;
- metodologia descrita;
- definição formal do grafo;
- métricas estruturais fundamentais;
- gráficos da distribuição de graus;
- ajuste por lei de potência com parâmetros reportados;
- discussão crítica dos resultados;
- identificação da equipe.

---

## Conclusão

O projeto mostra que a rede **Enron Email Network** apresenta uma estrutura compatível com redes reais de comunicação, com forte heterogeneidade de graus e presença de uma cauda pesada na distribuição.

Os resultados sustentam a interpretação de que existe **ajuste plausível por lei de potência na cauda**, mas a equipe evita uma classificação absoluta e simplista da rede como perfeitamente escala livre. Essa postura é metodologicamente mais adequada e está de acordo com a natureza empírica do problema proposto.

---
