
# Big Tech Stock Prices
Projeto de análise do histórico de ações das big techs.

 <details>
 <summary><strong style="font-size: 16px;">Equipe</strong></summary>
   
  - Cassia Silva
  - Vanessa Santana do Amaral
  
   </details>

 <details>
  <summary><strong style="font-size: 16px;">Ferramentas e tecnologias</strong></summary>
   
- Ambiente de desenvolvimento: Google Colab
- Linguagem de programação: Python
- Visualização e dashboards: Looker Studio
- Organização e documentação: Notion
 
  </details>
  
  <details>
  <summary><strong style="font-size: 16px;">Objetivo</strong></summary>
  Apresentar uma visão comparativa e interativa sobre a performance, o risco e o comportamento histórico das ações, permitindo que o investidor alinhe seu perfil de risco à estratégia de alocação de capital.
   
 </details>

 <details>
<summary><strong style="font-size: 16px;">Processamento dos dados</strong></summary>

### Importação dos dados
- **companies.csv:** Continha informações sobre as empresas e seus códigos de ações.
- **stock_prices.xlsx:** Apresentava dados históricos diários de preços (abertura, alta, baixa, fechamento, fechamento ajustado) e volume de negociações.

### Identificação e tratamento de valores nulos
Foi detectada uma linha completamente vazia, que foi removida por não conter informações relevantes para a análise, preservando a integridade e qualidade dos dados, evitando impacto nas análises subsequentes.
Valores nulos (`NaN`) também surgiram após a criação de novas variáveis (como `daily_pct_change` nas primeiras entradas de cada ação. Esses `NaN`s foram devidamente tratados (removidos) antes das análises e modelagens específicas para garantir a integridade dos cálculos.

### Identificação e tratamento de duplicados
Foi identificada a presença de uma linha duplicada, idêntica em todas as colunas. Optou-se por remover essa duplicata, pois registros repetidos não agregam informação e podem comprometer a precisão das análises.

### Validação e padronização dos dados
Foram analisadas as variáveis para identificar possíveis inconsistências, como erros de digitação, variações na grafia de categorias equivalentes ou categorias que não fazem sentido no contexto do conjunto de dados.
Após essa análise, as categorias foram padronizadas, garantindo maior qualidade e uniformidade dos dados para análise.

### União das tabelas (merge)
Como trabalhamos com apenas duas bases de dados — sendo uma delas responsável pela descrição dos nomes das empresas — optou-se por realizar o merge logo no início do processo. Dessa forma, todas as análises subsequentes foram feitas sobre a tabela unificada. 
Essa união não impactou as análises nem a formatação, facilitando o tratamento e a exploração dos dados de forma integrada.

### Tratamento de outliers
Para identificar e tratar possíveis outliers, utilizamos o método do IQR (Intervalo Interquartil), pois os dados apresentam assimetria, comum em preços de ações.
Inicialmente, foi gerado um código para apontar as variáveis com potenciais outliers. Em seguida, utilizamos boxplots para uma análise visual mais detalhada. As ações foram agrupadas por empresa e data, e os outliers foram analisados pelo contexto de mercado da época.
Outliers identificados em dados de ações foram mantidos, pois refletiam eventos reais de mercado, não erros.

- Para a **Netflix (NFLX)**, os picos em **janeiro de 2018** foram ligados a um forte crescimento e lucros acima do esperado no 4º trimestre de 2017, impulsionados pela expansão e conteúdo original.
- Para o **volume negociado de Apple (AAPL), Amazon (AMZN) e Google (GOOGL)**, os picos no **início de 2010** coincidiram com grandes lançamentos tecnológicos (como o iPad da Apple) e a recuperação do interesse do mercado de tecnologia após a crise de 2008/2009.

Esses dados foram considerados **legítimos** e cruciais para entender o comportamento real do mercado financeiro, por isso não foram removidos.
   
  </details>

<details>
  <summary><strong style="font-size: 16px;">Análise exploratória (EDA)</strong></summary>
  Na etapa de análise exploratória, focamos em entender a distribuição, padrões e tendências dos preços das ações, além de criar novas variáveis para aprofundar a análise.

- **Visão geral e análise por empresa:** Agrupamento e agregação de dados para compreender o comportamento geral e específico de cada empresa.
- **Estatísticas descritivas:** Cálculo de medidas de tendência central (média, mediana – priorizada pela robustez em dados assimétricos) e dispersão (desvio padrão, variância, IQR – priorizado pela robustez).
- **Comportamento temporal:** Utilização de `lineplots` para visualizar a evolução do preço de fechamento ao longo do tempo.
- **Criação de novas variáveis:** Essencial para as análises subsequentes:
    - `daily_pct_change`: Variação percentual diária do preço de fechamento.
    - `price_range`: Amplitude de preço intradiária (máxima - mínima do dia).
    - `month`: Mês extraído da data para análise de sazonalidade.
    - `is_bullish`: Variável binária (1 se `close > open`, 0 caso contrário) para identificar dias de alta.
    - **Features Defasadas para Modelagem:**
        - `prev_day_pct_change`: Variação percentual do dia anterior.
        - `prev_open_close_diff`: Diferença `close - open` do dia anterior.
        - `prev_volume`: Volume do dia anterior.
        - `prev_daily_range_pct`: Amplitude percentual do dia anterior.
    - **Métricas de Risco Consolidadas por Ação:**
        - `amplitude_historica`: Amplitude total do preço no período histórico.
        - `desvio_padrao_close`: Desvio padrão do preço de fechamento.
        - `std_daily_pct_change`: Desvio padrão da variação percentual diária.
        - `avg_price_range_intraday`: Média da amplitude de preço intradiária.
        - `volume_medio`: Volume médio diário.
        - `perfil_risco_absoluto`: Classificação de risco baseada na amplitude histórica absoluta (`Conservador`, `Moderado`, `Agressivo`).
        - `amplitude_relativa`: Amplitude histórica da ação em relação à média da amplitude de todo o portfólio.
        - `perfil_risco_relativo`: Classificação de risco baseada na amplitude relativa (`Conservador`, `Moderado`, `Agressivo`), com cortes definidos pelos quantis da distribuição da amplitude relativa.
- **Correlação e Visualização:** Cálculo de matrizes de correlação e `heatmaps` para revelar o grau de relacionamento entre as variáveis financeiras.

  </details>
  <details>
  <summary><strong style="font-size: 16px;">Técnicas de análises aplicadas</strong></summary>
    
### Segmentação

A segmentação foi usada para visualizar e comparar diferentes aspectos do comportamento do mercado, incluindo:

- **Por empresa:** Análise do desempenho individual de cada empresa.
- **Tendência de alta/baixa (`is_bullish`):** Comparação de períodos de valorização e desvalorização.
- **Média da variação percentual por Mês:** Entendimento das flutuações mensais (sazonalidade).
- **Faixa de variação percentual diária:** Observação da volatilidade diária.

### Hipóteses e teses de significância

Para validar as ideias iniciais e embasar as análises, foram levantadas **seis hipóteses**. Utilizamos diferentes testes de significância para verificar a validade de cada uma delas, conforme abaixo:

| Hipótese | Teste Aplicado | Resultado (Valor-p) | Decisão | Ação Relevante para o Investidor |
| --- | --- | --- | --- | --- |
| H1: Empresas com maior volume negociado tendem a ter maior variação percentual diária. | Correlação de Pearson | p=0.0064 | Estatisticamente significativa, mas a **correlação é muito fraca (0.0128)**. | Avaliar com cautela; o volume por si só não é um preditor forte da magnitude da variação percentual diária. |
| H2: Empresas do setor de tecnologia apresentam maior tendência de alta (bullish) do que outras. | Qui-quadrado | p=0.0110 | **Confirmada:** Diferença estatisticamente significativa na tendência de alta entre as empresas. | Sugere que o comportamento de alta pode variar significativamente entre as empresas, justificando análise e estratégia por ativo. |
| H3: Há diferença significativa na variação percentual diária entre diferentes empresas. | ANOVA | p=0.8457 | **Refutada:** Não há evidências de diferença significativa; as médias de variação diária são muito similares entre as empresas. | Comparações de performance entre essas empresas com base na variação percentual média são válidas, sem vieses significativos. |
| H4: Dias de maior amplitude de preço (`price_range`) estão associados a maiores volumes. | Correlação de Pearson | p=0.0000 | Estatisticamente significativa, mas a **correlação é fraca e positiva (0.1017)**. | Há uma relação, mas volume não é um preditor *forte* da amplitude; grandes amplitudes não necessariamente significam volumes massivos. |
| H5: A variação percentual média é significativamente diferente de zero. | Teste t de uma amostra | p=0.0000 | **Confirmada:** A média da variação percentual diária é estatisticamente diferente de zero. | Indica uma tendência média (positiva ou negativa, mas não neutra) no período, relevante para expectativas de retorno. |
| H6: A variação percentual é maior em meses específicos. | ANOVA | p=0.0000 | **Confirmada:** A variação percentual difere significativamente entre os meses. | Confirma a existência de sazonalidade; considerar esta informação para posicionamentos estratégicos ao longo do ano. |

### **Regressão Linear (Previsão do preço de fechamento):**

- **Ajustes:** O modelo foi ajustado para prever o preço de fechamento (`close`) de um dia com base em **features defasadas (do dia anterior)**, como `prev_open`, `prev_high`, `prev_low`, `prev_volume`. Isso transformou o modelo de uma interpolação intradiária para uma previsão mais realista do dia seguinte.
- **Resultados (Modelo Anterior de Interpolação):** R² = 0.999872 e MSE = 1.377. (Estes resultados eram para o modelo que previa o `close` com `open`, `high`, `low` do *mesmo dia*, demonstrando alta colinearidade).
- **Conclusão (Modelo Ajustado):** O novo modelo, embora ainda possa apresentar um bom ajuste (pois os preços históricos têm forte correlação sequencial), busca uma previsão mais realista ao usar dados do dia anterior. A alta correlação natural dos preços de fechamento consecutivos pode resultar em R² ainda elevado, mas agora com um caráter preditivo para o futuro próximo, e não uma mera replicação de dados contemporâneos. A possibilidade de overfitting sempre deve ser considerada em séries temporais.

## Tabela final
Após a finalização de toda a EDA, foi gerado uma nova tabela final (`df_merged_final`) com as seguintes variáveis:

- **`stock_symbol`**: É o identificador único de cada empresa no mercado de ações.
- **`date`**: Data da cotação.
- **`open`**: Preço de abertura da ação no início do dia de negociação.
- **`high`**: Preço máximo que a ação atingiu durante o dia de negociação.
- **`low`**: Preço mínimo que a ação atingiu durante o dia de negociação.
- **`close`**: Preço de fechamento da ação no final do dia de negociação. É o preço mais comumente usado para análises diárias.
- **`adj_close`**: Preço de fechamento ajustado. Este preço é ajustado para considerar eventos corporativos como desdobramentos de ações (splits) e dividendos, fornecendo uma representação mais precisa do valor da ação ao longo do tempo.
- **`volume`**: Número total de ações negociadas durante o dia. Indica a atividade e liquidez do papel.
- **`company`**: Nome completo da empresa à qual o `stock_symbol` pertence (ex: 'Apple Inc.', 'Netflix Inc.').
- **`Date`**: Usada para todas as análises temporais.
- **`daily_pct_change`**: Variação percentual do preço de fechamento de um dia para o outro. Indica o retorno diário da ação.
- **`price_range`**: Amplitude de preço intradiária, calculada como `high - low`. Representa a volatilidade do preço dentro de um único dia de negociação.
- **`month`**: Mês da cotação, extraído da coluna `Date`.
- **`is_bullish`**: Variável binária (0 ou 1) que indica a tendência do dia:
    - **`1` (True)**: Se o preço de fechamento (`close`) foi maior que o preço de abertura (`open`), indicando um dia de alta (bullish).
    - **`0` (False)**: Se o preço de fechamento (`close`) foi menor ou igual ao preço de abertura (`open`), indicando um dia de baixa ou estabilidade (bearish/sideways).
- **`preco_min`**: O preço de fechamento mínimo histórico da ação no período analisado.
- **`preco_max`**: O preço de fechamento máximo histórico da ação no período analisado.
- **`amplitude_historica`**: A diferença entre o `preco_max` e o `preco_min` histórico da ação. Representa a amplitude total de variação de preço da ação ao longo de todo o período no dataset.
- **`perfil_risco_absoluto`**: Classificação do risco da ação baseada em sua `amplitude_historica` (risco absoluto). As categorias são: 'Conservador' (amplitude < 100), 'Moderado' (amplitude entre 100 e 300) e 'Agressivo' (amplitude > 300).
- **`amplitude_relativa`**: A `amplitude_historica` da ação dividida pela média da `amplitude_historica` de *todas as ações* no portfólio. Isso fornece uma medida de risco relativo, comparando a volatilidade de uma ação com a do "mercado" (o conjunto de ações do seu dataset).
- **`perfil_risco_relativo`**: Classificação do risco da ação baseada em sua `amplitude_relativa` (risco relativo), usando cortes definidos pelos quantis da distribuição da `amplitude_relativa`. As categorias são: 'Conservador', 'Moderado' e 'Agressivo'.
- **`desvio_padrao_close`**: Desvio padrão do preço de fechamento da ação ao longo de seu histórico. Uma medida da volatilidade dos preços.
- **`std_daily_pct_change`**: Desvio padrão da `daily_pct_change` da ação. Uma medida chave da volatilidade de retorno da ação.
- **`avg_price_range_intraday`**: A média da `price_range` diária da ação ao longo de seu histórico. Indica a amplitude média de variação de preço dentro de cada dia para aquela ação.
- **`volume_medio`**: O volume médio diário de negociações da ação ao longo de seu histórico.

</details>

<details>
<summary><strong style="font-size: 16px;">Resultados e Recomendações</strong></summary>

  **Visão geral do comportamento das ações:**

- **Netflix (NFLX):** Lidera em frequência de dias de alta (57%) e maior variação percentual média diária (+1.25%), com volume médio de 38 milhões.
- **Google (GOOGL):** Menor frequência de alta (48%) e menor variação percentual média (+0.68%), com volume médio de 65 milhões.
- **Apple (AAPL):** 52% de dias com alta, +0.84% de variação média e 78 milhões de volume.
- **Microsoft (MSFT):** 50% de dias com alta, +0.73% de variação média e 55 milhões de volume.
- **Amazon (AMZN):** 51% de dias com alta, +0.91% de variação média e 98 milhões de volume.

**Ações com maior rentabilidade no período (variação percentual acumulada):**

- NFLX: +136.5%
- AMZN: +98.2%
- AAPL: +84.6%
- MSFT: +73.4%
- GOOGL: +65.7%

**Conclusão:** Netflix e Amazon destacam-se como líderes em valorização, indicando forte potencial para longo prazo, considerando a volatilidade.

**Ações mais voláteis:**

- A volatilidade foi avaliada pelo desvio padrão da variação percentual diária e pela amplitude média de preço diário.

| Empresa | Desvio Padrão (% diário) | Amplitude Média de Preço (USD) |
| --- | --- | --- |
| NFLX | 2.68% | 7.30 |
| AMZN | 2.01% | 5.45 |
| AAPL | 1.48% | 3.28 |
| GOOGL | 1.44% | 3.15 |
| MSFT | 1.37% | 2.90 |

**Conclusão:** Netflix (NFLX) é a mais volátil, enquanto Microsoft (MSFT) e Google (GOOGL) são as menos voláteis, indicadas para perfis mais conservadores.

**Análise de risco relativo:**

- **Conceito:** O risco relativo avalia a volatilidade de uma ação em comparação com a volatilidade média do mercado (média da amplitude histórica de todas as ações no portfólio).
- **Cálculo:** Calculou-se a `amplitude_relativa` para cada ação, dividindo sua `amplitude_historica` pela `média da amplitude histórica de mercado`.
- **Determinação de Cortes e Classificação:** Os cortes para as categorias de risco (`Conservador`, `Moderado`, `Agressivo`) foram definidos de forma **data-driven**, utilizando os **quantis (33% e 66% da distribuição da `amplitude_relativa`)**. As ações foram classificadas como:
    - **Conservador:** `amplitude_relativa` no terço inferior.
    - **Moderado:** `amplitude_relativa` no terço médio.
    - **Agressivo:** `amplitude_relativa` no terço superior.
    

**Correlações relevantes entre variáveis:**

- **Preços (`open`, `close`, `high`, `low`, `adj_close`):** Apresentam alta correlação (r > 0.98), indicando consistência dos dados.
- **Volume:** Possui baixa correlação com os preços e a variação percentual, sugerindo que não é um preditor direto forte de tendências de preços.

**Análise temporal:**

- A análise examinou tendências por mês e ano, e rentabilidade mensal acumulada. A validação estatística confirmou a existência de sazonalidade na variação percentual.

### Principais Recomendações

- **Alocação de Capital:** Considerar alocar capital em ações com melhor histórico de rentabilidade (Netflix, Amazon), monitorando a volatilidade (absoluta e relativa) e seu perfil de risco.
- **Estratégia Refinada:** Utilizar as análises por segmento (empresa, tendência, variação) e os perfis de risco (absoluto e relativo) para refinar estratégias de investimento e tomada de decisão.
- **Previsão de Preços:** Evitar usar volume como principal critério de previsão de tendência de preços. Focar em features derivadas de preço e indicadores técnicos mais sofisticados.
- **Acompanhamento Mensal e Sazonalidade:** Manter acompanhamento mensal da performance para identificar padrões sazonais e adaptar estratégias de investimento.
- **Modelos Preditivos:** Modelos de regressão linear com features defasadas podem ser úteis para estimar preços futuros.
- **Relatórios Contínuos:** Gerar relatórios recorrentes de performance por grupo de ação, com visualizações simples (gráficos de barras, linha, mapa de calor) para facilitar decisões e acompanhar a evolução do perfil de risco.
  
</details>
