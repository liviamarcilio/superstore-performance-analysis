# 📊 Ficha Técnica — Análise de Performance SuperStore

> Projeto 2 · Rota 2 · Laboratoria
> Análise de dados com Python (Google Colab) e Agente de IA (Gem)

---

## 🎯 Sobre este projeto

Este repositório documenta minha primeira análise de dados de ponta a ponta: da pergunta de negócio ao insight acionável, usando Python para tratamento de dados e um Agente de IA como parceiro analítico.

**Autora:** Lívia Marcilio Lopes
**Ferramentas:** Python (Google Colab), Gemini (Gem), Claude (dashboard executivo e apoio à análise)
**Status:** Concluído

---

## 0. Contexto de negócio (antes dos dados)

> Meta 0 do projeto: entender o problema real antes de escrever qualquer linha de código.

### O problema central da SuperStore — na minha visão (analista)

A SuperStore vem tomando decisões de estoque, expansão e desconto baseadas na intuição e na falta de visibilidade de dados, o que gera resultados operacionais inconsistentes entre regiões. A empresa aplica descontos comerciais de forma desalinhada apenas para inflar o volume de vendas, e assume custos de frete sem avaliar se as entregas estão corroendo a margem de lucro das operações.

### Stakeholders — na minha visão (analista)

- **Sofia Martins** (Diretora de Operações): quer entender por que o desempenho varia tanto entre regiões e ter uma base de dados sólida para orientar planejamento de estoque, expansão e decisões estratégicas, eliminando a dependência do "achismo".
- **Diego Ramírez** (Gerente Regional de Vendas): quer saber se a concessão de descontos no final do mês traz lucro real ou apenas aumenta o volume de pedidos, além de compreender como diferentes segmentos de clientes (Consumidor Final, Empresas e Home Office) e localizações reagem a essas ofertas.
- **Ana Ferreira** (Head de Logística): quer identificar quais modalidades de envio (como o frete expresso) estão anulando a margem de lucro dos produtos, mapeando combinações críticas entre tipo de produto, forma de entrega, região e perfil de cliente.

### O problema central da SuperStore — na visão da IA (Claude)

A diretoria está tomando decisões de estoque e expansão no "achismo" porque falta uma base de dados que explique por que os resultados operacionais variam tanto entre regiões e categorias. Não é um problema de dado ruim, é um problema de visibilidade cruzada: cada área (comercial, logística, geografia) enxerga sua própria parte do quebra-cabeça, mas ninguém está cruzando as três. Reduzindo à pergunta de negócio central: quais combinações de desconto, custo logístico, categoria de produto, segmento de cliente e localização estão corroendo a margem de lucro — de forma sistemática, sem que a diretoria tenha percebido?

### Stakeholders — na visão da IA (Claude)

| Stakeholder | Papel | O que quer saber com os dados |
|---|---|---|
| Sofia Martins — Diretora de Operações | Patrocinadora da análise; decide estoque e expansão | Se as decisões de estoque/expansão podem parar de ser no "achismo" — quer uma base sólida que sustente essas escolhas |
| Diego Ramírez — Gerente Regional de Vendas | Aplica desconto para bater meta comercial | Se o desconto aplicado no fechamento do mês está de fato aumentando o lucro ou só o volume de pedidos; pediu explicitamente "chega de decisão só por intuição" — quer recomendações com números |
| Ana Ferreira — Head de Logística | Responsável pelo custo e modo de envio | Onde o custo de envio está corroendo a margem, e se isso se concentra em categorias específicas (produtos "mais pesados ou volumosos") |

### Comparação com a resposta da IA

As duas versões coincidem na essência: o problema é decisão sem base de dados (achismo), com desconto comercial e custo logístico como as duas causas concretas, e segmento/localização como variáveis explicativas adicionais. Os três stakeholders e seus interesses também bateram quase ponto a ponto — nenhuma das duas versões atribuiu a alguém uma preocupação que a pessoa não expressou na reunião.

