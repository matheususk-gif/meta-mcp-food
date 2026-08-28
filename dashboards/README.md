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

## 🚨 REGRA DE OURO: custo só divide por lead PAGO

Todo CPL, custo por qualificado e custo por veterinário divide a verba do funil **apenas pelos leads cuja `Utm Source` é `meta-ads`**. Leads orgânicos entram como **volume**, em coluna própria, e **nunca** no denominador de custo.

**Por que isso é regra e não preferência:** até 21/08 a dashboard dividia verba paga pelo total de leads. Resultado — a página anunciava **CPL caindo 5,8%** quando o CPL real **subiu 47,0%**. O erro vinha de dois funis:

| Funil | Leads | Pagos | Orgânicos | CPL errado (todos) | CPL correto (pagos) |
|---|---|---|---|---|---|
| RT Class Agosto | 274 | 51 | **223** | R$ 4,07 | **R$ 21,89** |
| Lista de Espera | 76 | 46 | 30 | R$ 14,82 | **R$ 24,49** |
| Captação (4 funis) | 635 | 382 | 253 | R$ 12,76 | **R$ 21,21** |

Webinário, 5 Aulas e e-books são 100% `meta-ads`, então neles as duas bases coincidem — o erro ficava escondido nos outros dois.

O mesmo vale para **custo por veterinário**: no RT Class, 78 dos seus 90 vets são orgânicos. Dividir a verba por 90 dá R$ 12,40 e sugere que é o funil mais eficiente em ICP; dividindo pelos **12 vets pagos**, o custo real é **R$ 93,02** — o mais caro da conta.

### Números de referência (snapshot 21/08, base paga)
Captação 4 funis: R$ 5.338,93 → **R$ 8.100,35** (+51,7%) · leads pagos 370 → **382** (+3,2%) · **CPL pago R$ 14,43 → R$ 21,21 (+47,0%)** · vets pagos 233 → 238 · custo/vet pago R$ 22,91 → **R$ 34,04** (+48,5%) · fatia vet no pago 63,0% → 62,3% (estável).
Orgânicos: 23 → **253** (RT Class 223, Lista de Espera 30).

## Métricas por funil — como cada um é medido

Cada funil tem a métrica que corresponde ao que ele faz. **Não aplique CPL onde não cabe.**

| Funil | Métrica correta | Observação |
|---|---|---|
| Webinário Diário | CPL (leads da planilha) | 100% `meta-ads`, sem orgânico — CPL é o número real |
| 5 Aulas (NRTC) | CPL | 100% `meta-ads` |
| Lista de Espera | CPL **e** CPL só-pagos | capta orgânico; mostre os dois |
| RT Class Agosto | CPL **e** CPL só-pagos | ~81% orgânico; sem o corte pago a campanha parece 5× melhor |
| E-books | **venda**, não CPL | funil de `VENDAS`; a base de vendas não está disponível — registre a lacuna |
| Boletim Smart | **custo por engajamento** (meta real) + **custo por visualização de 50%** | ver abaixo |

### Boletim Smart — a métrica foi confirmada, não presumida
Os **16 conjuntos** otimizam por `POST_ENGAGEMENT` (verificado via `level: adset`, campo `optimization_goal`). Então:
- **Custo por engajamento** é a métrica pela qual o Meta entrega e cobra.
- **Custo por visualização de 50%** (`video_p50_watched_actions`) é a leitura de consumo de conteúdo que o usuário pediu. As visualizações de 50% **incluem quem pulou até esse ponto**, conforme definição do Meta.
- Use `video_p25/p50/p75/p95/p100_watched_actions` para a curva de retenção. **ThruPlay não é a métrica deste funil** — versões anteriores da dashboard usavam ThruPlay por engano.
- Achado de 21/08: com −19,7% de verba, o custo por engajamento **caiu** 4,7% mas o custo por visualização de 50% **subiu** 7,7%; a retenção 25%→50% ficou estável (43,4% → 42,9%). Ou seja, a entrega alcançou menos gente e gerou engajamento mais raso — se a marca de 50% é o objetivo, a otimização está premiando outra coisa.

