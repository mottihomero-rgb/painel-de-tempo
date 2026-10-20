# Painel de Tempo

Um painel para deixar à vista em qualquer tela (TV, monitor extra, tela pequena embaixo do monitor, celular em pé ou deitado) com o compromisso de **agora** da sua Google Agenda, em letra grande, e a contagem regressiva até ele acabar.

![Painel de Tempo](docs/painel.png)

- **Agora:** o nome do compromisso, quanto tempo falta e as tarefas dele.
- **Antes e Depois:** o que acabou de passar (apagado) e o que vem a seguir.
- **Linha do dia:** todos os compromissos de hoje, com um marcador na hora atual.
- **Alerta:** nos últimos 10 minutos a tela vai ficando vermelha. Nos 2 últimos, fica vermelha de vez.
- **Cores:** cada compromisso aparece na cor que você escolheu no Google Agenda.
- **Tarefas:** as linhas da descrição do evento que começam com `-` viram a lista do bloco.

**Tutorial:** https://mottihomero-rgb.github.io/painel-de-tempo/ · **Demonstração:** https://mottihomero-rgb.github.io/painel-de-tempo/painel/?demo=1

---

## Instalar com o Claude Code (o Claude faz tudo)

Funciona no Mac, no Windows e no Linux. Abra o Claude Code e cole:

```
Instala o Painel de Tempo e conecta na minha Google Agenda. Baixa https://github.com/mottihomero-rgb/painel-de-tempo para ~/.claude/skills/painel-de-tempo e segue o SKILL.md de lá do começo ao fim, fazendo tudo sozinho. Só me chama quando eu precisar clicar em algo no navegador.
```

O Claude prepara o computador, cria a conexão na sua conta Google com a ferramenta oficial do Google (clasp), publica e abre o painel. Você só clica em 3 telas do Google:

1. **Entrar:** escolha a conta, marque **Selecionar tudo** e **Continuar**.
2. **Chave:** ligue **Google Apps Script API** na página que o Claude abrir.
3. **Agenda:** **Revisar permissões**, **Avançado**, **Acessar Painel de Tempo** e **Continuar**.

No fim ficam na Área de Trabalho o atalho **Painel de Tempo** e um QR code para abrir no celular. O passo a passo que o Claude segue está em [`SKILL.md`](SKILL.md).

---

## Instalar sem o Claude Code

Leva uns 5 minutos e é feito uma vez só.

1. Abra [script.google.com](https://script.google.com), clique em **Novo projeto** e dê o nome **Painel de Tempo**.
2. Troque o conteúdo do arquivo `Código.gs` pelo de [`google/Codigo.gs`](google/Codigo.gs).
3. Em **Configurações do projeto**, marque **Mostrar arquivo de manifesto "appsscript.json" no editor**. No **Editor**, troque o `appsscript.json` pelo de [`google/appsscript.json`](google/appsscript.json). Isso faz o script pedir **só leitura** da agenda.
4. Salve. Escolha a função **testar** e clique em **Executar**. Em **Revisar permissões**, escolha a conta, **Avançado**, **Acessar Painel de Tempo (não seguro)** e **Continuar**.
5. **Implantar > Nova implantação**. Já vem **App da Web**, executando como **Eu**, acesso **Qualquer pessoa**. Clique em **Implantar** e copie o URL que termina em `/exec`.
6. Abra o [painel](https://mottihomero-rgb.github.io/painel-de-tempo/painel/), cole o endereço e clique em **Conectar**. Para levar a outro aparelho, use o link `https://mottihomero-rgb.github.io/painel-de-tempo/painel/#agenda=` seguido do seu endereço.

> O endereço `/exec` funciona como senha: quem tiver ele consegue ver os seus compromissos. Não publique.

---

## Usar

- **Qualquer tela:** deitada (TV, monitor, faixa baixa, celular deitado) o painel aparece inteiro, em escala; em pé (celular, monitor vertical) vira uma coluna só.
- **Computador:** `F` tela cheia (ou dois cliques) · `S` som (bipe quando começa um compromisso e 5 minutos antes de acabar) · `C` troca a agenda.
- **Android:** abra o link no Chrome e toque duas vezes para tela cheia. Para virar ícone: menu ⋮ > Adicionar à tela inicial.
- **iPhone:** abra no Safari e use Compartilhar > Adicionar à Tela de Início; pelo ícone ele abre em tela cheia.
- O painel pede para a tela ficar acesa. No celular, deixe na tomada e fora do modo economia.
- Evento de **dia inteiro** e convite **recusado** não aparecem.
- Quando dois compromissos se encavalam, o **mais curto ganha** naquele pedaço.
- Entre um compromisso e outro aparece **Livre**, com a contagem até o próximo.
- Um ponto laranja no topo avisa quando o painel está há mais de 15 minutos sem ler a agenda.
- Testar um horário: acrescente `?t=14:05&data=2026-10-21&vel=60` no endereço do painel.

Mais de uma agenda, ou só "Ocupado" no lugar dos títulos: mude o `CONFIG` no começo do `Codigo.gs` (ou peça ao Claude).

---

Feito por [Homero Motti](https://www.instagram.com/homeromotti/). Licença MIT: use, mude e distribua à vontade.
