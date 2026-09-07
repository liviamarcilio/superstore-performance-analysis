# Resumo Executivo — Análise de Performance SuperStore

## Frase única (a mensagem, não o assunto)

> **A margem líquida real da SuperStore é quase zero ou negativa na maioria dos meses — o lucro
> bruto que a empresa acompanha hoje esconde isso. Trocar a métrica de acompanhamento para lucro
> líquido pós-frete é a mudança de maior impacto que a diretoria pode aprovar agora.**

## Brief de 5 linhas

1. **Audiência:** Diretoria da SuperStore — Sofia Martins (Operações), Diego Ramírez (Comercial), Ana Ferreira (Logística). Perfil executivo: pouco tempo, quer decisão antes de prova.
2. **Decisão:** Aprovar a troca de métrica de acompanhamento (bruto → líquido pós-frete) e as 3 ações específicas abaixo.
3. **Prazo:** Próxima reunião de diretoria.
4. **Mensagem:** A frase única acima.
5. **Prova:** os 3 números e 2 gráficos da seção "KPIs e evidências" abaixo — nada além disso entra no painel principal.

## Perguntas da diretoria e onde cada uma é respondida

| Quem perguntou | Pergunta | Resposta | Evidência |
|---|---|---|---|
| Diego | Desconto de fim de mês traz lucro real ou só volume? | Acima de 20% de desconto, o lucro fica negativo (correlação -0,34), igual nos 3 segmentos. A concentração "no fim do mês" especificamente não se confirmou — o nível de desconto é praticamente igual em qualquer dia do mês | Gráfico 3 (dispersão desconto x lucro) |
| Ana | Onde o frete corrói a margem? | `Furniture` tem prejuízo líquido em todos os modos de envio, inclusive Standard Class — problema estrutural, não de velocidade | Gráfico 2 (bruto x líquido por categoria) |
| Ana | Modo de envio impacta prioridade e lucro? | Pedidos `Critical` custam 3,38x mais frete que `Medium` (R$62,58 vs R$18,50) sem gerar mais receita (+2,1%) nem lucro proporcional — mecânico: Critical usa 0% Standard Class | Gráfico 4 (frete médio por prioridade) |
| Sofia | Segmento de cliente explica a inconsistência? | Não — os 3 segmentos têm desconto médio, mix de categoria e curva de lucro por desconto praticamente idênticos | Nota de rodapé (testado, não confirmado) |
| Sofia | Localização (país/cidade) explica a inconsistência? | Sim, dois padrões: Turquia/Filipinas/Rep. Dominicana já operam no vermelho antes do frete (desconto excessivo); Brasil/Indonésia/Itália/Nova Zelândia e cidades como Filadélfia/Houston têm produto saudável mas frete ou desconto que anula o ganho | Gráfico 5 (tabela: top países/cidades com lucro líquido negativo) |

## KPIs de topo (painel — 4 números, cada um com unidade)

| KPI | Valor | Contexto |
|---|---|---|
| Receita total (Sales) | R$4.932.742 | jan/2023 a dez/2024, 19.955 pedidos |
| Lucro bruto total (profit) | R$556.253,57 | métrica hoje usada pela empresa |
| **Lucro líquido total (profit - frete)** | **R$27.861,86** | 95% menor que o bruto — é essa métrica que falta |
| Margem líquida | 0,56% | contra 11,28% de margem bruta aparente |

## As evidências que sustentam a mensagem e respondem às perguntas da diretoria

1. **Gráfico 1 — tendência (bruto x líquido, mensal, 2023-2024):** a distância entre as duas linhas cresce exatamente nos meses de pico de vendas — dez/2023 pareceu R$40,6k de lucro, o líquido real foi R$3.479,18; dez/2024 fechou negativo em -R$4.687,38.
2. **Gráfico 2 — comparação por categoria (bruto x líquido):** `Furniture` vira prejuízo líquido (-R$59.853); `Office Supplies` e `Technology` ficam positivas, mas o frete consome 82% e 79% do lucro bruto delas, respectivamente.
3. **Gráfico 3 — dispersão desconto x lucro por pedido:** visualiza a correlação -0,34; lucro médio cai de +R$61 (sem desconto) para -R$103 (desconto acima de 60%), cruzando o zero perto de 20%.
4. **Gráfico 4 — frete médio por prioridade do pedido:** R$18,50 (Medium) → R$32,53 (High) → R$62,58 (Critical), sem ganho de receita proporcional; nota lateral explicando que Critical usa 0% Standard Class (custo é mecânico, não "cego").
5. **Gráfico 5 — tabela: países/cidades com lucro líquido negativo apesar de vendas acima da média:** Turquia (-R$45.913, já negativo antes do frete), Filipinas (-R$13.806), Rep. Dominicana (-R$9.814), Itália (-R$8.400), Indonésia (-R$12.441), Brasil (-R$2.734), Nova Zelândia (-R$2.080); e as cidades Filadélfia (-R$9.709) e Houston (-R$6.287) dentro dos EUA, país com resultado agregado positivo.

