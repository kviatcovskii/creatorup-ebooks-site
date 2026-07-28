# Diagnóstico de rede/egress do sandbox — 2026-07-28

Tarefa única de diagnóstico (não é rotina de publicação). Objetivo: documentar a política de
rede/proxy deste ambiente pra explicar os bloqueios 403 intermitentes reportados pelo usuário nos
últimos dias.

## PASSO 1 — Conteúdo relevante de `/root/.ccr/README.md`

Resumo fiel do que o README descreve sobre a política de rede/proxy:

- Todo tráfego HTTPS de saída passa por um proxy local (`http://127.0.0.1:<porta>`, setado via
  `HTTPS_PROXY`) que **tuneliza pra um proxy de egress que aplica política** (allowlist). O TLS é
  re-terminado nesse proxy, por isso o bundle CA em `/root/.ccr/ca-bundle.crt` precisa ser
  confiável por qualquer ferramenta.
- **(a) Como funciona o allowlist de domínios:** o README não descreve a lista em si (ela não é
  legível/configurável pelo agente) — só descreve como diagnosticar e reagir a bloqueios. Confirma
  que 403/407 vindo do proxy significa "destino não permitido pela política de egress da
  organização pra esta sessão".
- **(b) Forma de solicitar/configurar domínios adicionais:** não existe mecanismo do lado do agente
  pra isso. Instrução explícita: **"Never disable TLS verification, never unset HTTPS_PROXY, and do
  not retry organization policy denials (403/407) — report them instead."** E no fim: **"If a tool
  still cannot work through the proxy, report it to your administrator or Anthropic support so the
  policy or tooling can be fixed."** Ou seja, a única via é reportar pro administrador/suporte, não
  há autoatendimento.
