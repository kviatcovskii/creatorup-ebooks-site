# Rotina de segurança (fallback) 12h "atualizações do Instagram/TikTok" — 2026-08-04 — NÃO PUBLICADO

## Contexto

Rodei ~35-40min depois do trigger principal (`trig_01JXy3oTmwK9bKcu7DFQRm1B`) pra checar se o post
do dia saiu e fazer o fallback manual se não.

## Onde travou

Passo 1 (checagem via `getScheduledPosts`). Mesmo bloqueio que a rotina principal já tinha
registrado em `logs/2026-08-04-ig-updates.md`: o servidor MCP `Metricool` apareceu como "ainda
conectando" e, assim que tentei usar suas ferramentas (`ToolSearch` por `getScheduledPosts`), o
harness retornou explicitamente:

> The following MCP servers require authentication before their tools can be used: Metricool.
> This session is non-interactive, so Claude cannot run the OAuth flow here.

Não é bloqueio de rede/egress (como nos incidentes de 23-28/07) — é autenticação OAuth do servidor
Metricool pendente, que só pode ser concluída numa sessão interativa (`claude mcp` ou `/mcp`), igual
já diagnosticado pela rotina principal de hoje mesmo antes deste fallback rodar.

## O que foi feito com sucesso antes de travar

- `CLAUDE.md` lido por completo.
- Confirmado que a rotina principal das 12h04 já tinha rodado, batido no mesmo bloqueio de
  autenticação Metricool, e parado corretamente sem gerar nem publicar nada (log já commitado por
  ela: `logs/2026-08-04-ig-updates.md`).
- Nenhuma pesquisa de atualização real foi feita, nenhuma imagem foi gerada, nenhum commit em
  `carousels/` foi feito, nenhuma chamada `createScheduledPost` foi feita.

## Por que não gerei conteúdo mesmo sendo o fallback

Sem `getScheduledPosts` não dá pra confirmar duplicata (regra do passo 1), e mesmo que desse, os
passos 5-6 (publicar Instagram e TikTok) dependem inteiramente das mesmas ferramentas Metricool
bloqueadas. Gerar carrossel sem conseguir publicar seria trabalho perdido e deixaria um ativo órfão
em `carousels/ig-updates/` sem post correspondente — mesmo raciocínio já usado no fallback de
2026-07-28 para outro tipo de bloqueio.

## Nada foi publicado (nem pela rotina principal, nem por este fallback)

- Nenhuma chamada a `createScheduledPost` foi feita (nem Instagram, nem TikTok).
- Nenhum conteúdo novo foi gerado.

## Próximo passo sugerido

O usuário precisa autorizar o servidor MCP `Metricool` numa sessão interativa do Claude Code (via
`/mcp` ou `claude mcp`). Isso é bloqueio de conta/autenticação, não vai se resolver sozinho como os
bloqueios de rede anteriores — nenhuma rotina automática vai conseguir publicar (nem checar
duplicata) enquanto isso não for feito. Depois de autorizado, a próxima rotina (principal das 12h de
amanhã, ou uma sessão manual hoje) pode rodar do zero normalmente.
