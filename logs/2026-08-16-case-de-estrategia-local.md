# Case de estratégia (15h) — rotina LOCAL — 2026-08-16

**Status: PAROU no PASSO 2 (checagem de duplicata via Metricool). Nenhum post publicado, nenhuma
imagem gerada.**

## O que foi feito antes de parar

1. PASSO 0: `git pull origin main` em `creatorup` e `creatorup-ebooks-site` — ambos já atualizados,
   sem conflito.
2. PASSO 1: lido `CLAUDE.md` raiz do projeto e a seção completa "FORMATO 'CASE DE ESTRATÉGIA'"
   (incluindo "Como baixar as fotos", "Escolha de foto por slide" e "'Já viralizou'") em
   `.claude\skills\creatorup-gerar-carrossel\SKILL.md`.
3. PASSO 2: tentei chamar `mcp__claude_ai_Metricool__getScheduledPosts` (blogId "6553817") pra
   conferir se já existe post de hoje desse formato. **A ferramenta não está disponível nesta
   sessão.**

## Erro exato

Ao buscar a tool via `ToolSearch`, o sistema retornou:

> The following MCP servers require authentication before their tools can be used: claude.ai
> Metricool [...] This session is non-interactive, so Claude cannot run the OAuth flow here.

Nenhuma tool `mcp__claude_ai_Metricool__*` apareceu na lista de ferramentas disponíveis desta
sessão headless.

## Diagnóstico (já documentado no `CLAUDE.md`, seção "Metricool MCP some nas execuções não
interativas")

Esta é a MESMA causa raiz já confirmada em 2026-08-04 e recorrente em 2026-08-10: o token OAuth do
conector Metricool expira para sessões não interativas (`claude -p` headless). Uma sessão
interativa consegue renovar sozinha (por isso `claude mcp list` costuma mostrar "Connected"), a
headless não consegue — e o harness remove o servidor da lista de ferramentas em vez de reportar
erro visível. Não é bug desta rotina nem do formato "case de estratégia" especificamente.

## Ação necessária (só o usuário consegue fazer)

Reautorizar o conector Metricool: `claude` → `/mcp` → `claude.ai Metricool` → reconectar, OU
claude.ai → Settings → Connectors → Metricool → desconectar e conectar de novo. Validar depois com
o teste headless documentado no `CLAUDE.md` antes de assumir que resolveu:

```
claude -p "Use ToolSearch pra procurar getScheduledPosts do Metricool. Responda so SIM ou NAO."
```

## Impacto

Nenhum post do formato "case de estratégia" foi publicado hoje (16/08) por esta rotina local. O
`RemoteTrigger` de nuvem das 15h e seu fallback continuam ativos como rede de segurança — se
também dependerem do mesmo conector Metricool, provavelmente vão falhar pela mesma causa. Vale
conferir o Metricool manualmente mais tarde pra confirmar se o post de hoje saiu por algum outro
caminho.
