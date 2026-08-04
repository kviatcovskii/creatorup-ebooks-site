# 2026-08-04 — formato "atualizações do Instagram/TikTok" (rotina das 12h) — FALHOU

## Onde travou
Passo 2 (`getScheduledPosts` pra conferir duplicata antes de gerar/publicar). O servidor MCP
`Metricool` apareceu na lista de servidores conectando e, assim que tentou ser usado, o harness
retornou explicitamente:

> The following MCP servers require authentication before their tools can be used: Metricool.
> This session is non-interactive, so Claude cannot run the OAuth flow here.

Ou seja, não é bloqueio de rede/egress (como nos incidentes anteriores de 23-28/07) — é autenticação
OAuth do servidor Metricool pendente, que só pode ser concluída numa sessão interativa (`claude mcp`
ou `/mcp`), não em uma execução automática headless como esta.

## O que foi feito com sucesso antes de travar
- `CLAUDE.md` lido por completo.
- Confirmado (via listagem local de `carousels/ig-updates/` no `creatorup-ebooks-site`) que o último
  post gerado foi `2026-08-03-01-instagram-edits-beta-tab` (rede Instagram) — então hoje a rede
  correta a alternar seria TikTok. Isso é só um indício local, não substitui a checagem oficial via
  `getScheduledPosts`.
- Nenhuma pesquisa de atualização real foi feita, nenhuma imagem foi gerada, nenhum commit/push foi
  feito, nenhuma chamada `createScheduledPost` foi feita — sem risco de post incompleto, duplicado ou
  fora de ordem.

## Por que parei em vez de seguir sem confirmar duplicata
Sem `getScheduledPosts`, não dá pra saber se já existe post de hoje nesse formato (regra do passo 2).
E mesmo que desse pra confirmar isso de outra forma, os passos 6-8 (publicar no Instagram e no
TikTok) dependem inteiramente das ferramentas do Metricool, que estão bloqueadas pelo mesmo motivo.
Não tem como completar a tarefa (pesquisar e gerar sem poder publicar seria trabalho perdido, já que
a checagem de duplicata do próximo dia também dependeria do Metricool).

## Próximo passo sugerido
O usuário (ou quem tiver acesso à conta) precisa autorizar o servidor MCP `Metricool` numa sessão
interativa do Claude Code (via `/mcp` ou `claude mcp`) para que as próximas rotinas automáticas
voltem a funcionar. Depois disso, a rotina das 12h pode rodar normalmente do zero (não há nenhum
ativo parcial gerado para reaproveitar desta vez).

Nota: hoje, 2026-08-04, é também a data marcada no `CLAUDE.md` pra checagem do status da política de
egress de rede que travava outras rotinas (case de estratégia das 15h) — isso é uma causa raiz
diferente (autenticação, não rede), mas relevante mencionar já que é o mesmo dia de checagem.
