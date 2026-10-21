# RDO Rondon — guia de instalação e uso

Sistema de análise do RDO (rendimento e desempenho) que roda no navegador: PC do trabalho, celular e iPad.
É **um único arquivo HTML** (`index.html`) + **um arquivo de dados** (`dados/rdo-base.json`). Nada para instalar.

## O que vem no pacote

```
rdo-rondon/
├── index.html                 ← o sistema inteiro (abre em qualquer navegador)
├── .nojekyll                  ← arquivo vazio que deixa o GitHub Pages mais rápido (não apague)
├── dados/
│   └── rdo-base.json          ← a base: um lançamento por turno por dia (já vem com 19/10 e 20/10 do 1º turno)
├── IA/
│   ├── INSTRUCOES_IA.md       ← mande este arquivo para a sua IA
│   └── modelo-registro.json   ← exemplo real de um lançamento (gabarito para a IA)
└── LEIA-ME.md                 ← este guia
```

---

## Parte 1 — Colocar no ar (faz uma vez só, ~10 minutos)

Faça pelo computador (no celular o GitHub não deixa subir pastas).

**1. Criar o repositório**
1. Entre em github.com (crie a conta se não tiver).
2. Botão verde **New** (ou o **+** no canto de cima → *New repository*).
3. Nome: `rdo-rondon` · marque **Public** · clique em **Create repository**.

**2. Subir os arquivos**
1. Na página do repositório, clique no link **uploading an existing file**.
2. Abra a pasta `rdo-rondon` (a que você descompactou) e **arraste tudo que está dentro dela** para a página: `index.html`, `.nojekyll`, `LEIA-ME.md` e as pastas `dados` e `IA`.
   - O `.nojekyll` começa com ponto e pode estar escondido. Se não aparecer, tudo bem: o sistema funciona sem ele.
3. Embaixo, clique em **Commit changes**.
4. Confira: na lista do repositório tem que aparecer a pasta `dados` com o `rdo-base.json` dentro.

**3. Ligar o site (GitHub Pages)**
1. No repositório: **Settings** → menu da esquerda **Pages**.
2. Em *Build and deployment* → *Source*: **Deploy from a branch**.
3. *Branch*: **main** e pasta **/ (root)** → **Save**.
4. Espere 1 a 2 minutos e atualize a página. Vai aparecer: *Your site is live at* `https://SEU-USUARIO.github.io/rdo-rondon/`

Pronto: esse é o link do sistema. Abre no PC do trabalho (é só uma página no navegador), no celular e no iPad.
No iPhone/iPad: abra o link no Safari → botão **Compartilhar** → **Adicionar à Tela de Início**. Ele vira um ícone, igual um app.

**4. Criar o token (a "chave" para gravar dados)**
Precisa disso para publicar pelo próprio sistema e para a IA gravar. Para só **ver** o painel, ninguém precisa de token.
1. No GitHub, clique na sua foto → **Settings** → no fim do menu, **Developer settings**.
2. **Personal access tokens** → **Fine-grained tokens** → **Generate new token**.
3. Nome: `RDO painel` · Expiração: 1 ano (ou o máximo permitido).
4. *Repository access*: **Only select repositories** → escolha `rdo-rondon`.
5. *Permissions* → *Repository permissions* → **Contents** → **Read and write**.
6. **Generate token** e **copie** (começa com `github_pat_`). O GitHub só mostra uma vez.

**5. Colar o token no sistema**
Abra o link do sistema → **Ajustes** → *Conexão com o GitHub* → cole no campo **Token** → **Salvar** → **Testar conexão**.
Tem que aparecer: "O token pode gravar: publicar daqui vai funcionar."
O token fica guardado **só naquele aparelho**. Faça isso em cada aparelho onde você for lançar RDO.

**6. Configurar a sua IA**
Mande para a IA o arquivo `IA/INSTRUCOES_IA.md` (e o `IA/modelo-registro.json`) com uma mensagem assim:

> A partir de agora, sempre que eu mandar um PDF de RDO, siga estas instruções. Repositório: `SEU-USUARIO/rdo-rondon`, ramo `main`, arquivo `dados/rdo-base.json`. Token: `github_pat_...`. Link do painel: `https://SEU-USUARIO.github.io/rdo-rondon/`

Se a IA não conseguir acessar a internet/GitHub, ela vai devolver o lançamento em JSON. Aí você cola no sistema (ver Parte 2, opção B).

---

## Parte 2 — Rotina do dia (escolha a mais prática)

**Opção A — pelo próprio sistema (mais rápida, ~20 segundos)**
1. Abra o sistema → **Lançar RDO**.
2. Arraste o PDF do turno (ou toque e escolha). Pode soltar o do 1º e o do 2º turno juntos.
3. O sistema lê o PDF sozinho, mostra os números e faz **16 conferências automáticas** (as mesmas contas do RDO).
4. Toque em **Publicar no GitHub**. Todo mundo que abrir o link já vê.

**Opção B — pela sua IA**
1. Mande o PDF para a IA, como você já faz.
2. Ela grava no GitHub e te devolve o resumo do WhatsApp. O painel atualiza sozinho em até 1 minuto (ou toque no botão de atualizar).
3. Se ela devolver um JSON: **Lançar RDO** → *Colar o que a IA gerou* → **Conferir** → **Publicar no GitHub**.

**Opção C — sem internet ou sem token**
Lance normalmente e toque em **Salvar só neste aparelho**. Depois, em **Base de dados**, toque em **Publicar** para mandar tudo de uma vez.

> O PDF de um dia traz a semana inteira. O sistema lança todos os dias que têm produção; se o dia já existe e está igual, ele avisa "sem mudanças".

---

