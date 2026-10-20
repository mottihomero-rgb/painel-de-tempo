---
name: painel-de-tempo
description: Monta o Painel de Tempo, uma tela para TV ou monitor que mostra o compromisso de agora da Google Agenda em letra grande, com contagem regressiva, tarefas, o que veio antes e o que vem depois, e alerta vermelho nos últimos 10 minutos. Faz também a conexão com a Google Agenda (Apps Script, só leitura). Aciona quando a pessoa disser painel de tempo, painel na TV, contagem regressiva da agenda, mostrar minha agenda na televisão, timer do compromisso, o que eu tenho que fazer agora na tela, painel do Google Agenda, relógio da agenda.
---

# Painel de Tempo

Tudo o que a skill usa está nesta pasta:

- `index.html`: o painel. Um arquivo só, abre em qualquer navegador (até direto do computador, sem servidor).
- `google/Codigo.gs` e `google/appsscript.json`: a ponte com a Google Agenda (Apps Script na conta da própria pessoa).
- `README.md`: o mesmo passo a passo, escrito para a pessoa.
- Versão publicada: https://mottihomero-rgb.github.io/painel-de-tempo/ (demonstração em `?demo=1`).

## Como conduzir

1. **Mostre primeiro.** Abra a demonstração (`index.html?demo=1` ou o link publicado) para a pessoa ver como fica antes de conectar qualquer coisa.
2. **Pergunte só o necessário, de uma vez:** qual conta Google tem a agenda e se ela quer ler mais de uma agenda ou esconder os títulos (agenda pessoal).
3. **Conecte** seguindo os passos abaixo. Se você tiver um navegador automatizado já logado na conta Google da pessoa, faça você mesmo; se não, guie um passo por vez e espere ela confirmar.
4. **Teste** com a agenda dela: o painel tem que sair da tela de conectar e mostrar o compromisso de agora (ou "Livre" e o próximo).
5. **Entregue** o painel aberto em tela cheia e diga as 3 teclas: `F`, `S`, `C`.

## Conectar (Apps Script)

1. script.google.com > **Novo projeto** > nome **Painel de Tempo**.
2. Trocar o conteúdo de `Código.gs` por `google/Codigo.gs`.
3. **Configurações do projeto** > marcar **Mostrar arquivo de manifesto "appsscript.json" no editor** > no Editor, trocar o `appsscript.json` por `google/appsscript.json`. É isso que faz o pedido de permissão ser **só leitura** da agenda.
4. Salvar. Escolher a função `testar` e **Executar**.
5. **Revisar permissões** > conta > **Avançado** > **Acessar Painel de Tempo (não seguro)** > **Continuar**. Avise a pessoa que o alerta é normal: o "app" é o script dela mesma.
6. **Implantar > Nova implantação**. O manifesto já deixa **App da Web**, executar como **Eu**, acesso **Qualquer pessoa**. **Implantar** e copiar o URL que termina em `/exec`.
7. Abrir o painel e colar o endereço (ou abrir `index.html?agenda=ENDEREÇO` uma vez: ele guarda e tira da barra de endereço).

Ajustes no `CONFIG` do `Codigo.gs`:
- `AGENDAS`: `'primary'` é a principal; outras pelo "ID da agenda" (Configurações da agenda > Integrar agenda).
- `DIAS`: quantos dias à frente o painel recebe (padrão 8).
- `SO_OCUPADO: true`: mostra "Ocupado" no lugar de título e descrição.

Mudou o código depois de implantar: **Implantar > Gerenciar implantações > lápis > Nova versão**. O endereço não muda.

## Como o painel funciona (para explicar e para mexer)

- Busca a agenda a cada 2 minutos por `<script>` com `callback` (JSONP). Por isso funciona aberto direto do disco e no navegador da TV.
- Guarda o endereço e a última agenda no `localStorage` do navegador. Sem isso (aba anônima), funciona igual, só não lembra.
- Cor do bloco = cor do evento no Google (as 11 cores da paleta) ou a cor da agenda.
- Tarefas = linhas da descrição que começam com `-`, `•` ou `*` (e itens de lista). No máximo 5.
- Encavalou: o compromisso mais curto ganha naquele pedaço. Sobra menor que 5 minutos some.
- Dia inteiro e convite recusado não entram.
- Alerta: a cor gira até o vermelho dos 10 aos 2 minutos finais; anel piscando nos 5 últimos; bipe opcional (tecla `S`).
- Ponto laranja no topo: mais de 15 minutos sem conseguir ler a agenda.
- Teste de horário: `?t=14:05&data=2026-10-21&vel=60`.
- Leveza: nada de `backdrop-filter`, blur grande ou `mix-blend-mode`; o tanque desce por `transform`; o relógio bate uma vez por segundo. Mantenha assim se mexer no visual: TV e computador velho agradecem.

## Quando dá errado

- **Volta para a tela de conectar com erro:** endereço sem `/exec`, ou implantação sem acesso "Qualquer pessoa".
- **Google Workspace bloqueia "Qualquer pessoa":** pedir ao administrador ou usar uma conta Gmail com acesso à agenda.
- **"Agenda não encontrada":** ID errado em `CONFIG.AGENDAS`, ou a conta não tem acesso àquela agenda.
- **Mudou o código e não refletiu:** faltou a Nova versão da implantação.

## Regras

- O endereço `/exec` dá acesso de leitura à agenda: nunca colar em lugar público, nem no repositório.
- Não pedir permissão de escrita na agenda: o manifesto é só leitura de propósito.