### Custo por formação
`custo por lead da formação X no funil Y = investimento total de Y ÷ leads de X em Y`.
São **rateios de leitura, não custos medidos** — o Meta não atribui gasto por formação do lead. Servem para comparar funis ("onde o veterinário sai mais barato"); **não somam**, porque o mesmo investimento está no denominador de todas as linhas. Sempre explicite isso na página.

Referência 14→21/08 — custo por veterinário: RT Class **R$ 12,77** · Lista de Espera R$ 25,85 · Webinário R$ 28,63 · 5 Aulas R$ 37,69.

A tabela mostra, em cada célula, **leads + fatia da formação naquele funil + custo**. As fatias são calculadas sobre o total de leads da própria coluna (funil), não sobre o total geral — cada coluna soma 100%. Fatias de veterinário por funil: Webinário 89,3% · Lista de Espera 56,6% · E-books 40,0% · RT Class 32,3% · 5 Aulas 27,5%.
Classificação: **Tecnólogo de Alimentos entra junto com Engenheiro de Alimentos**.

## Aba "Bolsa de Estudos" — funil SEM tráfego pago (isolado)

Planilha: **`1rObSclXCj9BysDm5hFoLHaDZEVlFf0OOKSuSZIdoFnY`** ("YayForms").
Distribuição por **e-mail, disparo em grupo e Botconversa** — **não tem campanha no Meta**.

🚫 **Regra dura, pedida explicitamente pelo usuário: NÃO misture com os funis de tráfego.**
Os leads da Bolsa não entram em nenhum total, CPL ou composição por formação da aba de tráfego pago. A dashboard usa **abas** (`role="tablist"`) justamente para garantir essa separação estrutural. O único ponto em que as duas bases se somam é a nota dos leads passados ao comercial, e lá isso está dito.

- Sem investimento ⇒ **não calcule CPL** para este funil. A métrica é volume + qualificação declarada.
- Colunas: formação de base na **6**, concluiu graduação **7**, registro no conselho **8**, perfil **9**, formato de pagamento **13**, quando começaria **14**, quem decide **15**.
- Formulário longo: dá para ler **intenção de compra**, não só volume. Use isso.

Referência 14→21/08: **58 leads reais** com a janela fechada (o valor **51**, medido em 21/08, era leitura de planilha ainda sincronizando), todos entre 19 e 21/08 (26 no dia 19 — padrão de **disparo pontual**, não fluxo). Veterinário 22 (43,1%), Eng./Tecnól. Alimentos 12 (23,5%), Nutricionista 11 (21,6%), Outras 6 (11,8%). Com registro ativo no conselho: 38 (74,5%). Começariam imediatamente: 39 (76,5%). Pagariam à vista: apenas 3 (5,9%) — **70,6% têm restrição de fluxo de caixa**.

## Leads passados ao comercial

Dado **informado manualmente pelo time**, não extraível de planilha nem do Meta. Último valor: **248**, referente a **14→21/08**.
⚠️ O time **não informa a quebra por funil**, então não atribua a nenhum funil específico. A dashboard mostra o número acima das abas, com as duas referências possíveis: 39,7% dos 625 leads do tráfego pago, ou 36,7% incluindo os 51 da Bolsa. **Nunca escolha uma das duas bases silenciosamente.**

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

### Valores de referência — semanas 07→14/08 e 14→21/08 (revisados no fim do dia 21/08)

| Funil | Inv. 07→14 | Inv. 14→21 | Leads 07→14 | Leads 14→21 | CPL 14→21 |
|---|---|---|---|---|---|
| Webinário Diário | R$ 4.159,78 | R$ 4.759,41 | 240 | 187 | R$ 25,45 |
| 5 Aulas | R$ 1.079,27 | R$ 940,99 | 123 | 91 | R$ 10,34 |
| Lista de Espera | R$ 99,88 | R$ 1.108,56 | 30 | 76 | R$ 14,59 (pagos R$ 24,10) |
| RT Class Agosto | — | R$ 1.094,75 | — | 266 | R$ 4,12 (pagos R$ 21,89) |
| E-books | — | R$ 1.652,66 | — | 5 | n/a (vendas) |
| Boletim Smart | R$ 859,30 | R$ 688,67 | — | — | — |
| **Total conta** | **R$ 6.198,23** | **R$ 10.245,04** | **393** | **625** | — |

