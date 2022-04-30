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

![Screenshot from 2022-04-30 12-20-13](https://user-images.githubusercontent.com/68928802/166111645-92bc6958-8ad7-44b2-90e3-c7e38159f55a.png)

### 2.2. Hipóteses de negócio

**H1.** Propriedades com vista para o lago são mais caras  
**H2.** ***Propriedades com mais quartos são mais caras***  
**H3.** ***O preço das propriedades aumenta conforme o tamanho interno da propriedade aumenta***  
**H4.** Propriedades térreas são mais caras  
**H5.** Propriedades em más condições são mais baratas  
**H6.** Propriedades de alto padrão são mais caras  
**H7.** Propriedades de alto padrão mesmo em más condições são mais caras do que propriedades de baixo padrão em boas condições  
**H8.** ***Propriedades com vista para o lago com qualquer qualidade são mais caras do que propriedades com vista da melhor qualidade***  
**H9.** Propriedades de alto padrão com vista para o lago e de boa qualidade e em boas condições são raras, compõem apenas 10% da base  
**H10.** O preço das casas diminui com a idade  
**H11.** Propriedades com vista para o lago são vendidas no verão por 15% a mais no valor médio de venda do que em outras estações  
**H12.** No geral, a época do ano não gera mais de 5% de variação no preço médio de venda entre as estações

### 4.1. Análise univariada

Preço de venda

![Screenshot from 2022-04-30 12-21-53](https://user-images.githubusercontent.com/68928802/166111862-c5c1d190-7f0a-4bc9-bafd-224d7bc374c5.png)


Plot de todas as features numéricas para checar a distribuição

![Screenshot from 2022-04-30 12-24-04](https://user-images.githubusercontent.com/68928802/166111864-a6653f10-d6a8-4e58-8878-d4c199030941.png)
![Screenshot from 2022-04-30 12-24-37](https://user-images.githubusercontent.com/68928802/166111870-3a00ebca-9ec1-4ad9-b70f-b60d783ac8d7.png)


É possível notar que o dataset está bem desbalanceado, apenas uma feature (sqft_living15) se aproxima de uma distribuição normal. 

As demais apresentam um comportamento irregular e com caldas muito longas, o que indica possível presença de outliers. 

Além disso, algumas features numéricas se comportam como variáveis categóricas então não é possível ter uma noção apenas olhando o histograma. 


### 4.2. Análise bivariada

#### H2. Propriedades com mais quartos são mais caras

**FALSE**

Não necessariamente. O maior preço médio de venda está nas propriedades de 8, 7 e 9 quartos. Enquanto na base existem propriedades de 10, 11 e até 33 quartos com o valor médio de venda menor do que o primeiro grupo.

![Screenshot from 2022-04-30 12-47-38](https://user-images.githubusercontent.com/68928802/166112656-95f822ba-1df1-42f0-ad0f-304c60a81884.png)

#### H3. O preço das propriedades aumenta conforme o tamanho interno da propriedade aumenta.

**TRUE**

A tendência mostra um correlação relevante e positiva entre o preço de venda e tamanho interno da propriedade. Então, quando o tamanho interno de uma propriedade aumenta, o preço de venda também aumenta.

![Screenshot from 2022-04-30 12-52-43](https://user-images.githubusercontent.com/68928802/166112840-76bfab63-6ae7-42ae-a74b-fd75a28476a4.png)
![Screenshot from 2022-04-30 12-53-31](https://user-images.githubusercontent.com/68928802/166112866-7bd8fe84-a9e1-416c-92b2-496108e95e12.png)


#### H8. Propriedades com vista para o lago com qualquer qualidade são mais caras do que propriedades com vista da melhor qualidade

**FALSE**

As propriedades com vista para o lago mas com má qualidade de vista não são mais caras, na média, do que as propriedades sem vista para o lago com qualidade de vista regular ou boa.

![Screenshot from 2022-04-30 12-57-09](https://user-images.githubusercontent.com/68928802/166112990-ca0eef9c-e1d2-438b-a25a-22deaab13f40.png)

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

![Screenshot from 2022-04-30 12-57-42](https://user-images.githubusercontent.com/68928802/166113019-5d0ca8f4-a3d2-4ee7-85cd-72c57787701e.png)

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

![Screenshot from 2022-04-30 12-58-52](https://user-images.githubusercontent.com/68928802/166113195-50017838-d977-450b-9776-f63816e3a576.png)

**Performance com validação cruzada**

![Screenshot from 2022-04-30 12-59-01](https://user-images.githubusercontent.com/68928802/166113201-cd986437-3b87-42b3-a48a-f009255af872.png)

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

![Screenshot from 2022-04-30 12-59-26](https://user-images.githubusercontent.com/68928802/166113207-6cc8a047-f4b2-43ca-93ec-8c0fbe1120b6.png)

### 9.0. Tradução do Erro

#### 9.1. Performance de Negócio

![Screenshot from 2022-04-30 12-59-47](https://user-images.githubusercontent.com/68928802/166113209-7a886a6b-c9f2-41c5-ba3d-72d85f4e7185.png)

#### 9.3. Performance do Modelo

Comparação dos preços previstos com o realizado e o baseline. 

É possível notar que o modelo errou mais nos maiores valores. Preciso voltar nestes pontos no próximo ciclo.

![Screenshot from 2022-04-30 13-09-56](https://user-images.githubusercontent.com/68928802/166113574-41f0d1f4-c2ee-4494-b8d8-2cf44cc0731d.png)

Disperção dos erros

![Screenshot from 2022-04-30 13-13-12](https://user-images.githubusercontent.com/68928802/166113577-d3f03ca4-f68b-4568-a229-0cc450096562.png)

## Conclusão e Próximos Passos

### Conclusão

![Screenshot from 2022-04-30 13-14-53](https://user-images.githubusercontent.com/68928802/166113579-9f72527e-5572-465c-aac2-21c6b34ad48b.png)

O que podemos notar olhando a tabela de variação percentual acima é que os preços previstos neste primeiro ciclo estão mais próximos do preço real do que a média utilizada. 

Sendo assim, a probabilidade de venda é muito maior utilizando o preço previsto do que a média, onde em alguns casos o preço sugerido seria 3 vezes mais alto do que valor do imóvel.

### Próximos Passos

- Apresentar os resultados ao time de negócios
- Retornar à criação das features para talvez agrupar algumas informações
- Utilizar um selecionador de features para o modelo
- Tentar uma nova reescala dos dados
- Encontrar os novos hiperparâmetros para o modelo
- Retornar com a apresentação ao time de negócios

# Obrigado! 

### E qualquer sugestão ou comentário, entre em [contato](https://www.linkedin.com/in/dennydaspinelli/) comigo.
