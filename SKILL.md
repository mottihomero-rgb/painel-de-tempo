---
name: painel-de-tempo
description: Instala sozinho o Painel de Tempo e conecta na Google Agenda da pessoa. É um painel para deixar à vista em qualquer tela (TV, monitor extra, tela pequena embaixo do monitor, celular em pé ou deitado) com o compromisso de agora em letra grande, contagem regressiva, tarefas, o que veio antes, o que vem depois e alerta vermelho nos últimos 10 minutos. Você prepara o computador (Mac, Windows ou Linux), cria a conexão no Google (Apps Script, só leitura), publica, abre o painel e deixa o QR code para o celular (Android ou iPhone); a pessoa só clica nas telas do Google. Aciona quando a pessoa disser instala o painel de tempo, painel de tempo, contagem regressiva da agenda, agenda na tela, agenda na TV, timer do compromisso, painel do Google Agenda.
---

# Painel de Tempo: instalação autônoma

A pessoa só pediu. **Você faz tudo**: prepara o computador, cria a conexão na Google Agenda dela, publica, abre o painel e deixa o QR code para o celular. Ela só clica nas telas do Google que abrirem no navegador.

Fale com ela em português simples, sem termo técnico, uma instrução por vez. Não peça para ela abrir programa, editar arquivo ou digitar comando.