O que divergiu foi mais de ênfase do que de conteúdo: a IA organizou o problema em três "frentes" separadas (desconto, logística, segmento/geografia) e citou uma fala literal de Diego ("chega de decisão só por intuição") para reforçar a exigência de recomendações com números; a versão da analista manteve os três pontos integrados em um só parágrafo e deu mais peso à reação dos segmentos de cliente ao desconto como parte do interesse do Diego. Não identifiquei alucinação: revisei a transcrição e todas as afirmações de ambas as versões — inclusive a citação usada pela IA — têm respaldo direto na fala de algum dos três participantes.

### Perguntas de negócio que vão guiar a análise (revisitar na Meta 8)

1. O desconto aplicado no fim do mês está de fato aumentando o lucro, ou apenas o volume de vendas — e isso varia por segmento de cliente?
2. Quais combinações de modo de envio e categoria de produto apresentam maior custo logístico relativo à margem, corroendo o lucro?
3. Segmento de cliente e localização (país/cidade) explicam parte das inconsistências de rentabilidade entre regiões?

---

## 1. Objetivo da análise

**Pergunta de negócio central:**
> Quais combinações de desconto, custo logístico, categoria de produto, segmento de cliente e localização estão corroendo a margem de lucro da SuperStore de forma sistemática, sem que a diretoria tenha percebido?

**Por que essa pergunta importa para a SuperStore:**
Porque as três lideranças da diretoria (comercial, logística, operações) chegaram, cada uma pelo seu próprio caminho, às mesmas variáveis-chave numa reunião — o que confirma que este recorte de análise responde a uma dor real do negócio, não a uma pergunta inventada pela analista.

---

## 2. Contexto dos dados

| Item | Descrição |
|---|---|
| Base(s) utilizada(s) | `order`, `shipping` (opcional) |
| Período coberto | jan/2023 a dez/2024 (24 meses) |
| Nº de linhas (antes da limpeza) | `order`: 19.960 · `shipping`: 19.960 |
| Nº de linhas (após a limpeza) | `df_final` (pós-merge): 19.955 linhas — igual a `order` limpo, sem multiplicação nem perda |
| Principais variáveis analisadas | `category`, `segment`, `discount`, `profit`, `Sales`, `quantity`, `order_priority`, `ship_mode`, `shipping_cost`, `country`, `city`, `order_date` |

---

## 3. Processo técnico

### 3.0 Convenção de comentários no código

O notebook não comenta sintaxe básica de Python/pandas — parto do princípio de que quem lê já tem conhecimento prévio de análise de dados. Um comentário só aparece quando carrega uma decisão relevante: por que um dado foi tratado de um jeito e não de outro, ou o que uma escolha técnica significa para a leitura de negócio (ex: por que `order_id` repetido não é erro, ou por que um tipo de dado veio diferente do esperado no `.info()`).

### 3.1 Limpeza e tratamento de dados

Documente aqui **o quê** você fez e, principalmente, **por quê** — cada decisão de limpeza é uma escolha analítica, não um passo mecânico.

