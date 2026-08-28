# Prompt para nova sessão — Dashboard de Campanhas Foodsmart

Copie o bloco abaixo inteiro e cole como primeira mensagem da nova sessão (com o MCP da Meta reconectado).

---

Você vai atualizar a dashboard de campanhas da Foodsmart. O repositório é `meta-mcp-food` e o arquivo fonte é `dashboards/campanhas-ativas.html`. **Antes de qualquer coisa, leia `dashboards/README.md`** — ele tem a metodologia completa e o histórico de erros já cometidos. O que está abaixo é o essencial; o README é a fonte de verdade.

## Divisão de fontes — isso não é negociável

- **Leads reais vêm SOMENTE das planilhas do Google Drive.** Nunca use a contagem de leads/conversões que o Meta reporta pelo pixel — ela infla e não bate com o formulário.
- **Do Meta vem SOMENTE investimento** (e status das campanhas, e as métricas de vídeo do Boletim). Nada de lead.

## Planilhas de leads reais — uma por funil

| Funil | Planilha |
|---|---|
| Webinário Diário | https://docs.google.com/spreadsheets/d/10TAoNrbs3kvAvYFVUt12R8vffcS3EuMB0vbrQJHOX1A |
| 5 Aulas (NRTC) | https://docs.google.com/spreadsheets/d/1dZBHAGh-SVgv3Uo7VpPCAJ6f1cGs1VUWyDOh0wp4aGw |
| Lista de Espera | https://docs.google.com/spreadsheets/d/1R03LtoNapMumPGdPgzA_3ueS09h4yXc_dRWMfAdCgX0 |
| RT Class Agosto | https://docs.google.com/spreadsheets/d/1ZFw_laGXaL26TWgmnIJKT1Q8xE-Nut6yltRr3ARVos0 |
| E-books (funil de vendas) | https://docs.google.com/spreadsheets/d/1AjK1GZ-mHJxoioL93W4iySYZ-drWbfsVjkhlxtJ2xFY |
| Bolsa de Estudos (aba isolada, sem tráfego) | https://docs.google.com/spreadsheets/d/1rObSclXCj9BysDm5hFoLHaDZEVlFf0OOKSuSZIdoFnY |

Boletim Smart não capta lead — é vídeo/engajamento, não tem planilha de KPI.

Use `mcp__Google_Drive__download_file_content` com `exportMimeType: "text/csv"`. Se o arquivo for grande, o retorno vem salvo em disco como JSON base64 `{content, id, mimeType, title}` — decodifique.

## Campanhas no Meta — conta `8434968583195601` ("CA (oficial) - Food Smart")

| Funil | Campaign IDs |
|---|---|
| Webinário Diário | `120252983859150728` |
| 5 Aulas (NRTC) | `120250662210340728` |
| Lista de Espera | `120249875636100728`, `120251343526400728`, `120249880935590728` |
| RT Class Agosto | `120253773020370728`, `120253772384610728` |
| E-books | `120253731874700728`, `120253731865160728`, `120253731698570728`, `120253731689630728`, `120253729485470728` |
| Boletim Smart (vídeo) | `120253256887400728` |

**Sempre rode primeiro uma consulta SEM filtro de ID e SEM filtro de status** (`level: campaign`, com `effective_status` + `spend`) no período. É assim que se descobre campanha nova gastando fora do escopo — foi exatamente o que escondeu a Lista de Espera por semanas e o que revelou o RT Class e os e-books em 21/08 (juntos, 42% do gasto do dia).

## 🚨 A regra que mais importa: custo só divide por lead PAGO

Todo CPL, custo por qualificado e custo por veterinário divide a verba **apenas pelos leads cuja coluna `Utm Source` é `meta-ads`**. Leads orgânicos entram como volume, em coluna própria, e **nunca** no denominador de custo.

Isso não é preferência — a dashboard já publicou "CPL caindo 5,8%" quando o CPL real tinha **subido 47,0%**, porque dividia verba paga pelo total de leads. Webinário, 5 Aulas e e-books são 100% pagos (as duas bases coincidem); Lista de Espera e RT Class captam muito orgânico (RT Class ~81%), e é neles que o erro se esconde.

Mostre, para Lista de Espera e RT Class, **o CPL cheio e o CPL só-pagos lado a lado**.

## A métrica certa de cada funil — não aplique CPL onde não cabe

- **Webinário Diário / 5 Aulas** → CPL (100% pago, o CPL é o número real).
- **Lista de Espera / RT Class** → CPL cheio **e** CPL só-pagos.
- **E-books** → a métrica é **venda**, não CPL. A base de vendas não está em nenhuma planilha disponível: **registre a lacuna** em vez de julgar o funil pelo custo por formulário.
- **Boletim Smart** → **custo por engajamento** (os 16 conjuntos otimizam por `POST_ENGAGEMENT`, isso foi verificado) + **custo por visualização de 50%** (`video_p50_watched_actions`). **ThruPlay não é a métrica deste funil** — versões antigas usavam por engano.

## Metodologia de contagem dos leads

1. Coluna de data = "Submitted At". Filtre pela janela.
2. **Remova linhas de teste**: nome ou e-mail contendo "teste", "test", "example", "matheus", "manychat"; telefones fictícios (5567991810237, 5567999999999, 5511987654321).
3. **Deduplique por e-mail** dentro da janela.
4. **Classifique a formação**: contém "veterin" → Veterinário; "nutri" → Nutricionista; "engenh"+"aliment" → Eng. Alimentos (**Tecnólogo de Alimentos entra junto**); "Estudante/Outros" → não qualificado; resto → Outras qualificadas. Qualificado = tudo exceto Estudante/Outros e vazio.
5. Particularidades: a planilha da **Lista de Espera tem 2 linhas de cabeçalho** (dados começam na 3ª; pule linhas cuja coluna 0 seja `Started At`) e usa **"Outra"** no lugar de "Estudante" — trate "Outra" como não qualificado. Na do **RT Class**, e-mail está na coluna 3 e telefone na 4 (invertido em relação às outras), formação na 5 e `Utm Source` na 7.

