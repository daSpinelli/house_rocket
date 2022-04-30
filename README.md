# Projeto House Rocket
___
### Este projeto tem como objetivo gerar insights através de análise exploratória de dados
### E ainda, a possibilidade de gerar um modelo predititivo para auxiliar na formação de preço de venda para os imóveis.

***Autor: [Denny de Almeida Spinelli](https://daspinelli.github.io/i_am_denny/)*** 

***Suporte: [Comunidade DS](https://www.comunidadedatascience.com/)***

***Disclaimer: Todos os cenários são hipotéticos*** 
___
## Problema do Negócio
A House Rocket é uma empresa de compra e venda de imóveis no Condado de King em Washington nos EUA. 

A empresa hoje tem dificuldade em identificar as faixas de preços para os imóveis. Por conta dessa dificuldade, a empresa perde muitas vendas por aplicar um preço atrativo para os compradores. 

Pensando nisso, o CEO da empresa solicitou uma análise de dados para clarear o caminho para maximizar as vendas e consequentemente a receita. 

Então para este projeto o escopo abrange:
- criar uma análise exploratória dos dados de venda
- gerar ao menos 8 insights para guiar o time de vendas na formação do preço (que é aplicado como uma média do catálogo)
- criar um modelo preditivo para informar o preço de venda para um imóvel

## Planejamento

- **Entrada**
  - Base kc_house_data encontrada no [Kaggle](https://www.kaggle.com/datasets/harlfoxem/housesalesprediction) com dados de preço de venda de casas em King County, WA, EUA
- **Saída**
  - Lista com 8 insights
  - Análise com preço sugerido de venda de cada imóvel
- **Tarefas**
  - Análisar as features disponíveis
  - Tratar os dados faltantes
  - Tratar os tipos de dados incorretos
  - Criar novas features
  - Gerar as hipóteses bases para os insights
  - Criar um modelo preditivo

## Solução

### Suposições

**Dados**

| Feature | Description |
|:--------|:------------|
| id | ID único para cada imóvel |
| date | Data da venda |
| price | Preço do imóvel |
| bedrooms | Número de quartos |
| bathrooms | Número de banheiros, o lavabo (sem chuveiro) conta como 0.5 |
| sqft_living | Tamanho em pés do espaço interior do imóvel |
| sqft_lot | Tamanho em pés do lote |
| floors | Número de andares |
| waterfront | Variável fictícia para indicar vista para a orla |
| view | Nota de 0 a 4 para indicar o quão boa é a vista do imóvel |
| condition | Nota de 1 a 5 para indicar a condição do imóvel |
| grade | Nota de 1 a 13, onde 1 a 3 baixo padrão, 7 médio padrão, e 11 a 13 alto padrão |
| sqft_above | Tamanho em pés do espaço interior do imóvel que está acima do solo |
| sqft_basement | Tamanho em pés do espaço interior do imóvel que está abaixo do solo |
| yr_built | Ano de construção do imóvel |
| yr_renovated | Ano da última reforma |
| zipcode | Código postal |
| lat | Latitude |
| long | Longitude |
| sqft_living15 | Tamanho em pés do espaço interior do imóvel dos 15 vizinhos próximos |
| sqft_lot15 | Tamanho em pés do lote dos 15 vizinhos próximos |

**Derivação de features para facilitar a análise**

- Visão para a orla
  - Se **0** então é **não**
  - Se **1** então é **sim** 

- Qualidade da vista do imóvel
  - Se **0 ou 1** então é **ruim**
  - Se **2 ou 3** então é **razoável**
  - Se **4** então é **boa** 

- Condição do imóvel
  - Se de **0 à 2** então é **ruim**
  - Se **3 ou 4** então é **razoável**
  - Se **5** então é **boa**
 
- Padrão do imóvel
  - Se de **1 à 3** então **baixo**
  - Se de **4 à 10** então **médio**
  - Se de **11 à 13** então **alto**
 
- Ano de construção
  - Se **até 2013** então **velho**
  - Se **a partir de 2014** então **novo**
 
- Estação da venda
  - Se **de março à maio** então **primavera**
  - Se **de junho à agosto** então **verão**
  - Se **de setembro à novembro** então **outono**
  - Se **de dezembro a fevereiro** então **inverno**

### Mapa mental de hipóteses

IMAGEM

### 4.1. Análise univariada

Preço de venda


Plot de todas as features numéricas para checar a distribuição



É possível notar que o dataset está bem desbalanceado, apenas uma feature (sqft_living15) se aproxima de uma distribuição normal. 

As demais apresentam um comportamento irregular e com caldas muito longas, o que indica possível presença de outliers. 

Além disso, algumas features numéricas se comportam como variáveis categóricas então não é possível ter uma noção apenas olhando o histograma. 


### 4.2. Análise bivariada

#### H2. Propriedades com mais quartos são mais caras

**FALSE**

Não necessariamente. O maior preço médio de venda está nas propriedades de 8, 7 e 9 quartos. Enquanto na base existem propriedades de 10, 11 e até 33 quartos com o valor médio de venda menor do que o primeiro grupo.

IMAGEM

#### H3. O preço das propriedades aumenta conforme o tamanho interno da propriedade aumenta.

**TRUE**

A tendência mostra um correlação relevante e positiva entre o preço de venda e tamanho interno da propriedade. Então, quando o tamanho interno de uma propriedade aumenta, o preço de venda também aumenta.

IMAGEM

#### H8. Propriedades com vista para o lago com qualquer qualidade são mais caras do que propriedades com vista da melhor qualidade

**FALSE**

As propriedades com vista para o lago mas com má qualidade de vista não são mais caras, na média, do que as propriedades sem vista para o lago com qualidade de vista regular ou boa.

IMAGEM

### 5.2. Rescala

Vou optar pelo robust scale para features com muitos possíveis outliers e minmax para os demais.  
Features para rescala com **robust**:
- price
- bedrooms
- bathrooms
- sqft_living
- sqft_lot
- floors
- view
- condition  

Features para rescala com **minmax**:
- grade
- sqft_above
- sqft_basement
- yr_built
- yr_renovated
- zipcode
- lat
- long
- sqft_living15
- sqft_lot15

### 6.1. Divisão dos dados em treino e teste

IMAGEM

### 6.3. Seleção manual de features

Features descartadas:
- sqft_above => que tem uma alta correlação com sqft_living
- date => foi transformada em estação
- id => não relevante para previsão
- waterfront_cat => vou utilizar a escala original dos dados
- view_cat => vou utilizar a escala original dos dados
- condition_cat => vou utilizar a escala original dos dados
- grade_cat => vou utilizar a escala original dos dados
- built_cat => vou utilizar a escala original dos dados

### 7.0. Modelo de machine learning
#### 7.6. Comparação entre modelos 

**Performance em único treinamento**

IMAGEM

**Performance com validação cruzada**

IMAGEM

Métricas utilizadas:
- **MAE** (mean absolute error) - Erro médio absoluto
    - É a média da diferença entre os valores previstos pelo modelo e os valores corretos (sabidos no dataset)  

- **MAPE** (mean absolute percetage error) - Erro percentual médio absoluto
    - É o percentual médio da diferença os valores previstos pelo modelo e os valores corretos (sabidos no dataset)  

- **RMSE** (root mean squared error) - Raíz quadrada do erro médio
    - É a raíz quadrada do erro médio , onde os erros são elevados ao quadrado (para evitar a anulação de valores positivos e negativos e depois é aplicada a raíz quadrada para devolver o erro para a escala padrão da previsão

O modelo que melhor performou foi o Random Forest Regressor.  
Então seguirei com ele.

### 8.0. Hiperparametrização do modelo

Melhores parâmetros:
- n_estimators: 450
- max_features: sqrt
- max_depth: 30
- bootstrap: False

#### 8.2. Modelo Final

IMAGEM

### 9.0. Tradução do Erro

#### 9.1. Performance de Negócio

IMAGEM

Dispersão dos erros

IMAGEM

#### 9.3. Performance do Modelo

Comparação dos preços previstos com o realizado e o baseline. 

É possível notar que o modelo errou mais nos maiores valores. Preciso voltar nestes pontos no próximo ciclo.

IMAGEM

Distribuição dos erros

IMAGEM

Disperção dos erros

IMAGEM

## Conclusão e Próximos Passos

### Conclusão

IMAGEM

O que podemos notar olhando a tabela acima é que os preços previstos neste primeiro ciclo estão mais próximos do preço real do que a média utilizada. 

Sendo assim, a probabilidade de venda é muito maior utilizando o preço previsto do que a média, onde em alguns casos o preço sugerido seria 3 vezes mais alto do que valor do imóvel.

### Próximos Passos

- Apresentar os resultados ao time de negócios
- Retornar à criação das features para talvez agrupar algumas informações
- Utilizar um selecionador de features para o modelo
- Tentar uma nova reescala dos dados
- Encontrar os novos hiperparâmetros para o modelo
- Retornar com a apresentação ao time de negócios