- **Valores nulos:** `shipping` não tem nenhum nulo. `order` tem 22 linhas (0,11% da base) com `Sales` vazio — nas 22, `profit` e `quantity` vieram preenchidos normalmente, e a distribuição por categoria (15 Office Supplies, 4 Furniture, 3 Technology) segue a proporção geral da base, então trato como falha pontual de registro, não como problema estrutural de uma categoria. Decisão: manter as linhas (descartá-las jogaria fora profit/quantity válidos e enviesaria a análise de lucratividade); não imputar `Sales` via média/mediana, porque não há como confirmar que o valor inventado refletiria a venda real; nas análises que dependem diretamente de `Sales`, excluir só essas 22 linhas no cálculo específico, não da base inteira.
- **Valores duplicados:** 16 pares de chave (`order_id` + `product_id`) repetidos em `order`. Divididos em dois grupos bem diferentes: 11 pares com valores diferentes de profit/Sales/quantity — pedidos legítimos com o mesmo produto lançado em linhas separadas, mantidos sem alteração; 5 pares 100% idênticos em todas as 15 colunas — estatisticamente improvável como coincidência legítima, tratados como erro de registro (possível lançamento duplicado no sistema) e removidos com `drop_duplicates(keep='first')`. Achado cruzado relevante: as mesmas 16 chaves também se repetem em `shipping`, mas nenhuma das 5 chaves "erro" tem linha de shipping idêntica entre si — ou seja, o pedido pode ter sido lançado duas vezes, mas os dois envios físicos registrados são reais e distintos (custo/data diferentes). Isso significa que o problema está na camada de pedido, não na de logística — e cria uma decisão em aberto para a Meta 6 (merge): como tratar os dois registros de shipping que ainda restam ligados a uma chave que em `order` agora só tem uma linha.
- **Valores atípicos (variáveis categóricas):** `category`, `segment`, `order_priority` (order) e `ship_mode` (shipping) já vieram limpos — sem variação de acento, caixa ou espaço; apliquei `.strip()` em todas mesmo assim, por segurança. `city` tem 2.592 valores únicos (não auditável manualmente). Em `country` (143 valores), encontrei `BR` e `MX` destoando do padrão de nome completo, em 16 linhas cujas cidades não batiam com Brasil/México na maioria dos casos. Primeira decisão foi converter para nulo em vez de adivinhar — mas isso foi revisado: usei a base geográfica `geonamescache` para checar, cidade a cidade, se havia correspondência inequívoca de país. 7 das 8 cidades tinham exatamente um país correspondente (evidência sólida), então corrigi 15 das 16 linhas com o país real (ex: Torreón → Mexico, Bayamo → Cuba, Chinandega → Nicaragua). A exceção é `Santa Ana`, que existe em pelo menos 6 países diferentes sem nenhum outro dado para desempatar — essa permanece nula, porque uma correspondência ambígua não é evidência suficiente para afirmar um país como fato.
- **Valores atípicos (variáveis numéricas):** o IQR sozinho marcou até 19,3% de `profit` como atípico — grande demais para ser erro de digitação, sinal de distribuição naturalmente assimétrica. Confirmei que não há valores impossíveis (`discount` sempre em [0,1], `quantity`/`Sales` sempre positivos, `shipping_cost` nunca negativo) e que os extremos batem com um padrão real: as 5 maiores perdas têm desconto entre 0,60-0,85; os 5 maiores lucros têm desconto zero. Correlação `profit` x `discount` = **-0,34**; lucro médio cai de +61 (sem desconto) para -103 (desconto acima de 60%), ficando negativo já na faixa de 20-40%. Decisão: mantive todos os outliers de `profit`, `Sales`, `discount`, `quantity` e `shipping_cost` sem remoção — removê-los pelo critério IQR esconderia justamente a relação desconto-lucro que responde à pergunta de negócio 1 (Meta 0).
- **Junção das tabelas (merge):** um merge direto (`on=['order_id','product_id'], how='left'`) multiplicou 27 linhas a mais do que `df_order` tem — exatamente porque as duas tabelas compartilham as mesmas 16 chaves duplicadas (pendência aberta desde a Meta 3). Em vez de assumir uma correspondência linha-a-linha não verificável entre as duplicatas de `order` e `shipping`, agreguei `shipping` por chave antes do merge: `shipping_cost` somado (múltiplas remessas reais do mesmo item = custo total real), `ship_mode` e `ship_date` (mais antiga) como primeira ocorrência. Resultado: `df_final` tem exatamente as 19.955 linhas de `df_order`, sem multiplicação e sem perda (0 linhas sem correspondência em shipping). Limitação assumida conscientemente: nas 11 chaves com 2 linhas legítimas em `order`, ambas herdam o mesmo `shipping_cost` agregado — não há como dividir o custo entre elas com os dados disponíveis.

### 3.2 Uso do Agente de IA (Gem)

> A instrução completa do Gem está em [`/agente/instrucao_gem.md`](./agente/instrucao_gem.md)