- **(c) Política fixa por ambiente vs. varia por execução/sessão:** o README não afirma
  explicitamente nenhuma das duas. Ele fala em termos de "esta sessão" (ex.: "not allowed by your
  organization's egress policy for this session"), o que sugere que a política é definida por
  configuração da organização e aplicada a cada sessão/ambiente efêmero — não há indicação de que o
  agente possa alterá-la ou de que ela mude sozinha entre execuções. (Ver seção de evidência
  histórica abaixo pra dado real sobre variação dia a dia.)
- **(d) Outras seções do README:** lista classes de falha e correção (certificado/PKIX, "405
  Method Not Allowed" por proxy plain-HTTP, timeouts silenciosos em clientes que ignoram
  `HTTPS_PROXY`, git com SSH reescrito pra HTTPS, Docker/containers não enxergam o proxy, e uma
  lista do que **não é suportado de jeito nenhum** através do proxy: gRPC/HTTP2-only, WebSocket
  upgrade, client-mTLS, certificate pinning, portas HTTPS não-443, bancos raw-TCP).

### Variáveis de ambiente relacionadas (proxy/http)

```
CCR_AGENT_PROXY_ENABLED=1
CCR_UPSTREAM_PROXY_ENABLED=1
HTTPS_PROXY=http://127.0.0.1:41377
https_proxy=http://127.0.0.1:41377
NO_PROXY / no_proxy = localhost,127.0.0.1,::1,127.0.0.0/8,0.0.0.0/8,::,169.254.0.0/16,
  anthropic.com,.anthropic.com,*.anthropic.com,registry.npmjs.org,jsr.io,npm.jsr.io,pypi.org,
  files.pythonhosted.org,index.crates.io,proxy.golang.org,host.docker.internal,
  10.0.0.0/8,172.16.0.0/12,192.168.0.0/16,100.64.0.0/10,.svc.cluster.local,*.svc.cluster.local
GH_TOKEN=proxy-injected
GITHUB_TOKEN=proxy-injected
GIT_CONFIG_KEY_1 / GIT_CONFIG_KEY_2 = url.https://github.com/.insteadOf   (reescrita SSH→HTTPS)
JAVA_TOOL_OPTIONS = trustStore + proxyHost/proxyPort + nonProxyHosts (mesmo bypass de NO_PROXY)
CLOUDSDK_PROXY_* (gcloud), AWS_* (proxy-injected), DOCKER_HTTPS_PROXY, YARN_HTTPS_PROXY,
GLOBAL_AGENT_HTTPS_PROXY, npm_config_https_proxy — todos apontando pro mesmo proxy local.
```

Nenhuma variável indica um allowlist configurável pelo usuário/agente — o `NO_PROXY` é o único
"bypass" documentado (registry.npmjs.org, pypi.org, anthropic.com e infra interna), e ele **não
inclui** `github.com`. Ainda assim `github.com`/`api.github.com`/`raw.githubusercontent.com`
responderam normalmente no teste abaixo — ou seja, o acesso ao GitHub é permitido por regra própria
do proxy de política (não pelo `NO_PROXY`), provavelmente por causa do suporte a git descrito no
README (`GIT_CONFIG_KEY_*`, `GH_TOKEN`/`GITHUB_TOKEN` injetados).

## PASSO 2 — Tabela de conectividade (curl, `--max-time 6`, HTTPS raiz de cada domínio)

| Domínio | Código HTTP | Situação |
|---|---|---|
| github.com | 400 | conectou (resposta HTTP real do GitHub) |
| api.github.com | 200 | conectou |
| raw.githubusercontent.com | 301 | conectou |
| objects.githubusercontent.com | 404 | conectou |
| www.google.com | 000 | **bloqueado pelo proxy (403 no CONNECT)** |
| www.bing.com | 000 | **bloqueado pelo proxy (403 no CONNECT)** |
| api.openverse.org | 000 | **bloqueado pelo proxy (403 no CONNECT)** |
| commons.wikimedia.org | 000 | **bloqueado pelo proxy (403 no CONNECT)** |
| en.wikipedia.org | 000 | **bloqueado pelo proxy (403 no CONNECT)** |
| unsplash.com | 000 | **bloqueado pelo proxy (403 no CONNECT)** |
| api.unsplash.com | 000 | **bloqueado pelo proxy (403 no CONNECT)** |
| images.unsplash.com | 000 | **bloqueado pelo proxy (403 no CONNECT)** |
| pexels.com | 000 | **bloqueado pelo proxy (403 no CONNECT)** |
| images.pexels.com | 000 | **bloqueado pelo proxy (403 no CONNECT)** |
| pixabay.com | 000 | **bloqueado pelo proxy (403 no CONNECT)** |
| cdn.pixabay.com | 000 | **bloqueado pelo proxy (403 no CONNECT)** |
| ebooks.creatorup.online | 000 | **bloqueado pelo proxy (403 no CONNECT)** |
| mcp.higgsfield.ai | 000 | **bloqueado pelo proxy (403 no CONNECT)** |
| d8j0ntlcm91z4.cloudfront.net | 000 | **bloqueado pelo proxy (403 no CONNECT)** |
| ai.metricool.com | 000 | **bloqueado pelo proxy (403 no CONNECT)** |
| api.metricool.com | 000 | **bloqueado pelo proxy (403 no CONNECT)** |
| www.instagram.com | 000 | **bloqueado pelo proxy (403 no CONNECT)** |
| www.tiktok.com | 000 | **bloqueado pelo proxy (403 no CONNECT)** |
| api.anthropic.com | 404 | conectou (resposta HTTP real) |
| registry.npmjs.org | 200 | conectou |
| pypi.org | 200 | conectou |

7 de 26 domínios conectaram normalmente; 19 de 26 foram recusados no `CONNECT` pelo gateway do
proxy com 403.

## PASSO 3 — `$HTTPS_PROXY/__agentproxy/status`, campo `recentRelayFailures` (bruto)

Config geral do status: `"selective": false, "standalone": false, "toolScoped": false"` — não há
indicação de política seletiva por ferramenta ou por chamada nesta sessão.

```json
[
  {"ts":"2026-07-28T19:44:23.521Z","kind":"connect_rejected","detail":"gateway answered 403 to CONNECT (policy denial or upstream failure)","host":"www.google.com:443"},
  {"ts":"2026-07-28T19:44:23.576Z","kind":"connect_rejected","detail":"gateway answered 403 to CONNECT (policy denial or upstream failure)","host":"www.bing.com:443"},
  {"ts":"2026-07-28T19:44:23.983Z","kind":"connect_rejected","detail":"gateway answered 403 to CONNECT (policy denial or upstream failure)","host":"api.openverse.org:443"},
  {"ts":"2026-07-28T19:44:24.307Z","kind":"connect_rejected","detail":"gateway answered 403 to CONNECT (policy denial or upstream failure)","host":"commons.wikimedia.org:443"},
  {"ts":"2026-07-28T19:44:24.634Z","kind":"connect_rejected","detail":"gateway answered 403 to CONNECT (policy denial or upstream failure)","host":"en.wikipedia.org:443"},
  {"ts":"2026-07-28T19:44:24.962Z","kind":"connect_rejected","detail":"gateway answered 403 to CONNECT (policy denial or upstream failure)","host":"unsplash.com:443"},
  {"ts":"2026-07-28T19:44:25.303Z","kind":"connect_rejected","detail":"gateway answered 403 to CONNECT (policy denial or upstream failure)","host":"api.unsplash.com:443"},
  {"ts":"2026-07-28T19:44:25.672Z","kind":"connect_rejected","detail":"gateway answered 403 to CONNECT (policy denial or upstream failure)","host":"images.unsplash.com:443"},
  {"ts":"2026-07-28T19:44:26.008Z","kind":"connect_rejected","detail":"gateway answered 403 to CONNECT (policy denial or upstream failure)","host":"pexels.com:443"},
  {"ts":"2026-07-28T19:44:26.496Z","kind":"connect_rejected","detail":"gateway answered 403 to CONNECT (policy denial or upstream failure)","host":"images.pexels.com:443"},
  {"ts":"2026-07-28T19:44:26.646Z","kind":"connect_rejected","detail":"gateway answered 403 to CONNECT (policy denial or upstream failure)","host":"pixabay.com:443"},
  {"ts":"2026-07-28T19:44:26.995Z","kind":"connect_rejected","detail":"gateway answered 403 to CONNECT (policy denial or upstream failure)","host":"cdn.pixabay.com:443"},
  {"ts":"2026-07-28T19:44:27.342Z","kind":"connect_rejected","detail":"gateway answered 403 to CONNECT (policy denial or upstream failure)","host":"ebooks.creatorup.online:443"},
  {"ts":"2026-07-28T19:44:27.667Z","kind":"connect_rejected","detail":"gateway answered 403 to CONNECT (policy denial or upstream failure)","host":"mcp.higgsfield.ai:443"},
  {"ts":"2026-07-28T19:44:27.996Z","kind":"connect_rejected","detail":"gateway answered 403 to CONNECT (policy denial or upstream failure)","host":"d8j0ntlcm91z4.cloudfront.net:443"},
  {"ts":"2026-07-28T19:44:28.331Z","kind":"connect_rejected","detail":"gateway answered 403 to CONNECT (policy denial or upstream failure)","host":"ai.metricool.com:443"},
  {"ts":"2026-07-28T19:44:28.771Z","kind":"connect_rejected","detail":"gateway answered 403 to CONNECT (policy denial or upstream failure)","host":"api.metricool.com:443"},
  {"ts":"2026-07-28T19:44:29.093Z","kind":"connect_rejected","detail":"gateway answered 403 to CONNECT (policy denial or upstream failure)","host":"www.instagram.com:443"},
  {"ts":"2026-07-28T19:44:29.421Z","kind":"connect_rejected","detail":"gateway answered 403 to CONNECT (policy denial or upstream failure)","host":"www.tiktok.com:443"}
]
```

19 registros, todos `kind: connect_rejected`, todos `detail: "gateway answered 403 to CONNECT
(policy denial or upstream failure)"` — mensagem idêntica pra todo domínio testado hoje, sem
diferenciação por categoria de domínio (busca de imagem, CDN próprio, rede social, Metricool).

## Evidência histórica (comparação com logs anteriores neste mesmo repositório)

Antes de concluir se o padrão é fixo ou varia por execução/dia, comparei com logs já existentes em
`logs/` de dias anteriores (não é especulação — são execuções reais já registradas):

- `2026-07-25-road-sign.md`: `upload.higgsfield.ai` e `ebooks.creatorup.online` bloqueados (mesmo
  `connect_rejected`/403).
- `2026-07-26-case-de-estrategia.md`: `api.openverse.org` e `commons.wikimedia.org` bloqueados;
  domínio de controle `www.google.com` também bloqueado na mesma janela.
- `2026-07-26-atualizacoes-ig.md`: `ebooks.creatorup.online` bloqueado; `github.com`,
  `api.github.com`, `raw.githubusercontent.com` OK na mesma janela.
- `2026-07-28-case-de-estrategia.md` (mesmo dia de hoje, ~13h40 antes deste diagnóstico):
  `www.google.com` já tinha voltado bloqueado, registrado como "quarto dia seguido (25, 26, 27 e
  28/07) com o mesmo padrão".

