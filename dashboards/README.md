# Dashboard — Campanhas Ativas (Foodsmart)

Referência para a atualização diária automática (via Routine/cron). Não é lida pelo usuário final — é o guia que a própria automação usa para saber o que buscar e como montar a seção "Hoje".

## Artifact
- URL publicada: **https://claude.ai/code/artifact/6aad9990-1a8a-4d94-b29a-74f2e8d19a32**
- Arquivo fonte: `dashboards/campanhas-ativas.html` (neste repo)
- Ao republicar, sempre usar `file_path: dashboards/campanhas-ativas.html` (redeploy automático na mesma URL).

## Campanhas ativas (entram na dashboard)
Conta Meta Ads: **`8434968583195601`** ("CA (oficial) - Food Smart") — única conta com gasto.

| Campanha | Campaign ID (Meta) | Planilha de leads (Google Sheets fileId) |
|---|---|---|
| Webinário Diário | `120252983859150728` | `10TAoNrbs3kvAvYFVUt12R8vffcS3EuMB0vbrQJHOX1A` |
| 5 Aulas (NRTC) | `120250662210340728` | `1dZBHAGh-SVgv3Uo7VpPCAJ6f1cGs1VUWyDOh0wp4aGw` |
| Boletim Smart (C2 — vídeo/engajamento, **não é funil de leads**) | `120253256887400728` | `1pgR3ftC8bLZlOnW_UVmZq8LzyZPdUJgDlq1VNJ0fLu8` (leads incidentais só como nota, não KPI) |

## Lista de Espera (bloco próprio — bloco 3 da dashboard)
Planilha: **`1R03LtoNapMumPGdPgzA_3ueS09h4yXc_dRWMfAdCgX0`** ("MCP Meta 2 - Lista de Espera").
São **3 campanhas** no Meta, somadas neste bloco:

| Campanha | Campaign ID |
|---|---|
| `[23/05][PAGINA - VET]` captação quente | `120249875636100728` |
| `[12/06][FRIO]` Lista de Espera — Teste do Frio | `120251343526400728` |
| `[23/05][QUENTE]` Lista de Espera TD PROF | `120249880935590728` |

⚠️ **Erro corrigido em 14/08:** versões anteriores deste README diziam que "Lista de Espera + Página VET" estavam **pausadas** e mandavam deixá-las fora. Estava errado — elas gastaram **R$ 2.399,34 em 24→31/jul** e **R$ 1.441,75 em 31/07→07/08**, e o "investimento total" da dashboard ficou subestimado por semanas. Pararam de entregar em 04/08 e foram **reativadas em 14/08**. **Sempre confira `effective_status` + gasto real no Meta antes de excluir uma campanha do escopo** — não confie na memória deste arquivo.

Particularidades desta planilha:
- Tem **duas linhas de cabeçalho** — os dados começam na 3ª linha. Pule qualquer linha cuja coluna 0 seja `Started At`.
- O formulário **não tem opção "Estudante"**; tem **"Outra"**. Trate `"Outra"` como **não qualificado** (equivalente a "Estudante/Outros" das outras planilhas), para as bases ficarem comparáveis. Texto livre com profissão declarada conta como qualificado.
- Recebe **tráfego orgânico relevante**. Sempre separe pago × orgânico pela coluna `Utm Source` (`meta-ads` vs `organico`) — sem isso o CPL fica sem sentido. Em 07/08→14/08, 22 dos 24 leads eram orgânicos com apenas R$ 4,51 de mídia.

## Campanhas realmente pausadas (NÃO entram)
- **RT Class** (e variações datadas) — planilha `171Xd5kdYqy83E4wsVz8nvY70whdWfnSsoveuf0Viexk`
- **Funis de vendas (Imersões)** — sem gasto nos períodos analisados
- Planilha antiga da lista de espera: `1gYXkMydt7yGqM0OhUXW0Ym0xRBKaOi_1bK6wiI8QNnk` (**substituída** pela `1R03...` acima)

## Como buscar os dados de "hoje"