**Papel definido para o Agente:** consultor / parceiro de raciocínio — nunca decisor autônomo. Nome do Gem: **Consultor de Dados SuperStore**.

**Como estruturei a instrução (Persona + Contexto + Tarefas + Restrições):**
- **Persona:** Analista de Dados Sênior especializado em varejo global, atuando como parceiro de raciocínio — nunca decide sozinho.
- **Contexto fixado:** as 3 perguntas de negócio da Meta 0, a estrutura de colunas de `df_final`, e as decisões de limpeza já tomadas (duplicatas removidas, nulos de país recuperados por evidência, outliers mantidos, merge com shipping agregado) — assim não preciso reexplicar isso a cada pergunta.
- **Tarefas:** explorar dados que eu compartilhar, responder as perguntas de negócio, sugerir visualizações, interpretar tabelas/gráficos, apontar padrões e limitações, apoiar a Ficha Técnica.
- **Restrições:** nunca inventar resultado sem eu ter colado o dado; sempre pedir a tabela antes de interpretar; diferenciar observação de interpretação de recomendação; apontar limitações; atuar como "advogado do diabo" quando pedido; linguagem sem jargão técnico excessivo (formato para reunião de diretoria).
- **Formato de saída fixado:** (1) o que os dados mostram, (2) como interpretar, (3) possível conclusão, (4) limitações, (5) recomendação, (6) pergunta crítica para reflexão.

**Teste de precisão do Agente — resultado real:** colei o achado da Meta 5 (correlação profit x discount = -0,34 e a tabela de lucro médio por faixa de desconto) e avaliei a resposta do Gem pelo checklist da Meta 0:

| Critério | Resultado |
|---|---|
| Está correto? | ✅ Leu certo o sinal e a magnitude, sem inverter nada |
| Está completo? | ✅ Cobriu os 6 pontos do formato, na ordem definida |
| Tem evidência? | ✅ Não inventou `quantity`/`segment` — disse explicitamente que a métrica não estava no trecho, em vez de estimar |
| Aponta causalidade reversa (sem eu pedir)? | ✅ Levantou sozinho que desconto de 60-90% pode ser liquidação de estoque parado, "onde o prejuízo já seria inevitável" |
| Eu assumiria a autoria da recomendação? | ✅ Separou recomendação em passo analítico interno vs. proposta à diretoria "após validação" — não pulou direto pra ação de negócio |

**Gem aprovado no teste**, com uma inconsistência menor a refinar: a seção "Possível conclusão" afirmou de forma categórica que a política "destrói o lucro", enquanto a seção de Limitações (logo em seguida) reconheceu que causalidade não está confirmada — as duas deveriam concordar em nível de confiança desde a conclusão, não só a limitação corrigindo depois. Ajuste de instrução sugerido: pedir que a linguagem da conclusão já venha com a mesma cautela da seção de limitações.

**Validação adicional (Meta 8, pergunta de Lucratividade):** apresentei ao Gem um dado que contradizia a recomendação inicial dele (Standard Class também dá prejuízo em Furniture, não só os modais expressos) — ele revisou a própria hipótese em vez de defender a conclusão anterior, migrando de "restringir modal expresso" para "taxa estrutural de porte/volumetria". Esse é o comportamento que a Meta 0 pedia (não aceitar a primeira resposta como definitiva). O mesmo padrão de inconsistência reapareceu, num grau menor: ele afirma "a causa raiz é a natureza física dos produtos" na seção de interpretação, e só na seção de limitações admite que a base não tem peso/volume para comprovar isso — vale pedir que o hedge apareça já na interpretação, não só na limitação.

**⚠️ Caso real de alucinação detectado (Meta 8, pergunta própria — fim de mês):** ao testar a afirmação do Diego sobre desconto se concentrar no fim do mês, o Gem apresentou um teste adicional com corte no dia ≥ 28 — dado que eu nunca forneci. A média reportada (13,9%) coincidiu com a realidade, mas o p-valor apresentado (0,2035) não bateu com o cálculo real (0,1926). Pior: quando questionado, o Gem justificou a *escolha* do corte ("últimos 3 dias do mês") sem isso resolver o problema — justificar por que testar algo não é o mesmo que ter calculado o resultado desse teste.

