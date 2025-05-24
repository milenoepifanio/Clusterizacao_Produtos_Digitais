# Documentação do Projeto

## 1. Carregamento dos arquivos CSV

Nesta etapa, foi inicialmente criado a estrutura de organização do projeto, alocando os conteúdos recebidos em pastas. Após isso, foi realizado a leitura dos arquivos 

```python
financial_transactions_path = r'..\data\BemolDesafioProdutosFinanceirosTransacoes.csv'
cotumers_path = r'..\data\BemolDesafioProdutosFinanceirosClientes.csv'

df_financial_transactions = pd.read_csv(financial_transactions_path)
df_clientes = pd.read_csv(cotumers_path)
```

Após isso, foi realizado o tratamento dessas colunas de PRODUTO_TRATADO e CIDADE_TRATADA, lidando com inconsistências presentes nas duas bases de dados fornecidas para esse desafio. Esse processo é destinado e descrito com mais informação na etapa 2


## 2. Tratamento de colunas via pandas

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


## 3. Criação da clusterização baseada em RFV (Recência, Frequência e Valor)







