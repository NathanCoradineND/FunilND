# FunilND

Documentação e relatórios do funil comercial da Nação Digital (RD Station CRM).

## Estrutura

- **`docs/mvp-funil-nacao-digital.html`** ("MVP Funil Nação Digital") — MVP do funil de vendas Nação Digital: estrutura de etapas (SQL → Discovery → Proposta → Negociação → Fechado), campos personalizados, automações e uso de MCP/API do RD Station CRM.
- **`reports/relatorio-desempenho-funis-comerciais/`** — leitura periódica do RD CRM (Nação Digital e Precisian), atualizada a partir dos dados da API.
  - `README.md` — documentação de referência do relatório: fonte de dados, cálculo de cada indicador, estrutura do HTML e como reproduzir a leitura semana a semana.
  - `report-rd-crm-nacao-digital.html` ("Report RD CRM Nação Digital") — o relatório em si (HTML autocontido, pode ser aberto direto no navegador).

## Convenção de atualização

Quando um novo relatório/material chega (ex.: nova leitura semanal), ele entra como **documento à parte** dentro de `reports/` (nunca substitui o anterior sem necessidade — decidir com o Nathan se vira nova pasta por data ou atualiza a mesma). O que for **estrutural e reaproveitável** (novo campo criado, novo requisito por etapa, nova automação confirmada, achado que muda o processo) é **incorporado ao MVP** em `docs/mvp-funil-nacao-digital.html`, que é a fonte viva do processo — o relatório é o retrato pontual, o MVP é o que fica valendo.

Toda vez que um desses dois arquivos é atualizado, o retorno inclui: o link do Artifact (pra visualizar/interagir) **e** o arquivo `.html` em si (pra baixar).
