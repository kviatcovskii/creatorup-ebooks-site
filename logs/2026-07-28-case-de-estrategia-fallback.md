# Rotina de segurança (fallback) 15h "case de estratégia" — 2026-07-28 — NÃO PUBLICADO

## Contexto

Rodei ~30min depois do trigger principal (`trig_01XjPEqCnG6pFJWRKeYdooQn`) pra checar se o post do
dia saiu e fazer o fallback manual se não. Confirmei via `getScheduledPosts` (blogId 6553817,
timezone America/Sao_Paulo, hoje, extendedRange true) que **nenhum post do formato "case de
estratégia" existe hoje** — só o carrossel de venda de ebook das 09h17/09h18 e o post de
atualização IG/TikTok das 14h52/14h53. Nenhuma rotina local publicou o formato hoje.

## O que eu tentei

1. Clonados `creatorup-carrosseis-automation` e `creatorup-ebooks-site`, lido `CLAUDE.md` inteiro.
2. Testei o domínio de controle e as duas fontes de foto, de forma independente (não confiei em
   nenhuma afirmação do prompt agendado sobre o estado da rede em dias anteriores):
   ```
   curl -s -o /dev/null -w "%{http_code}" --max-time 8 https://www.google.com
   → 000 (curl exit 56, CONNECT tunnel failed)

   curl -s -o /dev/null -w "%{http_code}" --max-time 8 https://api.openverse.org/v1/images/?q=test
   → 000 (curl exit 56, CONNECT tunnel failed)

   curl -s -o /dev/null -w "%{http_code}" --max-time 8 \
     "https://commons.wikimedia.org/w/api.php?action=query&format=json"
   → 000 (curl exit 56, CONNECT tunnel failed)
   ```
3. Confirmado via `$HTTPS_PROXY/__agentproxy/status`, campo `recentRelayFailures`:
   ```json
   {"ts": "2026-07-28T18:36:31.468Z", "kind": "connect_rejected",
    "detail": "gateway answered 403 to CONNECT (policy denial or upstream failure)",
    "host": "www.google.com:443"},
   {"ts": "2026-07-28T18:36:31.788Z", "kind": "connect_rejected",
    "detail": "gateway answered 403 to CONNECT (policy denial or upstream failure)",
    "host": "api.openverse.org:443"},
   {"ts": "2026-07-28T18:36:32.115Z", "kind": "connect_rejected",
    "detail": "gateway answered 403 to CONNECT (policy denial or upstream failure)",
    "host": "commons.wikimedia.org:443"}
   ```
   Domínios do GitHub (`github.com`, `raw.githubusercontent.com`, `api.github.com`) responderam
   normalmente no mesmo teste — o bloqueio é específico de política de egress do ambiente de nuvem
   de hoje (não geral a ponto de impedir git clone/push), mas cobre as duas fontes de foto de
   licença aberta exigidas pelo formato.

## Achado: já existia log da rotina principal de hoje, mesma conclusão

Encontrei `logs/2026-07-28-case-de-estrategia.md`, já commitado pela rotina principal (15h) antes
deste fallback rodar. Ela chegou à mesma conclusão de forma independente: controle (`google.com`)
falhou primeiro, e por instrução do próprio `SKILL.md` não testou Openverse/Wikimedia
individualmente. Este fallback testou as duas fontes de foto mesmo assim (por ser a última rede de
segurança) e confirmou que ambas também falham, quinto dia seguido (25, 26, 27, 28) do mesmo padrão
`gateway answered 403 to CONNECT`.

O log da rotina principal também registra uma discrepância relevante, que reproduzo aqui: o prompt
armazenado (deste fallback e do trigger principal) afirma que existe uma "rotina LOCAL primária às
14h50", criada por causa dos bloqueios de 25-27/07, cobrindo este formato antes da rotina de nuvem.
Essa mudança operacional **não está documentada no `CLAUDE.md`** deste repositório — que é, pela
regra própria do projeto ("toda correção vira arquivo"), onde qualquer mudança permanente de
rotina deveria estar. Nenhum post do formato apareceu no Metricool até a hora desta execução, então
se essa rotina local existe, ela não cobriu o dia de hoje (ou a afirmação sobre sua existência não
corresponde ao que está de fato configurado).

## Onde parei

Teste de conectividade (controle + duas fontes de foto), antes de escolher o case do dia e antes de
pesquisar via WebSearch. Seguindo a instrução explícita do `SKILL.md`: não inventar outro método de
scraping quando as fontes de foto estão bloqueadas, não publicar carrossel incompleto ou com foto
de origem não confirmada.

## Nada foi publicado (nem pela rotina principal, nem por este fallback)

- Nenhuma chamada a `createScheduledPost` foi feita (nem Instagram nem TikTok).
- Nenhum case foi pesquisado/escolhido, nenhuma imagem gerada.
- Nenhum commit no `creatorup-carrosseis-automation` (só este log em `creatorup-ebooks-site`).

## Ponto de atenção pro usuário

1. Bloqueio de rede real e confirmado, quinto dia seguido (25-28/07) — vale investigar a política
   de egress do ambiente de nuvem usado por estes `RemoteTrigger`s, já que GitHub segue liberado
   mas Google/Openverse/Wikimedia não.
2. A "rotina local das 14h50" mencionada no prompt agendado não está documentada no `CLAUDE.md` e
   não há evidência de que publicou hoje. Recomendo confirmar se ela está mesmo configurada antes
   de contar com ela pra cobrir os próximos dias — do contrário este formato fica sem nenhuma
   publicação enquanto o bloqueio de rede persistir.