## Estrutura da dashboard — versão minimalista, mantenha assim

O usuário pediu explicitamente: *"mais minimalista, está muito poluída de informações. Esqueça a comparação diária"*. **Não recrie** o bloco de acompanhamento diário, os cards de campanha, as barras de composição nem a tabela de vídeo do Boletim.

Duas abas (`role="tablist"`):

**Aba 1 — Tráfego pago**
1. Faixa de indicadores: investimento, leads, CPL de captação, veterinários (com Δ).
2. **Funil por funil** — células no formato `semana anterior → semana atual`, chip de situação. Nesta seção, fale também dos **ganhos**, não só das pioras: o usuário reclamou que a leitura tinha ficado "meio pessimista". Ex.: o Webinário ficou mais caro, mas a fatia de veterinários subiu de 85,0% para 89,3%.
3. **Leads e custo por formação** — cada célula com leads + **% daquela formação dentro do próprio funil** (cada coluna soma 100%) + custo. Deixe explícito que o custo por formação é **rateio de leitura, não custo medido** — o Meta não atribui gasto por formação, então as linhas não somam.
4. **Só os funis de captação** — consolidado (Webinário, 5 Aulas, Lista de Espera, RT Class; sem Boletim e sem e-books).
5. **Boletim Smart** — engajamento e retenção de vídeo.
6. **O que explica a semana** — leituras curtas.
7. **Metodologia** + carimbo de atualização.

**Aba 2 — Bolsa de Estudos**
🚫 **Regra dura: NÃO misture com os funis de tráfego.** Este funil roda só por e-mail, disparo em grupo e Botconversa — não tem campanha no Meta, não tem investimento, **não calcule CPL** e os leads dele **não entram em nenhum total, CPL ou composição** da aba de tráfego. A separação em abas existe para garantir isso.
Colunas dessa planilha: formação de base 6, concluiu graduação 7, registro no conselho 8, perfil 9, formato de pagamento 13, quando começaria 14, quem decide 15. O formulário é longo — dá para ler intenção de compra, não só volume.

**Leads passados ao comercial**: dado informado manualmente pelo time, fica acima das abas. Último valor: **248**, referente a 14→21/08. O time não informa quebra por funil — não atribua a nenhum. E sempre mostre as **duas** bases possíveis (% sobre o tráfego pago e % incluindo a Bolsa), nunca escolha uma silenciosamente.

## Armadilhas já vividas — confira todas antes de publicar

1. **NUNCA confie no relógio do container.** Ele já errou duas vezes (marcou 12/08 quando era 14/08; 17/08 quando era 21/08). Ancore a data chamando o Meta com `date_preset: today` e cruze com a série diária (`time_increment: "1"`) — a linha que bate com o total de `today` é a data real.
2. **A quebra horária do Meta atrasa horas.** Às ~11h a consulta dava R$ 28,01 para o Webinário no dia; à tarde, R$ 464,64 (a conta foi de R$ 65,22 para R$ 1.055,18). Leitura de manhã **não fecha semana** — marque como provisória e reconsulte.
3. **As planilhas sincronizam com atraso.** De manhã as 5 paravam no dia anterior. Se a planilha não tem o dia, **não escreva 0 leads** — registre a lacuna. Zero é "ninguém se cadastrou"; ausência de exportação é outra coisa.
4. Ajustes de centavos no investimento entre consultas são normais (atribuição do Meta se assenta em alguns dias), não é erro.

## O que fazer nesta execução

1. Rode a consulta sem filtro no Meta e me diga **quais campanhas estão ativas e quanto cada uma gastou de 21/08 a 28/08** — inclusive campanha nova que não esteja na lista acima.
2. Atualize a dashboard rolando as janelas para **21→28/08 vs 14→21/08**, com os leads reais das planilhas e o investimento do Meta, seguindo tudo acima.
3. Republique com a ferramenta Artifact usando `file_path: dashboards/campanhas-ativas.html` e `url: https://claude.ai/code/artifact/6aad9990-1a8a-4d94-b29a-74f2e8d19a32` (mesma URL, mantém o link).
4. Commit e push na branch `claude/analise-campanhas-julho-24-31-qblarr`.

## Pendência operacional separada

A campanha do **Webinário Diário (`120252983859150728`) precisa ser pausada** — o pedido foi feito e não chegou a ser executado porque o MCP da Meta ficou sem permissão. Depois de pausar, **confirme lendo `status` e `effective_status`** de volta, e verifique também os conjuntos: ativar/pausar a campanha pai não altera automaticamente os filhos.

---

## Sugestão à parte: atualizar o texto da Routine

A rotina diária (`trig_01TTPnrcRqR7hpeadS6AhHvZ`) ainda manda *"edite APENAS a seção Hoje e o rodapé"* — **essa seção não existe mais** desde a reescrita minimalista de 21/08. Ela disparou em 26, 27 e 28/08 sem conseguir concluir. Vale trocar o texto dela por: *"role as duas janelas semanais (a atual termina hoje, a de comparação são os 8 dias anteriores) e atualize o carimbo do rodapé"*.
