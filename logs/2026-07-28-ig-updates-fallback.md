# Rotina de segurança (fallback) 12h "atualizações do Instagram/TikTok" — 2026-07-28 — NÃO PUBLICADO

## Contexto

Rodei ~35-40min depois do trigger principal (`trig_01JXy3oTmwK9bKcu7DFQRm1B`) pra checar se o post
do dia saiu e fazer o fallback manual se não. Confirmei via `getScheduledPosts` (blogId 6553817,
timezone Europe/Madrid, hoje com extendedRange) que **nenhum post do formato "atualizações"
existe hoje** — só o carrossel de venda das 09h ("monetização digital"), publicado normalmente.

## Achado: rotina principal já tinha rodado e parado num ponto específico

Antes de gerar conteúdo novo, encontrei `logs/2026-07-28-ig-updates.md` (já commitado pela rotina
principal, ~15:22 UTC). Ela pesquisou uma atualização real ("Comentários com Foto" no Instagram,
rollout desde julho/2026 — raadworld.com, 24fbr.com, nowbam.com, digitbin.com), alternou a rede
corretamente (ontem foi TikTok, hoje Instagram), gerou os 4 slides
(`carousels/ig-updates/2026-07-28-01-instagram-comentarios-foto/slide_01..04.png` + `.jpg`) e fez
commit+push com sucesso (`75cb246`). Travou só no passo de confirmação `curl -I` HTTP 200 nas URLs
do CDN antes de chamar `createScheduledPost` — bloqueio de política de egress específico do host
`ebooks.creatorup.online` (e `*.github.io`) nesse sandbox, mesmo com `github.com`/`api.github.com`
liberados. Ela confirmou os arquivos via `raw.githubusercontent.com` (200) mas não substituiu a URL
de publicação sem validação prévia do usuário, e corretamente não chamou `createScheduledPost`.

## O que eu testei neste fallback

Reproduzi o mesmo teste de forma independente, no meu próprio sandbox:

```
curl -sS -o /dev/null -w "HTTP %{http_code}" -I \
  https://ebooks.creatorup.online/carousels/ig-updates/2026-07-28-01-instagram-comentarios-foto/slide_01.png
→ HTTP 000 (curl exit 56, CONNECT tunnel failed, response 403)
```

Confirmado via `$HTTPS_PROXY/__agentproxy/status`:

```json
{"ts": "2026-07-28T15:42:12.401Z", "kind": "connect_rejected",
 "detail": "gateway answered 403 to CONNECT (policy denial or upstream failure)",
 "host": "ebooks.creatorup.online:443"}
```

Mesmo bloqueio de política de egress, mesmo host — não é intermitente nem específico do sandbox da
rotina principal, persiste neste sandbox de fallback também. `/root/.ccr/README.md` deste ambiente
instrui explicitamente a não tentar contornar bloqueio de política 403 nem trocar de host de
publicação sem confirmação prévia.

## Por que não gerei conteúdo novo nem publiquei mesmo assim

O conteúdo do dia já existe, é real (pesquisa com fonte), já está no formato certo (4 slides, rede
corretamente alternada) e já está commitado/pushed — gerar de novo seria trabalho duplicado e
arriscaria um segundo carrossel divergente pro mesmo dia. Publicar sem a confirmação HTTP 200 exigida
pelo formato arriscaria postar um carrossel com imagem quebrada por cache de CDN desatualizado.
Trocar a URL de mídia pra `raw.githubusercontent.com` sairia do padrão estabelecido do projeto
(domínio de publicação sempre `ebooks.creatorup.online`) sem confirmação prévia do usuário.

## Onde parei

Mesmo ponto onde a rotina principal já tinha parado: confirmação `curl -I` HTTP 200 das URLs do CDN,
antes de qualquer chamada `createScheduledPost`.

## Nada foi publicado (nem pela rotina principal, nem por este fallback)

- Nenhuma chamada a `createScheduledPost` foi feita (nem Instagram, nem TikTok).
- Nenhum conteúdo novo foi gerado, nenhum commit adicional em `carousels/` (só este log).

## Estado dos ativos gerados (prontos, não publicados)

- `carousels/ig-updates/2026-07-28-01-instagram-comentarios-foto/slide_01..04.png` e `.jpg`,
  confirmados acessíveis via `raw.githubusercontent.com`, aguardando o bloqueio de
  `ebooks.creatorup.online` liberar neste ambiente de nuvem.

## Próximo passo sugerido

Numa próxima execução (rotina principal de amanhã ou outro fallback), testar de novo
`curl -I https://ebooks.creatorup.online/...`. Se voltar 200, publicar usando as URLs já geradas
acima (não precisa gerar de novo). Se o bloqueio persistir por vários dias seguidos, vale o usuário
revisar a política de egress do ambiente de nuvem pra liberar esse domínio especificamente, já que
`github.com`/`api.github.com` estão liberados mas o domínio customizado de publicação não.