**Padrão identificado ao cruzar com a validação de Geografia:** na pergunta de Geografia, o Gem trouxe por conta própria os descontos médios de 6 países/cidades (Turquia, Filipinas, Rep. Dominicana, EUA, Filadélfia, Houston) — dado que também não foi fornecido — e **todos bateram com exatidão**, inclusive valores nada óbvios como 32,0% e 36,2%. A explicação mais provável é que o Gem consultou a base de conhecimento anexada (`superstore_final.csv`) para calcular essas médias simples. Isso revela um padrão mais arriscado do que "o Agente inventa": **ele parece calcular de verdade agregações simples (média, contagem) a partir da base, mas estima — sem calcular de fato — resultados estatísticos mais complexos (teste t, p-valor)**, apresentando os dois tipos de resposta com o mesmo nível de confiança na escrita. Não é possível diferenciar um do outro só pela leitura da resposta — por isso o checklist da Meta 0 precisa ser aplicado em toda resposta numérica do Agente, não só nas que "parecem" suspeitas.

**✅ Validação limpa (Meta 8, pergunta de Segmentação):** todos os números conferidos batem com precisão (desconto, lucro, faturamento e a curva completa de lucro por faixa de desconto x segmento, incluindo os extremos -$38,38 a -$111,87). Sem inconsistências entre conclusão e limitações. Melhor resposta do conjunto: em vez de só confirmar "segmento não explica nada", o Gem redirecionou o critério de segmentação (de tipo de cliente para ticket mínimo/categoria) conectando esse achado aos resultados de Lucratividade e Furniture já obtidos — sinal de que o contexto fixado na instrução está funcionando ao longo de várias perguntas, não só isoladamente.

**Exemplo de prompt com contexto (antes → depois da iteração):**
```
Prompt solto: "Analise o lucro da SuperStore."

Prompt com contexto: "Aqui está o lucro médio por categoria [colar groupby].
Qual categoria está performando abaixo do esperado e por quê?"

Refinamento: "Isso ficou muito técnico. Reescreva focando no impacto
para a decisão da diretoria sobre onde investir no próximo trimestre."
```

**Checklist de validação aplicada às respostas do Agente:**
- [ ] Está correto? (confere com o DataFrame)
- [ ] Está completo?
- [ ] Tem evidência verificável?
- [ ] Faz sentido para o contexto real da SuperStore?
- [ ] Eu assumiria a autoria dessa conclusão?

---

## 4. Principais resultados

<!-- Traga números concretos, não apenas descrições. Cada resultado deve responder
a um pedaço da pergunta de negócio da seção 1. -->