1. **Leads (Webinário Diário e 5 Aulas)**: `mcp__Google_Drive__download_file_content` com `exportMimeType: "text/csv"` nos 2 fileIds acima. Decodificar base64 → CSV. Coluna de data = "Submitted At" (índice 1). Filtrar linhas onde a data (`YYYY-MM-DD`) é igual à data de hoje.
   - Se o arquivo for grande, o resultado vem salvo em disco (erro com caminho do `.txt` — é JSON `{content, id, mimeType, title}` em base64).
2. **Leads (Boletim Smart)**: mesma planilha, mas trate como **nota/bônus**, não KPI principal.
3. **Remover linhas de teste**: nome ou e-mail contendo "teste"/"test"/"example"/"matheus"; telefones fictícios conhecidos (5567991810237, 5567999999999, 5511987654321, etc.).
4. **Deduplicar por e-mail** dentro do dia.
5. **Classificar profissão**: contém "veterin" → Veterinário; contém "nutri" → Nutricionista; contém "engenh"+"aliment" → Eng. Alimentos; é "Estudante/Outros" → Estudante/Outros; qualquer outra coisa → Outras qualificadas. "Qualificado" = tudo exceto Estudante/Outros e vazio.
6. **Investimento de hoje**: `mcp__Meta_Ads__ads_get_ad_entities` nível `campaign`, `ad_account_id: "8434968583195601"`, `filtering` por `campaign.id IN [os 3 IDs acima + os 3 da Lista de Espera]`, `time_range: {since: HOJE, until: HOJE}`, campos `amount_spent`.
   - **Não filtre por ID sem antes rodar uma consulta sem filtro** no período: é assim que se descobre campanha gastando fora do escopo (foi exatamente o que escondeu a Lista de Espera). Rode `level: campaign` sem `filtering` com `effective_status` + `spend` e confira se apareceu alguma campanha nova com gasto.
7. **Vídeo (Boletim Smart)**: mesma chamada, campanha `120253256887400728`, campos `impressions, reach, video_play_actions, video_p100_watched_actions, video_thruplay_watched_actions, cost_per_thruplay`.

## Estrutura da dashboard (ordem das seções)

A dashboard está organizada em 5 blocos nesta ordem — **manter essa ordem**, foi pedido explicitamente pelo usuário:

1. **Período atual** (`sec-h` nº 1) — KPIs com investimento total das 3 campanhas + métricas do período, tabela por campanha com total no `<tfoot>`, composição por formação (inclui barra "Total do período") e os 3 cards de campanha.
2. **Comparativo entre períodos** (`sec-h` nº 2) — tabela consolidada (com coluna de contexto do período anterior ao anterior) e tabela aberta por campanha. **A Lista de Espera entra nas duas**: na consolidada como grupo final "Conta inteira — somando a Lista de Espera", na por-campanha como grupo próprio. Não basta citá-la no bloco 3 — o usuário pediu explicitamente que ela apareça nos comparativos.
3. **Lista de Espera** (`sec-h` nº 3) — KPIs, tabela dos 3 períodos com split pago/orgânico, composição por formação e tabela das 3 campanhas no Meta.
4. **Acompanhamento diário** (`sec-h` nº 4) — banners, painel "Hoje", `#daySnapshot` e composição ontem-vs-hoje.
5. **Leituras principais e metodologia** (`sec-h` nº 5) — rodapé.

## O que atualizar no HTML

**A cada execução diária**, recalcule com dados de hoje e ontem (mesmo horário de coleta):
- O painel **"Hoje"** (`today-panel`): KPIs com delta, tabela `.today-tbl`, tabela `.cmp` de CPL hoje-vs-ontem, e o `#daySnapshot`.
- A seção **"Composição por formação — ontem vs hoje"**: 2 barras empilhadas por campanha (Webinário Diário e 5 Aulas), uma para "Ontem" e uma para "Hoje". Atualize o título (`ontem vs hoje (DD→DD/MM)`), os leads/percentuais no `.comp-head .meta`, as larguras/rótulos das `.stack` e a frase de leitura.
- O banner de campanha pausada (`.banner.off`), se houver campanha sem gasto no dia — checar `effective_status` antes de afirmar que está pausada.
- O rodapé `#updatedAt`.

