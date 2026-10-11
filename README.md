# Demonstrativo Financeiro de uma Operação de E-commerce

## Visão Geral

Painel desenvolvido em Power BI para acompanhar o desempenho financeiro de uma operação de e-commerce global, com base no dataset TheLook E-commerce, do Google BigQuery. Em uma única página, o painel reúne receita, lucro e margem ao longo do tempo, o lucro por país e o detalhamento financeiro por categoria de produto.

A visão foi pensada para um perfil executivo de CFO: entender a saúde financeira da empresa e identificar se há espaço para crescer. Os dados cobrem o período de 2019 a 2026 e, com os filtros em "Todos", os valores exibidos são o acumulado desse período. Os valores monetários seguem o formato exibido no painel, em reais.

**Pergunta central:** Qual é a saúde financeira da operação e existem sinais de oportunidade de crescimento?

## Abordagem

Para responder à pergunta central, o painel foi organizado em frentes complementares:

- Indicadores principais em cards: receita e lucro, com comparativo contra o mês anterior e o ano anterior
- Evolução mensal da margem de lucro, com linha de média e destaque para os meses abaixo dela
- Evolução mensal da receita, com linha de média e destaque para os meses abaixo dela
- Lucro por país, com destaque para os países cujo lucro ficou abaixo do ano anterior
- Visão detalhada por categoria, com receita, participação na receita, lucro bruto, margem de lucro, ticket médio, variação anual YoY e receita perdida, e com alertas visuais quando a receita fica abaixo do valor perdido ou quando o YoY cai em relação ao ano anterior
- Filtros por Ano, Mês, Semestre, País e Categoria

## Preparação dos dados

Os dados utilizados vêm do dataset TheLook E-commerce, no Google BigQuery. No BigQuery, as informações foram organizadas em tabelas de fatos com prefixo `ft_` e views de dimensão com prefixo `vw_`, que alimentam o painel no Power BI.

### Modelo de dados

O modelo segue a estrutura de fatos e dimensões, com relações de um para muitos:

- **Fatos:** `ft_venda`, com os pedidos, e `ft_ordem_items`, com os itens dos pedidos
- **Dimensões:** `vw_usuario`, com os clientes, `vw_produto`, com os produtos, e `calendario`, com as datas
- **Tabela de medidas:** `Medida`, que concentra os cálculos do painel

Relações do modelo:

- `vw_usuario` com `ft_venda` e `ft_ordem_items`
- `vw_produto` com `ft_ordem_items`
- `calendario` com `ft_venda` e `ft_ordem_items`
- Uma relação adicional inativa entre `calendario` e `ft_ordem_items`

A tabela `calendario` foi criada no Power BI com a função `CALENDAR()`, começando em 1º de janeiro do primeiro ano dos dados e terminando na última data de criação dos pedidos.

`![Modelo de dados](imagens/modelo-de-dados.png)`

### Medidas

Todas as medidas são calculadas sobre `ft_ordem_items` e concentradas na tabela `Medida`:

- **Receita:** soma dos itens de pedidos com status "Complete"
- **% Receita:** participação de cada categoria na receita total
- **Lucro Bruto:** receita menos o custo do produto
- **Margem Lucro:** lucro bruto dividido pela receita
- **Ticket Médio:** receita dividida pelo número de pedidos
- **YoY:** variação em relação ao ano anterior
- **Receita perdida:** soma dos itens de pedidos cancelados e devolvidos

### Comportamento dos comparativos

Os comparativos seguem os filtros aplicados: com apenas o Ano selecionado, o painel compara com o ano anterior. Com Ano e Mês, compara também com o mês anterior. Com apenas o Mês, sem Ano, o comparativo fica em 0% de propósito, pois não há um período de referência definido.

## O Painel

O painel é composto por uma única página, com filtros de Ano, Mês, Semestre, País e Categoria que atualizam todos os visuais ao mesmo tempo.

`![Visão geral do painel](imagens/painel-completo.png)`

### Como ler o painel

A cor vinho sinaliza pontos de atenção:

- **Receita e Lucro nos cards:** resultado menor que o do ano anterior ou do mês anterior
- **Margem de lucro e receita por mês:** meses abaixo da média do período
- **Lucro por localidade:** países com lucro menor que o do ano anterior
- **Receita perdida:** categorias em que a receita é menor que o valor perdido
- **YoY:** categorias com resultado menor que o do ano anterior

