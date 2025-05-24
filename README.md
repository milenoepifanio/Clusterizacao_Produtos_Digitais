# Clusterização de Clientes com RFV (Recência, Frequência e Valor)

Este projeto tem como objetivo segmentar os clientes de uma operação comercial com base em seus comportamentos de compra, utilizando a técnica de clusterização RFV (Recência, Frequência e Valor). Essa segmentação permite identificar diferentes perfis de consumo e oferecer insights estratégicos para ações de marketing, retenção e fidelização.

> A abordagem é orientada por dados reais de transações e clientes, aplicando pré-processamento, análise estatística, modelagem de machine learning e visualizações interpretativas.

---

## Estrutura do Projeto

### 1. **Carregamento dos Dados**
- Leitura de transações financeiras (`Transacoes_2.csv`) e dados de clientes (`Clientes_2.csv`);
- Padronização de formatos e verificação de valores nulos;
- Conversão de colunas de datas e valores monetários.

### 2. **Tratamento e Limpeza**
- Filtragem do período de interesse: de `2022-06-01` a `2023-12-31`;
- Conversão de colunas, normalização e remoção de outliers (IQR e valores negativos);
- Padronização dos nomes dos produtos;
- Ajustes em colunas como loja (`NUM_LOJA`) e `VALOR`.

### 3. **Análise Exploratória**
- Estatísticas descritivas;
- Distribuição geográfica dos clientes;
- Representatividade de lojas em número de transações e valores;
- Sazonalidade dos valores por mês/ano;
- Distribuição dos valores de transação.

### 4. **Modelagem de Cluster RFV**
- Definição das variáveis:
  - **Recência**: Dias desde a última compra;
  - **Frequência**: Total de transações por cliente;
  - **Valor**: Soma dos valores transacionados;
- Aplicação de `MinMaxScaler` para normalização;
- Definição do número ideal de clusters via **Silhouette Score**;
- Aplicação de **KMeans** para segmentação.

### 5. **Perfis Gerados**

Segmentamos os clientes com base em comportamento e valor transacionado, identificando 4 perfis por produto:

**Recarga Digital**
- **Entusiastas:** Engajados, mas com valores baixos.
- **Desacelerados:** Uso esporádico e risco de churn.
- **Estrelas:** Heavy users.
- **Abandonadores:** Inativos, porém com valor relevante.

**Vale Pré-Pago**
- **Econômicos:** Regulares e estáveis.
- **Desengajados:** Pouco ou nenhum uso.
- **Campeões:** Heavy users.
- **Pontuais:** Uso ocasional com bons valores.

### Ações recomendadas

- **Entusiastas (Recarga):** Engajar com promoções e experiências sociais.
- **Desacelerados (Recarga):** Reativar com cross-sell e pacotes promocionais.
- **Campeões (Vale):** Fidelizar com benefícios e gamificação.
- **Econômicos (Vale):** Estimular com ações de upsell e engajamento.


> Os nomes são definidos com base nas características médias de cada grupo (recência, frequência e valor).

### 6. **Visualização**
- Gráfico de dispersão com os clusters coloridos por perfil;
- Plotagem dos centróides;
- Tabela resumo com média de Recência, Frequência e Valor por perfil.

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

📁 output/
├── usuarios_por_cluster_produto_vale.xlsx
├── usuarios_por_cluster_produto_digital.xlsx

📁 notebooks/
└── clusterizacao_rfv.ipynb

.gitignore
README.md
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
scr/ETL.ipynb
```

# Autor:
Mileno Epifânio ([GitHub](https://github.com/milenoepifanio) / [LinkedIn](https://www.linkedin.com/in/milenoepifanio/))