**O bloco "Período atual" e o "Comparativo" também precisam rolar** conforme o tempo passa: o período atual é a janela de 8 dias que termina hoje, e o período anterior é a janela de 8 dias imediatamente anterior (ambas inclusivas, compartilhando a data de virada). Ao rolar, recalcule os dois períodos **na mesma base** (mesmo filtro de teste, mesma dedup, mesmo classificador) — nunca reaproveite números de versões antigas da dashboard, que podem ter sido calculados com outro critério.

> Nota: o total de leads de 24/07→31/07 foi **recalculado** e passou de 152 (versões antigas) para 158. O investimento bate exatamente com o Meta (R$ 714,86 + R$ 537,64 + R$ 339,75). Isso está documentado no rodapé da dashboard.

⚠️ **Nunca congele um período cujo último dia ainda está rodando.** A versão de 07/08 fotografou 31/07→07/08 com o dia 07/08 em curso e publicou 205 leads / R$ 2.624,66. Reconsultado com o dia fechado, o mesmo período dá **211 leads / R$ 2.807,03** (5 Aulas 108→115 leads e R$ 678,44→R$ 758,25; Boletim R$ 633,46→R$ 736,02; Webinário inalterado porque já estava pausado naquele dia). Ao rolar a janela, **sempre reconsulte o período anterior inteiro** em vez de reaproveitar o número publicado.

### Valores de referência já validados (recalculados em 14/08, mesma base)
Servem de checagem: se um recálculo futuro divergir destes, é sinal de que o filtro/dedup mudou.

| Período | Investimento (3 campanhas) | Leads | Vets | Qualificados | CPL | Custo/qual |
|---|---|---|---|---|---|---|
| 24/07→31/07 | R$ 1.592,25 | 158 | 90 | 126 | R$ 7,93 | R$ 9,94 |
| 31/07→07/08 | R$ 2.807,03 | 211 | 105 | 163 | R$ 9,82 | R$ 12,71 |
| 07/08→14/08 | R$ 5.994,08 | 359 | 229 | 282 | R$ 14,44 | R$ 18,38 |

Lista de Espera (mesmos períodos): R$ 2.399,34 / 111 leads (78 pagos) · R$ 1.441,75 / 59 leads (30 pagos) · R$ 4,51 / 24 leads (1 pago).

**Conta inteira** (3 campanhas + Lista de Espera; captação = tudo menos o Boletim):

| Período | Investimento | Leads (pagos / org.) | Vets | Qualif. | CPL | CPL pago | Custo/qual |
|---|---|---|---|---|---|---|---|
| 24/07→31/07 | R$ 3.991,59 | 269 (236 / 32) | 171 | 232 | R$ 13,58 | R$ 15,47 | R$ 15,74 |
| 31/07→07/08 | R$ 4.248,78 | 270 (241 / 27) | 145 | 222 | R$ 13,01 | R$ 14,58 | R$ 15,82 |
| 07/08→14/08 | R$ 5.998,59 | 383 (360 / 22) | 246 | 306 | R$ 13,54 | R$ 14,41 | R$ 16,95 |

⚠️ **Sempre mostre as duas leituras.** Só com as 3 campanhas, a semana 07–14/08 parece uma piora de 47,1% no CPL; com a Lista de Espera somada, o CPL fica em +4,1% e o CPL pago em −1,2%, porque houve **remanejamento** de verba do funil caro (Lista de Espera, R$ 48,06/lead pago) para o barato (Webinário, R$ 17,33). Reportar só a primeira leitura dá diagnóstico errado. Webinário e 5 Aulas são **100% `meta-ads`** na coluna `Utm Source` (verificado), então o corte "só pago" é válido; a Lista de Espera é a única que capta orgânico.

**Não incluir** métricas de atendimento comercial na seção "Hoje" — esse dado vem de fora (time humano) e só deve aparecer se o usuário fornecer explicitamente.

**Leads passados para o comercial** (KPI `Passados p/ o comercial`, 5º card do bloco "Período atual"): dado informado manualmente pelo usuário, **não** buscável via planilha ou Meta Ads. Último valor informado: **135**, referente ao período **31/07→07/08**. Ao rolar o período, **não recalcule nem invente** esse número. Desde 14/08 o card aparece **vazio (`—`)** no período corrente, com o último valor e seu período citados no `.delta` e detalhados no rodapé — foi a forma escolhida de sinalizar a defasagem sem repetir um número que não corresponde à semana exibida. Se o usuário informar um valor novo, preencha o card e recalcule os percentuais sobre as bases do período exibido (sobre 31/07→07/08, os 135 equivalem a 64,0% dos 211 leads e 82,8% dos 163 qualificados).

