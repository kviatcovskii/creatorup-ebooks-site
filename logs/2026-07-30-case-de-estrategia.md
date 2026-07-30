# Rotina de nuvem "case de estratégia" (15h) — 2026-07-30 — NÃO PUBLICADO

## Contexto

Execução de nuvem do formato das 15h, agora rede de segurança (a rotina LOCAL das 14h50 é
primária desde 2026-07-28 — ver `CLAUDE.md`).

## O que foi feito

1. Clonados `creatorup-carrosseis-automation` e `creatorup-ebooks-site` com sucesso (git/GitHub
   segue funcionando normalmente nesta execução).
2. Lido `CLAUDE.md` inteiro e a seção completa "FORMATO CASE DE ESTRATÉGIA" do `SKILL.md`.
3. `getScheduledPosts` (blogId 6553817, janela 2026-07-30, timezone America/Sao_Paulo,
   extendedRange true): **nenhum post do formato "case de estratégia" hoje.** Únicos posts de hoje
   até o momento: 09h11/09h14 (formato `parcerias-marcas`, venda de ebook) e 12h25/12h28
   (formato "atualizações IG/TikTok"). Ou seja, a rotina LOCAL das 14h50 também não deixou rastro
   de publicação hoje — sem duplicata a evitar, segui em frente.
4. Testado o domínio de controle antes de qualquer fonte de foto, como manda o `SKILL.md`:
   ```
   curl -s -o /dev/null -w "%{http_code}" --max-time 8 https://www.google.com
   → 000 (falha de conexão)
   ```
   Confirmado via `$HTTPS_PROXY/__agentproxy/status`, `recentRelayFailures`:
   ```json
   {"ts": "2026-07-30T18:06:11.536Z", "kind": "connect_rejected", "detail": "gateway answered 403 to CONNECT (policy denial or upstream failure)", "host": "www.google.com:443"}
   {"ts": "2026-07-30T18:06:12.061Z", "kind": "connect_rejected", "detail": "gateway answered 403 to CONNECT (policy denial or upstream failure)", "host": "api.openverse.org:443"}
   {"ts": "2026-07-30T18:06:12.377Z", "kind": "connect_rejected", "detail": "gateway answered 403 to CONNECT (policy denial or upstream failure)", "host": "commons.wikimedia.org:443"}
   ```
   Testei Openverse e Wikimedia mesmo assim (antes de ver o status do proxy) e ambos também deram
   `000`/403 no CONNECT — mesmo padrão exato dos dias anteriores. `raw.githubusercontent.com`
   respondeu normalmente (404 numa URL de teste, ou seja, conexão OK, só o caminho testado que não
   existe).

## Onde parou

Teste do domínio de controle + fontes de foto (passo 1 da seção "Como baixar as fotos" do
`SKILL.md`). Não cheguei a pesquisar case nem escolher marca/pessoa do dia, pois a etapa de fotos
bloqueia antes disso ser útil.

## Nada foi publicado

- Nenhuma chamada a `createScheduledPost` (nem Instagram nem TikTok).
- Nenhum case pesquisado/escolhido, nenhuma imagem gerada, nenhum commit/push em
  `creatorup-carrosseis-automation`.

## Ponto de atenção pro usuário

**Sétimo dia seguido (25, 26, 27, 28, 29, 30/07) de bloqueio total do gateway de rede em pelo menos
uma das execuções de nuvem desse formato**, sempre o mesmo padrão (`gateway answered 403 to
CONNECT` mesmo no domínio de controle `google.com`). Também é o **terceiro dia seguido (28, 29,
30/07) em que a rotina LOCAL das 14h50 não deixa rastro de ter publicado** até o horário desta
execução de nuvem — recomendo checar
`C:\Users\Administrador\.claude\local-headless-routines\case-de-estrategia-15h\logs\` na máquina
local pra achar a causa exata de hoje (pode ainda publicar depois, se só está atrasada). Checagem de
status da política de rede já agendada pro usuário em 2026-08-04 (ver `2026-07-28-diagnostico-
rede.md`).

---

# Addendum — rotina de SEGURANÇA/fallback (roda ~30min após o trigger principal) — 2026-07-30

Confirmação independente, mesma checagem repetida por esta execução (fallback, não a principal):

- `getScheduledPosts` de novo: ainda nenhum "case de estratégia" publicado ou pendente para hoje
  (2026-07-30) no momento desta execução (~15h33-15h36 Brasília).
- Teste de controle repetido, resultado idêntico ao da execução principal logo acima:
  ```
  curl -v https://www.google.com → HTTP/1.1 403 Forbidden no CONNECT tunnel (curl exit 56)
  ```
  ```json
  {"ts": "2026-07-30T18:36:10.505Z", "kind": "connect_rejected",
   "detail": "gateway answered 403 to CONNECT (policy denial or upstream failure)",
   "host": "www.google.com:443"}
  ```
- Por essa execução ter vindo depois e já ver o controle falhar, segui a instrução do `SKILL.md` ao
  pé da letra: **não testei Openverse/Wikimedia de novo** (o log da execução principal, acima, já
  confirmou minutos antes que ambos também falham quando o controle falha).
- Nenhuma chamada a `createScheduledPost` foi feita por este fallback. Nenhum case pesquisado,
  nenhuma imagem gerada.

Conclusão desta execução: mesmo bloqueio de rede, sem novidade em relação à execução principal —
apenas confirma que o bloqueio persiste minutos depois, não é uma falha intermitente que já passou.
