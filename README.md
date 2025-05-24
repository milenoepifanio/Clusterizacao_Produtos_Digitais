# Clusterização de Clientes com RFV (Recência, Frequência e Valor)

Este projeto tem como objetivo segmentar os clientes de uma operação comercial com base em seus comportamentos de compra, utilizando a técnica de clusterização RFV (Recência, Frequência e Valor). Essa segmentação permite identificar diferentes perfis de consumo e oferecer insights estratégicos para ações de marketing, retenção e fidelização.

> A abordagem é orientada por dados reais de transações e clientes, aplicando pré-processamento, análise estatística, modelagem de machine learning e visualizações interpretativas.

---

## Estrutura do Projeto

### 1. **Carregamento dos Dados**
- Leitura de transações financeiras (`Transacoes.csv`) e dados de clientes (`Clientes.csv`);
- Padronização de formatos e verificação de valores nulos;
- Conversão de colunas de datas e valores monetários.

### 2. **Tratamento e Limpeza**
- Filtragem do período de interesse: de `2022-06-01` a `2023-12-31`;
- Conversão de colunas, normalização e remoção de outliers (IQR e valores negativos);
- Padronização dos nomes dos produtos;
- Ajustes em colunas como loja (`NUM_LOJA`) e `VALOR`.

### 3. **Análise Exploratória**
- **Baixo valor agregado:** 75% das transações são abaixo de R$ 93,80; distribuição assimétrica com poucos valores altos.
- **Concentração geográfica:** 83% dos usuários estão em São Paulo/SP; outras capitais apresentam potencial de expansão.
- **Lojas destaque:** Lojas 11, 17 e 19 lideram em volume e valor; ticket médio varia entre elas.
- **Sazonalidade:** Pico entre março e julho/23; queda em jan/fev após Black Friday e Natal.
- **Comportamento de compra:** Predominância de transações pequenas; picos em valores redondos (R$ 100, R$ 200) indicam padrões de preferência.

### 4. Modelagem de Clusters RFV

Segmentação dos clientes com base em três pilares:

- **Recência**: Dias desde a última transação;
- **Frequência**: Total de transações por cliente;
- **Valor**: Soma dos valores transacionados.

#### Pré-processamento
Utilizamos o `MinMaxScaler` para normalizar os dados entre 0 e 1. Essa técnica mantém a forma da distribuição original e evita que variáveis com magnitudes maiores dominem o algoritmo de clustering (como o KMeans, que é sensível à escala dos dados).

#### Escolha do número de clusters
Aplicamos o **Silhouette Score** para avaliar a coesão e separação entre os grupos. Apesar do maior score ter ocorrido com 2 clusters, optamos por **4 clusters** para garantir granularidade analítica sem perder qualidade na segmentação.


### 5. **Perfis Gerados**

Segmentação dos clientes com base em comportamento, identificando 4 perfis por produto:

**Produto Recarga:**
- 🟢 **Entusiastas** – Engajados, mas com valores baixos.
- 🟡 **Desacelerados** – Baixa frequência e risco de churn.
- 🔵 **Estrelas** – Heavy users.
- 🔴 **Abandonadores** – Inativos, mas ainda com valor relevante.

**Produto Vale:**
- 🟢 **Econômicos** – Regulares e estáveis.
- ⚫ **Desengajados** – Pouco ou nenhum uso.
- 🔵 **Campeões** – Heavy users.
- 🟡 **Pontuais** – Uso esporádico com bons valores.

#### Ações recomendadas

- **Entusiastas (Recarga):** Engajar com promoções e experiências sociais.
- **Desacelerados (Recarga):** Reativar com cross-sell e pacotes promocionais.
- **Campeões (Vale):** Fidelizar com benefícios e gamificação.
- **Econômicos (Vale):** Estimular com ações de upsell e engajamento.


> Os nomes são definidos com base nas características médias de cada grupo (recência, frequência e valor).

### 6. **Visualização**

Abaixo estão os principais recursos visuais utilizados na análise de segmentação:

- **Avaliação da Qualidade da Segmentação - Silhouette Score**  
  Métrica usada para avaliar a coesão e separação entre os clusters.  
  ![Silhouette Score](img/Silhouette%20Score.png)

- **Segmentação dos Clientes - Recarga Digital**  
  Gráfico de dispersão com os clusters coloridos por perfil.  
  ![Segmentação Recarga](img/Segmentação%20de%20Clientes%20RECARGA.png)

- **Segmentação dos Clientes - Vale Pré-Pago**  
  Gráfico de dispersão com os clusters coloridos e centróides plotados.  
  ![Segmentação Vale](img/Segmentação%20de%20Clientes%20VALE.png)


### 7. **Exportação**
- Exportação dos resultados para Excel:
  - Aba com os usuários por cluster;
  - Aba com resumo da quantidade de usuários por grupo.

---

## Estrutura de Diretórios

```bash
📁 data/
├── Transacoes.csv
├── Clientes.csv

📁 documentacao/
├── entregavel_pm.md

📁 output/
├── usuarios_por_cluster_produto_vale.xlsx
├── usuarios_por_cluster_produto_digital.xlsx

📁 notebooks/
└── clusterizacao_rfv.ipynb

.gitignore
LICENSE.txt
README.md
requirements.txt
```

## Requisitos

- Python 3.9+
- Bibliotecas:
  - pandas
  - numpy
  - scikit-learn
  - seaborn
  - matplotlib
  - openpyxl

```bash
pip install pandas numpy scikit-learn seaborn matplotlib openpyxl
```

---

## 8 Como Executar o Projeto

1. **Clone o repositório:**
```bash
git clone https://github.com/milenoepifanio/Clusterizacao_Produtos_Digitais.git
```

2. **Instale as dependências:**
```bash
pip install -r requirements.txt
```

Execute o projeto no Jupyter Notebook. Abra o arquivo:
```bash
scr/clusterizacao_rfv.ipynb
```

---

## Licença

Este projeto está licenciado sob a [Creative Commons BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/deed.pt_BR).  
Você pode usar, compartilhar e adaptar, **desde que cite a autoria (Mileno Epifanio)** e **não utilize para fins comerciais**.


---
## Sobre o Autor

Projeto desenvolvido por **Mileno Epifanio** — Analista de Dados com foco em soluções orientadas por dados para tomada de decisão.  
[LinkedIn](https://www.linkedin.com/in/milenoepifanio) • [GitHub](https://github.com/milenoepifanio)