O que tem nesta pasta:
- `painel/index.html`: o painel (publicado em https://mottihomero-rgb.github.io/painel-de-tempo/painel/). Se ajusta sozinho a qualquer tela: deitada (TV, monitor, faixa baixa, celular deitado) ou em pé (celular, monitor vertical).
- `google/Codigo.gs` e `google/appsscript.json`: a conexão com a agenda. O manifesto pede **só leitura** da agenda e já deixa o app da Web pronto.
- `index.html`: a página com o tutorial para humanos.

## 0. Começo

1. Descubra o sistema: `uname -s` (Darwin = Mac, Linux = Linux; MINGW/MSYS/CYGWIN = Windows com Git Bash). No Windows sem bash, use PowerShell.
2. Diga em uma frase o que vai acontecer: "Vou instalar o painel e ligar na sua agenda. Vão abrir 3 telas do Google no navegador; eu aviso o que clicar em cada uma."
3. Se esta pasta ainda não estiver em `~/.claude/skills/painel-de-tempo` (no Windows: `%USERPROFILE%\.claude\skills\painel-de-tempo`), coloque com `git clone https://github.com/mottihomero-rgb/painel-de-tempo <pasta>`. Sem git: baixe `https://github.com/mottihomero-rgb/painel-de-tempo/archive/refs/heads/main.zip` e extraia lá.

## 1. Ferramenta do Google (clasp)

O clasp é a ferramenta oficial do Google para criar Apps Script pela linha de comando. Precisa de Node 18 ou mais novo.

- Teste `node -v`. Se tiver, use `npx -y @google/clasp@3` (testado com a 3.4.1). No PowerShell, `npx` funciona igual.
- Se não tiver Node, **não peça para a pessoa instalar**: baixe o Node portátil (não precisa de senha nem de administrador) para `~/.painel-de-tempo/node`:
  - leia `https://nodejs.org/dist/latest-v22.x/SHASUMS256.txt` para achar o nome exato do arquivo;
  - **Mac**: `node-v22.*-darwin-arm64.tar.gz` (ou `darwin-x64` se `uname -m` der `x86_64`). Extraia com `tar -xzf`.
  - **Windows**: `node-v22.*-win-x64.zip` (ou `win-arm64`). Extraia com `tar -xf arquivo.zip` (vem no Windows 10 e 11) ou `Expand-Archive` no PowerShell. O `node.exe` e o `npx.cmd` ficam na raiz da pasta.
  - **Linux**: `node-v22.*-linux-x64.tar.xz` (ou `linux-arm64`). Extraia com `tar -xJf`.
  - Ponha a pasta do Node (o `bin` no Mac e Linux, a raiz no Windows) no começo do PATH só nos comandos desta instalação.
- Todos os comandos do clasp abaixo rodam dentro de `~/.claude/skills/painel-de-tempo/conexao/` (crie a pasta).

## 2. Entrar na conta Google

- Rode `npx -y @google/clasp@3 login`. Ele abre o navegador sozinho e fica esperando: rode com tempo limite longo (10 minutos) ou em segundo plano e acompanhe a saída.
- Diga à pessoa: "Abriu o Google no navegador. **Escolha a conta** da agenda, clique em **Continuar**, marque **Selecionar tudo** e clique em **Continuar** de novo."
- Se ela estranhar a lista de acessos: é a ferramenta oficial do Google para criar o script; no fim você sai dela.
- Pronto quando aparecer `You are logged in as <email>`. Guarde esse e-mail.
- Se o navegador não abrir (máquina sem tela, sessão remota), use `login --no-localhost`: mostre o endereço, peça para ela abrir, aceitar e colar de volta o endereço da página que der erro no fim (começa com `http://localhost:8888/?...`).

## 3. Criar o projeto

1. `npx -y @google/clasp@3 create-script --type standalone --title "Painel de Tempo"`
2. Se der **"User has not enabled the Apps Script API"**: abra `https://script.google.com/home/usersettings?authuser=<email>` e diga: "Ligue a chave **Google Apps Script API** e me avise." Tente de novo a cada 20 segundos; pode levar 1 ou 2 minutos para valer.
3. O `create-script` escreve um `appsscript.json` padrão por cima. **Depois dele**, copie `google/Codigo.gs` e `google/appsscript.json` desta skill para a pasta `conexao/`, substituindo.
4. `npx -y @google/clasp@3 push --force`

Se já existir um `.clasp.json` em `conexao/` (instalação antiga), pule o passo 1: só copie os arquivos e dê o push.

## 4. Publicar

- `npx -y @google/clasp@3 create-deployment --description "Painel de Tempo" --json` e pegue o `deploymentId`.
- Endereço da agenda: `https://script.google.com/macros/s/<deploymentId>/exec`.
- Guarde em `conexao/endereco.txt` (no Mac e Linux, `chmod 600`).
- **Nunca** escreva o endereço inteiro na conversa nem em lugar público: ele dá leitura da agenda para quem tiver.

## 5. Liberar a leitura da agenda

- Abra o endereço no navegador: Mac `open "<url>"`; Windows `start "" "<url>"` (cmd), `Start-Process "<url>"` (PowerShell) ou `cmd //c start "" "<url>"` (Git Bash); Linux `xdg-open "<url>"`.
- Diga: "Clique em **Revisar permissões** (ou Review Permissions) e escolha a conta. Na tela **O Google não verificou este app**, clique em **Avançado** e depois em **Acessar Painel de Tempo (não seguro)**. Por fim, **Continuar**."
- Explique em uma linha: o aviso é normal, o app é dela mesma, acabou de ser criado.
- Confira sozinho, sem esperar ela avisar: `curl -sL "<endereço>?callback=x"` a cada 5 segundos, por até 5 minutos, até a resposta começar com `x({"ok":true`. Antes da permissão volta uma página HTML. (No PowerShell antigo, use `curl.exe`, não o apelido `curl`.)
- Se vier `"ok":false`, leia o `erro` e veja "Se der errado".

## 6. Abrir o painel e levar para as telas

**Link do painel:** `https://mottihomero-rgb.github.io/painel-de-tempo/painel/#agenda=` + o endereço codificado (como `encodeURIComponent`). O endereço vai depois do `#`: assim ele não chega ao servidor do site e continua no link, o que faz favorito e Tela de Início do celular funcionarem.

1. **Atalho na Área de Trabalho**, chamado `Painel de Tempo`:
   - Mac: `~/Desktop/Painel de Tempo.webloc` (plist com a chave `URL`).
   - Windows: `Painel de Tempo.url` com `[InternetShortcut]` e `URL=<link>`. Ache a Área de Trabalho com `powershell -NoProfile -Command "[Environment]::GetFolderPath('Desktop')"` (muitas vezes ela fica dentro do OneDrive).
   - Linux: `Painel de Tempo.desktop` com `Type=Link` e `URL=<link>`, na pasta de `xdg-user-dir DESKTOP`.
2. **QR code para o celular**: `npx -y qrcode -o "<Área de Trabalho>/Painel de Tempo no celular.png" "<link>" < /dev/null` (no PowerShell, sem o `< /dev/null`; sem ele no bash, o comando fica esperando). Abra a imagem.
3. Abra o link no navegador do computador e confira que saiu da tela "Conectar a agenda".
4. Saia do clasp: `npx -y @google/clasp@3 logout`. O painel continua funcionando; só não fica acesso aberto à toa. (Para mexer de novo, é só fazer o login outra vez.)

## 7. Entregar

Diga, curto:
- Está pronto. O atalho **Painel de Tempo** está na Área de Trabalho.
- Funciona em qualquer tela: TV, monitor extra, tela pequena, celular em pé ou deitado.
- **Computador**: `F` tela cheia, `S` liga o som, `C` troca a agenda. Dois cliques também põem em tela cheia.
- **Celular**: aponte a câmera para o QR code que está na Área de Trabalho.
  - **Android (Chrome)**: toque duas vezes para tela cheia. Para virar ícone: menu ⋮ > Adicionar à tela inicial.
  - **iPhone (Safari)**: Compartilhar > Adicionar à Tela de Início. Abrindo pelo ícone, fica em tela cheia.
  - Deixe na tomada: o painel pede para a tela ficar acesa, mas o celular pode apagar se a bateria estiver baixa ou no modo economia.
- A cor do bloco é a cor do evento no Google. Tarefas: na descrição do evento, uma por linha começando com `-`.

## Mudanças que a pessoa pode pedir depois

Edite `conexao/Codigo.gs` (o `CONFIG` no topo), faça login, `push --force` e atualize a mesma implantação para não mudar o endereço: `npx -y @google/clasp@3 update-deployment <deploymentId> --description "Painel de Tempo"`. Depois, logout.
- `AGENDAS`: `'primary'` é a principal; outras pelo ID (Configurações da agenda > Integrar agenda).
- `DIAS`: quantos dias à frente o painel recebe (padrão 8).
- `SO_OCUPADO: true`: mostra "Ocupado" no lugar do título e da descrição.

## Como o painel funciona

- Busca a agenda a cada 2 minutos por `<script>` com `callback` (JSONP). Funciona aberto do disco, no GitHub Pages, no navegador da TV e no celular.
- O endereço vem do `#agenda=` do link (ou do `?agenda=`, que some da barra) e fica guardado no `localStorage`. Em aba anônima funciona igual, só não lembra.
- Tela deitada: o desenho de 16:9 escala pela medida que faltar, então cabe inteiro em TV, monitor, faixa baixa (ex.: 1920x480) e celular deitado. Tela em pé: uma coluna só, cabendo na altura.
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
- **Windows: "npx não é reconhecido"**: o Node portátil não está no PATH daquele comando; chame pelo caminho completo (`<pasta do node>\npx.cmd`).

## Regras

- Não peça para a pessoa programar, abrir terminal, editar arquivo ou instalar nada. Se travar, resolva você.
- O endereço `/exec` dá acesso de leitura à agenda: nunca na conversa inteiro, nunca em repositório ou lugar público.
- Não peça permissão de escrita na agenda: o manifesto é só leitura de propósito.