| Achado | Evidência (dado/gráfico) |
|---|---|
| **O lucro líquido (após frete) fica próximo de zero ou negativo na maioria dos 24 meses analisados** — não é um problema pontual de dezembro nem de uma categoria isolada, é uma condição estrutural do negócio inteiro. O lucro bruto sozinho (métrica hoje usada) esconde isso completamente | Notebook e Gráfico 1, Meta 9 — revisado após feedback crítico do Gem |
| Desconto alto está associado a prejuízo, não a mais lucro: correlação profit x discount = -0,34; lucro médio cai de +61 (sem desconto) para -103 (desconto acima de 60%), ficando negativo já entre 20-40% de desconto | Notebook, Meta 5 — investigação de outliers numéricos |
| O custo de frete consome proporcionalmente mais o lucro em Furniture e Technology com Same Day/First Class (mediana de 0,66-0,70x o lucro) do que em Standard Class (0,28-0,34x) — indício direto de que modo de envio expresso está corroendo a margem, como a Ana suspeitava na reunião | Notebook, Meta 6 — prévia pós-merge (`custo_frete_pct_lucro`) |
| A coluna `profit` não desconta o frete real: das 12 combinações categoria x modo de envio, todas são positivas olhando só `profit`, mas 8 viram prejuízo ao subtrair `shipping_cost`. Em `Furniture`, o prejuízo é universal — **os 4 modos de envio dão saldo líquido negativo, inclusive Standard Class** (o mais barato e de maior volume, 2.353 pedidos), o que descarta "problema de modal expresso" como explicação isolada | Notebook, Meta 8 — Pergunta 1 (Lucratividade), validado com o Gem em 2 rodadas |
| A hipótese do Diego (segmentos reagem diferente a desconto) não se confirma: os 3 segmentos têm desconto médio, mix de categoria e curva de lucro por desconto praticamente idênticos (diferença de 0,6 p.p.) | Notebook, Meta 8 — Pergunta 2 (Segmentação) |
| A percepção do Diego de que o desconto se concentra no fim do mês também não se confirma: desconto médio quase igual (14,3% vs 14,5%), teste t não significativo (p=0,63) | Notebook, Meta 8 — Pergunta própria |
| 67,2% dos pedidos com `Furniture` vêm acompanhados de outra categoria no mesmo carrinho, e o lucro dessas outras categorias nesses pedidos ($140.257) é 2,3x maior que o déficit total de `Furniture` ($59.853) — teto de risco, não previsão, mas grande o bastante para pedir cautela antes de restringir `Furniture` | Notebook, Meta 8 — checagem de venda casada |
| Custo de frete triplica com a prioridade do pedido (R$18,50 Medium → R$62,58 Critical, 3,38x) enquanto `Sales` (+2,1%) e `profit` (praticamente igual) não acompanham — mecanismo explicado: pedidos Critical usam 0% Standard Class (100% First/Second Class/Same Day), contra 73,6% Standard Class em Medium. A SuperStore não cobra taxa de conveniência pelo envio prioritário | Notebook, Meta 8 — pergunta de Logística (`order_priority` x `ship_mode`) |
| 7 países vendem acima da média mas fecham no vermelho após o frete real. `Turquia` é caso à parte: `profit` já é negativo (-$40.871) **antes** do frete — problema de viabilidade do mercado, não de logística. No nível de cidade, 191 de 646 cidades acima da média em vendas ficam negativas após o frete, inclusive dentro dos EUA (país com resultado agregado positivo) | Notebook, Meta 8 — pergunta de Geografia (país e cidade) |

---

## 5. Visualizações

<!-- Insira aqui os gráficos gerados (Python/Sheets/Looker Studio), com uma legenda
de 1-2 linhas explicando o que cada um revela -->

**Gráfico 1 — Lucro bruto vs. líquido, mês a mês (2023-2024)**
`imagens/grafico_tendencia_lucro_mensal.png`
> Versão revisada após feedback crítico do Gem (a original só mostrava `profit` bruto, mascarando o problema). Com as duas linhas juntas, fica visível que o lucro **líquido** (após frete) fica próximo de zero ou negativo na maior parte dos 24 meses — não é um problema pontual de dezembro, é uma condição estrutural do negócio inteiro. Dez/2023 parecia um mês de R$40,6k de lucro; o líquido real foi de R$3.479,18. Dez/2024, com R$32,9k de bruto, fechou negativo em -R$4.687,38.

**Gráfico 2 — Lucro bruto vs. lucro líquido (após frete) por categoria**
`imagens/grafico_lucro_bruto_vs_liquido.png`
> Olhando só a barra azul (`profit`), as 3 categorias parecem saudáveis — mas a barra vermelha (depois do frete) mostra `Furniture` virando prejuízo líquido, enquanto `Technology` e `Office Supplies` seguem positivas. Decidir investimento por categoria olhando só `profit` esconde exatamente o problema mais importante da análise.

### 5.1 Rastreabilidade: evidência do dashboard x onde está no notebook

O dashboard executivo (`dashboard_superstore.html`) tem 5 evidências, uma para cada pergunta da diretoria (ver seção 0). Esta tabela existe para quem for auditar o método: de onde vem cada número que aparece no painel.

