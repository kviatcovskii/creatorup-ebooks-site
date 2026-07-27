# Rotina 15h "case de estratégia" — 2026-07-27 — NÃO PUBLICADO

## O que foi feito antes de travar

1. Clonados `creatorup-carrosseis-automation` e `creatorup-ebooks-site` com sucesso.
2. Lido `CLAUDE.md` inteiro e a seção completa "FORMATO 'CASE DE ESTRATÉGIA'" (linhas 143-283) do
   `SKILL.md` da skill `creatorup-gerar-carrossel`, incluindo as subseções "Como baixar as fotos",
   "Escolha de foto por slide" e "'Já viralizou' como inspiração".
3. Checado `git log` do `SKILL.md` — nenhum commit reintroduz Wikimedia como fonte válida desde a
   troca definitiva pra Openverse (72e045a, 2026-07-17). A seção "Como baixar as fotos" no arquivo
   real continua dizendo pra descartar qualquer resultado de domínio `wikipedia.org`/`wikimedia.org`
   por bloqueio de rede.
4. Chamado `getScheduledPosts` (blogId 6553817, janela de hoje, timezone America/Sao_Paulo) — **sem
   post do formato "case de estratégia" ainda hoje** (só existiam posts de venda de ebook das 09h e
   de atualizações IG/TikTok das 12h, nenhum dos dois é este formato). Sem duplicata, rotina
   liberada pra prosseguir.
5. **Discrepância encontrada antes de prosseguir**: o prompt desta rotina afirma "testado
   2026-07-26: as duas fontes estavam acessíveis nesse dia". Isso contradiz diretamente o log já
   commitado neste mesmo repositório em `logs/2026-07-26-case-de-estrategia.md`, que documenta (com
   evidência de `curl -v` e do status do proxy) que **as duas fontes estavam bloqueadas em
   2026-07-26**, inclusive um domínio de controle (`google.com`) também bloqueado naquele dia. Não
   encontrei nenhum commit no `SKILL.md` refletindo uma atualização real dessa seção em 2026-07-26.
   Segui o arquivo real (fonte de verdade do projeto, por instrução do próprio prompt desta rotina:
   "não confie em conhecimento de execuções anteriores, use sempre a versão atual do SKILL.md") em
   vez da afirmação do prompt, e testei conectividade de forma independente antes de decidir
   qualquer coisa.
6. Testado Openverse, Wikimedia Commons e um domínio de controle (google.com) hoje:
   ```
   curl -s -o /dev/null -w "%{http_code}" --max-time 8 https://api.openverse.org/v1/images/?q=test
   → 000 (curl exit 56)

   curl -s -o /dev/null -w "%{http_code}" --max-time 8 https://commons.wikimedia.org/w/api.php?action=query&format=json
   → 000

   curl -s -o /dev/null -w "%{http_code}" --max-time 8 https://www.google.com
   → 000
   ```
   Confirmado via `$HTTPS_PROXY/__agentproxy/status`, campo `recentRelayFailures`:
   ```json
   {"ts": "2026-07-27T18:07:31.632Z", "kind": "connect_rejected", "detail": "gateway answered 403 to CONNECT (policy denial or upstream failure)", "host": "api.openverse.org:443"},
   {"ts": "2026-07-27T18:07:32.059Z", "kind": "connect_rejected", "detail": "gateway answered 403 to CONNECT (policy denial or upstream failure)", "host": "commons.wikimedia.org:443"},
   {"ts": "2026-07-27T18:07:32.391Z", "kind": "connect_rejected", "detail": "gateway answered 403 to CONNECT (policy denial or upstream failure)", "host": "www.google.com:443"}
   ```

## Onde parou

Teste de conectividade das fontes de foto, antes de escolher o case do dia (mesmo ponto onde a
rotina principal parou em 2026-07-26).

## Interpretação

Bloqueio de política de rede do ambiente de nuvem de hoje, igual ao de 2026-07-26 — inclusive o
domínio de controle (`google.com`) também recusado no `CONNECT`, o que descarta bloqueio específico
de Openverse/Wikimedia e confirma bloqueio geral do gateway pra este ambiente/dia. Seguindo a
instrução explícita do `SKILL.md` (não inventar outro método de scraping quando a fonte de foto
está bloqueada), a execução foi interrompida aqui, **sem pesquisar case, sem baixar foto, sem gerar
imagem, sem publicar carrossel incompleto ou com foto de fonte não confirmada**.

## Nada foi publicado

- Nenhuma chamada a `createScheduledPost` foi feita (nem Instagram nem TikTok).
- Nenhum case foi pesquisado/escolhido.
- Nenhuma imagem foi gerada.
- Nenhum commit/push feito no `creatorup-carrosseis-automation`.

## Ponto de atenção pro usuário

O prompt armazenado desta rotina (`RemoteTrigger` das 15h) contém uma afirmação factual sobre o
estado da rede em 2026-07-26 que não bate com o log já commitado no repositório sobre esse mesmo
dia. Vale revisar o texto desse prompt agendado — o trecho sobre Wikimedia ter sido "reintroduzida
como 2ª tentativa em 2026-07-26" parece não corresponder ao que de fato foi testado/commitado nesse
dia.