Captação (4 funis): R$ 5.338,93 → R$ 7.903,71 · 393 → 620 leads · CPL R$ 13,59 → R$ 12,75 · qualificados 315 → 569 · vets 250 → 321 (63,6% → 51,8%).

Veterinários por funil na semana atual: Webinário 167/187 (**89,3%**), Lista de Espera 43/76 (56,6%), RT Class 86/266 (32,3%), 5 Aulas 25/91 (27,5%).

⚠️ **Duas armadilhas de atraso, ambas vividas em 21/08 — sempre reconsulte antes de publicar:**
1. **Quebra horária do Meta atrasa horas.** Às ~11h a consulta devolvia R$ 28,01 para o Webinário no dia; à tarde, R$ 464,64. A conta inteira passou de R$ 65,22 para R$ 1.055,18. Uma leitura feita cedo **não serve** para fechar semana.
2. **As planilhas sincronizam com atraso.** De manhã as 5 paravam em 20/08; à tarde todas tinham 21/08. A primeira versão da dashboard publicou o Webinário com 175 leads; o real era 187.

Consequência prática: se a rotina rodar de manhã, **marque os números como provisórios** e rode de novo mais tarde antes de tratar a semana como fechada.

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

## ⚠️ Achados da execução de 28/08 — ler antes da próxima

### A planilha do Webinário quebrou
A planilha configurada `10TAoNrbs3kvAvYFVUt12R8vffcS3EuMB0vbrQJHOX1A` ("[WD] MCP Meta") **retorna `#REF!` na exportação inteira** — CSV e `read_file_content` devolvem só isso. Não é ausência de dados, é fórmula quebrada; o arquivo não é modificado desde 24/07.

**Substituto usado:** `19be-4RV6C-j4B4SM1N8aWet5Q3cBVeos7cxq5-A8XoY` ("[WD] Dashboard"), modificado no mesmo dia e **com cabeçalho e primeira linha idênticos** ao snippet em cache do arquivo quebrado — é o mesmo dataset. Índices: nome 2, telefone 3, e-mail 4, formação 5, **`Utm Source` 9** (atenção: no [NRTC] o `Utm Source` está na coluna 12).

Antes de trocar de novo, confira se a original voltou. Se as duas estiverem vivas, prefira a configurada.

### Campanha nova, ainda sem gasto
`120253882963110728` — **[27/08] [LEAD - CAPTAÇÃO] [Profissão Vet RT (L32)] [15/09]**, `ACTIVE`, **R$ 0,00** em 14→28/08. Criada em 27/08 e ainda não entregou. **Entra no escopo quando começar a gastar.**

### Mudanças de status dentro da janela
- **RT Class Agosto** (as 2 campanhas): gastaram até **25/08** e estão **PAUSED**.
- **E-books**: **3 das 5 pausadas**; seguem ativas só `120253729485470728` (Check List Fiscalização) e `120253731689630728` (Consultoria Lucrativa).

### Convenção de janela — confirmada empiricamente
As janelas são **fechadas nas duas pontas** e **compartilham o dia de fronteira**. Confirmado: o Webinário publicado como R$ 4.905 para 14→21/08 corresponde ao 14..21 inclusive recalculado (R$ 4.958,07 — a diferença é o assentamento da atribuição). Mantenha isso, mas **diga na página** que o dia de fronteira entra nas duas e que o último dia é parcial.

### Números de referência — 14→21/08 (fechado) vs 21→28/08 (28/08 parcial)

| Funil | Inv. 14→21 | Inv. 21→28 | Pagos | Orgânicos | CPL pago | Custo/vet pago |
|---|---|---|---|---|---|---|
| Webinário Diário | R$ 4.958,07 | R$ 3.276,80 | 197 → 99 | — | R$ 25,17 → **R$ 33,10** | R$ 28,33 → R$ 36,82 |
| 5 Aulas (NRTC) | R$ 981,44 | R$ 625,28 | 94 → 56 | — | R$ 10,44 → **R$ 11,17** | R$ 37,75 → R$ 41,69 |
| Lista de Espera | R$ 1.177,26 | R$ 1.081,46 | 48 → 36 | 30 → 13 | R$ 24,53 → **R$ 30,04** | R$ 37,98 → R$ 51,50 |
| RT Class Agosto | R$ 1.204,68 | R$ 1.615,21 | 55 → 100 | 230 → 89 | R$ 21,90 → **R$ 16,15** | R$ 92,67 → R$ 73,42 |
| E-books | R$ 1.725,88 | R$ 755,15 | 5 → 4 | — | n/a (venda) | n/a |
| Boletim Smart | R$ 741,43 | R$ 684,22 | — | — | — | — |
| **Total conta** | **R$ 10.788,76** | **R$ 8.038,12** | | | | |

