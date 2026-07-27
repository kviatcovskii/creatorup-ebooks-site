# Rotina de segurança (fallback) 15h "case de estratégia" — 2026-07-27 — NÃO PUBLICADO

## Contexto

Rodei ~30min depois do trigger principal (`trig_01XjPEqCnG6pFJWRKeYdooQn`) pra checar se o post do
dia saiu e fazer o fallback manual se não. Confirmei via `getScheduledPosts` (blogId 6553817,
timezone America/Sao_Paulo, hoje) que **nenhum post do formato "case de estratégia" existe hoje** —
só o carrossel de venda das 09h e o post de atualização IG/TikTok das 12h.

## O que eu tentei

1. Clonados `creatorup-carrosseis-automation` e `creatorup-ebooks-site`, lido `CLAUDE.md` inteiro e
   a seção completa "FORMATO 'CASE DE ESTRATÉGIA'" (linhas 143-283) do `SKILL.md`.
2. Testei conectividade das duas fontes de foto de licença aberta, como instruído:
   ```
   curl -s -o /dev/null -w "%{http_code}" --max-time 8 https://api.openverse.org/v1/images/?q=test
   → 000 (curl exit 56, CONNECT tunnel failed)

   curl -s -o /dev/null -w "%{http_code}" --max-time 8 \
     "https://commons.wikimedia.org/w/api.php?action=query&format=json"
   → 000 (curl exit 56, CONNECT tunnel failed)
   ```
3. Confirmei a causa via `$HTTPS_PROXY/__agentproxy/status` (não é bug de código, é política de
   rede do ambiente — ver `/root/.ccr/README.md`, seção "403/407 from the proxy": "Do not retry or
   route around it — report the blocked host"):
   ```json
   {"ts": "2026-07-27T18:36:42.121Z", "kind": "connect_rejected",
    "detail": "gateway answered 403 to CONNECT (policy denial or upstream failure)",
    "host": "api.openverse.org:443"},
   {"ts": "2026-07-27T18:36:42.560Z", "kind": "connect_rejected",
    "detail": "gateway answered 403 to CONNECT (policy denial or upstream failure)",
    "host": "commons.wikimedia.org:443"}
   ```

## Achado importante: já existia um log da rotina principal de hoje

Antes de decidir parar, encontrei `logs/2026-07-27-case-de-estrategia.md` (já commitado neste repo
pela rotina principal). Ela chegou à mesma conclusão de forma independente hoje mais cedo: testou
Openverse, Wikimedia **e um domínio de controle (google.com)**, os três recusados com o mesmo erro
de política de proxy — o que descarta bloqueio específico das fontes de foto e confirma bloqueio
geral do gateway do ambiente de nuvem de hoje. A rotina principal corretamente não publicou nada e
não pesquisou/gerou/commitou conteúdo incompleto.

Esse mesmo log da rotina principal também registra uma discrepância que eu reproduzi de forma
independente: o prompt armazenado (tanto do trigger principal quanto do fallback, incluindo o meu)
afirma que em 2026-07-26 "o bloqueio varia por dia, teste Wikimedia como 2ª fonte" / dá a entender
que a rede estava ok em algum teste anterior — mas isso não bate com `logs/2026-07-26-case-de-
estrategia.md`, que documenta bloqueio total naquele dia também, nem existe commit no `SKILL.md`
adicionando Wikimedia como 2ª fonte. Segui o arquivo real do `SKILL.md` (fonte de verdade) e testei
a rede de forma independente antes de decidir, como manda o próprio texto do prompt agendado.

## Onde parei

Teste de conectividade das fontes de foto, antes de escolher o case do dia — mesmo ponto onde a
rotina principal já havia parado hoje. Segui a instrução explícita do `SKILL.md` de não inventar
outro método de scraping quando as duas fontes de foto estão bloqueadas.

## Nada foi publicado (nem pela rotina principal, nem por este fallback)

- Nenhuma chamada a `createScheduledPost` foi feita (nem Instagram nem TikTok).
- Nenhum case foi pesquisado/escolhido, nenhuma imagem gerada, nenhum commit no
  `creatorup-carrosseis-automation`.

## Ponto de atenção pro usuário

Recomendo revisar o texto armazenado dos triggers `trig_01XjPEqCnG6pFJWRKeYdooQn` (principal) e do
seu fallback: ambos contêm uma afirmação sobre o estado da rede em 2026-07-26 que não corresponde ao
que está de fato commitado no histórico deste repositório sobre esse dia. Vale corrigir o texto do
prompt agendado pra não repetir essa afirmação em execuções futuras.