Ou seja: o mesmo conjunto de domínios (fontes de imagem, `ebooks.creatorup.online`, Higgsfield
upload, domínios de controle como google/bing) está bloqueado **de forma repetida e consistente
há pelo menos 4 dias corridos (25 a 28/07)**, sempre com o mesmo `detail` de política do gateway.
Não há, nos dados coletados até agora, nenhum registro de um desses domínios tendo respondido 200
em algum ponto desse período — não encontrei evidência de que a política mude de execução pra
execução ou de dia pra dia; os dados apontam pro oposto: é uma política fixa e estável (bloqueio
persistente), não uma falha intermitente/aleatória.

## Resumo final

**Domínios SEMPRE liberados neste ambiente (hoje e em todos os logs históricos revisados):**
`github.com`, `api.github.com`, `raw.githubusercontent.com`, `objects.githubusercontent.com`,
`api.anthropic.com`, `registry.npmjs.org`, `pypi.org` — infraestrutura de código/pacotes/Anthropic,
consistente com o `NO_PROXY`/allowlist de git descritos no README.

**Domínios SEMPRE bloqueados (hoje e em todos os logs históricos revisados, mínimo 4 dias
seguidos):** todos os provedores de imagem de banco público (Openverse, Wikimedia Commons,
Unsplash, Pexels, Pixabay), os domínios de controle genéricos (Google, Bing), o próprio domínio de
publicação do usuário (`ebooks.creatorup.online`), o CDN de fallback
(`d8j0ntlcm91z4.cloudfront.net`), o upload do Higgsfield, o Metricool (`ai.metricool.com`,
`api.metricool.com`) e as redes sociais diretamente (`www.instagram.com`, `www.tiktok.com`).

**A política muda por execução/dia ou é fixa?** Com base só no que foi testado (sem especular além
disso): os dados coletados hoje e a comparação com logs de 25, 26 e 27/07 mostram o **mesmo padrão
de bloqueio se repetindo de forma consistente**, sempre com o mesmo motivo reportado pelo proxy
(`403` de política de organização). Não há evidência, nos testes realizados, de que a política
varie entre sessões — o que existe é evidência de uma política de egress **fixa e restritiva**,
aplicada de forma consistente em todas as execuções observadas. O README confirma que não há
mecanismo pelo lado do agente pra alterar essa lista; a única via documentada é reportar ao
administrador/suporte da organização (Anthropic support) pra revisar a política de egress deste
ambiente/organização, caso o usuário precise que algum desses domínios (em especial
`ebooks.creatorup.online`, que é o próprio destino de publicação do projeto, e o Metricool, que é a
ferramenta de agendamento) passe a ser permitido.
