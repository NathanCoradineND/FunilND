# Relatório de Desempenho — Funis Comerciais — Documentação

Documentação de referência do **"Relatório de Desempenho — Funis Comerciais Nação Digital e Precisian"** (arquivo HTML autocontido), para ser incorporada na estrutura do funil de vendas da Nação Digital. Explica de onde vêm os dados, como cada número é calculado, como o relatório está organizado e como reproduzi-lo semana a semana.

Link do relatório: https://claude.ai/code/artifact/767483b8-33a4-49f6-ae23-a1a2227ef395

---

## 1. O que é este relatório

Um raio-X semanal dos dois funis comerciais ativos no RD Station CRM da Nação Digital:

- **Funil Nação Digital** (pipeline `5f0f9fc808c0c723ae8d4611`) — 5 etapas: SQL, Discovery, Proposta, Negociação, Fechado.
- **Precisian — Comercial** (pipeline `6a73aeb860c3530020048153`) — 5 etapas: Qualificação, Diagnóstico e Prova, Proposta enviada, Negociação, Contrato em assinatura.

O objetivo é dar visibilidade de **volume, valor em jogo, estagnação e qualidade de preenchimento** para o time comercial agir toda semana — o relatório termina em um plano de ação com prazo, não é só leitura passiva.

O arquivo é um único HTML autocontido (sem servidor, sem dependência externa além de fontes do Google Fonts) — pode ser aberto direto no navegador, hospedado em qualquer lugar (ClickUp, intranet, etc.) ou editado à mão.

## 2. Fonte de dados e como a pesquisa foi feita

Todos os números vêm direto da **API do RD Station CRM**, consultada via MCP (Model Context Protocol), sem nenhum dado inventado ou estimado. As chamadas feitas para montar a leitura mais recente (14/09/2026):

| Endpoint / tool | Para quê |
|---|---|
| `pipelines` (`funnel_list`) | Listar todos os funis da conta e confirmar os IDs de Nação Digital e Precisian, além de identificar os demais funis existentes |
| `pipelines/{id}/stages` (`funnel_stages_list`) | Nomes e ordem das etapas de cada funil |
| `deals` (`deals_list`, filtrado por `pipeline_id`) | Todas as negociações de cada funil — nome, etapa, status, responsável, datas de criação/atualização, valor, campos personalizados |
| `custom_fields` (`custom_fields_list`) | Quais campos personalizados existem, a qual entidade pertencem e em quais pipelines/etapas eles aparecem (`display_rules` / `required_rules`) |
| `users` (`users_list`) | Nome dos responsáveis por trás de cada `owner_id` |
| `lost_reasons` (`lost_reasons_list`) | Nomes dos motivos de perda |

Validação: os 72 registros (49 no Nação Digital + 23 no Precisian) foram conferidos um a um contra o payload bruto da API antes de entrar no relatório — inclusive casos como negociações duplicadas e campos que aparecem em branco, que só foram encontrados porque a checagem foi campo a campo, não só nos agregados. Os outros 4 funis da conta (Alocação, Outbound, Propostas antigas, Demonstração) foram checados via API e confirmados sem nenhuma negociação lançada.

## 3. Estrutura do HTML

### 3.1 Cabeçalho e navegação

- Cabeçalho com título, subtítulo, data de atualização (visível ali e também repetida no rodapé — ver seção 6) e o botão de exportação em CSV.
- Menu de abas logo abaixo do cabeçalho. Ao rolar a página para baixo, um **menu fixo idêntico aparece grudado no topo da tela** (com o mesmo conjunto de abas), para nunca perder o acesso à navegação sem precisar voltar ao topo manualmente.

### 3.2 Abas

1. **Visão geral** — os dois funis combinados:
   - 9 cards de indicador (ver seção 4) em grid fixo de 3×3, cada um com uma linha "Nação Digital + Precisian" combinada em destaque, seguida da linha de cada funil.
   - Painel "Comparativo entre os funis": dois gráficos — status geral por funil (a largura de cada barra representa o tamanho relativo do funil) e novas oportunidades por mês, Nação Digital x Precisian lado a lado.
   - Três tabelas combinadas com filtro (Todos / Nação Digital / Precisian): propostas paradas, em andamento sem valor, mais antigas ainda abertas.
   - Sugestões de novos indicadores (o que ainda não dá pra medir e por quê).
