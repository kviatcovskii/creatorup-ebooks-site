# 2026-07-28 — formato "atualizações do Instagram/TikTok" (rotina das 12h) — FALHOU

## O que foi feito com sucesso
- Verificado via `getScheduledPosts` que não havia post duplicado desse formato hoje.
- Pesquisado via WebSearch uma atualização real do Instagram: "Comentários com Foto"
  (anexar foto direto no comentário de post/Reels), em rollout gradual desde julho de 2026.
  Fontes: raadworld.com, 24fbr.com, nowbam.com, digitbin.com (July 2026 tracker).
- Rede alternada corretamente (ontem, 2026-07-27, foi TikTok "Drama Center" — hoje Instagram).
- `generate_ig_update_post.py` editado (DATE=2026-07-28, POSTS com a atualização acima, FOLLOW mantido).
- `python3 generate_ig_update_post.py` rodou sem erro, gerou slide_01..04 .png + .jpg em
  `out/ig-updates/2026-07-28-01-instagram-comentarios-foto/` (conferido visualmente, 4 slides corretos:
  capa, miolo com mockup, destaque, segue-a-página).
- Pasta copiada para `/tmp/site-repo/carousels/ig-updates/2026-07-28-01-instagram-comentarios-foto/`,
  `git add` + `git commit` + `git push origin main` — **push confirmado com sucesso**
  (commit `75cb246`, 8 arquivos, `5c68860..75cb246 main -> main`).

## Onde travou
Passo 5 (confirmar `curl -I` HTTP 200 nas 4 URLs .png e .jpg antes de publicar no Metricool).

Toda tentativa de acessar `https://ebooks.creatorup.online/...` a partir deste ambiente falhou:
- `curl` direto: `CONNECT tunnel failed, response 403` (via proxy local).
- Status do proxy (`$HTTPS_PROXY/__agentproxy/status`, `recentRelayFailures`): múltiplas entradas
  `"kind":"connect_rejected","detail":"gateway answered 403 to CONNECT (policy denial or upstream
  failure)","host":"ebooks.creatorup.online:443"`.
- `WebFetch` na mesma URL: `The server returned HTTP 403 Forbidden.`
- Domínio de controle testado em paralelo: `github.com` (400, mas conecta), `api.github.com` (200),
  `raw.githubusercontent.com` (200, confirma que os 4 arquivos estão de fato publicados e acessíveis
  publicamente pelo GitHub) — só `google.com` (000) e o próprio domínio customizado
  `ebooks.creatorup.online` / `*.github.io` retornaram bloqueio.
- `/root/.ccr/README.md` (documentação do proxy deste ambiente) confirma: "403 / 407 from the proxy:
  The destination host is not allowed by your organization's egress policy for this session. Do not
  retry or route around it — report the blocked host."

Ou seja: **não é o CDN/site que está fora do ar** (o conteúdo está confirmado publicado via
`raw.githubusercontent.com`, mesmo commit) — é a política de egress deste sandbox específico que
bloqueia especificamente o domínio customizado `ebooks.creatorup.online` (e `github.io`) nesta
execução, mesmo com `github.com`/`api.github.com` liberados. Isso é diferente dos bloqueios
anteriores documentados (23-27/07), que bloqueavam hosts de fonte de foto (Openverse etc.), nunca o
próprio domínio de destino da publicação.

## Por que parei em vez de publicar mesmo assim
A instrução do formato exige confirmar HTTP 200 nas 4 URLs antes de chamar `createScheduledPost`,
justamente para evitar publicar um carrossel com imagens quebradas (cache de CDN desatualizado). Sem
essa confirmação — e com o próprio proxy deste ambiente instruindo explicitamente a não tentar
contornar um bloqueio de política 403 — não dava pra ter certeza suficiente pra publicar sem risco.
Não editei o `blogId`/URLs pra apontar pra `raw.githubusercontent.com` como substituto, já que isso
sairia do padrão estabelecido do projeto sem confirmação prévia do usuário.

## Estado dos ativos gerados (prontos, não publicados)
- Imagens geradas e já commitadas/pushed em
  `creatorup-ebooks-site/carousels/ig-updates/2026-07-28-01-instagram-comentarios-foto/`
  (slide_01..04.png e .jpg), confirmadas acessíveis via `raw.githubusercontent.com`.
- Nenhuma chamada `createScheduledPost` foi feita (nem Instagram, nem TikTok) — sem risco de post
  incompleto ou duplicado.

## Próximo passo sugerido
Se numa próxima execução (ou rotina de fallback) o mesmo `curl -I` pra `ebooks.creatorup.online`
retornar 200, publicar normalmente usando as URLs já geradas acima (não precisa gerar de novo).
