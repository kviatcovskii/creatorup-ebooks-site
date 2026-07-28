# Rotina 15h "case de estratégia" — 2026-07-28 — NÃO PUBLICADO

## O que foi feito antes de travar

1. Clonados `creatorup-carrosseis-automation` e `creatorup-ebooks-site` com sucesso.
2. Lido `CLAUDE.md` inteiro. **Nenhuma menção a uma "rotina local primária às 14h50" pra este
   formato** — o prompt desta rotina afirmava que a partir de hoje existiria uma rotina local
   rodando direto na máquina do usuário, substituindo esta execução de nuvem como principal. O
   `CLAUDE.md` (fonte de verdade do projeto, que por regra própria do arquivo recebe qualquer
   mudança operacional permanente) não documenta isso em lugar nenhum, apesar de documentar outras
   mudanças de 2026-07-28 (troca de domínio de mídia pra `raw.githubusercontent.com`).
3. Lida a seção completa "FORMATO 'CASE DE ESTRATÉGIA'" (linhas 143-311) do `SKILL.md` da skill
   `creatorup-gerar-carrossel`, incluindo "Como baixar as fotos", "Escolha de foto por slide" e
   "'Já viralizou' como inspiração".
4. Chamado `getScheduledPosts` (blogId 6553817, janela de hoje 2026-07-28, timezone
   America/Sao_Paulo, extendedRange true) — **sem post do formato "case de estratégia" (8+ slides,
   sem venda de ebook) hoje**. Os únicos posts de hoje até o momento são: 09h17/09h18 (formato de
   venda de ebook, 6 slides, menciona "comenta CREATOR que eu mando o ebook") e 14h52/14h53
   (formato "atualizações IG/TikTok", 4 slides). Nenhum dos dois é este formato, e nenhuma rotina
   local publicou o case de hoje. Sem duplicata, rotina liberada pra prosseguir (a suposta cobertura
   pela rotina local de 14h50 não se confirmou).
5. Testado o domínio de controle (`google.com`) ANTES de qualquer fonte de foto, como manda a
   correção de 2026-07-27 do `SKILL.md`:
   ```
   curl -s -o /dev/null -w "%{http_code}" --max-time 8 https://www.google.com
   → 000 (falha de conexão)
   ```
   Confirmado via `$HTTPS_PROXY/__agentproxy/status`, campo `recentRelayFailures`:
   ```json
   {"ts": "2026-07-28T18:06:25.374Z", "kind": "connect_rejected", "detail": "gateway answered 403 to CONNECT (policy denial or upstream failure)", "host": "www.google.com:443"}
   ```
   Seguindo a instrução do próprio `SKILL.md` ("se o controle já falhou, pule direto pro passo 5,
   não perca tempo testando Openverse/Wikimedia individualmente"), **não testei Openverse nem
   Wikimedia separadamente** — o controle já confirma bloqueio geral do gateway desta execução.

## Onde parou

Teste do domínio de controle, antes de escolher o case do dia e antes de testar qualquer fonte de
foto. Mesmo ponto (bloqueio geral confirmado pelo controle) onde a rotina parou em 2026-07-26 e
2026-07-27.

## Interpretação

Bloqueio de política de rede do ambiente de nuvem de hoje, quarto dia seguido (25, 26, 27 e agora
28/07) com o mesmo padrão: `gateway answered 403 to CONNECT`. Seguindo a instrução explícita do
`SKILL.md` (não inventar outro método de scraping quando a fonte de foto está bloqueada, não
publicar carrossel incompleto), a execução foi interrompida aqui, **sem pesquisar case, sem baixar
foto, sem gerar imagem, sem publicar carrossel incompleto ou com foto de fonte não confirmada**.

## Nada foi publicado

- Nenhuma chamada a `createScheduledPost` foi feita (nem Instagram nem TikTok).
- Nenhum case foi pesquisado/escolhido.
- Nenhuma imagem foi gerada.
- Nenhum commit/push feito no `creatorup-carrosseis-automation`.

## Ponto de atenção pro usuário

O prompt armazenado desta rotina (`RemoteTrigger` das 15h) descreve uma "rotina LOCAL primária às
14h50" criada por causa do bloqueio de rede dos dias 25-27/07, com esta execução de nuvem virando
apenas rede de segurança. Essa mudança operacional não está documentada no `CLAUDE.md` do
repositório (que é onde, pela regra própria do projeto, qualquer correção permanente deveria ser
propagada) e nenhum post do formato apareceu no Metricool hoje até a hora desta execução — ou seja,
se a rotina local existe, ela não publicou hoje (ou não existe de fato). Recomendo confirmar se essa
rotina local está mesmo configurada e rodando antes de contar com ela pra cobrir os próximos dias.
Independente disso, o bloqueio de rede de hoje é real e confirmado (ver evidência acima), quarto dia
seguido — vale investigar a política de egress do ambiente de nuvem usado por este `RemoteTrigger`.
