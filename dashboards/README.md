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

## Campanhas pausadas (NÃO entram)
- **RT Class** — planilha `171Xd5kdYqy83E4wsVz8nvY70whdWfnSsoveuf0Viexk`
- **Lista de Espera + Página VET** — planilha `1gYXkMydt7yGqM0OhUXW0Ym0xRBKaOi_1bK6wiI8QNnk`

## Como buscar os dados de "hoje"

1. **Leads (Webinário Diário e 5 Aulas)**: `mcp__Google_Drive__download_file_content` com `exportMimeType: "text/csv"` nos 2 fileIds acima. Decodificar base64 → CSV. Coluna de data = "Submitted At" (índice 1). Filtrar linhas onde a data (`YYYY-MM-DD`) é igual à data de hoje.
   - Se o arquivo for grande, o resultado vem salvo em disco (erro com caminho do `.txt` — é JSON `{content, id, mimeType, title}` em base64).
2. **Leads (Boletim Smart)**: mesma planilha, mas trate como **nota/bônus**, não KPI principal.
3. **Remover linhas de teste**: nome ou e-mail contendo "teste"/"test"/"example"/"matheus"; telefones fictícios conhecidos (5567991810237, 5567999999999, 5511987654321, etc.).
4. **Deduplicar por e-mail** dentro do dia.
5. **Classificar profissão**: contém "veterin" → Veterinário; contém "nutri" → Nutricionista; contém "engenh"+"aliment" → Eng. Alimentos; é "Estudante/Outros" → Estudante/Outros; qualquer outra coisa → Outras qualificadas. "Qualificado" = tudo exceto Estudante/Outros e vazio.
6. **Investimento de hoje**: `mcp__Meta_Ads__ads_get_ad_entities` nível `campaign`, `ad_account_id: "8434968583195601"`, `filtering` por `campaign.id IN [os 3 IDs acima]`, `time_range: {since: HOJE, until: HOJE}`, campos `amount_spent`.
7. **Vídeo (Boletim Smart)**: mesma chamada, campanha `120253256887400728`, campos `impressions, reach, video_play_actions, video_p100_watched_actions, video_thruplay_watched_actions, cost_per_thruplay`.

## O que atualizar no HTML

Só a seção **"Hoje"** (bloco com `id`/classe `today-panel` e a tabela `.today-tbl`) e o rodapé `#updatedAt` devem ser recalculados a cada execução. As seções "Última semana fechada" (comparação 17→24 vs 24→31/jul, composição por formação, cards de campanha) são **histórico estático** — só mudar quando o usuário pedir explicitamente para fechar uma nova semana.

**Não incluir** métricas de atendimento comercial na seção "Hoje" — esse dado vem de fora (time humano) e só deve aparecer se o usuário fornecer explicitamente.

## Regras gerais
- Nunca usar o total de leads que o Meta reporta via pixel/conta — só os confirmados em planilha.
- Sempre indicar que os dados de "hoje" são **parciais** (hora da última consulta), já que o dia ainda não terminou.
- Investimento pode sofrer pequenos ajustes de centavos em consultas subsequentes (atribuição do Meta se assenta em alguns dias) — não é erro.
