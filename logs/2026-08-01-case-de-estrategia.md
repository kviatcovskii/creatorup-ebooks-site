# Rotina de nuvem "case de estratégia" (15h) — 2026-08-01 — NÃO PUBLICADO

## Contexto

Execução de nuvem do formato das 15h, rede de segurança (a rotina LOCAL das 14h50 é primária
desde 2026-07-28 — ver `CLAUDE.md`).

## O que foi feito

1. Clonados `creatorup-carrosseis-automation` e `creatorup-ebooks-site` com sucesso via HTTPS
   (git/GitHub segue funcionando normalmente nesta execução, mesmo com o resto da rede bloqueado
   — ver item 4).
2. Lido `CLAUDE.md` inteiro e a seção completa "FORMATO 'CASE DE ESTRATÉGIA'" (linhas 143-329,
   com as subseções "Como baixar as fotos", "Escolha de foto por slide" e "'Já viralizou' como
   inspiração") do `SKILL.md` da skill `creatorup-gerar-carrossel`.
3. `getScheduledPosts` (blogId 6553817, janela 2026-08-01, timezone America/Sao_Paulo,
   extendedRange true): **nenhum post do formato "case de estratégia" hoje até o momento desta
   execução.** Únicos posts de hoje: 09h10/09h12 (formato de venda de ebook, "comenta CLAUDE...",
   6 slides) e 12h15/12h20 (formato "atualizações IG/TikTok", 4 slides). Ou seja, a rotina LOCAL
   das 14h50 ainda não deixou rastro de publicação até agora — sem duplicata a evitar, segui em
   frente.
4. Testado o domínio de controle (`google.com`) ANTES de qualquer fonte de foto, como manda a
   correção de 2026-07-27 do `SKILL.md`:
   ```
   curl -s -o /dev/null -w "%{http_code}" --max-time 8 https://www.google.com
   → 000 (falha de conexão)
   ```
   Também testados Openverse e Wikimedia em paralelo (só pra reunir diagnóstico completo de uma
   vez, não como segunda tentativa depois do controle falhar): ambos também falharam com `000`.
   Confirmado via `$HTTPS_PROXY/__agentproxy/status`, campo `recentRelayFailures`, os três com o
   mesmo padrão:
   ```json
   {"ts": "2026-08-01T18:05:53.005Z", "kind": "connect_rejected",
    "detail": "gateway answered 403 to CONNECT (policy denial or upstream failure)",
    "host": "www.google.com:443"}
   {"ts": "2026-08-01T18:05:53.276Z", "kind": "connect_rejected",
    "detail": "gateway answered 403 to CONNECT (policy denial or upstream failure)",
    "host": "api.openverse.org:443"}
   {"ts": "2026-08-01T18:05:53.553Z", "kind": "connect_rejected",
    "detail": "gateway answered 403 to CONNECT (policy denial or upstream failure)",
    "host": "commons.wikimedia.org:443"}
   ```
   `raw.githubusercontent.com` respondeu normalmente (301) no mesmo momento — confirma que é
   bloqueio seletivo do gateway (github liberado, resto bloqueado), não uma falha geral de rede da
   execução.

## Onde parou

Teste do domínio de controle (mais Openverse/Wikimedia, já reunidos no mesmo diagnóstico), antes
de escolher o case do dia e antes de pesquisar qualquer número/fato via WebSearch. Mesmo ponto
onde a rotina de nuvem parou em 25, 26, 27, 28, 29, 30 e 31/07.

## Nada foi publicado

- Nenhuma chamada a `createScheduledPost` foi feita (nem Instagram nem TikTok).
- Nenhum case foi pesquisado/escolhido.
- Nenhuma imagem foi gerada.
- Nenhum commit/push feito no `creatorup-carrosseis-automation`.

## Ponto de atenção pro usuário

**Oitavo dia seguido (25/07 a 01/08) de bloqueio do gateway de rede (exceto GitHub) na execução de
nuvem deste formato**, sempre o mesmo padrão (`gateway answered 403 to CONNECT`, inclusive no
domínio de controle `google.com`). A rotina LOCAL das 14h50 ainda não tinha publicado até o
horário desta execução (~15h Brasília) — pode ser só atraso (em 31/07 a publicação também não
tinha saído no momento do check de nuvem, mas apareceu depois no Metricool às 15h16). Recomendo
checar `C:\Users\Administrador\.claude\local-headless-routines\case-de-estrategia-15h\logs\` na
máquina local, e checar o Metricool de novo daqui a alguns minutos antes de assumir falha total do
dia. Checagem de status da política de rede do ambiente de nuvem já agendada pro usuário em
2026-08-04 (ver `2026-07-28-diagnostico-rede.md`).
