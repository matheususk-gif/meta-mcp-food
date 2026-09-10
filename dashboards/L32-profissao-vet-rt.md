# L32 — Profissão Vet RT (apuração de 10/09/2026)

> Complemento ao `dashboards/README.md`, seção *"Campanha nova, ainda sem gasto"* (execução de 28/08), que dizia: *"Entra no escopo quando começar a gastar."* Ela começou. **Dobre este conteúdo no README na próxima execução** — ficou em arquivo separado porque as operações de git locais foram bloqueadas nesta sessão.

Campanha `120253882963110728` — **[27/08] [LEAD - CAPTAÇÃO] [Profissão Vet RT (L32)] [15/09]**.

## Planilha de leads (descoberta em 10/09)

`1b6Mu45KdwYHnt9Cav_a4Jc-VM0KttzpNetSj3rfIvuY` — **"[L32] Lista de leads"**.

Uma linha de cabeçalho (ao contrário da Lista de Espera, que tem duas). Índices das colunas:

| Campo | Índice |
|---|---|
| Started At | 0 |
| Submitted At | 1 |
| Nome | 2 |
| **E-mail** | **3** |
| Telefone | 4 |
| **Formação/graduação** | **5** |
| Idade | 6 |
| Já atuou com RT? | 7 |
| Quanto investiria por mês | 8 |
| Maior obstáculo (texto livre) | 9 |
| Conteúdo mais importante (texto livre) | 10 |
| Score | 11 |
| **Utm Source** | **12** |
| Utm Medium / Campaign / Content / Term | 13–16 |

⚠️ E-mail na 3 e telefone na 4 — mesmo layout do RT Class Agosto, invertido em relação ao Webinário e ao NRTC.

## Base apurada — 28/08 a 08/09

Última submissão: **08/09/2026 21:06**. Metodologia padrão do README (remoção de teste + dedup por e-mail).

| Métrica | Valor |
|---|---|
| Linhas brutas | 36 |
| Linhas de teste | **0** |
| Duplicados por e-mail | 1 (`marciavet.1574@gmail.com`, duas submissões em 03/09 às 22:04 e 22:05) |
| **Leads válidos** | **35** |
| Veterinários | **29 (82,9%)** |
| Nutricionistas | 2 (5,7%) |
| Eng./Tecn. de Alimentos | 2 (5,7%) |
| Outras qualificadas | 2 (5,7%) — Biomédica, Assistente de Qualidade |
| Estudante/Outros | **0** |
| Qualificados | **35 (100%)** |

Distribuição diária:

| Dia | Leads |
|---|---|
| 28/08 | 3 |
| 29/08 | 3 |
| 30/08 | **0** |
| 31/08 | 2 |
| 01/09 | 7 |
| 02/09 | 8 |
| 03/09 | 9 |
| 04/09 | 2 |
| 05→07/09 | **0** |
| 08/09 | 1 |
| 09→10/09 | **0** |

**A captação secou depois de 04/09.** Pico em 01→03/09 (24 dos 35 leads, 68,6%), depois praticamente nada: 1 lead em 08/09 e zero nos últimos dois dias. Com o evento marcado para **15/09**, isso é o achado operacional da leitura, independente de custo — confirme no gerenciador se a campanha parou de entregar ou se o orçamento saiu dela.

## 🚨 Duas lacunas que impedem o CPL — não improvisar em cima delas

### 1. A planilha não grava UTM

`Utm Source`, `Utm Medium`, `Utm Campaign`, `Utm Content` e `Utm Term` estão **vazios nas 36 linhas** — não é ausência de tráfego pago, é ausência de captura.

Consequência direta: a **REGRA DE OURO** deste repositório (custo divide só por lead cuja `Utm Source` é `meta-ads`) **não pode ser aplicada à L32**. Não há como separar pago de orgânico. O máximo que sai é um **CPL cheio, com denominador contaminado** — exatamente o tipo de número que, no RT Class, fez a campanha parecer 5× melhor do que era.

**Antes de publicar qualquer CPL da L32:** peça a correção da captura de UTM no formulário. Enquanto isso não acontecer, rotule o número na página como *CPL cheio — denominador contaminado, sem corte pago × orgânico*.

### 2. Não há investimento apurado

O MCP da Meta (`Meta_Ads`) **não estava autorizado** na sessão de 10/09 e a sessão era não-interativa, então o `spend` da campanha desde 28/08 **não foi lido**. Sem investimento não existe CPL. **Não estime por média de outro funil.**

## O que fazer na próxima sessão com a Meta conectada

1. Rodar a consulta **sem filtro de ID e sem filtro de status** no período (`level: campaign`, com `effective_status` + `spend`) — regra permanente do README.
2. Ler o `spend` de `120253882963110728` de 28/08 até hoje.
3. Conferir `effective_status` e a série diária (`time_increment: 1`) para explicar o buraco de 05→07/09 e 09→10/09.
4. Dividir por **35** (ou pelo recorte da janela publicada) e publicar como **CPL cheio**, com a lacuna de UTM dita na própria célula.

Referência para leitura rápida, dividindo pelos 35 leads e pelos 29 veterinários:

| Se o gasto for | CPL cheio | Custo por veterinário |
|---|---|---|
| R$ 500 | R$ 14,29 | R$ 17,24 |
| R$ 1.000 | R$ 28,57 | R$ 34,48 |
| R$ 1.500 | R$ 42,86 | R$ 51,72 |
| R$ 2.000 | R$ 57,14 | R$ 68,97 |

Comparação com a última janela fechada (21→28/08, CPL **pago**): Webinário R$ 33,10 · Lista de Espera R$ 30,04 · RT Class R$ 16,15 · 5 Aulas R$ 11,17. Acima de ~R$ 1.200 de gasto, a L32 já sai mais cara que o Webinário — com o agravante de o denominador dela incluir orgânico.
