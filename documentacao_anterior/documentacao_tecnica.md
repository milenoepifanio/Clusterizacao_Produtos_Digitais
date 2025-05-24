# Documentação do Projeto
## 0. Estrutura dos arquivos


Essa documentação é referente ao desafio para a vaga de Business Analyst - BSF, desenvolvido pelo candidato Mileno Alexandre Barbosa Epifânio ( +84 987650731, e-mail: mileno.alexandre86@gmail.com). A estrutura do arquivo está descrita da seguite forma:

```
BSF/
├── apresentacao/
│   ├── apresentacao.pdf
│   ├── apresentacao.ppt
├── data/
│   ├── BemolDesafioProdutosFinanceirosClientes.csv
│   └── BemolDesafioProdutosFinanceirosTransacoes.csv
├── desafio/
│   └── Desafio Business Analyst - Bemol Serviços Financeiros.pdf
├── documentacao/
│   └── documentacao_tecnica.md
│   └── entregavel_pm.md
├── img/
│   └── Silhouette Score.png
│   └── Segmentação de Clientes RECARGA DIGITAL.png
│   └── Segmentação de Clientes VALE PRÉ-PAGO.png
├── output/
│   └── usuarios_por_cluster_pre_pago.xlsx
│   └── usuarios_por_cluster_recarga_digital.xlsx
├── src/
│   └── ETL
```


## 1. Carregamento dos arquivos CSV

Nesta etapa, foi inicialmente criado a estrutura de organização do projeto, alocando os conteúdos recebidos em pastas. Após isso, foi realizado a leitura dos arquivos 

```python
financial_transactions_path = r'..\data\BemolDesafioProdutosFinanceirosTransacoes.csv'
cotumers_path = r'..\data\BemolDesafioProdutosFinanceirosClientes.csv'

df_financial_transactions = pd.read_csv(financial_transactions_path)
df_clientes = pd.read_csv(cotumers_path)
```

Após isso, foi realizado o tratamento dessas colunas de PRODUTO_TRATADO e CIDADE_TRATADA, lidando com inconsistências presentes nas duas bases de dados fornecidas para esse desafio. Esse processo é destinado e descrito com mais informação na etapa 2.


## 2. Tratamento de dados

O primeiro ponto adotado nessa etapa foi visualizar quais colunas precisaria ser tratadas, para obter os dados mais assertivos possíveis. Com isso, foi detectado que os campos de cidade e produto precisam passar por otimizações.

O primeiro passo foi mapear, por um value_counts() de maneira distinta, quais cidades apareciam na base, para ai sim mapear os padrões e ajustar por meio de uma função definida como ajustar_cidade.


```python
def ajustar_cidade(cidade):
    cidade_upper = cidade.upper()
    
    if 'MANACA' in cidade_upper or 'MANACAPUR' in cidade_upper:
        return 'MANACAPURU'
    elif 'MANAOS' in cidade_upper or 'MANAUS' in cidade_upper or 'MANAU' == cidade_upper or 'MANA' == cidade_upper:
        return 'MANAUS'
    elif 'ITACOA' in cidade_upper:
        return 'ITACOATIARA'
    elif 'IRANDUB' in cidade_upper or 'IRAN' in cidade_upper:
        return 'IRANDUBA'
    elif 'AUTAZ' in cidade_upper:
        return 'Autazes'
    elif 'FIGUEIREDO' in cidade_upper or 'PRESIDENTE' in cidade_upper:
        return 'PRESIDENTE FIGUEREIDO'
    else:
        return cidade.title()
```


Para a coluna de produtos, foi um pouco mais simples esse mapeamento, visto que são somente dois produtos, e os padrões de inserção não foram muito distintos. Foi criado a função de ajustar_produto, disponível a seguir:


```python
def ajustar_produto(produto):
    produto_upper = produto.upper()

    if 'VALE' in produto_upper and 'PRE' in produto_upper and 'PAGO' in produto_upper or 'VALE' in produto_upper:
        return 'VALE PRÉ-PAGO'
    elif 'RECARGA' in produto_upper or 'REGARGA' in produto_upper or 'DIGITAL' in produto_upper or 'RECARG' in produto_upper:
        return 'RECARGA DIGITAL'
    else:
        return produto.title()
```


Após a realização, foi realizado o drop das colunas originais, ficando somente as colunas devidamente tratadas.

Outro ponto realizado durante o tratamento de dados foi a remoção dos valores de transações financeiros considerados outliers, por meio do método de IQR. Foi utilizado por conta da distribuição dos dados não seguirem uma distribuição normal ou possuem assimetrias. 

```python
df_original = df_financial_transactions.copy()


Q1 = df_financial_transactions['VALOR'].quantile(0.25)
Q3 = df_financial_transactions['VALOR'].quantile(0.75)
IQR = Q3 - Q1

lower_bound = Q1 - 1.5 * IQR
upper_bound = Q3 + 1.5 * IQR

mask_iqr = (df_financial_transactions['VALOR'] >= lower_bound) & (df_financial_transactions['VALOR'] <= upper_bound)

df_financial_transactions = df_financial_transactions[mask_iqr]

```


## 3. Análise Exploratória dos dados tratados

- Distribuição das Transações:

    * 75% das transações são de valores abaixo de 100 reais, indicando que a maioria das transações tem baixo valor agregado.
    * Existem picos em valores como 100, 200 e 300 reais, sugerindo uma adesão a pagamentos de produtos nesses valores.
    * A variância de 70,95 e a assimetria positiva indicam grande dispersão, com algumas transações de alto valor (mínimo de 0,20 reais e máximo de 358,80 reais).
        * Isso pode indicar que as pessoas utuilizam para pagamento de diversos produtos, e não uma categoria nichada

