---
name: painel-de-tempo
description: Instala sozinho o Painel de Tempo e conecta na Google Agenda da pessoa. É uma tela para TV ou monitor com o compromisso de agora em letra grande, contagem regressiva, tarefas, o que veio antes, o que vem depois e alerta vermelho nos últimos 10 minutos. Você prepara o computador, cria a conexão no Google (Apps Script, só leitura), publica e abre o painel; a pessoa só clica nas telas do Google. Aciona quando a pessoa disser instala o painel de tempo, painel de tempo, painel na TV, contagem regressiva da agenda, mostrar minha agenda na televisão, timer do compromisso, painel do Google Agenda.
---

# Painel de Tempo: instalação autônoma

A pessoa só pediu. **Você faz tudo**: prepara o computador, cria a conexão na Google Agenda dela, publica e abre o painel. Ela só clica nas telas do Google que abrirem no navegador.

Fale com ela em português simples, sem termo técnico, uma instrução por vez. Não peça para ela abrir programa, editar arquivo ou digitar comando.

O que tem nesta pasta:
- `painel/index.html`: o painel (também publicado em https://mottihomero-rgb.github.io/painel-de-tempo/painel/).
- `google/Codigo.gs` e `google/appsscript.json`: a conexão com a agenda. O manifesto pede **só leitura** da agenda e já deixa o app da Web pronto (executa como a pessoa, acesso por endereço).
- `index.html`: a página com o tutorial para humanos.

## 0. Começo

Diga em uma frase o que vai acontecer: "Vou instalar o painel e ligar na sua agenda. Vão abrir 3 telas do Google no navegador; eu aviso o que clicar em cada uma."

Se esta pasta ainda não estiver em `~/.claude/skills/painel-de-tempo`, coloque: `git clone https://github.com/mottihomero-rgb/painel-de-tempo ~/.claude/skills/painel-de-tempo` (sem git: baixe `https://github.com/mottihomero-rgb/painel-de-tempo/archive/refs/heads/main.zip` e extraia lá).

## 1. Ferramenta do Google (clasp)

O clasp é a ferramenta oficial do Google para criar Apps Script pela linha de comando. Precisa de Node 18 ou mais novo.

- Teste `node -v`. Se tiver, use `npx -y @google/clasp@3` (testado com a 3.4.1).
- Se não tiver Node, **não peça para a pessoa instalar**: baixe o Node portátil (não precisa de senha) para `~/.painel-de-tempo/node`:
  - leia `https://nodejs.org/dist/latest-v22.x/SHASUMS256.txt` para achar o nome exato do arquivo;
  - Mac: `node-v22.*-darwin-arm64.tar.gz` (ou `darwin-x64` se `uname -m` der `x86_64`); Linux: `linux-x64.tar.xz`; Windows: `win-x64.zip`;
  - extraia e use o `bin` dele (no Windows, a raiz da pasta) no começo do PATH, só nos comandos desta instalação.
- Todos os comandos do clasp abaixo rodam dentro de `~/.claude/skills/painel-de-tempo/conexao/` (crie a pasta).

## 2. Entrar na conta Google

- Rode `npx -y @google/clasp@3 login`. Ele abre o navegador sozinho e fica esperando: rode com tempo limite longo (10 minutos) ou em segundo plano e acompanhe a saída.
- Diga à pessoa: "Abriu o Google no navegador. **Escolha a conta** da agenda, clique em **Continuar**, marque **Selecionar tudo** e clique em **Continuar** de novo."
- Se ela estranhar a lista de acessos: é a ferramenta oficial do Google para criar o script; no fim você sai dela.
- Pronto quando aparecer `You are logged in as <email>`. Guarde esse e-mail.
- Se o navegador não abrir (máquina sem tela), use `login --no-localhost` e peça para ela colar o endereço que aparecer depois de aceitar.

## 3. Criar o projeto

1. `npx -y @google/clasp@3 create-script --type standalone --title "Painel de Tempo"`
2. Se der **"User has not enabled the Apps Script API"**: abra `https://script.google.com/home/usersettings?authuser=<email>` e diga: "Ligue a chave **Google Apps Script API** e me avise." Tente de novo a cada 20 segundos; pode levar 1 ou 2 minutos para valer.
3. O `create-script` escreve um `appsscript.json` padrão por cima. **Depois dele**, copie `google/Codigo.gs` e `google/appsscript.json` desta skill para a pasta `conexao/`, substituindo.
4. `npx -y @google/clasp@3 push --force`

Se já existir um `.clasp.json` em `conexao/` (instalação antiga), pule o passo 1: só copie os arquivos e dê o push.

## 4. Publicar

- `npx -y @google/clasp@3 create-deployment --description "Painel de Tempo" --json` e pegue o `deploymentId`.
- Endereço da agenda: `https://script.google.com/macros/s/<deploymentId>/exec`.
- Guarde em `conexao/endereco.txt` (permissão só do dono: `chmod 600` no Mac e Linux).
- **Nunca** escreva o endereço inteiro na conversa nem em lugar público: ele dá leitura da agenda para quem tiver.

## 5. Liberar a leitura da agenda

- Abra o endereço no navegador (Mac `open`, Windows `start ""`, Linux `xdg-open`).
- Diga: "Clique em **Revisar permissões** (ou Review Permissions) e escolha a conta. Na tela **O Google não verificou este app**, clique em **Avançado** e depois em **Acessar Painel de Tempo (não seguro)**. Por fim, **Continuar**."
- Explique em uma linha: o aviso é normal, o app é dela mesma, acabou de ser criado.
- Confira sozinho, sem esperar ela avisar: `curl -sL "<endereço>?callback=x"` a cada 5 segundos, por até 5 minutos, até a resposta começar com `x({"ok":true`. Antes da permissão volta uma página HTML.
- Se vier `"ok":false`, leia o `erro` e veja "Se der errado".

## 6. Abrir o painel

- Link: `https://mottihomero-rgb.github.io/painel-de-tempo/painel/?agenda=` + o endereço codificado (como `encodeURIComponent`). O painel guarda o endereço naquele navegador e tira da barra.
- Crie um atalho na Área de Trabalho chamado **Painel de Tempo** com esse link (Mac: `.webloc`; Windows: `.url`; Linux: `.desktop`). É o que ela abre na TV ou em outro computador.
- Abra o link no navegador e confira que saiu da tela "Conectar a agenda".
- Saia do clasp: `npx -y @google/clasp@3 logout`. O painel continua funcionando; só não fica acesso aberto à toa. (Para mexer de novo depois, é só fazer o login outra vez.)

## 7. Entregar

Diga, curto:
- Está pronto, e o atalho **Painel de Tempo** está na Área de Trabalho.
- **F** tela cheia, **S** liga o som, **C** troca a agenda.
- A cor do bloco é a cor do evento no Google. Tarefas: na descrição do evento, uma por linha começando com `-`.
- Na TV: abrir o atalho no navegador da TV ou espelhar a tela.

## Mudanças que a pessoa pode pedir depois

Edite `conexao/Codigo.gs` (o `CONFIG` no topo), faça login, `push --force` e atualize a mesma implantação para não mudar o endereço: `npx -y @google/clasp@3 update-deployment <deploymentId> --description "Painel de Tempo"`. Depois, logout.
- `AGENDAS`: `'primary'` é a principal; outras pelo ID (Configurações da agenda > Integrar agenda).
- `DIAS`: quantos dias à frente o painel recebe (padrão 8).
- `SO_OCUPADO: true`: mostra "Ocupado" no lugar do título e da descrição.

## Como o painel funciona

- Busca a agenda a cada 2 minutos por `<script>` com `callback` (JSONP). Funciona aberto do disco, no GitHub Pages e no navegador da TV.
- Guarda o endereço e a última agenda no `localStorage`. Em aba anônima funciona igual, só não lembra.
- Encavalou: o compromisso mais curto ganha naquele pedaço. Dia inteiro e convite recusado não entram.
- Alerta: a cor gira até o vermelho entre 10 e 2 minutos do fim; anel piscando nos 5 últimos.
- Ponto laranja no topo: mais de 15 minutos sem conseguir ler a agenda.
- Teste de horário: `?t=14:05&data=2026-10-21&vel=60`. Demonstração: `?demo=1`.
- Leveza: sem `backdrop-filter`, blur grande ou `mix-blend-mode`; o tanque desce por `transform`. Mantenha assim se mexer no visual.

## Se der errado

- **"User has not enabled the Apps Script API"**: passo 3.2. Pode levar uns minutos depois de ligar.
- **Volta HTML para sempre no passo 5**: a pessoa não terminou a permissão. Abra o endereço de novo e repita as instruções.
- **Conta do Google Workspace (e-mail de empresa) bloqueia acesso por endereço**: troque `"access": "ANYONE_ANONYMOUS"` por `"ANYONE"` no `appsscript.json`, push e nova implantação. Aí o painel só funciona num navegador logado no Google. Se nem isso for permitido, o administrador precisa liberar, ou use uma conta Gmail que tenha a agenda compartilhada.
- **"Agenda não encontrada"**: ID errado em `CONFIG.AGENDAS`, ou a conta não tem acesso àquela agenda.
- **Mudou o código e não refletiu**: faltou o `update-deployment`.
- **`delete-script` do clasp falha**: normal (a permissão dele não alcança). Para apagar um projeto, use script.google.com > Meus projetos.

## Regras

- Não peça para a pessoa programar, abrir terminal, editar arquivo ou instalar nada. Se travar, resolva você.
- O endereço `/exec` dá acesso de leitura à agenda: nunca na conversa inteiro, nunca em repositório ou lugar público.
- Não peça permissão de escrita na agenda: o manifesto é só leitura de propósito.