## Comparação diária — hoje vs ontem no MESMO horário (importante)

A rotina roda todo dia por volta do mesmo horário, então os dados de "hoje" e "ontem" são coletados em pontos parecidos do dia — isso é o que torna a comparação justa. **Nunca compare o "hoje" parcial com a média/total da última semana fechada** para CPL ou custo — um dia pela metade sempre parece pior, porque o investimento é gasto de forma mais linear ao longo do dia do que os leads chegam (o Meta "gasta na frente"). A comparação certa é sempre **hoje até HHhMM vs ontem até um HHhMM parecido**.

Passo a passo:
1. **Antes de editar o arquivo**, leia o `<script type="application/json" id="daySnapshot">` que já está no `dashboards/campanhas-ativas.html` (versão ainda não atualizada) — esse JSON é o snapshot de **ontem**, salvo pela execução anterior. Ele tem `investment_total`, `leads_total`, `qualified_total`, `vet_total`, `cpl_global`, `cost_per_qualified` e o detalhe por campanha (`campaigns.webinario`, `campaigns.aulas`, `campaigns.boletim`).
2. Calcule os números de **hoje** normalmente (passos abaixo).
3. Calcule as variações % de hoje vs esse snapshot de ontem (investimento, leads, qualificados, veterinários, CPL global, custo por qualificado, CPL por campanha, custo/ThruPlay do Boletim).
4. Atualize os KPIs (`.kpi .delta`), a tabela `.today-tbl` e a tabela `.cmp` de "CPL / custo — hoje vs ontem" com os novos valores e variações.
5. **Substitua** o `#daySnapshot` pelo snapshot de **hoje** (mesmo formato JSON), para que a execução de amanhã use os números de hoje como "ontem".
6. Sentido das cores (classes `.delta`/`.val`): investimento usa `flat` (neutro, é só informativo). Leads, qualificados e veterinários usam `up` quando sobem (bom). CPL, custo por qualificado e custo/ThruPlay usam `up` quando **caem** (bom, fica mais barato) e `down` quando sobem (fica mais caro) — o sinal da cor é sobre o que é bom/ruim pro negócio, não sobre a seta do número.
7. Se por algum motivo não houver `#daySnapshot` no arquivo (primeira execução após uma mudança estrutural), registre isso na dashboard em vez de travar, e apenas grave o snapshot de hoje normalmente.

## Regras gerais
- ⚠️ **NUNCA confie no relógio do container.** Ele já errou duas vezes: marcou 12/08 quando era 14/08, e 17/08 quando era **21/08**. Ancore a data chamando `ads_get_ad_entities` com `date_preset: today` e cruze o resultado com a série diária (`time_increment: 1`) — a linha que bate exatamente com o total de `today` é a data real de hoje.
- **Rode a consulta sem filtro a cada execução.** O escopo da conta muda sem aviso. Em 21/08 apareceram, todas `ACTIVE` e fora da dashboard: **RT-Class Agosto 26** (2 campanhas, RT Class retomado depois de meses) e um **funil novo de venda de e-books** (5 campanhas criadas em 17/08) — juntas, 42% do gasto do dia.
- **Distinga "zero" de "sem dado".** Se a planilha não sincronizou o dia corrente (aconteceu em 21/08: as 3 planilhas paravam em 20/08), **não escreva 0 leads** — registre a lacuna e use o último dia fechado como leitura confiável. Zero significa "ninguém se cadastrou"; a ausência de exportação é outra coisa.
- **A quebra horária do Meta atrasa.** Em 21/08 só havia hora fechada até 01h59. Use como janela o que existe nos dois dias e diga qual é — não estenda a janela de hoje além do último dado real.
- Nunca usar o total de leads que o Meta reporta via pixel/conta — só os confirmados em planilha.
- Sempre indicar que os dados de "hoje" são **parciais** (hora da última consulta), já que o dia ainda não terminou.
- Investimento pode sofrer pequenos ajustes de centavos em consultas subsequentes (atribuição do Meta se assenta em alguns dias) — não é erro.