Captação (4 funis): R$ 8.321,45 → **R$ 6.598,75** (−20,7%) · pagos 394 → **291** (−26,1%) · orgânicos 260 → **102** · **CPL pago R$ 21,12 → R$ 22,68 (+7,4%)** · qualificados 341 → 270 · **custo/qualificado R$ 24,40 → R$ 24,44 (+0,2%, estável)** · vets pagos 245 → **147** · custo/vet R$ 33,97 → **R$ 44,89** (+32,1%) · fatia vet no pago 62,2% → **50,5%**.

**A leitura da semana:** a verba saiu do Webinário (89,9% vet, caro) para o RT Class (22,0% vet, barato). Por isso o **CPL subiu pouco (+7,4%) e o custo por veterinário subiu muito (+32,1%)**. Não foi perda de qualidade de nenhum funil — foi mudança de mix. Sempre separe esses dois efeitos.

Boletim 21→28/08: gasto R$ 684,22 · alcance 14.568 · engajamentos 6.677 · **custo/engajamento R$ 0,1025 (−16,2%)** · p25 1.309 · p50 602 · **custo/visualização 50% R$ 1,14 (−26,0%)** · retenção 25→50 **46,0%** (era 43,4%) · p100 225. Primeira semana em que as duas métricas melhoram juntas.

### Bolsa de Estudos
21→28/08: **36 leads** (vet 21 / 58,3%, nutri 9, eng 2, outras 4); registro ativo 26 (72,2%); começariam já 21 (58,3%); à vista só 2 (5,6%). Dois disparos: 21/08 (13) e 25/08 (10).
⚠️ A referência de **51 leads para 14→21/08 estava desatualizada** — fechada a janela, são **58**. O mesmo vale para os outros funis: os números medidos em 21/08 subiram depois que as planilhas terminaram de sincronizar. **Ao comparar com a semana anterior, recalcule-a; não copie o snapshot antigo.**

### Leads passados ao comercial — corrigido pelo usuário em 28/08
⚠️ Os **248 são da semana 21→28/08**, não de 14→21/08. O usuário confirmou: **247 → 248, +0,40%**. A referência anterior deste README (248 para 14→21/08) estava deslocada de uma semana.

Bases da janela atual: **62,5%** dos 397 leads de tráfego (pagos + orgânicos), ou **57,3%** somando os 36 da Bolsa. Sempre mostre as duas.

**Não chame isso de taxa de conversão.** O repasse ficou estável (+0,40%) enquanto o tráfego caiu 39,8% (659 → 397), então a razão saltou de 37,5% para 62,5%. O time não informa quebra por funil nem data de origem do lead, e lead de semana anterior pode ser trabalhado depois — os dois conjuntos não são a mesma safra.

### Pago × orgânico — distribuição real medida (não presumir)

A dashboard passou a ter **três abas**: Tráfego pago, **Leads totais** (pagos + orgânicos) e Bolsa de Estudos. O corte sai da coluna `Utm Source`, **lead a lead**. Medido em 21→28/08:

| Funil | Pagos | Orgânicos | Total | Valores de `Utm Source` encontrados |
|---|---|---|---|---|
| Webinário Diário | 99 | **0** | 99 | só `meta-ads` (99/99) |
| 5 Aulas (NRTC) | 56 | **0** | 56 | só `meta-ads` (56/56) |
| Lista de Espera | 36 | 13 | 49 | `meta-ads`, `organico` |
| RT Class Agosto | 100 | 89 | 189 | `meta-ads`, `organico` (88) + **1 linha sem UTM** |
| E-books | 4 | **n/d** | — | **planilha não tem a coluna `Utm Source`** |
| **Captação (4 funis)** | **291** | **102** | **393** | |