- Geografia dos Usuários:
    * 83% dos usuários estão localizados em Manaus/AM.
    * Outras cidades como Manacapuru (6%) e Itacoatiara (4%) têm representatividade menor.

- Lojas com Maior Atividade:
    * Lojas 58, 59 e 55 possuem a maior quantidade de transações e destacam-se também na representatividade financeira.

- Padrão de Comportamento:
    * A base apresenta uma cauda longa, com transações de valores mais elevados ainda presentes.
    * A maior parte do público é mais ativa em transações de baixo valor.
    * Esses pontos refletem um mercado concentrado em transações menores, com alguns picos de pagamentos médios e grandes, sugerindo potenciais oportunidades de otimização e expansão.

- Padrão Sazonal
    * A partir de janeiro de 2023, observa-se um crescimento nos valores transacionados, crescendo mês a mês até dezembro de 2023.
    * É registrado uma queda nos meses de janeiro e fevereiro de 2023, o que pode indicar um período de sazonalidade negativa após eventos de fim de ano como Black Friday (Novembro) e Natal (Dezembro).
    * Após fevereiro de 2023, há um crescimento consistente até o final do período analisado.

## 4. Criação da clusterização baseada em RFV (Recência, Frequência e Valor)

Inicialmente, foi buscado definir, por meio de uma amostra de 20% da base de dados fornecida, com base nos campos de id_cliente e produto_ajustado, qual o comportamento da Silhouette Score, que é uma métrica utilizada para avaliar a qualidade da clusterização. Basicamete, indica-nos qual a quantidade aceitável de agrupamentos possíveis para trabalhar esse desafio. 
COm base nisso, foi testado o comportamento da curva com agruamentos que oscilam entre 2 e 11 clusters, sendo possível visualizar na imagem abaixo:


![Silhuette Score](../img/Silhouette%20Score.png)


Como é possível ver, o melhor agrupamento ocorre em dois clusters, mas todas as opções apresentam valores acima de 0.50, que é considerado como uma boa definição e dados agrupados de forma separada, sendo consideradas todas os agrupamentos considerados como "satisfatórios" nas variáveis escolhidas.


Foram escolhidos trabalhar com 4 clusters, por ser um valor ainda considerado satisfatório, seguindo a Curva da Silhuette e sendo mais rápido assiciar padrões. Como é possível ver nas imagens abaixo, tanto o agrupamento para a RECARGA DIGITAL, tanto para VALE PRÉ-PAGO, ambas tem valores de curva acima de 0.68, nos informando que podemos trabalhar com esses dados.


![Recarga Digital](../img/Segmentação%20de%20Clientes%20RECARGA%20DIGITAL.png)
![Vale Pré-Pago](../img/Segmentação%20de%20Clientes%20VALE%20PRÉ-PAGO.png)


## 4. Definição e análise dos Clusters

### 4.1 Recarga Digital:

* **Cluster 0: Entusiastas**
  * Recência: Moderada (~148 dias).
  * Frequência: Moderada (~4.6).
  * Valor: Moderado (~387).
  - **Descrição**: Clientes com transações regulares e valores médios, mas que não estão tão recentes.


* **Cluster 1: Desacelerados**
  * Recência: Alta (~293 dias).
  * Frequência: Baixa (~3.3).
  * Valor: Moderado (~287).
  - **Descrição**: Clientes que realizaram transações há algum tempo, mas ainda apresentam valores razoáveis.

* **Cluster 2: Estrelas**
  * Recência: Muito baixa (~30 dias).
  * Frequência: Alta (~13.4).
  * Valor: Muito alto (~1046).
  - **Descrição**: Clientes muito ativos, com alto volume de transações recentes e grande valor transacionado. Grupo prioritário para retenção.


* **Cluster 3: Abandonadores**
  * Recência: Alta (~474 dias).
  * Frequência: Baixa (~2.2).
  * Valor: Moderado (~184).
  - **Descrição**: Clientes inativos com transações esporádicas e valores médios.


### 4.2 Vale Pré-Pago:

* **Cluster 0: Econômicos**
  * Recência: Moderada (~241 dias).
  * Frequência: Moderada (~2.1).
  * Valor: Médio (~114).
  - **Descrição**: Clientes regulares, com frequência e valores medianos.

* **Cluster 1: Desengajados**
  * Recência: Muito alta (~546 dias).
  * Frequência: Muito baixa (~1.38).
  * Valor: Baixo (~73).
  - **Descrição**: Praticamente inativos, com transações esporádicas e valores baixos. Possível grupo a reativar.

* **Cluster 2: Campeões**
  * Recência: Muito baixa (~77 dias).
  * Frequência: Alta (~3.1).
  * Valor: Alto (~181).
  - **Descrição**: Clientes ativos com transações recentes, alta frequência e valores elevados. Grupo prioritário para retenção.


* **Cluster 3: Pontuais**
  * Recência: Alta (~399 dias).
  * Frequência: Baixa (~1.7).
  * Valor: Moderado (~97).
  - **Descrição**: Clientes que realizam transações menos frequentes, mas com valores razoáveis.

## 5. Considerações

A análise executada, de forma focada no comportamento do cliente e das transações financeiras dos dois produtos analisados. Alguns pontos abordados foram:

  * Para o produto Recarga Digital, o foco deve estar nos clusters "Estrelas" (retenção de clientes altamente valiosos) e "Entusiastas" (engajamento contínuo).
  * Para o Vale Pré-Pago, a atenção deve ser voltada para os "Campeões" e para a reativação dos "Desengajados".

Como próximos passos:

* Desenvolver campanhas personalizadas para cada cluster, sendo focadas na melhoria e retenção a retenção e adesão do ecosistemas de produtos.

* Analisar outros fatores externos que possam influenciar os padrões de utilização das ferramentas.