## Recomendações (uma ação, um responsável, uma razão baseada em dado)

1. **Recomendamos que a diretoria substitua `profit` bruto por lucro líquido pós-frete como métrica oficial de acompanhamento**, porque a margem real é 95% menor que a reportada hoje e isso já vinha mascarando o resultado de dezembro de 2024 (negativo).
2. **A Ana deveria implementar uma taxa de porte/volumetria moderada e faseada em `Furniture`**, já que a categoria tem prejuízo líquido em todos os modos de envio (mesmo Standard Class) — mas com cautela: 67,2% dos pedidos de Furniture vêm acompanhados de outra categoria no carrinho, com lucro conjunto 2,3x maior que o déficit, então a ação deve ser monitorada antes de escalar.
3. **Para o Diego, a ação mais direta é estabelecer um teto de 20% de desconto como regra geral**, uma vez que acima disso o lucro médio já fica negativo (correlação -0,34), e isso vale igualmente para os 3 segmentos de cliente — não é preciso política diferenciada por tipo de cliente.
4. **Também cabe à Ana criar uma taxa de despacho prioritário para pedidos `Critical`**, pois eles custam 3,38x mais frete que `Medium` sem gerar mais receita ou lucro proporcional — o custo hoje é 100% absorvido pela SuperStore.
5. **Por fim, sugerimos que a Sofia trate Turquia, Filipinas e Rep. Dominicana como revisão de precificação** (não de frete, já operam no vermelho antes dele) **e aplique taxa de frete regional ou ticket mínimo em Brasil, Indonésia, Itália e Nova Zelândia** (produto saudável, frete que anula o ganho), **além do teto de desconto em Filadélfia/Houston**, onde a média local (32-36%) é mais que o dobro da média nacional dos EUA (15,7%).

## O que ficou de fora (WON'T, com justificativa)

- **Segmentação de cliente (Consumer/Corporate/Home Office):** testamos a hipótese de reação diferente a desconto — não se confirmou (diferença de 0,6 p.p. entre segmentos). Não entra como ação porque os dados não sustentam diferenciação por esse critério.
- **Concentração de desconto no fim do mês:** cruzamos os dados para checar se o time comercial realmente dá mais desconto perto do fechamento — não é o caso, o nível é praticamente igual em qualquer dia do mês. Não entra como causa dos problemas de margem.
- **Efeito de venda casada (cross-sell) em Furniture:** identificamos o risco (item 2 das recomendações), mas não temos como medir a elasticidade real sem um teste controlado — fica como próximo passo, não como conclusão fechada.
- **Causa raiz física do frete caro em Furniture (peso/volumetria):** é a hipótese mais provável, mas a base não tem colunas de peso/volume para confirmar — fica como hipótese, não fato.

## Limitações e fonte

- Base: `order.csv` + `shipping.csv`, unidas e limpas em Python (Colab) — 19.955 pedidos, jan/2023 a dez/2024.
- 1 linha com `country` não resolvido (cidade "Santa Ana", ambígua entre 6 países) — excluída de análises por país.
- Custo de frete de 11 pares de chave duplicada (mesmo produto, mesmo pedido, linhas legítimas) foi somado e atribuído igualmente às linhas — pequena imprecisão aceita e documentada.
- Dashboard e Ficha Técnica completos, incluindo o processo de limpeza e a validação com IA, disponíveis no Notebook Colab anexado — este resumo é a síntese para apresentação executiva, não substitui o notebook para quem quiser auditar o método.
