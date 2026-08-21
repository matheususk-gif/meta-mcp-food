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

## Estrutura da dashboard (reescrita em 21/08 — versão minimalista)

O usuário pediu explicitamente uma versão **enxuta**: *"mais minimalista, está muito poluída de informações. Esqueça a comparação diária"*. A dashboard passou de ~77 KB para ~19 KB e agora tem só:

1. **Cabeçalho** — título, as duas janelas comparadas, lede de 2 linhas.
2. **Faixa de 4 indicadores** — investimento, leads, CPL de captação, veterinários (com Δ).
3. **Funil por funil** — uma tabela, células no formato `anterior → atual`, chip de situação por linha. Rodapé com o total da conta.
4. **Só os funis de captação** — tabela consolidada de 7 métricas com variação.
5. **O que explica a semana** — 4 leituras curtas.
6. **Metodologia** — 3 parágrafos + carimbo de atualização.

⚠️ **O bloco de acompanhamento diário foi REMOVIDO**, junto com os cards de campanha, as barras de composição por formação e a tabela de vídeo do Boletim. Não recrie nada disso sem o usuário pedir.

### Consequência para a rotina diária agendada
A Routine `trig_01TTPnrcRqR7hpeadS6AhHvZ` ainda manda *"edite APENAS a seção Hoje e o rodapé"*. **Essa seção não existe mais.** Até o usuário atualizar o texto da Routine, a interpretação correta é: **atualizar as duas janelas semanais** (a atual termina hoje, a de comparação são os 8 dias anteriores) e o carimbo do rodapé. Se a rotina rodar no meio de uma semana, role as janelas — não invente um bloco diário.

## Funis que entram (todos, desde 21/08)

| Funil | Campanhas Meta | Planilha de leads |
|---|---|---|
| Webinário Diário | `120252983859150728` | `10TAoNrbs3kvAvYFVUt12R8vffcS3EuMB0vbrQJHOX1A` |
| 5 Aulas (NRTC) | `120250662210340728` | `1dZBHAGh-SVgv3Uo7VpPCAJ6f1cGs1VUWyDOh0wp4aGw` |
| Lista de Espera | `120249875636100728`, `120251343526400728`, `120249880935590728` | `1R03LtoNapMumPGdPgzA_3ueS09h4yXc_dRWMfAdCgX0` |
| RT Class Agosto | `120253773020370728`, `120253772384610728` | `1ZFw_laGXaL26TWgmnIJKT1Q8xE-Nut6yltRr3ARVos0` |
| E-books (vendas) | `120253731874700728`, `120253731865160728`, `120253731698570728`, `120253731689630728`, `120253729485470728` | `1AjK1GZ-mHJxoioL93W4iySYZ-drWbfsVjkhlxtJ2xFY` |
| Boletim Smart (vídeo) | `120253256887400728` | — (não capta lead) |

Notas por funil:
- **RT Class Agosto**: planilha `MCP Meta Visualizer - RT Class Agosto`, formação na coluna 5, `Utm Source` na 7, **e-mail na coluna 3 / telefone na 4** (invertido em relação às outras planilhas). Tem linhas de teste com nome/e-mail "manychat" — filtre também por esse termo. **81% dos leads são orgânicos** → sempre mostre o CPL só dos pagos junto do CPL cheio, senão a campanha parece 5× melhor do que é.
- **E-books**: é **funil de vendas**, não de captação. Não entre com ele no CPL consolidado. A planilha é um formulário longo de qualificação (renda, endereço), formação na coluna 11. A métrica que decide o funil é **venda**, que não está em nenhuma planilha disponível — registre essa lacuna em vez de julgar pelo CPL.
- **Lista de Espera** e **RT Class** captam orgânico; Webinário e 5 Aulas são 100% `meta-ads` (verificado).

### Valores de referência — semanas 07→14/08 e 14→21/08 (recalculados em 21/08)

| Funil | Inv. 07→14 | Inv. 14→21 | Leads 07→14 | Leads 14→21 | CPL 14→21 |
|---|---|---|---|---|---|
| Webinário Diário | R$ 4.159,78 | R$ 4.704,16 | 240 | 175 | R$ 26,88 |
| 5 Aulas | R$ 1.079,27 | R$ 937,12 | 123 | 81 | R$ 11,57 |
| Lista de Espera | R$ 99,88 | R$ 1.100,95 | 30 | 70 | R$ 15,73 (pagos R$ 26,85) |
| RT Class Agosto | — | R$ 1.080,60 | — | 264 | R$ 4,09 (pagos R$ 22,05) |
| E-books | — | R$ 1.629,59 | — | 5 | n/a (vendas) |
| Boletim Smart | R$ 859,30 | R$ 683,08 | — | — | — |
| **Total conta** | **R$ 6.198,23** | **R$ 10.135,50** | **393** | **595** | — |

Captação (4 funis, sem Boletim e sem e-books): R$ 5.338,93 → R$ 7.822,83 · 393 → 590 leads · CPL R$ 13,59 → R$ 13,26 · qualificados 315 → 541 · vets 250 → 301 (63,6% → 51,0%).

⚠️ Os leads de 14→21/08 **não incluem 21/08** (planilhas sincronizadas até 20/08), então são piso; o investimento **inclui** 21/08 parcial.

## Comparação diária — hoje vs ontem (HISTÓRICO — bloco removido em 21/08)

> Mantido só como referência de método, caso o usuário peça o bloco diário de volta. **Não aplique numa execução normal.**

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