2. **Nação Digital** e **Precisian** — o mesmo padrão de análise para cada funil:
   - *Novas oportunidades*: quantas negociações foram criadas nos últimos 30 dias (janela fixa — ver seção 5), com a composição ganhas/em andamento/perdidas, mais um gráfico de evolução mensal (volume de criação mês a mês, desde que os dados existem).
   - *Valor em jogo*: quanto está em andamento, parado, ganho e perdido, em R$.
   - *Distribuição por etapa e status*: quantas negociações em cada etapa (número absoluto **e** percentual) e a divisão ganhas/em andamento/perdidas.
   - *Outros indicadores*: valor por etapa (funil de valor), negociações por responsável (volume, valor total, ticket médio) e há quanto tempo as negociações em andamento estão sem atualização (aging, em faixas de dias).
   - *Preenchimento de campos personalizados*: cards com contagem e percentual de preenchido/em branco/sem campos, mais o detalhamento campo a campo (quantas negociações têm cada campo específico preenchido, em branco ou nem alcançado ainda), exemplos reais de negociações 100% preenchidas, e a lista dos campos mais recorrentemente vazios.
   - *Plano de ação — prazo de 1 semana*: 4 ações fixas (replanejar/fechar paradas, preencher campos em branco, lançar valor, resolver duplicações), cada uma com o volume real de negociações envolvidas e a data-limite (sempre 7 dias corridos a partir da data do relatório).
   - *Ficha completa*: todas as negociações do funil, uma por linha, com etapa, responsável, datas, valor, status de campos e observação.
3. **Outros funis** — os demais funis já configurados na conta (Alocação, Outbound, Propostas antigas, Demonstração), confirmados via API como sem nenhuma negociação lançada. Ficam com a ressalva de que ainda não estão ativos; assim que tiverem volume, passam a receber a mesma estrutura de análise.
4. **Qualidade dos dados** — achados que não se encaixam em nenhum número isolado: negociações duplicadas, registros de teste, prazos vencidos, campos "vazando" de um funil para outro, o padrão de que o preenchimento só acontece quando o campo é obrigatório, e a explicação de por que taxa de conversão por etapa e tempo médio por etapa ainda não são possíveis de calcular.

### 3.3 Exportação e navegação

- Botão **"Baixar dados desta semana (CSV)"** no cabeçalho — gera um CSV com a ficha completa das 72 negociações.
- Menu fixo ao rolar a página (seção 3.1).

## 4. Os 9 cards da Visão Geral

Cada card mostra o combinado (Nação Digital + Precisian) em destaque, seguido de uma linha por funil. O período de referência está sempre escrito no próprio título do card — nunca implícito:

| Card | O que mede | Base de cálculo |
|---|---|---|
| **Negociações totais** | Total de negociações, sem recorte de período (foto do momento) | Contagem simples de todas as negociações do funil |
| **Novas oportunidades — últimos 30 dias** | Quantas negociações foram **criadas** nos últimos 30 dias | `created_at` dentro da janela de 30 dias |
| **Ganhas — últimos 30 dias** | Quantas negociações **fecharam como ganhas** nos últimos 30 dias, e o valor somado | Status = ganha **e** data de fechamento (`updated_at`, que reflete `closed_at`) dentro da janela de 30 dias — não importa quando a negociação foi criada |
| **Perdidas — últimos 30 dias** | Mesma lógica de "Ganhas", para negociações perdidas | Status = perdida **e** data de fechamento dentro da janela de 30 dias |
| **Em andamento — agora** | Quantas negociações estão abertas neste exato momento | Foto do momento, sem período — por isso o card diz explicitamente "sem período" |
| **Valor em jogo (em andamento)** | Soma do valor de todas as negociações em andamento | Foto do momento |
| **Paradas há mais de 20 dias** | Negociações em andamento sem nenhuma atualização há mais de 20 dias, e o valor parado | `updated_at` há mais de 20 dias, só para negociações em andamento |
| **Sem valor registrado** | Negociações em andamento com o campo de valor zerado | `total_price = 0`, só para negociações em andamento |
| **Campos 100% preenchidos** | Percentual de negociações com os campos personalizados do funil totalmente preenchidos | Ver seção 5 |

**Por que "últimos 30 dias" e não "este mês"**: o mês corrente é uma janela que varia de tamanho (dia 1 a dia 30/31), o que confunde a leitura no meio do mês — um "0" no dia 5 do mês não quer dizer a mesma coisa que um "0" no dia 28. Os últimos 30 dias são sempre uma janela fixa e comparável de leitura para leitura.

## 5. Definições usadas (para não haver ambiguidade)