| Evidência no dashboard | Pergunta que responde | Onde está no notebook |
|---|---|---|
| 01 — Tendência mensal (lucro bruto x líquido) | Sofia — o pico de fim de ano é lucro de verdade? | Meta 9, células 89-91 |
| 02 — Lucro bruto x líquido por categoria | Ana — onde o frete corrói a margem por categoria? | Meta 8 "Pergunta 1 (Lucratividade)", células 63-65 + Meta 9, células 92-93 |
| 03 — Lucro médio por faixa de desconto | Diego — desconto de fim de mês traz lucro real ou só volume? | Meta 5, células 41-50 + Meta 8 "Pergunta própria" (fim de mês), células 69-71 |
| 04 — Frete médio por prioridade do pedido | Ana — o modo de envio prioritário compensa financeiramente? | Meta 8 "Pergunta de Logística", células 76-80 |
| 05 — Países/cidades com lucro líquido negativo | Sofia — alguma região vende bem mas não sustenta o resultado? | Meta 8 "Pergunta de Geografia", células 81-85 |

*Numeração de células referente à versão do notebook após a limpeza de células duplicadas (94 células no total). Se o notebook for editado depois, os números podem mudar — use os títulos das seções (`### Pergunta de Logística`, etc.) como referência mais estável.*

---

## 6. Insights e recomendações

<!-- A ponte entre "o que os dados mostram" e "o que a SuperStore deveria fazer" -->

| Insight | Recomendação para a SuperStore |
|---|---|
| A métrica de lucro usada hoje (`profit`) não desconta o frete real, e isso esconde que a margem líquida verdadeira é próxima de zero ou negativa na maioria dos meses — não um problema isolado de categoria ou período | Antes de qualquer ação pontual (Furniture, países, prioridade), a diretoria precisa trocar a métrica de acompanhamento mensal de `profit` bruto para lucro líquido pós-frete — é a mudança de maior alavancagem de todo o projeto |
| Desconto acima de 20% está associado a prejuízo médio crescente (correlação -0,34), na mesma proporção nos 3 segmentos de cliente | Estabelecer um teto promocional de desconto (~20%) como regra geral, não como decisão caso a caso do time comercial |
| `Furniture` tem prejuízo líquido em **todos** os modos de envio (déficit total de -$59.853), não só nos expressos — o problema é estrutural (provável peso/volumetria dos produtos), não uma questão de velocidade de entrega. Mas 67,2% dos pedidos com `Furniture` vêm acompanhados de outra categoria, com lucro conjunto 2,3x maior que o déficit — risco real de venda casada | Começar com uma **taxa de porte moderada** (não repasse integral) em `Furniture`, faseada e monitorada — checar se o carrinho médio muda antes de escalar para qualquer medida mais dura (repasse total de frete expresso, ticket mínimo). Não recomendo restrição agressiva de saída, dado o risco de arrastar vendas de `Technology`/`Office Supplies` no mesmo pedido |
| Pedidos `Critical` custam 3,38x mais frete que `Medium`, sem gerar mais `Sales` (+2,1%) ou `profit` proporcional — a SuperStore absorve o custo do envio expresso sem cobrar taxa de conveniência, e isso é mecânico (Critical usa só modos rápidos, nunca Standard Class) | Criar uma taxa de despacho prioritário para cobrir o custo extra de frete em pedidos `Critical`, protegendo a margem sem mudar o prazo de entrega prometido |
| 7 países e 191 cidades vendem acima da média mas ficam no vermelho após o frete real — dois problemas distintos: Turquia/Filipinas/Rep. Dominicana têm prejuízo já no bruto (crise de precificação, desconto de até 60%); Brasil/Indonésia/Itália/Nova Zelândia e cidades como Filadélfia/Houston (dentro de um país lucrativo, os EUA) têm produto saudável mas frete ou desconto que anula o ganho | (1) Congelar a política de desconto de 60% na Turquia e recalcular a tabela de preços; (2) taxa de frete regional ou ticket mínimo para Brasil/Indonésia/Itália/Nova Zelândia, preservando o lucro bruto já positivo; (3) aplicar o teto de 20% de desconto (já validado na Meta 5) em Filadélfia e Houston, onde a média local (32-36%) é mais que o dobro da média nacional dos EUA (15,7%) |
| A suspeita do Diego de que segmento de cliente e concentração de desconto no fim do mês explicam a inconsistência **não se confirmou** nos dados | Direcionar o esforço da diretoria para `discount` e `category`/`ship_mode` (onde a variação real está), em vez de segmentar a política comercial por tipo de cliente ou por período do mês |

