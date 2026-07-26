# Log de falha — rotina "atualizações do Instagram/TikTok" (12h) — 2026-07-26

## O que foi feito com sucesso
- Clonados `creatorup-carrosseis-automation` e `creatorup-ebooks-site`.
- Lido `CLAUDE.md` e a seção "FORMATO 'ATUALIZAÇÕES DO INSTAGRAM/TIKTOK'" do `SKILL.md`.
- `getScheduledPosts` (blogId 6553817, janela de hoje): confirmado que NÃO havia post desse formato
  ainda hoje (só um post de letreiro e um post de parcerias-com-marcas, formatos diferentes).
- Pesquisa real via WebSearch: feature "Replace Audio" do Instagram (trocar a música de um post/
  carrossel já publicado sem apagar e subir de novo, sem perder curtidas/comentários/alcance),
  anunciada e em rollout desde 21/07/2026. Fontes: TechCrunch
  (https://techcrunch.com/2026/07/21/instagram-now-lets-you-swap-out-the-music-in-your-old-posts/),
  Fast Company, Music Ally. Último post do formato (2026-07-25) foi TikTok, então hoje ficou
  Instagram, alternando a rede corretamente.
- Editado `generate_ig_update_post.py` (DATE=2026-07-26, 1 item em POSTS sobre a feature acima,
  FOLLOW mantido). Rodado `pip install pillow numpy && python3 generate_ig_update_post.py` com
  sucesso: gerados slide_01..04 .png + .jpg irmão em
  `out/ig-updates/2026-07-26-01-instagram-substituir-audio/`. Conferido visualmente
  (Read das imagens) — capa, miolo com mockup, destaque, todos ok, mesmo padrão visual dos dias
  anteriores.
- Copiado pra `creatorup-ebooks-site/carousels/ig-updates/2026-07-26-01-instagram-substituir-audio/`,
  `git add` + commit + push — push confirmado com sucesso (`06a5042..4c48940 main -> main`).
- Commit do script de produção também enviado pro `creatorup-carrosseis-automation`
  (`c663084..6329f9f main -> main`, depois de rebase em cima de um commit não relacionado de outra
  rotina — "docs: validate Higgsfield-only generation for road-sign format (18h)").

## Onde travou
Passo 5, verificação das URLs antes de publicar (`curl -I` nas 4 URLs .png e .jpg).

**Mensagem de erro exata** (endpoint de status do proxy do ambiente,
`http://127.0.0.1:34433/__agentproxy/status`):

```
"recentRelayFailures": [
  {
    "ts": "2026-07-26T15:09:02.819Z",
    "kind": "connect_rejected",
    "detail": "gateway answered 403 to CONNECT (policy denial or upstream failure)",
    "host": "ebooks.creatorup.online:443"
  },
  ... (9 ocorrências repetidas, mesmo detail/host)
]
```

`curl -v -I https://ebooks.creatorup.online/...` (inclusive numa URL antiga de 2026-07-25 que
certamente já existe e já foi publicada com sucesso antes) retorna:

```
* Establish HTTP proxy tunnel to ebooks.creatorup.online:443
> CONNECT ebooks.creatorup.online:443 HTTP/1.1
< HTTP/1.1 403 Forbidden
* CONNECT tunnel failed, response 403
curl: (56) CONNECT tunnel failed, response 403
```

Repeti 4x ao longo de ~1 minuto (política do README: "Do not retry or route around it — report the
blocked host", então não insisti além disso). Todas as tentativas: mesmo 403.

**Não é bloqueio de rede geral do ambiente** — `github.com`, `api.github.com` e
`raw.githubusercontent.com` responderam normalmente (400/301, ou seja, conexão OK) na mesma janela
de tempo. É especificamente o domínio customizado `ebooks.creatorup.online` (o CDN público que serve
as imagens geradas, hospedado via GitHub Pages com CNAME próprio) que a política de rede da nuvem
bloqueou hoje — categoria nova, diferente do bloqueio de Openverse/Pexels/etc. já documentado no
`SKILL.md` (aquele já tinha sido contornado fixando a foto do mockup em asset local; este bloqueia o
próprio domínio de publicação, não uma busca de foto).

## O que NÃO foi feito (parado aqui de propósito)
- NÃO chamei `createScheduledPost` pro Instagram nem pro TikTok. Sem conseguir confirmar as 4 URLs
  com HTTP 200 a partir deste ambiente, publicar seria arriscar um post ao vivo (`autoPublish: true`)
  com mídia potencialmente quebrada — decisão de não forçar publicação incompleta, por instrução
  explícita do prompt da rotina.
- O commit e push das imagens (passo 5) já foi feito e confirmado — as imagens provavelmente ESTÃO
  no CDN (o push funcionou), só não consegui confirmar servindo HTTP 200 a partir deste sandbox por
  causa do bloqueio de rede acima. É possível que o Metricool (infraestrutura externa a este sandbox)
  consiga buscar as imagens normalmente mesmo com este bloqueio sendo específico da política de saída
  deste ambiente — mas isso não foi testado nem confirmado.

## Próximo passo sugerido
Rodar a rotina de novo (ou a fallback, se existir p/ este formato) depois de confirmar que
`ebooks.creatorup.online` está acessível na política de rede do ambiente daquele dia — se persistir,
vale considerar liberar esse domínio especificamente na política de rede da nuvem, já que ele é o
próprio destino de publicação do projeto, não uma dependência externa opcional.