Três regras que saíram disso:
1. **Webinário e 5 Aulas têm zero orgânico medido**, não ausência de dado — escreva `0`, e diga que é medido. Nas duas janelas todas as linhas são `meta-ads`.
2. **E-books é `n/d`, nunca `0`.** A planilha não tem a coluna, então não há como afirmar que não houve orgânico.
3. Linha sem UTM conta como **orgânico** (não é mídia), mas **declare** — no RT Class desta janela é 1 de 89.

Totais da captação: **654 → 393 (−39,9%)** · pagos 394 → 291 (−26,1%) · **orgânicos 260 → 102 (−60,8%)** · fatia de orgânico **39,8% → 26,0%**.

Orgânicos por formação (21→28/08, só Lista de Espera + RT Class): vet 48 (47,1%), nutri 38 (37,3%), eng 10 (9,8%), outras 6 (5,9%).

**Achado que vale repetir:** o **orgânico do RT Class entrega veterinário melhor que a mídia paga dele** — 42 de 89 (47,2%) contra 22 de 100 (22,0%). Na Lista de Espera é o inverso (58,3% pago × 46,2% orgânico). E na base total a fatia de veterinário quase não se moveu (51,8% → **49,6%**, −2,2 p.p.) enquanto no recorte pago caiu 11,7 p.p. — o orgânico, rico em vet, é o que segurou o perfil.

⚠️ **Não calcule custo na aba de totais.** CPL, custo por qualificado e custo por veterinário continuam dividindo a verba **só pelos leads pagos**. O "CPL cheio" é a única exceção e existe apenas para mostrar o tamanho da distorção.

### Recomendações registradas (não executadas — sessão de leitura)
1. **Consertar a planilha `[WD] MCP Meta`** ou trocar a configuração para `[WD] Dashboard`.
2. **Decidir sobre o RT Class**: foi o único funil que melhorou CPL pago e custo por vet, e está pausado desde 25/08.
3. **Lista de Espera** piorou em todas as leituras ao mesmo tempo — vale revisar.
4. **E-books**: sem a base de vendas não há como avaliar; ou se liga essa fonte, ou o funil segue sem métrica de decisão.
5. **RT Class:** o canal orgânico dele tem o dobro da densidade de veterinário da mídia paga (47,2% × 22,0%) — a segmentação paga está puxando nutricionista (58 de 100). Vale revisar público antes de reativar.
6. Atualizar o texto da Routine `trig_01TTPnrcRqR7hpeadS6AhHvZ`, que ainda manda editar a seção "Hoje" — ela não existe desde 21/08.

---

## Regras gerais
- ⚠️ **NUNCA confie no relógio do container.** Ele já errou duas vezes: marcou 12/08 quando era 14/08, e 17/08 quando era **21/08**. Ancore a data chamando `ads_get_ad_entities` com `date_preset: today` e cruze o resultado com a série diária (`time_increment: 1`) — a linha que bate exatamente com o total de `today` é a data real de hoje.
- **Rode a consulta sem filtro a cada execução.** O escopo da conta muda sem aviso. Em 21/08 apareceram, todas `ACTIVE` e fora da dashboard: **RT-Class Agosto 26** (2 campanhas, RT Class retomado depois de meses) e um **funil novo de venda de e-books** (5 campanhas criadas em 17/08) — juntas, 42% do gasto do dia.
- **Distinga "zero" de "sem dado".** Se a planilha não sincronizou o dia corrente (aconteceu em 21/08: as 3 planilhas paravam em 20/08), **não escreva 0 leads** — registre a lacuna e use o último dia fechado como leitura confiável. Zero significa "ninguém se cadastrou"; a ausência de exportação é outra coisa.
- **A quebra horária do Meta atrasa.** Em 21/08 só havia hora fechada até 01h59. Use como janela o que existe nos dois dias e diga qual é — não estenda a janela de hoje além do último dado real.
- Nunca usar o total de leads que o Meta reporta via pixel/conta — só os confirmados em planilha.
- Sempre indicar que os dados de "hoje" são **parciais** (hora da última consulta), já que o dia ainda não terminou.
- Investimento pode sofrer pequenos ajustes de centavos em consultas subsequentes (atribuição do Meta se assenta em alguns dias) — não é erro.