| Termo | Definição |
|---|---|
| **Dias em aberto** | Dias corridos entre `created_at` da negociação e a data do relatório. |
| **Dias parada** | Dias corridos entre `updated_at` e a data do relatório — só calculado para negociações com status **em andamento**. Para negociações ganhas/perdidas, o mesmo cálculo (dias desde `updated_at`) é usado como proxy de "há quantos dias fechou". |
| **Parada / estagnada** | Negociação em andamento com mais de **20 dias** sem nenhuma atualização. O limiar de 20 dias foi escolhido observando a distribuição real dos dados: a maior parte das negociações ativas tem entre 10 e 17 dias desde o último toque, e um segundo grupo salta para 24+ dias — 20 dias é o corte que separa esses dois grupos. |
| **Últimos 30 dias** | Janela fixa e móvel de 30 dias corridos, contados retroativamente a partir da data do relatório. Nunca é o mês civil corrente. |
| **Sem valor registrado** | Negociação em andamento com o campo de valor (`total_price`) igual a zero. |
| **Preenchido / Em branco / Sem campos** | *Preenchido* = a negociação tem os campos personalizados do funil com valor real lançado. *Em branco* = os campos já aparecem no formulário da negociação (a etapa atual os exige ou exibe), mas estão vazios. *Sem campos* = a negociação ainda não chegou numa etapa onde esses campos aparecem — o objeto de campos personalizados retornado pela API está vazio. |
| **Plano de ação — prazo de 1 semana** | Toda leitura deste relatório gera uma lista de ações com prazo fixo de 7 dias corridos a partir da data do relatório. Na leitura seguinte, a primeira coisa a checar é se essas ações foram resolvidas antes de gerar uma lista nova. |

## 6. Onde a data de atualização aparece

Para nunca haver dúvida sobre qual leitura está sendo vista, a data de atualização aparece em **dois lugares fixos**:
1. No cabeçalho, ao lado do botão de exportação ("🕓 Atualizado em [data]").
2. No rodapé, em destaque, antes do texto de metodologia.

## 7. Por que os dois funis não têm exatamente os mesmos gráficos internamente

Os blocos de análise (novas oportunidades, valor em jogo, distribuição, outros indicadores, preenchimento, plano de ação, ficha completa) são os mesmos nos dois funis — é a mesma função de renderização reaproveitada com os dados de cada pipeline. As únicas diferenças reais entre Nação Digital e Precisian são:
- Os **nomes das etapas** (cada funil tem a sua nomenclatura).
- Os **campos personalizados monitorados** (Nação Digital tem 3 campos comerciais simples + 1 campo novo "Data Discovery" nunca usado; Precisian tem ~17-25 campos operacionais detalhados, exigidos só na etapa final "Contrato em assinatura").
- Os **itens do plano de ação** citam o volume real de cada funil, mas seguem a mesma estrutura de 4 ações nos dois.

## 8. Limitações atuais (o que ainda não dá pra construir)

- **Tempo médio por etapa** e **taxa de conversão etapa a etapa**: precisam do histórico de mudança de estágio de cada negociação (quando entrou e saiu de cada etapa). O RD Station guarda esse histórico, mas a consulta atual não trouxe esse dado — só a etapa **atual** de cada negociação.
- **Evolução semana a semana** do valor em jogo e das paradas: só existe a partir do momento em que houver 2+ leituras semanais acumuladas para comparar. O gráfico de "novas oportunidades por mês" já existe porque usa a data de criação (`created_at`), que é histórica desde o primeiro registro — isso é diferente de comparar o **estado** do funil entre relatórios.
- **Outros funis** (Alocação, Outbound, Propostas antigas, Demonstração): sem negociações lançadas na data desta leitura.

## 9. Como atualizar este relatório na próxima semana

1. Repetir as chamadas da tabela da seção 2 (`funnel_list`, `deals_list` por pipeline, `custom_fields_list`, `users_list`, `lost_reasons_list`) no RD Station CRM.
2. Comparar cada negociação com a leitura anterior — checar principalmente as que estavam no plano de ação da semana passada: foram resolvidas ou continuam pendentes?
3. Atualizar o array de dados (`deals`) no HTML com os valores novos — status, etapa, `updated_at`, valor, preenchimento de campos.
4. Ajustar `REPORT_DATE` para a nova data do relatório (isso recalcula automaticamente dias parada, dias em aberto, os últimos 30 dias e o novo prazo do plano de ação).
5. Atualizar a data de atualização no cabeçalho e no rodapé (seção 6).
6. Revisar a aba "Qualidade dos dados" — achados resolvidos saem da lista, achados novos entram.
7. Republicar/hospedar o arquivo atualizado.

---

*Documentação atualizada em 14/09/2026 a partir da mesma leitura de dados usada no relatório.*
