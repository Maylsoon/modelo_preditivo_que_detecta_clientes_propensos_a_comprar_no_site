#👨‍💻 **Previsão de intenção de compras em um site de e-commerce**

##🎯 **Objetivo do projeto**

 desenvolver um modelo preditivo de machine learning capaz de analisar os padrões de comportamento dos clientes e identificar sinais que indiquem uma propensão deles a realizar compras no site da empresa. 

 o modelo consegue auxiliar no aprimoramento da experiência do cliente, permitindo que o negócio direcione seus esforços de marketing de forma mais eficiente. 

 O modelo apresentou 90% de acurácia e 88% de precisão.

## **Entendendo a base**

há dados demográficos dos clientes: 

- idade,
- nível de escolaridade,
- estado civil,
- renda,
- composicao familiar

além de dados comportamentais detalhados de compra.

o dataset possui ***2.240 registros e 16 variáveis*** sendo:

- 2 categóricas (`'nivel_educacional'` e `'estado_civil'`) e as demais numéricas.

- há uma alta variabilidade nos dados de renda e outliers também nas variáveis de comportamento de compras, o que é esperado que alguns clientes consumam mais produtos que outros.

## **Tratamento de nulos e duplicatas**

- a variável `'renda'` na base de treino possuía 1% de valores nulos, que foram substituídos pela mediana para manter a integridade da base. Procedimento aplicado também na base de teste. Além disso, houve um único registro extremamente superior de 666666.00 na renda, que foi excluido por não representar um grupo real de clientes e para não distorcer a análise.

- a base também possuía 9% de linhas duplicadas com registros iguais em todas as variáveis, indicando erro nas etapas anteriores de consolidação da base e, portanto, essas linhas duplicadas foram removidas para evitar viés nos resultados.

- a variável `'ano_nascimento'` também apresentou três registros inferiores a 1901, com idades muito extremas (indivíduos com + de 110 anos) que foram substituídos pela mediana histórica.

## **Análise de outliers**

É esperado em bases de consumo, que renda contenha valores extremos, onde determinados clientes podem apresentar níveis significativamente mais elevados de gasto em comparação com a média da população. Os valores foram, portanto, mantidos por representarem comportamentos reais de clientes com alto volume de compras, sendo relevantes para o entendimento do padrão de consumo.

## **Análise exploratória de dados**

- foi analisado que clientes que compram no site, consumem muito vinho, gastando em média 483,00
enquanto usuários que não compram no site gastam em média com vinho 75% a menos (119,00).
- Para esse dataset, quanto mais crianças na residência, menor a probabilidade do cliente realizar compras na internet.
Há 806 compradores sem crianças em casa, enquanto quando se há 2 crianças em casa, o número de compradores cai para 9.

## **Engenharia de atributos**

- ano de nascimento foi transformada em idade, pois é mais interpretável para o modelo.
- as variáveis de consumo foram agregadas gerando a receita total gasto pelo cliente na busca de padrões fortes de comportamento.
- criou-se notas de recência, frequência de compras e gastos dos clientes para munir o modelo de informações com dados numéricos ordenados.

## **Modelagem**

|    | modelo            |   roc_auc |   precision |   desvio_padrão |   acurácia |
|---:|:------------------|----------:|------------:|----------------:|-----------:|
|  0 | Árvore de decisão |      0.98 |        0.92 |            0.02 |       0.95 |
|  1 | Random Forest     |      0.98 |        0.87 |            0.01 |       0.91 |
|  2 | Xgboost           |      0.99 |        0.85 |            0.01 |       0.91 |


![duelo de modelos]('FIGURAS/duelo.png')


A árvore de decisão como baseline apresentou ótima performance inicial, com 0.98 de ROC_AUC, que mostra a capacidade do modelo em separar bem as duas classes, embora tenha apresentado leve instabilidade entre os folds de treinamento (desvio: 0.02).
- Acurácia: 0.95, proporção de acertos do modelo baseline.
- Precisão: 0.92, proporção de observações em que o modelo preveu corretamente.

