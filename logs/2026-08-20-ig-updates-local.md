# ig-updates local - 2026-08-20

## Resultado
- Pesquisa real (WebSearch, com fonte): Instagram lançou o recurso "Replace Audio" (troca de
  áudio/música em post ou carrossel JÁ publicado, sem apagar e reenviar). Rede escolhida:
  Instagram (post anterior, 2026-08-19, tinha sido TikTok - carousels/ig-updates/2026-08-19-01-tiktok-cover-title-ia).
- Carrossel de 4 slides gerado (generate_ig_update_post.py), conferido visualmente (slide_01,
  slide_02, slide_03), script revertido depois (git checkout --) conforme instrução.
- Pasta copiada pra creatorup-ebooks-site/carousels/ig-updates/2026-08-20-01-instagram-replace-audio/,
  commit + push OK (826e0ff). As 8 URLs (.png e .jpg via raw.githubusercontent.com) confirmadas
  HTTP 200 antes de publicar.
- createScheduledPost chamado 1x pro Instagram (id 364487648) e 1x pro TikTok (id 364487726),
  blogId 6553817, autoPublish true, agendados pra 2026-08-20T11:55/11:57 America/Sao_Paulo.
  As duas respostas vieram sem erro, status inicial PENDING.

## Pendência - publicação NÃO confirmada ainda
Em 6 checagens sucessivas de getScheduledPosts (a última bem depois da janela normal de
30-40 min descrita no CLAUDE.md pro ciclo completo), os dois posts (id 364487648 Instagram e
364487726 TikTok) continuam com status "PENDING", nunca viraram "PUBLISHED" nem "ERROR".
Pra comparação: o post das 09h25 do mesmo dia (formato diferente, parceria com hotéis) já
apareceu "PUBLISHED" desde a primeira checagem.

**Observação relevante pra quem for investigar depois**: os horários reportados pela ferramenta
de agendamento de wakeup nesta sessão vieram fora de ordem (ex.: um wakeup marcado pra 12:10 foi
seguido por outro marcado pra 12:02, retrocedendo) e o relógio do shell mal avançou entre as
checagens apesar de várias esperas de minutos terem sido solicitadas. Ou seja, não dá pra
confiar que "passou tempo real de verdade" entre as checagens desta sessão - o status PENDING
pode estar correto e ainda dentro da janela normal, não necessariamente uma falha real do
Metricool. Não forcei um novo createScheduledPost (evitando duplicata) e não declarei sucesso.

## Ação recomendada
Conferir getScheduledPosts (blogId 6553817, ids 364487648 e 364487726) numa sessão posterior
pra confirmar se virou PUBLISHED. Se continuar PENDING por muito mais tempo em relação ao
horário real do relógio, ou virar ERROR, investigar causa (mesma categoria dos bloqueios já
documentados no CLAUDE.md - token Metricool, rede, etc.).
