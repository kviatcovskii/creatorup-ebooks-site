# Rotina de segurança (fallback, ~15h30) "case de estratégia" — 2026-07-29 — NÃO PUBLICADO

## Contexto

Esta é a rotina de SEGURANÇA de nuvem (roda ~30 min depois do trigger principal de nuvem
`trig_01XjPEqCnG6pFJWRKeYdooQn`), última rede de segurança depois da rotina LOCAL (14h50) e do
trigger principal de nuvem (15h03/04).

## O que foi feito

1. Clonados `creatorup-carrosseis-automation` e `creatorup-ebooks-site` com sucesso (git/GitHub
   segue funcionando normalmente nesta execução — só a rede de fontes de foto/domínio de controle
   está bloqueada).
2. Lido `CLAUDE.md` inteiro.
3. `getScheduledPosts` (blogId 6553817, janela 2026-07-29, timezone America/Sao_Paulo,
   extendedRange true): **nenhum post do formato "case de estratégia" hoje.** Únicos posts de hoje:
   09h11/09h13 (formato `parcerias-marcas`, venda de ebook) e 12h15/12h17 (formato "atualizações
   IG/TikTok"). Confirmado também o log `2026-07-29-case-de-estrategia.md` já deixado pelo trigger
   principal de nuvem (rodou ~15h06 Brasília): ele bateu no mesmo bloqueio de rede e não publicou
   nada — nenhuma duplicata a evitar, rotina liberada pra prosseguir com o fallback.
4. Testado o domínio de controle antes de qualquer fonte de foto, como manda o `SKILL.md`:
   ```
   curl -s -o /dev/null -w "%{http_code}" --max-time 8 https://www.google.com
   → 000 (falha de conexão), curl -v mostra 403 no CONNECT tunnel
   ```
   Confirmado via `$HTTPS_PROXY/__agentproxy/status`, `recentRelayFailures`:
   ```json
   {"ts": "2026-07-29T18:36:43.761Z", "kind": "connect_rejected", "detail": "gateway answered 403 to CONNECT (policy denial or upstream failure)", "host": "www.google.com:443"}
   ```
   Seguindo a instrução explícita do prompt desta rotina ("se o controle já falhou, pule direto,
   não perca tempo testando Openverse/Wikimedia individualmente"), **não testei Openverse nem
   Wikimedia separadamente**.

## Onde parou

Teste do domínio de controle, mesmo ponto onde o trigger principal de hoje (e a rotina deste
mesmo fallback em 27/07 e 28/07) parou.

## Nada foi publicado

- Nenhuma chamada a `createScheduledPost` (nem Instagram nem TikTok).
- Nenhum case pesquisado/escolhido, nenhuma imagem gerada, nenhum commit/push no
  `creatorup-carrosseis-automation`.

## Ponto de atenção pro usuário

**Sexto dia seguido (25, 26, 27, 28, 29/07) de bloqueio total do gateway de rede em pelo menos uma
das execuções de nuvem desse formato**, sempre o mesmo padrão (`gateway answered 403 to CONNECT`
mesmo no domínio de controle `google.com`). Também é o **segundo dia seguido (28 e 29/07) em que a
rotina LOCAL das 14h50 não deixa rastro de ter publicado** — ver nota do trigger principal no log
`2026-07-29-case-de-estrategia.md`. Com local, principal de nuvem e este fallback todos sem
publicar hoje, o formato das 15h não saiu — recomendo publicação manual se ainda houver tempo hábil
no dia, e verificar `C:\Users\Administrador\.claude\local-headless-routines\case-de-estrategia-15h\
logs\` na máquina local pra achar a causa exata de hoje. Checagem de status da política de rede já
agendada pro usuário em 2026-08-04 (ver `2026-07-28-diagnostico-rede.md`).
