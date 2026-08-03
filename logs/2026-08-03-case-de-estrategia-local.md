# Case de estratégia — rotina LOCAL — 2026-08-03

**Status: FALHOU no PASSO 0 (sincronização), sem publicar nada.**

Horário: ~14:51 America/Sao_Paulo (17:51 UTC).

## O que foi tentado

`git pull origin main` nos dois repositórios locais, conforme PASSO 0 do prompt:

- `C:\Users\Administrador\Desktop\creatorup-ebooks-site` → **OK**, fast-forward
  (`70543db..cd09417`). Repositório público, pull anônimo não precisa de credencial.
- `C:\Users\Administrador\Desktop\creatorup` → **FALHOU**. Repositório privado, precisa de
  autenticação para `git pull`.

## Erro exato

```
fatal: Unable to persist credentials with the 'wincredman' credential store.
See https://aka.ms/gcm/credstores for more information.
bash: line 1: /dev/tty: No such device or address
error: failed to execute prompt script (exit code 1)
fatal: could not read Password for 'https://kviatcovskii@github.com': No such file or directory
```

## Diagnóstico

`cmdkey /list` retorna **NENHUM** item para `github.com` — não há nenhuma credencial cacheada no
Windows Credential Manager desta máquina (diferente do bug de 2026-07-31 documentado no
`CLAUDE.md`, que era sobre 2 credenciais cacheadas causando um seletor de conta; aqui não há
nenhuma). `credential.https://github.com.username` ainda está configurado como `kviatcovskii`,
mas isso não substitui a senha/token em si.

Sem nenhuma credencial cacheada, o Git Credential Manager tentou abrir um fluxo interativo de
login (provavelmente device flow / browser) e, em modo headless (`claude -p`, sem TTY), esse
fluxo falhou ao tentar acessar `/dev/tty`.

## Por que parei em vez de contornar

Não há forma de completar um login interativo do GitHub (`git credential-manager github login`)
sozinho em modo headless — precisa de input humano (abrir navegador, autorizar). Seguindo a regra
do prompt desta rotina ("se algo travar de verdade... PARE e reporte, não force"), parei aqui sem
gerar case, sem baixar fotos, sem publicar.

## Próximo passo sugerido

Numa sessão interativa nesta máquina, rodar `git pull` uma vez em
`C:\Users\Administrador\Desktop\creatorup` pra disparar o prompt de login do GitHub e completá-lo
manualmente (ou `git credential-manager github login` diretamente), o que deve cachear a
credencial pro Credential Manager e destravar as próximas execuções headless — mesma lógica do
fix de 2026-07-31 pro problema de `git push`, mas agora para leitura (`pull`), num cenário onde a
credencial simplesmente não existe (não é ambiguidade entre duas).

O `RemoteTrigger` de nuvem das 15h (`trig_01XjPEqCnG6pFJWRKeYdooQn`) e seu fallback
(`trig_01V7mnk6Tibnbc7hwuCZtScW`) continuam ativos como rede de segurança — se a rede deles não
estiver bloqueada hoje, devem publicar no lugar desta rotina local.