---

## 7. Limitações e próximos passos

- Causa física do frete caro em `Furniture` (peso/volumetria) é hipótese, não fato — base não tem colunas de peso/volume para confirmar.
- Efeito de venda casada em `Furniture` foi quantificado como teto de risco (2,3x o déficit), não como previsão — exigiria teste controlado (aplicar taxa numa região/período e comparar) para confirmar causalidade.
- 1 linha com `country` não resolvido (`Santa Ana`, ambígua entre 6 países) — excluída de análises por país.
- Custo de frete das 11 chaves com duplicata legítima (mesmo produto, mesmo pedido) foi somado e atribuído igualmente às duas linhas — pequena imprecisão aceita e documentada (Meta 6).
- Próximo passo natural: repetir a checagem de venda casada por categoria (não só Furniture) e testar o teto de 20% de desconto com dados de um trimestre real, se a diretoria aprovar um piloto.

---

## 8. Estrutura do repositório

```
📁 superstore-performance-analysis
├── 📄 README.md                  ← esta ficha técnica
├── 📄 resumo_executivo.md        ← síntese para apresentação/dashboard executivo
├── 📁 notebook/
│   └── analise_superstore.ipynb  ← link também para o Colab
├── 📁 dados/
│   └── order.csv, shipping.csv — bases fornecidas pelo Laboratoria; não incluídas neste repositório por serem material do curso (ver notebook para reprodução dos passos)
├── 📄 dashboard_superstore.html  ← painel executivo interativo (Artifact)
├── 📁 agente/
│   └── instrucao_gem.md          ← Gem "Consultor de Dados SuperStore" (exploração/validação)
└── 📁 imagens/
    └── grafico_tendencia_lucro_mensal.png, grafico_lucro_bruto_vs_liquido.png
```

---

## 🔗 Links

- **Notebook (Google Colab):** [analise_superstore.ipynb](https://colab.research.google.com/drive/1Z1bg8PvPOFguFeYX1cqemJ6lOEyHbaD3?usp=sharing)
- **Dashboard executivo:** [`dashboard_superstore.html`](https://liviamarcilio.github.io/superstore-performance-analysis/dashboard_superstore.html) — painel interativo (Artifact), construído com o Claude a partir de um resumo executivo ([`resumo_executivo.md`](./resumo_executivo.md)) sintetizado do notebook completo — não passou por revisão de um Gem
- **Instrução do Agente de exploração (Gem):** [`/agente/instrucao_gem.md`](./agente/instrucao_gem.md) — colar no Gemini ao criar o Gem

---

## 📌 Progresso das metas

- [x] Meta 0 — Contexto de negócio
- [x] Meta 1 — Importar tabelas no Colab (`analise_superstore.ipynb` criado, executado e com dicionário de dados)
- [x] Meta 2 — Nulos
- [x] Meta 3 — Duplicados
- [x] Meta 4 — Valores atípicos (categóricas)
- [x] Meta 5 — Valores atípicos (numéricas)
- [x] Meta 6 — Merge das tabelas
- [x] Meta 7 — Criar Agente (Gem)
- [x] Meta 8 — Perguntas de negócio com o Agente (Lucratividade, Segmentação, Logística, Geografia + pergunta própria)
- [x] Meta 9 — Visualização e interpretação com gráficos
- [x] Meta 10 — Apresentar resultados (dashboard executivo entregue como Artifact)

---

*Projeto desenvolvido como parte da Rota 2 do [Laboratoria](https://www.laboratoria.la/).*