### 1. Indicadores principais

Os cards de Receita e Lucro mostram o resultado total do período selecionado, com o comparativo contra o mês anterior e contra o ano anterior. O comparativo com o ano anterior aparece quando um Ano é selecionado. O comparativo com o mês anterior só aparece quando Ano e Mês estão selecionados, o que foi definido de propósito para evitar ambiguidade sobre qual período está sendo comparado.

`![Indicadores principais](imagens/indicadores-principais.png)`

### 2. Margem de lucro por mês

O gráfico acompanha a margem de lucro mês a mês e permite identificar em quais meses a rentabilidade ficou acima ou abaixo da média do período, representada pela linha tracejada.

`![Margem de lucro por mês](imagens/margem-por-mes.png)`

### 3. Receita por mês

A evolução mensal da receita mostra a distribuição da receita ao longo do ano e destaca os meses com desempenho abaixo da média.

`![Receita por mês](imagens/receita-por-mes.png)`

### 4. Lucro por localidade

O ranking de países por lucro mostra onde a operação gera mais resultado e quais mercados tiveram queda de lucro em relação ao ano anterior.

`![Lucro por localidade](imagens/lucro-por-localidade.png)`

### 5. Visão detalhada por categoria

A tabela reúne, por categoria de produto, Receita, % Receita, Lucro Bruto, Margem Lucro, Ticket Médio, YoY e Receita perdida, com total ao final.

`![Visão detalhada](imagens/visao-detalhada.png)`

## Principais Insights do Painel

### Saúde financeira

**1. Rentabilidade sólida e estável**

No acumulado de 2019 a 2026, a operação gerou R$ 1,41 Mi de lucro sobre R$ 2,71 Mi de receita, uma margem de 51,95%. Mês a mês, a margem varia apenas entre 51,58% em setembro e 52,36% em março, menos de 1 ponto percentual de diferença. A rentabilidade é consistente ao longo do ano.

**2. Receita irregular ao longo dos meses**

A receita acumulada por mês, somando cada mês de 2019 a 2026, varia de R$ 0,16 Mi em setembro a R$ 0,35 Mi em agosto, e sete dos doze meses ficam abaixo da média mensal. Como a margem se mantém estável, o lucro acompanha a receita, e o resultado do ano depende dos meses de maior volume.

**3. Lucro concentrado em três mercados**

China, com R$ 0,48 Mi, Estados Unidos, com R$ 0,31 Mi, e Brasil, com R$ 0,20 Mi, somam cerca de R$ 0,99 Mi, aproximadamente 70% do lucro total. Só a China responde por cerca de um terço. Na sequência vêm cinco mercados de porte semelhante: Coreia do Sul, Reino Unido, França, Alemanha e Espanha, que somam cerca de 23%. O gráfico sinaliza em vinho os países cujo lucro ficou abaixo do ano anterior, o que permite, com um Ano selecionado, identificar quais mercados perderam força. Como o filtro de País atualiza todos os visuais, também é possível analisar receita, margem e receita perdida de cada mercado individualmente.

**4. Receita perdida equivale a quase toda a receita realizada**

A receita perdida, formada por pedidos cancelados e devolvidos, soma R$ 2,68 Mi, cerca de 99% da receita realizada de R$ 2,71 Mi. Em 15 das 26 categorias, o valor perdido supera a receita, e a tabela sinaliza esses casos em vinho. Em valor absoluto, a perda se concentra em cinco categorias: Outerwear & Coats, com R$ 321,2 mil, Jeans, com R$ 314,6 mil, Sweaters, com R$ 208,0 mil, Swim, com R$ 162,3 mil, e Fashion Hoodies & Sweatshirts, com R$ 162,1 mil, que juntas somam cerca de 44% do valor perdido. Em Jeans, o valor perdido é praticamente igual à receita, R$ 314,6 mil contra R$ 315,5 mil.

### Oportunidades de crescimento

**5. Mais da metade da receita vem de categorias com margem abaixo da média**

Das 26 categorias, 15 têm margem abaixo da média da operação, de 51,95%, e respondem por cerca de 56% da receita. Jeans, a 2ª maior receita, com 11,62% do total, tem margem de 46,47%, a 7ª menor da tabela. Já as cinco categorias de maior margem, entre 59,84% e 62,13%, representam apenas 14,61% da receita. A diferença entre a menor margem, de Clothing Sets com 38,00%, e a maior, de Blazers & Jackets com 62,13%, é de mais de 24 pontos percentuais. Aumentar o peso das categorias de margem alta no mix de vendas é uma alavanca de lucro sem depender de mais volume.

