# Log — rotina "espelhar Reels IG → YouTube" (Marcos Moraes, 21h) — 2026-07-26

## Resultado desta execução
**Nenhum Reels publicado hoje no Instagram @marcosmoraesk — nada pra espelhar.** Isso é normal,
não é erro.

## O que foi feito
- Clonados `creatorup-carrosseis-automation` e `creatorup-ebooks-site`, lido `CLAUDE.md` inteiro.
- A skill `creatorup-espelhar-youtube-marcosmoraesk` referenciada no prompt da rotina **não
  existia** no repo (`.claude/skills/` não tinha essa pasta) — esta é a primeira execução real.
  Criada agora, com o mecanismo documentado abaixo.
- `getScheduledPosts` (blogId 6577352, timezone America/Sao_Paulo, janela de hoje e depois janelas
  mais largas — semana anterior e semana seguinte, `extendedRange: true`): sempre retornou
  `{"data":[]}`. A própria descrição da tool explica por quê: "Only retrieves posts that are
  scheduled (not yet published)" — ela nunca vai listar um post já publicado, então o Passo 1/3 do
  prompt original da rotina (baseados nela) não funcionam como desenhados.
- Confirmado blogId 6577352 = conta "marcosmoraesk" via `getBrandSettings` (Instagram e YouTube
  conectados corretamente).
- Testado caminho alternativo: `getAnalyticsDataByMetrics` com connector `reels` do Instagram
  (campos IGRE01-06). Esse SIM retorna Reels já publicados de verdade — 17 Reels entre 18/07 e
  24/07 confirmados, com data, legenda, id e URL pública. Consultado especificamente a janela de
  hoje (2026-07-26T00:00 a 23:59, America/Sao_Paulo) e nenhuma linha retornou. Consultado também
  ontem (25/07) dentro da janela 18-26/07 — também nenhuma linha. Último Reels real é de
  2026-07-24 04:27 (horário do dado, aparentemente UTC/interno do Metricool).
- Conclusão: nenhum Reels novo desde 24/07, então hoje (26/07) realmente não teve Reels publicado.

## Gap descoberto (documentado no SKILL.md pra não repetir a investigação amanhã)
Mesmo que houvesse um Reels hoje, o Passo 4 do mecanismo original (pegar a URL do arquivo .mp4 pra
mandar pro YouTube) provavelmente teria travado: o `getAnalyticsDataByMetrics` só devolve a URL da
PÁGINA pública do Reel (`instagram.com/reel/...`), não o arquivo de vídeo em si, e não há hoje
(testado: Metricool, Windsor_ai, Higgsfield) uma tool MCP disponível confirmada pra resolver a URL
do .mp4 a partir disso. Não tentei nenhum workaround tipo raspar HTML da página — ficaria fora do
escopo de tools autorizadas e é o tipo de contorno que o CLAUDE.md deste projeto pede pra evitar.
Isso significa: **a próxima vez que houver um Reels de verdade, a rotina provavelmente vai parar
no Passo 4 e vai precisar reportar esse bloqueio ao usuário** (a menos que ele consiga confirmar
um jeito de disponibilizar o vídeo em URL pública própria, ou que uma das tools MCP tenha um método
ainda não descoberto).

## O que NÃO foi feito
- Nenhuma chamada a `createScheduledPost` (não havia nada pra espelhar).
- Nenhuma alteração em posts existentes.

## Próximo passo sugerido
Da próxima vez que o usuário postar um Reels manualmente, esta rotina vai detectar via analytics,
mas pode travar ao tentar pegar o arquivo de vídeo. Vale o usuário já saber disso e decidir um
plano B (ex.: também subir o vídeo original num storage próprio, como já é feito com os
carrosséis) antes da próxima execução real com Reels novo.