## Parte 3 — O que o sistema faz

**Visão geral** — o veredito do dia/semana/mês ("6 de 9 itens fecharam"), a faixa *Para onde foi cada 100 kg de frango vivo*, leitura automática em frases, cartões de cada item (toque para abrir a conta completa), ponte do rendimento de cortes, OEE, volume, tempo de linha e perdas.

**RDO vivo** — o RDO no mesmo formato do PDF (códigos, metas, dias da semana e acumulado), com cores e linhas que abrem: explicação, fórmula com os números do dia e histórico.

**Itens** — análise profunda de cada item: evolução, saldo acumulado em kg, dia da semana, 1º × 2º turno e quais descartes tiraram peso dele.

**Perdas e impacto** — separa o que tira **volume** (paradas, reduções, mortalidade) do que tira **rendimento** (descartes e condenas), estima quantos frangos, kg e caixas cada perda custou e simula "e se não houvesse descartes, o item fecharia?".

**Comparar** — 1º × 2º turno, período × período (anterior, mês passado, ano passado ou datas livres), dias da semana, itens lado a lado e relações (ex.: peso médio × rendimento do filé, com explicação).

**Tendências** — qualquer indicador no tempo (por turno, dia, semana ou mês), média móvel e calendário colorido do mês.

**Previsões** — projeção do fechamento do mês por item, quanto cada item **precisa render** nos dias que faltam, chance de fechar e previsão do próximo lançamento com faixa provável. Precisa de pelo menos 4 dias lançados.

**Modo apresentação** — botão ▶ no topo. O sistema vira slides para a reunião, com cada número explicado. Use as setas do teclado, deslize no celular ou toque nas setas. `Esc` sai.

**Resumo para WhatsApp** — botão de balão no topo. Gera o mesmo texto que a sua IA manda, para o dia, a semana ou o mês escolhido.

**Filtros** — no topo: Dia, Semana, Mês, Ano ou Período livre · setas para andar no tempo · T1, T2 ou Ambos.

**Modo demonstração** — em Ajustes. Liga 3 meses de dados simulados para você explorar comparações e previsões antes de a base crescer. Não mistura com os dados reais.

---

## Parte 4 — Como as contas foram descobertas (conferido no próprio PDF)

Todas as fórmulas abaixo foram testadas com os números do RDO de 20/10 e bateram com o PDF e com o resumo da sua IA.

- **Rendimento de cada item (%)** = kg do item ÷ peso vivo abatido (`S`) × 100.
- **Diferença em kg** = (realizado % − meta %) ÷ 100 × `S`. Ex.: fígado (1,041 − 1,600) × 155.613 = **−870 kg**.
- **Caixas** = kg ÷ peso da caixa (20 kg; pés 16 kg). Arredonda só no fim. Menos de 1 caixa → só kg.
- **Rendimento de cortes (BA)** = soma de todos os itens, **incluindo CMS e dorso**. A meta de CMS (15%) cobre CMS + dorso juntos (o dorso tem meta 0). Por isso a "ponte" mostra CMS + Dorso juntos.
- **Acumulado** = média ponderada pelo peso vivo (não é média simples dos %). Ex.: coxa (25,990 × 167.363 + 26,661 × 155.613) ÷ 322.976 = 26,313% — igual ao PDF.
- **Horas disponíveis** = (término − início) − paradas programadas. Ex.: 13:18 − 03:30 = 9,8 h − 128 min = 7,67 h.
- **Horas produtivas** = frangos ÷ ritmo padrão. O RDO usa um ritmo de **≈ 6.408 frangos/h** (≈ 107 por minuto) — descoberto dividindo frangos por horas produtivas nos dois dias (dá 6.408 nos dois).
- **OEE** = utilização × eficiência × rendimento. Ex.: 100% × 107,80% × 84,83% = 91,45%.
- **Plano de produção (N)** é do dia inteiro: cada turno sozinho fica perto de 57% dele. Ao juntar T1 + T2 o sistema **não** soma o plano duas vezes (dá para mudar em Ajustes).
- **Cores**: verde = bateu a meta; amarelo = abaixo, mas a menos de 5% da meta; vermelho = mais longe. É a mesma régua que o PDF usa (ex.: filé 26,209 contra 26,500 fica amarelo; fígado 1,294 contra 1,600 fica vermelho). Ajustável em Ajustes.
- **Paradas e mortalidade não mudam o % do item** (a conta é sempre kg do item ÷ kg abatido); elas tiram volume. **Descartes em kg** tiram peso do item e aí sim derrubam o %.

---

## Parte 5 — Cuidados

- **Privacidade:** repositório público = quem tiver o link (ou procurar no GitHub) consegue ver os números. Antes de usar com dados reais, confirme com seu gestor se pode. Se precisar fechar: deixe o repositório **privado**, mantenha o `index.html` num repositório público separado (ou abra o arquivo direto do computador) e cole em cada aparelho um token com permissão de leitura (*Contents: Read-only*) em Ajustes, apontando para o repositório privado. O sistema já sabe ler assim.
- **Token:** trate como senha. Se vazar, apague em GitHub → Settings → Developer settings → tokens, e crie outro.
- **Base quebrou?** (aviso "JSON inválido"): abra `dados/rdo-base.json` no GitHub → **History** → abra a versão anterior → copie o conteúdo e salve por cima. A última linha que a IA mexeu é a suspeita.
- **PC do trabalho:** só precisa abrir o link no navegador. Se abrir o `index.html` direto do computador, vá em Ajustes e preencha usuário e repositório para ele buscar os dados.
- **Painel não atualizou:** o GitHub Pages leva até 1 minuto depois de gravar. Toque no botão de atualizar (seta circular no topo).
