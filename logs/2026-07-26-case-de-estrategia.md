# Rotina 15h "case de estratégia" — 2026-07-26 — NÃO PUBLICADO

## O que foi feito antes de travar

1. Clonados `creatorup-carrosseis-automation` e `creatorup-ebooks-site` com sucesso.
2. Lido `CLAUDE.md` inteiro e a seção completa "FORMATO 'CASE DE ESTRATÉGIA'" (linhas 143-283) do
   `SKILL.md` da skill `creatorup-gerar-carrossel`, incluindo as subseções "Como baixar as fotos",
   "Escolha de foto por slide" e "Já viralizou como inspiração".
3. Chamado `getScheduledPosts` (blogId 6553817, janela de hoje, timezone America/Sao_Paulo) — **sem
   post do formato "case de estratégia" ainda hoje** (só existiam o post das 09h de venda de ebook
   e o das 12h de atualizações do Instagram, nenhum dos dois é este formato). Não havia duplicata,
   rotina liberada pra prosseguir.
4. Ainda **não tinha sido escolhido nenhum case** (parei antes da pesquisa do case, por ordem do
   próprio passo a passo: testar o Openverse primeiro).
5. Testado o Openverse conforme instruído:
   `curl -s -o /dev/null -w "%{http_code}" https://api.openverse.org/v1/images/?q=test`

## Onde parou

Passo 4 do fluxo (teste de acesso ao Openverse, antes de escolher o case/pesquisar fotos).

## Mensagem de erro EXATA

Primeira tentativa (curl simples):
```
Exit code 56
000
```

Segunda tentativa (curl -v, pra diagnosticar):
```
* Uses proxy env variable https_proxy == 'http://127.0.0.1:38757'
* Trying 127.0.0.1:38757...
* Connected to 127.0.0.1 (127.0.0.1) port 38757
* CONNECT tunnel: HTTP/1.1 negotiated
* allocate connect buffer
* Establish HTTP proxy tunnel to api.openverse.org:443
> CONNECT api.openverse.org:443 HTTP/1.1
< HTTP/1.1 403 Forbidden
< Content-Length: 36
* CONNECT tunnel failed, response 403
* Closing connection
000
```

Confirmado via status do proxy (`$HTTPS_PROXY/__agentproxy/status`), campo `recentRelayFailures`:
```json
{
  "ts": "2026-07-26T18:05:54.327Z",
  "kind": "connect_rejected",
  "detail": "gateway answered 403 to CONNECT (policy denial or upstream failure)",
  "host": "api.openverse.org:443"
}
```

## Interpretação

Não veio `200` (nem chegou a haver resposta HTTP do Openverse — o `CONNECT` pro host já foi
recusado pelo gateway/proxy do ambiente). Isso é bloqueio de política de rede do ambiente de nuvem
de hoje, igual ao que já tinha acontecido em 2026-07-17 (motivo original da troca de Wikimedia pra
Openverse). Seguindo a instrução explícita do prompt desta rotina — "se não vier 200, PARE e
reporte que a fonte de foto está bloqueada nesse ambiente hoje (não invente outro método de
scraping)" — a execução foi interrompida aqui, **sem publicar carrossel incompleto ou com fotos de
fonte não confirmada**.

## Nada foi publicado

- Nenhuma chamada a `createScheduledPost` foi feita (nem Instagram nem TikTok).
- Nenhum case foi pesquisado/escolhido ainda.
- Nenhuma imagem foi gerada.
- Nenhum commit/push feito no `creatorup-carrosseis-automation` (só leitura).

## Próximo passo sugerido

Se isso se repetir, considerar (decisão do usuário, não decisão autônoma de infraestrutura):
- Verificar se a política de rede do ambiente de nuvem pode liberar `api.openverse.org` pra este
  trigger, ou
- Definir uma fonte de foto alternativa formalmente documentada no `SKILL.md` pra este formato
  específico (o formato das 12h já resolveu um bloqueio parecido trocando pra foto fixa local, mas
  esse formato de 15h precisa de fotos diferentes por case a cada dia, então a mesma solução não
  se aplica diretamente).
