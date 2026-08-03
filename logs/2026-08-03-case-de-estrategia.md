# Rotina de nuvem "case de estratégia" (15h) — 2026-08-03 — NÃO PUBLICADO

## Contexto

Execução de nuvem do formato das 15h, rede de segurança (a rotina LOCAL das 14h50 é primária
desde 2026-07-28 — ver `CLAUDE.md`).

## O que foi feito

1. Clonados `creatorup-carrosseis-automation` e `creatorup-ebooks-site` com sucesso via HTTPS
   (git/GitHub segue funcionando normalmente nesta execução, mesmo com o resto da rede bloqueado
   — ver item 3).
2. Lido `CLAUDE.md` inteiro e a seção completa "FORMATO 'CASE DE ESTRATÉGIA'" (com as subseções
   "Como baixar as fotos", "Escolha de foto por slide" e "'Já viralizou' como inspiração") do
   `SKILL.md` da skill `creatorup-gerar-carrossel`.
3. `getScheduledPosts` (blogId 6553817, janela 2026-08-03, timezone America/Sao_Paulo,
   extendedRange true): **nenhum post do formato "case de estratégia" hoje até o momento desta
   execução.** Únicos posts de hoje: 09h22/09h23 (venda de ebook, "comenta 10K", 6 slides) e
   12h17/12h19 (formato "atualizações IG/TikTok", 4 slides, sobre a aba Beta do Edits). Ou seja, a
   rotina LOCAL das 14h50 também não deixou rastro de publicação hoje até agora — sem duplicata a
   evitar, segui em frente.
4. Testado o domínio de controle (`google.com`) ANTES de qualquer fonte de foto, como manda a
   correção de 2026-07-27 do `SKILL.md`:
   ```
   curl -s -o /dev/null -w "%{http_code}" --max-time 8 https://www.google.com
   → 000 (falha de conexão)
   ```
   Confirmado via `$HTTPS_PROXY/__agentproxy/status`, campo `recentRelayFailures`:
   ```json
   {"ts": "2026-08-03T18:09:17.780Z", "kind": "connect_rejected",
    "detail": "gateway answered 403 to CONNECT (policy denial or upstream failure)",
    "host": "www.google.com:443"}
   ```
   Mesmo padrão exato dos 9 dias anteriores (25/07 a 02/08).
5. Seguindo a instrução explícita do `SKILL.md` ("se o controle já falhou, pule direto pro passo
   5, não perca tempo testando Openverse/Wikimedia individualmente"), **não testei Openverse nem
   Wikimedia separadamente** — o controle já confirma bloqueio geral do gateway desta execução.

## Onde parou

Teste do domínio de controle, antes de escolher o case do dia e antes de pesquisar qualquer
número/fato via WebSearch. Mesmo ponto onde a rotina de nuvem parou em 25, 26, 27, 28, 29, 30,
31/07 e 01, 02/08.

## Nada foi publicado

- Nenhuma chamada a `createScheduledPost` foi feita (nem Instagram nem TikTok).
- Nenhum case foi pesquisado/escolhido.
- Nenhuma imagem foi gerada.
- Nenhum commit/push feito no `creatorup-carrosseis-automation`.

## Ponto de atenção pro usuário

**Décimo dia seguido (25/07 a 03/08) de bloqueio total do gateway de rede na execução de nuvem
deste formato**, sempre o mesmo padrão (`gateway answered 403 to CONNECT`, mesmo no domínio de
controle `google.com`). Até o momento desta execução (~15h Brasília), a rotina LOCAL das 14h50
também não tinha deixado rastro de publicação do case de hoje. A checagem de status da política de
rede do ambiente de nuvem estava agendada pro usuário para 2026-08-04 (ver
`2026-07-28-diagnostico-rede.md`) — com 10 dias seguidos do mesmo bloqueio, vale a pena antecipar
essa checagem em vez de esperar amanhã.