**6. Suits & Sport Coats é a categoria de referência**

É a única categoria que combina alta receita e alta margem: a 4ª maior receita, com 6,46% do total, e a 4ª maior margem, com 59,92%. Seu ticket médio de R$ 138,87 é cerca de 59% superior à média da operação, de R$ 87,33. É um bom ponto de partida para priorizar investimento.

## Recomendações

As recomendações priorizam ações de baixo esforço e com maior potencial de retorno.

### Mix de categorias

Sem reestruturar o catálogo, recomenda-se dar mais destaque em vitrine, campanhas e e-mails às cinco categorias de maior margem: Blazers & Jackets, Skirts, Accessories, Suits & Sport Coats e Socks & Hosiery. Suits & Sport Coats é a melhor referência, porque une receita alta, margem alta e ticket médio elevado. Recomenda-se também revisar o preço de Jeans, a 2ª maior receita, que opera com margem abaixo da média. Levar a margem de Jeans até a média da operação representaria cerca de R$ 17 mil de lucro sobre a receita atual.

### Meses de menor receita

O painel mostra sete meses abaixo da média, e setembro é o mais fraco. Recomenda-se concentrar campanhas e promoções nesses meses, em vez de distribuir o esforço comercial de forma uniforme ao longo do ano. Levar setembro ao nível da média mensal acrescentaria cerca de R$ 0,07 Mi em receita.

### Mercados

Como o lucro está concentrado em três países, recomenda-se testar uma ação de crescimento em um único mercado intermediário, como a Coreia do Sul, que lidera esse grupo, e ampliar para os demais apenas se houver resultado. Assim a dependência diminui sem espalhar o investimento por todos os mercados de uma vez.

### Receita perdida

Cinco categorias concentram cerca de 44% da receita perdida: Outerwear & Coats, Jeans, Sweaters, Swim e Fashion Hoodies & Sweatshirts. Recomenda-se começar por elas, em vez de tratar as 26 categorias ao mesmo tempo. Com a logística e o atendimento, levantar os motivos de cancelamento e devolução nessas cinco categorias e testar duas melhorias de baixo custo: revisar descrição, fotos e tabela de medidas dos produtos, e conferir prazo de entrega e disponibilidade de estoque. Como referência de meta, cada 10% de redução da receita perdida representa cerca de R$ 268 mil em receita e, mantida a margem de 51,95%, cerca de R$ 139 mil em lucro. Esses valores são estimativas com base nos números do painel, e não previsões.

## Conclusão

Respondendo à pergunta central: a operação tem uma saúde financeira sólida, com pontos de atenção relevantes e oportunidades claras de crescimento, principalmente na redução da receita perdida e na composição das vendas.

A rentabilidade é o ponto forte. No acumulado de 2019 a 2026, com R$ 1,41 Mi de lucro sobre R$ 2,71 Mi de receita, a margem de 51,95% se mantém praticamente constante ao longo do ano, variando menos de 1 ponto percentual entre os meses. Isso significa que o lucro acompanha o volume de receita, e a receita é irregular, variando de R$ 0,16 Mi em setembro a R$ 0,35 Mi em agosto, com sete meses abaixo da média.

A maior oportunidade está na receita perdida. Pedidos cancelados e devolvidos somam R$ 2,68 Mi, cerca de 99% da receita realizada, e cinco categorias concentram cerca de 44% desse valor. Reduzir essa perda é a alavanca de maior potencial, já que cada 10% recuperados representam cerca de R$ 139 mil de lucro, em uma estimativa feita com a margem média da operação.

O mix de categorias funciona como ajuste complementar. Cerca de 56% da receita vem de categorias com margem abaixo da média, enquanto as cinco de maior margem representam apenas 14,61%, e Suits & Sport Coats se destaca como referência ao unir alta receita, alta margem e ticket médio elevado.

Há ainda um ponto de atenção: cerca de 70% do lucro está concentrado em três países, China, Estados Unidos e Brasil.

O próximo passo natural é acompanhar a receita perdida, a Margem Lucro por categoria e a receita dos meses abaixo da média, usando os comparativos do painel para validar se as ações estão surtindo efeito.