No treinamento do Random Forest, o segundo modelo, foi reduzida ainda mais a profundidade das árvores para evitar eventual overfitting,
e embora tenha tido uma performance razoavelmente menor, as métricas se mantiveram alta devido a robustez do Random Forest, além de trazer melhor estabilidade entre os folds de treinamento.
- Acurácia: 0.91
- Precisão: 0.86
- Roc_Auc: 0.98

O xgboost, terceiro modelo, conseguiu melhorar ainda mais a métrica roc_auc, que alcançou incríveis 0.99 no treinamento, além de manter a performance das métricas do Random Forest e estabilidade entre os folds de treinamento. O modelo quase detectou todos os casos reais de clientes compradores do site, com esse maior foco nos casos reais, é esperado que ele tenha perdido um pouco de sua precisão, que ficou em 0.85
- Acurácia: 0.91 

## **Modelo campeão**

o modelo campeão escolhido nesse projeto foi o ***Xgboost*** pela robustez, estabilidade e capacidade de lidar com relações não lineares, outliers e multicolinearidade.

Foi utilizada a técnica do `randomized search cv` para encontrar os melhores hiperparâmetros do modelo:

-`max_depth = 6` indica a profundidade em cada árvore, pois como os dados compõem um cenário de complexidade moderada a alta, então são exigidas regras detalhadas para serem decifrados.
-`min_child_weight = 2` indica que existem pequenas exceções ou subgrupos importantes que não podem ser ignorados
-`reg_alpha/lambda` indica que existem variáveis irrelevantes ou ruídos que precisam ser filtrados
-`learning_rate: 0.5` mostra que o modelo se beneficia de um refinamento passo a passo.

## **Teste final**

|    | modelo             |   roc_auc |   acurácia |   precisão |
|---:|:-------------------|----------:|-----------:|-----------:|
|  0 | Xgboost Classifier |      0.95 |        0.9 |       0.88 |

o modelo manteve uma excelente performance para novos dados, com ótima capacidade de separar bem as classes (0.95 roc_auc),
o gráfico da distribuição das probabilidades mostra os 'não compradores próximos de zero e os compradores próximos de um.

O modelo acertou 458 dos 510 registros do estudo (90% acurácia), além de uma ótima precisão, acertando 230 das 260 amostras (88% de precisão) e sendo ainda mais eficiente em detectar os casos reais: 230 acertos de 252 registros (91% de recall)

|                |   não comprador |   comprador |
|---------------:|----------------:|------------:|
|  não comprador | 228             |          30 |
|  comprador     |  22             |         230 |

## **Variáveis importantes para o modelo**

![features_importance]('FIGURAS/feature_importance.png')
## **Conclusões**

O modelo se apoia em variáveis comportamentais: `gastos totais, gastos com vinhos, com carnes`
e variáveis socioeconômicas, como `faixa de gastos`, cruzando essas informações históricas e interpretando que quem gastou muito tem probabilidade de comprar novamente.

É recomendado, portanto, estratégias de marketing focadas em clientes já engajados, utilizando campanhas personalizadas baseadas no histórico de consumo.

Uma limitação do modelo, por outro lado, é que ele pode ter menor capacidade de identificar novos clientes com potencial de compra, pois depende fortemente de comportamento anterior. Ou seja, o modelo é uma ótima solução de negócio para o auxilio de estratégias de cross-sell e upsell, mas não tão bom para apoiar o time na aquisição de novos clientes.

Outra recomendação é integrar o modelo a estratégias de marketing para segmentação de clientes, permitindo campanhas direcionadas, aumento de conversão e recompra, mostrando eficiência em reduzir custos de aquisição de novos clientes.

---

## 💾 Estrutura do Repositório e Execução
```text
📁 PREVISÃO DE INTENÇÃO DE COMPRA/
├── 📁 bases/                     
├── 📁 notebooks/                 
│   ├── 01_pre_processamento_eda.ipynb
│   ├── 02_modelagem.ipynb
├── 📜 .gitignore                          
└── 📜 readme.md                  
```

Para reproduzir este projeto, instale as dependências e execute os notebooks respeitando a ordem cronológica.