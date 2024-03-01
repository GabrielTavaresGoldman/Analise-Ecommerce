🎯 **Objetivo:**
Este repositório tem como objetivo demonstrar uma análise exploratória de dados de e-commerce (fictícios) retirados do SQL, utilizando Python.

A primeira coisa que eu fiz foi carregar o arquivo e excluir as colunas que não seriam relevantes na minha análise, logo em seguida, renomeei algumas colunas para que a informação tenha mais clareza.

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

caminho_arquivo = r"C:\Users\gabriel.goldman\OneDrive - FEDERACAO PAULISTA DE FUTEBOL\Área de Trabalho\Python\Ecommerce\SQL_Ecommerce.csv"
geral = pd.read_csv(caminho_arquivo, encoding= 'latin-1')
geral.dtypes
ecommerce = geral.drop(columns =['id_1', 'category_id', 'id_2','order_id', 'product_id','id_3','id_4','created_at_1','additionals' ] )
ecommerce.dtypes
ecommerce['created_at'] = pd.to_datetime(ecommerce['created_at'])
print(ecommerce.head())
ecommerce.rename(columns={'name_1': 'product'}, inplace=True)
ecommerce.rename(columns={'name': 'category'}, inplace=True)
```
Após a preparação dos dados, calculei a média da quantidade vendida dos produtos por estado. Nessa análise foi possível identificar 3 estados que se destacaram em quantidade de produtos comprados, São Paulo em 1º lugar com 5736 produtos, seguido de Pernambuco em 2º lugar com 5275 produtos e em 3º lugar Rio Grande do Norte com 4834 produtos.

![](https://github.com/GabrielTavaresGoldman/Analise-Ecommerce/blob/GabrielTavaresGoldman-patch-1/qtdde%20vendida%20p%20estado.png?raw=true)

```python
sum_quantity_state = ecommerce.groupby('state')['quantity'].sum()
plt.figure(figsize=(10, 5))
plt.bar(sum_quantity_state.index, sum_quantity_state.values, color='purple')
plt.ylabel('Quantidade Vendida')
plt.title('Quantidade Vendida por Estado')
plt.xticks(rotation=85)
plt.tight_layout()
plt.show()
```

Em seguida, sabendo que a quantidade de vendas pode não ser o principal indicador de sucesso em vendas, calculei o valor total vendido em reais de cada um dos Estados, confirmando o insight calculado anteriormente, os mesmos Estados com as mesmas posições, destacando-se novamente, agora em valores gastos. São Paulo com R$2.843.928,00 (R$495,80 por produto), Pernambuco com R$2.696.765 (R$511,23 por produto) e Rio Grande do Norte com R$2.240.602,00 (R$463,50 por produto).

![](https://github.com/GabrielTavaresGoldman/Analise-Ecommerce/blob/GabrielTavaresGoldman-patch-1/Total%20Vendido%20por%20Estado.png?raw=true)

```python
total_price_state = ecommerce.groupby('state')['total_price'].sum()
plt.figure(figsize=(13, 5))
plt.bar(total_price_state.index, total_price_state.values, color='darkblue')
plt.ylabel('Vendas em milhões de reais')
plt.title('Total Vendido por Estado')
plt.xticks(rotation=85)
plt.tight_layout()
plt.show()
```

**Análise Sazonal dos Produtos**

Depois dessas análises quis entender quais épocas do ano os consumidores tendem a comprar mais e se existem variações de preços nos períodos, para analisar estratégias de mercado e identificar padrões temporais.

Através das análises mensais, foi possível indentificar alguns picos indicando períodos de maiores vendas como em dezembro de 2018 que teve uma aumento de mais de 100% em relação ao mês outubro, mas quando analisamos a transiçaõ de janeiro/fevereiro vemos que teve uma queda gigante nas vendas de aproximadamente 250% indicando um ponto de alerta os vendedores, um dos fatores dessa discrepância entre as vendas de transiçao do ano deve-se pelas datas comemorativas, recebimento do 13º salário e principalmente férias, vemos acontecer algo semelhante em julho (período de férias), onde existe uma crescente, atinge uma máxima e depois despenca, como vemos na figura abaixo:

![](https://github.com/GabrielTavaresGoldman/Analise-Ecommerce/blob/GabrielTavaresGoldman-patch-1/valor%20total%20vendas%20mensais.png?raw=true)

```python
month_sales = ecommerce.groupby(pd.Grouper(key='created_at', freq='M'))['total_price'].sum()
plt.figure(figsize=(10, 6))
month_sales.plot(kind='line', marker='o', color='purple')
plt.title('Vendas Mensais')
plt.xlabel('')
plt.ylabel('Valor Total de Vendas em milhão de reais')
plt.grid(True)
plt.xticks(month_sales.index, [index.strftime('%b %Y') for index in month_sales.index], rotation=45)
plt.tight_layout()
plt.show()
```

Após essa análise mensal, decidi realizar uma análise trimestral para identificar outros padrões e tendências, e claramente ficou evidente que o 2º trimestre foi o pior trimestre, com um declíneo nas vendas atingindo sua mínima, esse insight é muito valioso para qualquer vendedor, pois identificando períodos de baixa nas vendas é possível traçar estratégias antecipadamente e reservar capital para evitar supresas inesperadas. 

![](https://github.com/GabrielTavaresGoldman/Analise-Ecommerce/blob/GabrielTavaresGoldman-patch-1/valor%20total%20vendas%20trimestrais.png?raw=true)

```python
quarterly_sales = ecommerce.groupby(pd.Grouper(key='created_at', freq='Q'))['total_price'].sum()
plt.figure(figsize=(10, 6))
quarterly_sales.plot(kind='line', marker='o', color='purple')
plt.title('Vendas Trimestrais')
plt.xlabel('')
plt.ylabel('Valor Total de Vendas em milhão de reais')
plt.grid(True)
plt.xticks(quarterly_sales.index, [str(index.date()) for index in quarterly_sales.index], rotation=45)
plt.tight_layout()
plt.show()
```

Outra análise que decidi realizar foi o preço médio dos produtos, tentando identificar estratégias de preço, altas e baixas e consegui identificar um aumento dos preços no 2º trimestre, que pode ser um dos influenciadores da queda de vendas no período, também foi possível observar uma crescente gigante no aumento dos preços do 3º pro 4º trimestre, indicador que pode explicar o motivo da redução das vendas se compararmos o 4º trimestre de 2017 com o 4º trimestre de 2018, onde houve uma queda de aproximadamente 30% das vendas.

![](https://github.com/GabrielTavaresGoldman/Analise-Ecommerce/blob/GabrielTavaresGoldman-patch-1/pre%C3%A7o%20m%C3%A9dio%20dos%20produtos%20por%20trimestre.png?raw=true)

```python
average_price_per_quarter = ecommerce.groupby(pd.Grouper(key='created_at', freq='Q'))['price'].mean()
plt.figure(figsize=(10, 6))
average_price_per_quarter.plot(kind='line', marker='o', color='purple')
plt.title('Preço Médio dos Produtos por Trimestre')
plt.xlabel('')
plt.ylabel('Preço Médio')
plt.grid(True)
plt.xticks(average_price_per_quarter.index, [str(index.strftime('%b %Y')) for index in average_price_per_quarter.index], rotation=45)
plt.tight_layout()
plt.show()
```

Após essas análises, decidi analisar a correlação entre a quantidade vendida por trimestre e o preço médio por trimestre para identificar se existe de fato algums relação entre as duas variáveis e obtive o resultado de -0,25. Essa correlação sugere que, em geral, mesmo que fraca, há uma tendência de que, quando a quantidade vendida aumenta em um trimestre, o preço médio dos produtos tende a diminuir, e vice-versa. No entanto, essa associação não é extremamente forte, mas ainda é significativa o suficiente para ser considerada na análise das vendas trimestrais.

```python
correlation = quarterly_sales.corr(average_price_per_quarter)
```

*Análise dos Produtos*

Com todas essas análises, busquei compreender a influência dos produtos no desempenho das vendas.

A primeira coisa que fiz foi identificar quais foram top 5 produtos em termos de vendas totais e os valores totais de vendas de cada um deles.

![](https://github.com/GabrielTavaresGoldman/Analise-Ecommerce/blob/GabrielTavaresGoldman-patch-1/top%205%20prodcom%20mais%20valor%20de%20vendas.png?raw=true)

```python
total_sales_by_product = ecommerce.groupby('product')['total_price'].sum().reset_index()
total_sales_by_product = total_sales_by_product.sort_values(by='total_price', ascending=False)
top_5_products = total_sales_by_product.head(5)
plt.figure(figsize=(12, 6))
sns.barplot(x='product', y='total_price', data=top_5_products.head(5), palette='viridis')
plt.title('Top 5 Produtos com Maior Valor de Vendas')
plt.xlabel('')
plt.ylabel('Valor Total de Vendas em milhão de reais')
plt.xticks(rotation=85)
plt.tight_layout()
plt.show()
```

Depois analisei o percentual do valor total de cada produto em relação ao valor total de vendas para entender a representatividade que esses produtos tem em relação à receita e foi possível observar que os 5 produtos juntos representam 47,43% do valor total de vendas, significando que os mesmos, impactam diretamente na relação de quantidade/valor. 

![](https://github.com/GabrielTavaresGoldman/Analise-Ecommerce/blob/GabrielTavaresGoldman-patch-1/porcent%20produtos%20em%20rela%C3%A7%C3%A3o%20ao%20total.png?raw=true)

```python
total_general_sales = ecommerce['total_price'].sum()
sales_per_product = ecommerce.groupby('product')['total_price'].sum().reset_index()
sales_per_product['percentual'] = (sales_per_product['total_price'] / total_general_sales) * 100
sales_per_product = sales_per_product.sort_values(by='percentual', ascending=False)
top_5_sales_per_product = sales_per_product.head(5)
top_5_sales_per_product['percentual'].sum() # 47,43% da receita total se concentra nesses produtos
plt.figure(figsize=(12, 6))
sns.barplot(x='product', y='percentual', data=top_5_sales_per_product, palette='viridis')
plt.title('Porcentagem do Valor Total de Cada Produto em Relação ao Valor Total Geral de Vendas')
plt.xlabel('')
plt.ylabel('Porcentagem do Valor Total (%)')
plt.xticks(rotation=55)
plt.tight_layout()
plt.show()
```

Em seguida analisei o percentual de representatividade das vendas em relação ao total vendido por Estado e através disso, descobri que talvez, o principal fator que influenciou no desempenho daqueles Estados que ficaram em TOP3 nas primeiras análises (São Paulo, Pernambuco e Rio Grande do Norte) foi a relação de vendas com esses 5 produtos, 57% das vendas desses produtos estão concentradas nesses 3 Estados, explicando o motivo dessa performance de vendas nesses locais.

![](https://github.com/GabrielTavaresGoldman/Analise-Ecommerce/blob/GabrielTavaresGoldman-patch-1/porcent%20de%20vendas%20dos%20top%205%20prod%20por%20estado.png?raw=true)

```python
total_sales_top_5_products = top_5_sales_per_product['total_price'].sum()
total_sales_by_state = ecommerce.groupby('state')['total_price'].sum()
percentage_by_state = (total_sales_by_state / total_sales_top_5_products) * 100
percentage_by_state = percentage_by_state.sort_values(ascending=False)
plt.figure(figsize=(12, 6))
sns.barplot(x=percentage_by_state.values, y=percentage_by_state.index, palette='viridis')
plt.title('Porcentagem de Vendas dos Top 5 Produtos por Estado')
plt.xlabel('Porcentagem de Vendas (%)')
plt.ylabel('')
plt.grid(axis='x', linewidth=0.5)  
plt.tight_layout()
plt.show()
```

*Conclusão*

A análise exploratória dos dados de e-commerce revelou insights valiosos sobre o desempenho das vendas, padrões sazonais e influências nos resultados. Identificamos que os estados de São Paulo, Pernambuco e Rio Grande do Norte se destacaram tanto em quantidade de produtos vendidos quanto em valor total das vendas, destacando-se como mercados-chave.

Além disso, observamos padrões sazonais significativos, com picos de vendas durante os períodos de férias e datas comemorativas, sugerindo a importância de estratégias de marketing direcionadas a esses momentos.

A correlação entre a quantidade vendida e o preço médio dos produtos evidenciou uma tendência de que, quando a quantidade vendida aumenta, o preço médio tende a diminuir, e vice-versa, embora essa associação não seja extremamente forte.

Por fim, a análise dos produtos mais vendidos revelou a importância estratégica de alguns itens-chave, que representam uma parcela significativa da receita total da empresa e têm um impacto direto no desempenho de vendas por estado.

Esta análise fornece uma base sólida para a tomada de decisões informadas e destaca a importância da análise de dados para o sucesso no ambiente competitivo do e-commerce.


