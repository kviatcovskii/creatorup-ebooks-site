# Rotina LOCAL "case de estratégia" (15h) — 2026-08-04 — NÃO PUBLICADO

## Contexto

Execução da rotina local primária (`case-de-estrategia-15h`), que roda direto nesta máquina desde
2026-07-28 por causa do bloqueio de rede recorrente no sandbox de nuvem (ver `CLAUDE.md`).

## O que foi feito

1. `git pull origin main` com sucesso nos dois repositórios (`creatorup-carrosseis-automation` e
   `creatorup-ebooks-site`) — rede local funcionando normalmente.
2. Lido `CLAUDE.md`.
3. **Bloqueio: o MCP do Metricool não está autorizado nesta sessão.** O ambiente informou
   explicitamente que a sessão é não interativa e não consegue completar o fluxo OAuth do
   Metricool aqui. `ToolSearch` por `mcp__claude_ai_Metricool__getScheduledPosts` e
   `...__createScheduledPost` não retornou nenhuma ferramenta.
4. Ao sincronizar, o pull do `creatorup-ebooks-site` já trouxe
   `logs/2026-08-04-case-de-estrategia-fallback.md`, escrito às 18h07 (Brasília) pela rotina de
   nuvem (fallback) — **mesmo bloqueio exato de Metricool não autorizado**, além de confirmar o
   11º dia seguido do bloqueio de gateway (`gateway answered 403 to CONNECT`) no ambiente de
   nuvem. A rotina local não teve esse segundo problema (rede local ok), só o do Metricool.

## Onde parou

Passo 2 (checar duplicata via `getScheduledPosts`) — antes disso, nem pesquisa de case nem
download de fotos foram feitos, porque publicar seria impossível de qualquer forma sem o
Metricool autorizado.

## Nada foi publicado

- Nenhuma chamada a `getScheduledPosts` ou `createScheduledPost`.
- Nenhum case pesquisado/escolhido, nenhuma foto baixada, nenhuma imagem gerada.
- Nenhum commit/push no `creatorup-carrosseis-automation`.

## Ponto de atenção pro usuário

O conector MCP do Metricool parece ter perdido a autorização/token de acesso — isso afetou **tanto
a rotina local quanto a de nuvem (fallback) no mesmo dia**, então não é um problema de sandbox
específico, é do conector em si. Reautorizar o Metricool nas integrações do Claude Code
(`claude mcp` ou `/mcp` numa sessão interativa) antes da próxima execução, dessa e de qualquer
outra rotina que dependa dele (publicação de carrossel, letreiro, etc.).
