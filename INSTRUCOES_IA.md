# LEI DE LANÇAMENTO DO RDO · RDO Rondon

> **Documento exclusivo da IA que lança o RDO.** Não aparece no painel e não é para o usuário ler.
> **Versão 2 · 21/10/2026.** Vale para todo lançamento, sempre, do começo ao fim.

---

## 0. Antes de tudo

**Você foi chamada para transformar o PDF do RDO em registros na base do sistema.** Leia esta LEI inteira antes de começar, mesmo que já tenha lançado antes: ela pode ter mudado (veja a versão acima).

**Onde esta LEI está** (as três cópias são iguais; se divergirem, vale a de versão mais nova):

1. `INSTRUCOES_IA.md`, na raiz do repositório;
2. dentro do `index.html`, no bloco `script` de id `lei-lancamento-ia` (o navegador não mostra esse bloco);
3. a base `dados/rdo-base.json` tem a chave `LEI_IA` apontando para cá.

**Princípio de ouro: você é transcritor, não analista.** Copie exatamente o que o PDF mostra, célula por célula. O painel faz todas as contas: rendimento, kg, caixas, impacto dos descartes, previsões, OEE. Um número inventado, arredondado ou "corrigido" estraga a análise de todo mundo sem ninguém perceber. Na dúvida entre copiar e deduzir, copie. Na dúvida sobre o que o PDF diz, pergunte ao usuário.

---

## 1. Roteiro obrigatório (12 passos)

| # | Passo | Seção |
|---|---|---|
| 1 | Confirme que tem o PDF e acesso de escrita ao repositório. Sem acesso, siga o plano B. | 2 e 13 |
| 2 | Identifique o arquivo: nome, páginas, qual é a página 1 (códigos N…BX) e qual é a página 2 (PP, P, R, 1.1…). | 3 |
| 3 | Leia o cabeçalho: turno, semana e as datas das colunas. | 3 |
| 4 | Extraia a página 1, uma coluna de dia por vez. | 4 e 5 |
| 5 | Extraia a página 2, uma coluna de dia por vez. | 4 e 6 |
| 6 | Leia a coluna META das duas páginas (vai em `metas`). | 5 e 6 |
| 7 | Escolha os dias: só colunas com R > 0 **e** S > 0. | 8 |
| 8 | Monte um registro por dia × turno. | 7 |
| 9 | Rode as 16 conferências. Falhou: releia; se o PDF for assim mesmo, mantenha e anote em `obs`. | 9 |
| 10 | Leia a base atual, faça o upsert por `id`, preserve `descartes` e tudo que não é deste PDF. | 10 |
| 11 | Grave `dados/rdo-base.json` na branch `main` com a mensagem padrão. | 10 |
| 12 | Responda ao usuário no modelo, dizendo quais linhas de descarte vieram em branco. | 11 e 12 |

**Atalho recomendado:** o Anexo A traz um leitor em Python que faz os passos 3 a 9 com a mesma lógica do leitor embutido no painel. Use-o **e** faça a conferência visual do Anexo B. O script não dispensa os seus olhos.

---

## 2. Onde gravar

| Item | Valor |
|---|---|
| Repositório | o mesmo onde está este arquivo (junto do `index.html`) |
| Arquivo | `dados/rdo-base.json` |
| Branch | `main` |
| Codificação | UTF-8 sem BOM |
| Quem mais grava nele | o próprio painel (descartes lançados à mão, lançamentos conferidos pelo usuário) |

Estrutura do arquivo (exatamente nesta forma):

```
{"sistema":"RDO Rondon","versao":1,"atualizadoEm":"2026-10-21T07:40:00-03:00","LEI_IA":"…","registros":[
{ registro de um dia e turno, numa linha só },
{ outro registro },
…
],"descartes":[
{ descarte lançado no painel, numa linha só },
…
]}
```

- `registros`: um objeto por **dia × turno**, ordenados por data e depois por turno, **um por linha**.
- `descartes`: lançamentos feitos no painel. **Não é seu. Preserve intacto**, mesma ordem, mesmo conteúdo.
- `LEI_IA`: o aviso que aponta para esta LEI. Mantenha.

---

## 3. Anatomia do PDF

### 3.1 O arquivo

- Um PDF por turno: `RDORONDON dd.mm Nº Turno.pdf` (ex.: `RDORONDON 20.10 1º Turno.pdf`).
- É a planilha do RDO exportada em PDF. A planilha é **semanal e acumulada**: cada PDF traz as colunas da semana inteira (domingo a sábado), preenchidas até o dia do envio. O PDF de sábado traz a semana toda.
- **Página 1** = indicadores do turno (códigos N … BX).
- **Página 2** = paradas e não-qualidade (códigos PP, P, R, 1 … 5.2.3).
- Página 3 em diante: ignore. Se a ordem vier trocada, identifique pelo conteúdo: a página que tem `PP01` é a página 2.

### 3.2 Página 1: mapa

O desenho abaixo mostra a **posição** de cada coisa. Os números são os do RDO real de 19/10/2026; a formatação exata das células pode variar um pouco.

```
┌──────────────────────────────────────────────────────────────────────────────────────┐
│ RDO RONDON …            TURNO   1º TURNO                        SEMANA 43            │ ← cabeçalho
├──────┬─────────────────────────┬────────┬────────┬────────┬─────┬────────┬───────────┤
│ cód. │ descrição               │  META  │18/10/26│19/10/26│ ... │24/10/26│ ACUMULADO │ ← linha das datas
├──────┼─────────────────────────┼────────┼────────┼────────┼─────┼────────┼───────────┤
│ N    │ plano de produção       │ 90.400 │        │ 93.400 │     │        │           │
│ O    │ diferença plano × prod. │        │        │-40.034 │     │        │           │
│ P    │ % atingimento do plano  │   100% │        │  57,1% │     │        │           │
│ …    │                         │        │        │        │     │        │           │
│ Y.1  │ leg                     │        │        │  2.525 │     │        │           │ ← vira Y1
│ …    │                         │        │        │        │     │        │           │
│ AG.2 │ temperado coxinha asa   │        │        │        │     │        │           │
│      │ TEMPERADO MEIO DA ASA   │        │        │        │     │        │           │ ← sem código: AG3
│ …    │                         │        │        │        │     │        │           │
│ AS   │ hora de início          │        │        │  03:30 │     │        │           │
│ AT   │ hora de término         │        │        │  13:18 │     │        │           │
│ …    │                         │        │        │        │     │        │           │
│ BP   │ CMS                     │    15% │        │ 4,853% │     │        │           │
│      │ DORSO                   │     0% │        │12,399% │     │        │           │ ← sem código: DORSO
│ BQ   │ sambiquira              │   0,8% │        │     0% │     │        │           │
│ …    │                         │        │        │        │     │        │           │
│ BX   │ OEE                     │ 81,19% │        │ 89,15% │     │        │           │
└──────┴─────────────────────────┴────────┴────────┴────────┴─────┴────────┴───────────┘
```

Como achar cada coisa na página 1:

- **Coluna de códigos**: a primeira coluna à esquerda, estreita (até uns 75 pt da borda). Códigos com ponto **perdem o ponto** no JSON: `Y.1 → Y1`, `AC.2 → AC2`, `AG.1 → AG1`, `AO.1 → AO1`, `AQ.1 → AQ1`.
- **Duas linhas não têm código**, só descrição:
  - `TEMPERADO MEIO DA ASA` → chave **`AG3`** (logo abaixo de AG.2);
  - `DORSO` → chave **`DORSO`** (entre BP e BQ, no bloco de rendimento).
- **Linha das datas**: datas `dd/mm/aa` no topo das colunas de dia (ex.: `19/10/26` = 2026-10-19). Data brasileira: dia primeiro, mês depois. `05/11/26` é 5 de novembro.
- **META**: coluna na mesma linha das datas, **à esquerda** do primeiro dia. Não é um dia.
- **ACUMULADO**: coluna **à direita** do último dia. Não é um dia. Não copie.
- **TURNO**: no cabeçalho, o rótulo `TURNO` com `1º TURNO` / `2º TURNO` / `3º TURNO` na mesma linha. `1º → T1`, `2º → T2`, `3º → T3`. Confirme com o nome do arquivo. Se discordarem ou não houver turno, **pergunte ao usuário**.
- **SEMANA**: no cabeçalho, `SEMANA 43`.

### 3.3 Página 2: mapa

```
┌──────┬────────────────────────────────────────┬──────┬───────┬───────┬─────┬───────┬───────┐
│ cód. │ descrição                              │ META │18-out │19-out │ ... │24-out │ ACUM. │ ← datas dd-mmm
├──────┼────────────────────────────────────────┼──────┼───────┼───────┼─────┼───────┼───────┤
│ PP01 │ pausa ergonômica (escala)              │      │       │       │     │       │       │ ┐
│ …    │                                        │      │       │       │     │       │       │ │ minutos
│ PP06 │ parada de refeição                     │      │       │    60 │     │       │       │ ┘
│ P01  │ falha operacional                      │      │       │       │     │       │       │ ┐ minutos
│ … P14│                                        │      │       │       │     │       │       │ ┘
│ R01  │ falha operacional (redução)            │      │       │    21 │     │       │       │ ┐ minutos
│ … R12│                                        │      │       │       │     │       │       │ ┘
│ 1    │ DESCARTES – FALHAS OPERACIONAIS        │      │       │       │     │       │       │
│ 1.1  │ escaldagem excessiva (unidades)        │      │       │       │     │       │       │
│ …    │                                        │      │       │       │     │       │       │
│ 3.9  │ pés (PCC)                              │      │       │       │     │       │       │
│ 4    │ CONDENAÇÕES                            │      │       │    78 │     │       │       │
│ 4.1  │ mortalidade no transporte              │ 0,18 │       │    78 │     │       │       │
│ 5    │ CONTAMINAÇÕES                          │    3 │       │       │     │       │       │
│ …    │                                        │      │       │       │     │       │       │
└──────┴────────────────────────────────────────┴──────┴───────┴───────┴─────┴───────┴───────┘
```

Como achar cada coisa na página 2:

- **Coluna de códigos**: a primeira à esquerda (até uns 62 pt). Aqui os códigos **mantêm o ponto**: `1.15`, `4.1`, `5.1.1`.
- **Descrição**: coluna larga; os números começam por volta de 425 pt da borda esquerda.
- **Datas**: no formato `19-out` (dia-mês abreviado: jan fev mar abr mai jun jul ago set out nov dez). Case cada uma com a data da página 1 (`19-out` = `19/10/26`). A ordem é a mesma da página 1.
- **META**: à esquerda do primeiro dia (uns 45 pt antes).
- Pode haver colunas **`%`** ao lado dos dias e uma **`ACUM.`** no fim: são calculadas, **não copie**.

---

## 4. Como ler os números (crítico)

| O PDF mostra | No JSON | Regra |
|---|---|---|
| `93.400` | `93400` | ponto é separador de milhar |
| `-40.034` | `-40034` | o sinal de menos fica |
| `57,1%` | `57.1` | vírgula é decimal; o `%` some; **não** divida por 100 |
| `4,613%` | `4.613` | itens têm 3 casas: é 4.613, **nunca** 4613 |
| `26,209%` | `26.209` | copie todas as casas |
| `3,14` | `3.14` | |
| `7,8` | `7.8` | horas em decimal (AU a AZ) |
| `03:30` ou `3:30` | `"03:30"` | **só AS e AT**: texto, hora com 2 dígitos |
| `0` | `0` | zero escrito é zero |
| célula vazia | **omitir a chave** | nunca escreva 0 no lugar de vazio |
| `-` ou `–` sozinho | **omitir a chave** | igual ao leitor do painel |
| `#DIV/0!`, `#N/D`, `#VALOR!`, `#REF!` | **omitir a chave** | erro do Excel |

- Copie **todas** as casas decimais que o PDF mostra. Não arredonde e não complete com zeros.
- Número vai como número JSON: sem aspas, ponto como decimal, sem separador de milhar. A única exceção é AS e AT (texto `"hh:mm"`).
- Inteiro fica inteiro: `2,0` vira `2`, `100,00%` vira `100`.

---

## 5. Página 1, linha a linha (vai em `v`)

Importância: **★★★** sem isso o registro não serve ou a análise principal quebra · **★★** alimenta telas inteiras · **★** complementar (copie se houver).

### 5.1 Volume de produção

| Código no PDF | Chave | O que é | Formato | Imp. |
|---|---|---|---|---|
| N | `N` | Plano de produção. É do **dia todo**: os dois turnos mostram o mesmo número. Copie como está, **não divida**. | inteiro | ★★ |
| O | `O` | Diferença plano × produção (R − N) | inteiro, pode ser negativo | ★ |
| P | `P` | % atingimento do plano | % | ★ |
| Q | `Q` | Quantidade programada para o turno | inteiro | ★★ |
| R | `R` | **Frangos abatidos** | inteiro | ★★★ |
| S | `S` | **Peso vivo abatido (kg)**: base de **todos** os rendimentos | inteiro | ★★★ |
| T | `T` | % atingimento da programação | % | ★ |
| U | `U` | Peso médio (kg) | 3 casas | ★ |
| V | `V` | Frangos por hora | inteiro | ★ |

### 5.2 Produção em caixas

| Código no PDF | Chave | O que é | Imp. |
|---|---|---|---|
| X | `X` | Frango inteiro (cx) | ★ |
| Y | `Y` | Coxa/sobrecoxa total (cx) = Y.1 + Y.2 + Y.3 + Y.4 | ★★ |
| Y.1 · Y.2 · Y.3 · Y.4 | `Y1` · `Y2` · `Y3` · `Y4` | Leg · Shawarma · BL · Coxa/sobrecoxa (cx) | ★ |
| Z | `Z` | Peito (cx) | ★ |
| AA | `AA` | Asa (cx) | ★ |
| AB | `AB` | CMS (cx) | ★ |
| AC | `AC` | Miúdos (cx) | ★ |
| AC.1 · AC.2 · AC.3 | `AC1` · `AC2` · `AC3` | Coração · Fígado · Moela (cx) | ★ |
| AE | `AE` | Frango à passarinho (cx) | ★ |
| AF | `AF` | Sambiquira (cx) | ★ |
| AG | `AG` | Temperado (cx) | ★ |
| AG.1 · AG.2 | `AG1` · `AG2` | Temperado fr. inteiro s/ miúdos · Temperado coxinha da asa | ★ |
| *(sem código)* TEMPERADO MEIO DA ASA | `AG3` | Temperado meio da asa (cx) | ★ |
| AH | `AH` | Total geral produzido (cx) | ★★ |
| AI | `AI` | **Total geral produzido (kg)** | ★★★ |
| AJ | `AJ` | **Rendimento industrial (%)** = AI ÷ S × 100 | ★★★ |
| AK | `AK` | % pés A | ★★ |
| AL | `AL` | % pés B | ★★ |
| AM | `AM` | Meio da asa MI (%) | ★ |
| AN | `AN` | Meio da asa MI (kg) | ★ |
| AO · AO.1 | `AO` · `AO1` | Exportação (cx) · (kg) | ★ |
| AP | `AP` | % exportação | ★ |
| AQ · AQ.1 | `AQ` · `AQ1` | Mercado interno (cx) · (kg) | ★ |
| AR | `AR` | % mercado interno | ★ |

### 5.3 Horas máquina

| Código | Chave | O que é | Formato | Imp. |
|---|---|---|---|---|
| AS | `AS` | Hora de início | texto `"hh:mm"` | ★★ |
| AT | `AT` | Hora de término | texto `"hh:mm"` | ★★ |
| AU | `AU` | Paradas programadas (h) | decimal | ★★ |
| AV | `AV` | Horas disponíveis | decimal | ★★ |
| AW | `AW` | Paradas não planejadas (h) | decimal | ★★ |
| AX | `AX` | Horas trabalhadas | decimal | ★★ |
| AY | `AY` | Horas produtivas (dá o ritmo padrão usado para calcular os frangos perdidos em paradas) | decimal | ★★ |
| AZ | `AZ` | Desvio de ritmo (AX − AY) | decimal, pode ser negativo | ★ |

### 5.4 Rendimento por item (o coração da análise)

| Código | Chave | Item | Imp. |
|---|---|---|---|
| BA | `BA` | Rendimento de cortes = soma dos 12 itens | ★★★ |
| BB | `BB` | Quebra industrial = 100 − BA | ★★ |
| BD | `BD` | Coxa/sobrecoxa | ★★★ |
| BE | `BE` | Filé | ★★★ |
| BG | `BG` | Coxinha da asa | ★★★ |
| BH | `BH` | Meio da asa | ★★★ |
| BI | `BI` | Ponta da asa | ★★★ |
| BL | `BL` | Fígado | ★★★ |
| BM | `BM` | Moela | ★★★ |
| BN | `BN` | Coração | ★★★ |
| BO | `BO` | Pés | ★★★ |
| BP | `BP` | CMS | ★★★ |
| *(sem código)* DORSO | `DORSO` | Dorso | ★★★ |
| BQ | `BQ` | Sambiquira | ★★★ |

**Os 12 itens precisam vir todos.** O sistema usa a proporção **real** entre eles no turno para dividir os descartes de carcaça e de asa entre os itens. Com menos de 5 itens o cálculo cai para as metas, que é menos exato.

### 5.5 Desempenho (OEE)

| Código | Chave | O que é | Imp. |
|---|---|---|---|
| BT | `BT` | Utilização | ★★ |
| BU | `BU` | Eficiência | ★★ |
| BV | `BV` | Rendimento | ★★ |
| BW | `BW` | Produtividade | ★ |
| BX | `BX` | OEE = BT × BU × BV ÷ 10 000 | ★★ |

Se aparecer na coluna de códigos algum código que não está nestas tabelas, copie-o também (mesma regra, sem o ponto) e cite-o na resposta ao usuário.

---

## 6. Página 2, linha a linha

### 6.1 Paradas programadas → `paradasProgramadas` (minutos)

| Código | O que é |
|---|---|
| PP01 | Pausa ergonômica (escala) |
| PP02 | Troca de uniforme |
| PP03 | Setup |
| PP04 | Pausa ergonômica |
| PP05 | Término de abate |
| PP06 | Parada de refeição |

### 6.2 Interrupções (paradas não planejadas) → `interrupcoes` (minutos)

| Código | O que é | Código | O que é |
|---|---|---|---|
| P01 | Falha operacional | P08 | Condenação – DIF |
| P02 | Defeito elétrico | P09 | Falta de frango – fomento |
| P03 | Defeito mecânico | P10 | Falha em equipamentos de TI |
| P04 | Falta de energia elétrica | P11 | Condenação – PCC |
| P05 | Falta de frango – logística | P14 | Estrutural |
| P06 | Falta de vapor | P12, P13 | se existirem no PDF, copie |
| P07 | Falta de água | | |

### 6.3 Reduções de velocidade → `reducoes` (minutos)

R01 Falha operacional · R02 Defeito elétrico · R03 Defeito mecânico · R04 Falta de energia elétrica · R05 Falta de frango – logística · R06 Falta de vapor · R07 Falta de água · R08 Condenação – DIF · R09 Falta de frango – fomento · R10 Falha de TI · R11 Condenação – PCC · R12 Chiller pré-resfriamento/miúdos.

### 6.4 Não-qualidade → `naoQualidade`

**Grupo 1 · Descartes – falhas operacionais**

| Código | Linha | Unidade | Tira de (o painel usa) |
|---|---|---|---|
| 1.1 | Escaldagem excessiva | und (carcaças) | par do 1.5 |
| 1.2 | Má sangria | und | par do 1.6 |
| 1.3 | Não eviscerado | und | par do 1.7 |
| 1.4 | Queda na escaldagem | und | par do 1.8 |
| 1.5 | Escaldagem excessiva | kg | carcaça inteira (todos os itens) |
| 1.6 | Má sangria | kg | carcaça inteira |
| 1.7 | Não eviscerado | kg | carcaça inteira |
| 1.8 | Queda na escaldagem | kg | carcaça inteira |
| 1.9 | Pés – inteiro | kg | Pés |
| 1.10 | Pés – escaldagem excessiva | kg | Pés |
| 1.11 | Pés – com calo | kg | Pés |
| 1.12 | Pés – queda | kg | Pés |
| 1.13 | Coração – queda | kg | Coração |
| 1.14 | Moela – queda | kg | Moela |
| 1.15 | Fígado – queda | kg | Fígado |

**Grupo 2 · Descartes DIF**

| Código | Linha | Unidade | Tira de |
|---|---|---|---|
| 2.1 | Asa | kg | coxinha, meio e ponta da asa |
| 2.2 | Filé | kg | Filé |
| 2.3 | Dorso | kg | Dorso |
| 2.4 | Coxa | kg | Coxa/sobrecoxa |
| 2.5 | Papo cheio | kg | carcaça inteira |
| 2.6 | Pés/pata | kg | Pés |
| 2.7 | Carcaças pré-inspeção (condenas na escaldagem) | und | par do 2.9 |
| 2.8 | Carcaças DIF (condenas na evisceração) | und | par do 2.10 |
| 2.9 | Carcaças pré-inspeção | kg | carcaça inteira |
| 2.10 | Carcaças DIF | kg | carcaça inteira |

**Grupo 3 · Descartes PCC 1B**

| Código | Linha | Unidade | Tira de |
|---|---|---|---|
| 3.1 | Total PCC 1B | kg | só total: copie, o painel não soma de novo |
| 3.2 | Asa | kg | coxinha, meio e ponta |
| 3.3 | Filé | kg | Filé |
| 3.4 | Dorso | kg | Dorso |
| 3.5 | Coxa | kg | Coxa/sobrecoxa |
| 3.6 | Artrite | kg | Coxa/sobrecoxa |
| 3.7 | Papo cheio | kg | carcaça inteira |
| 3.8 | Aspecto repugnante | kg | carcaça inteira |
| 3.9 | Pés | kg | Pés |

**Grupo 4 · Condenações**: `4.1` Mortalidade no transporte (und) **★★★**. A meta dela vem em % na coluna META (ex.: `0,18` → `"4.1": 0.18` em `metas`).

**Grupo 5 · Contaminações (und)**: `5.1` Revisão de carcaças · `5.1.1` Contaminações · `5.1.1.1` Fecal · `5.1.1.2` Biliar · `5.1.1.3` Gástrica · `5.1.2` Cloaca · `5.1.3` Frango mal eviscerado · `5.2` PCC 1B · `5.2.1` Contaminações · `5.2.1.1` Fecal · `5.2.1.2` Biliar · `5.2.1.3` Gástrica · `5.2.2` Cloaca · `5.2.3` Frango mal eviscerado.

Regras da não-qualidade:

- **Linhas-título** (`1`, `2`, `3`, `4`, `5`, `5.1`, `5.2`): se tiverem número, copie com o próprio código (ex.: `"4": 78`).
- **Pares unidade/kg** (1.1↔1.5, 1.2↔1.6, 1.3↔1.7, 1.4↔1.8, 2.7↔2.9, 2.8↔2.10): copie cada número no seu próprio código. **Nunca** converta unidades em kg, nunca some os dois, nunca preencha um a partir do outro. O painel converte carcaças em kg com o peso médio do turno e, quando os dois existem, usa o kg.

---

## 7. Montar o registro

| Campo | Regra | Exemplo |
|---|---|---|
| `id` | `AAAA-MM-DD_T#` | `"2026-10-19_T1"` |
| `data` | data ISO da coluna | `"2026-10-19"` |
| `turno` | `T1`, `T2` ou `T3` | `"T1"` |
| `semana` | número de `SEMANA nn`. Sem cabeçalho: semana ISO da segunda-feira da semana domingo–sábado | `43` |
| `fonte` | nome exato do PDF | `"RDORONDON 20.10 1º Turno.pdf"` |
| `lancadoPor` | sempre `"IA · LEI de lançamento"` | |
| `lancadoEm` | agora, ISO com fuso de Brasília | `"2026-10-21T07:40:00-03:00"` |
| `v` | página 1, coluna do dia (seção 5) | |
| `metas` | coluna META da página 1 **+** META da página 2. As mesmas metas valem para todos os dias do PDF. | |
| `paradasProgramadas` | PP do dia, em minutos | `{"PP04":60,"PP05":1,"PP06":60}` |
| `interrupcoes` | P do dia, em minutos | `{}` |
| `reducoes` | R do dia, em minutos | `{"R01":21}` |
| `naoQualidade` | 1 … 5.2.3 do dia | `{"4":78,"4.1":78}` |
| `obs` | `""`, ou o aviso de conferência que falhou (seção 9) | `""` |

Seção sem nenhum número fica como objeto vazio `{}`. **Nunca omita os campos do registro**; omita só as chaves vazias dentro deles.

**Exemplo real e completo** (números do RDO de 19/10/2026, 1º turno; aqui quebrado em linhas para leitura, mas no arquivo vai numa linha só):

```json
{"id":"2026-10-19_T1","data":"2026-10-19","turno":"T1","semana":43,
 "fonte":"RDORONDON 20.10 1º Turno.pdf","lancadoPor":"IA · LEI de lançamento","lancadoEm":"2026-10-20T22:50:00-03:00",
 "v":{"N":93400,"O":-40034,"P":57.1,"Q":53444,"R":53366,"S":167363,"T":99.9,"U":3.14,"V":6856,
      "Y":2641,"Y1":2525,"Y4":116,"Z":2431,"AA":699,"AB":1,"AC":453,"AC1":48,"AC2":124,"AC3":107,
      "AH":6225,"AI":139446,"AJ":83.32,"AK":89.08,"AL":10.92,"AQ":6225,"AQ1":139446,"AR":100,
      "AS":"03:30","AT":"13:18","AU":2,"AV":7.8,"AW":0,"AX":7.8,"AY":8.3,"AZ":-0.5,
      "BA":82.602,"BB":17.398,"BD":25.99,"BE":26.209,"BG":4.613,"BH":2.808,"BI":0.933,
      "BL":1.294,"BM":1.279,"BN":0.574,"BO":1.652,"BP":4.853,"DORSO":12.399,"BQ":0,
      "BT":100,"BU":107,"BV":83.32,"BW":107,"BX":89.15},
 "metas":{"5":3,"N":90400,"P":100,"T":98,"AJ":81.6,"AK":90,"AL":10,"AM":1.5,"AY":200,"BA":83.16,"BB":16.84,
          "BD":26.5,"BE":26.5,"BG":4.6,"BH":2.8,"BI":0.93,"BL":1.6,"BM":1.1,"BN":0.48,"BO":2.85,"BP":15,
          "DORSO":0,"BQ":0.8,"BT":99.5,"BU":100,"BV":81.6,"BW":99.5,"BX":81.19,"4.1":0.18},
 "paradasProgramadas":{"PP04":60,"PP05":1,"PP06":60},"interrupcoes":{},"reducoes":{"R01":21},
 "naoQualidade":{"4":78,"4.1":78},"obs":""}
```

Repare: neste dia X, Y2, Y3, AE, AF, AG, AM, AN, AO… vieram vazios e por isso **não aparecem**; BQ e AW vieram com `0` e por isso aparecem com `0`. As linhas de descarte 1.1 a 3.9 vieram em branco (caso da seção 11).

---

## 8. Quais dias lançar

- Lance toda coluna de dia com **R > 0 e S > 0**. Coluna com R ou S vazio ou zero é dia sem produção (domingo, feriado, dia que ainda não aconteceu): não lance.
- O PDF é acumulado na semana, então traz de novo os dias anteriores. **Lance-os também** (upsert): se estiverem iguais, nada muda; se o RDO foi corrigido, a correção entra. Para os dias que traz, vale sempre o PDF mais novo.
- **Nunca apague nem altere** registros de dias ou turnos que não estão neste PDF.
- Um PDF é um turno. O outro turno do mesmo dia é outro registro (outro `id`) e não deve ser tocado.
- O plano N é do dia: os dois turnos mostram o mesmo N. Copie igual nos dois; o painel sabe não somar.

---

## 9. Conferências obrigatórias (para cada registro)

São as mesmas que o painel faz na tela Base de dados. Tolerâncias absolutas.

| # | Conferência | Conta | Tolerância |
|---|---|---|---|
| 1 | Data e turno | data `AAAA-MM-DD`, turno `T1/T2/T3` | exato |
| 2 | Tem produção | R > 0 e S > 0 | exato |
| 3 | Rendimento industrial | AI ÷ S × 100 ≈ AJ | ±0,02 |
| 4 | Programação | R ÷ Q × 100 ≈ T | ±0,15 |
| 5 | Diferença do plano | R − N ≈ O | ±1,5 |
| 6 | Peso médio | S ÷ R ≈ U | ±0,012 |
| 7 | Total de caixas | X + Y + Z + AA + AB + AC + AE + AF + AG ≈ AH | ±2 |
| 8 | Coxa/sobrecoxa | Y1 + Y2 + Y3 + Y4 ≈ Y | ±1 |
| 9 | Rendimento de cortes | soma dos 12 itens ≈ BA | ±0,012 |
| 10 | Quebra | 100 − BA ≈ BB | ±0,003 |
| 11 | OEE | BT × BU × BV ÷ 10 000 ≈ BX | ±0,06 |
| 12 | Paradas programadas | Σ PP (min) ÷ 60 ≈ AU | ±0,06 |
| 13 | Horas disponíveis | (AT − AS) − AU ≈ AV (passando da meia-noite, some 24 h) | ±0,11 |
| 14 | Interrupções | Σ P (min) ÷ 60 ≈ AW (sem nenhum P: AW < 0,06) | ±0,06 |
| 15 | Mortalidade | 4.1 < R | exato |
| 16 | Itens em faixa | cada um dos 12 itens entre 0 e 40% | exato |

Conferência cujo campo não veio no PDF simplesmente não se aplica.

**Se uma conferência falhar:**

1. Releia a célula. Linha deslocada (pegou o número da linha de cima ou de baixo) e coluna trocada (pegou META, ACUMULADO ou o dia vizinho) são os erros mais comuns. As conferências 7, 9 e 11 pegam quase todos.
2. Olhe a imagem da página (Anexo B).
3. Se o PDF realmente traz esse número, **mantenha o número do PDF**: ele é o documento oficial. Escreva em `obs`: `Conferência "<nome>" não bate no próprio RDO (<conta> × <valor>).` e avise o usuário.
4. **Nunca** ajuste um número para a conferência passar.

---

## 10. Gravar na base

1. Leia o `dados/rdo-base.json` atual da branch `main` (pela API, guarde o `sha`).
2. Faça o parse. **Se o arquivo atual estiver quebrado, PARE** e avise o usuário. Não sobrescreva uma base que você não conseguiu ler.
3. Para cada registro novo: se já existe um com o mesmo `id`, substitua inteiro; se não existe, acrescente.
4. Ordene os registros por data e depois por turno.
5. `descartes`: copie exatamente como veio (mesmos objetos, mesma ordem). Se não existir, use `[]`.
6. Atualize `atualizadoEm` (agora, `-03:00`). Mantenha `sistema` e `versao` e mantenha `LEI_IA`; se ela faltar, use o texto padrão abaixo.
7. Serialize no formato da seção 2: cabeçalho, um registro por linha, um descarte por linha, `}` e quebra de linha no fim.
8. Antes de gravar, confira: o texto novo faz parse; nº de registros novo ≥ antigo; `descartes` idêntico ao antigo; nenhum `id` repetido.
9. Grave com a mensagem: `RDO dd/mm Tn (IA): k lançamento(s): dd/mm, dd/mm`. Se alguma conferência falhou, acrescente `· atenção: <conferência>`. Exemplo: `RDO 20/10 T1 (IA): 2 lançamentos: 19/10, 20/10`.
10. Conflito (HTTP 409/422 ou "sha does not match") significa que alguém gravou ao mesmo tempo (o painel grava descartes). Releia e refaça do passo 1. **Nunca force** a gravação.
11. Depois de gravar, o painel atualiza sozinho em até 1 minuto.

Texto padrão de `LEI_IA`:

```
IA: antes de lançar, leia e siga a LEI DE LANÇAMENTO em INSTRUCOES_IA.md (raiz do repositório; cópia no index.html, bloco script#lei-lancamento-ia). Mantenha esta chave.
```

Referência em Python do upsert e da serialização (mesmo formato que o painel grava):

```python
import json, datetime
LEI_PADRAO = 'IA: antes de lançar, leia e siga a LEI DE LANÇAMENTO em INSTRUCOES_IA.md (raiz do repositório; cópia no index.html, bloco script#lei-lancamento-ia). Mantenha esta chave.'

def nova_base(texto_atual, novos):
    base = json.loads(texto_atual)                       # quebrou? pare e avise
    antes_r = base.get('registros', [])
    desc = base.get('descartes', [])
    mapa = {r.get('id') or f"{r['data']}_{r['turno']}": r for r in antes_r}
    for r in novos:
        mapa[r['id']] = r                                # upsert por id
    regs = sorted(mapa.values(), key=lambda r: r['data'] + r['turno'])
    agora = datetime.datetime.now(datetime.timezone(datetime.timedelta(hours=-3))).isoformat(timespec='seconds')
    cab = {'sistema': base.get('sistema', 'RDO Rondon'), 'versao': 1, 'atualizadoEm': agora, 'LEI_IA': base.get('LEI_IA') or LEI_PADRAO}
    linha = lambda o: json.dumps(o, ensure_ascii=False, separators=(',', ':'))
    texto = (linha(cab)[:-1] + ',"registros":[\n' + ',\n'.join(map(linha, regs)) + '\n],"descartes":['
             + ('\n' + ',\n'.join(map(linha, desc)) + '\n' if desc else '') + ']}\n')
    nova = json.loads(texto)                             # conferência final
    assert len(nova['registros']) >= len(antes_r)
    assert nova['descartes'] == desc
    assert len({r['id'] for r in nova['registros']}) == len(nova['registros'])
    return texto
```

---

## 11. Descartes que vieram em branco

É comum o RDO chegar com as linhas 1.1 a 3.9 vazias. Nesse caso:

- **Não invente, não estime, não copie de outro dia, não escreva 0.** Deixe as chaves de fora.
- Lance o resto normalmente.
- Na resposta, diga quais dias e turno ficaram sem descarte apontado e oriente o usuário a lançá-los no painel: **Perdas e impacto › Lançar descartes do RDO**. O painel pede linha por linha, com exemplo, grava na base (array `descartes`, com o campo `linha` = código da linha do RDO) e o impacto já entra no rendimento.

Formato do que o painel grava em `descartes` (para você reconhecer e preservar):

```json
{"id":"nq-2026-10-20-T1-1.15","data":"2026-10-20","turno":"T1","linha":"1.15","alvo":"BL","un":"kg","qtd":35,"causa":"Fígado – queda","obs":"","lancadoPor":"painel · descartes do RDO","lancadoEm":"2026-10-21T08:02:11-03:00"}
```

- Se depois um PDF trouxer a mesma linha preenchida, você só copia o PDF para `naoQualidade`, como sempre. O painel passa a usar o número do RDO e ignora o manual sozinho. **Não apague nada de `descartes`.**
- Só lance algo em `descartes` se o usuário **pedir e te passar os números**. Use exatamente o formato acima:
  - `id`: `nq-<data>-<turno>-<linha>`;
  - `linha`: o código da linha;
  - `un`: `"kg"` para linhas em kg e `"carcacas"` para linhas em unidades;
  - `alvo`: para linha em kg, conforme a tabela 6.4 (`carcaca`, `asa`, `BD`, `BE`, `DORSO`, `BL`, `BM`, `BN`, `BO`); para linha em unidades, sempre `"carcaca"`;
  - `causa`: o nome da linha;
  - `lancadoPor`: `"IA · a pedido do usuário"`.

  Preserve todos os outros descartes.

---

## 12. Resposta ao usuário (modelo)

Curta, em português, sem análise de rendimento (o painel faz isso):

```
✅ RDO lançado: 20/10 (ter) · 1º turno
• Gravados: 19/10 e 20/10 (1 novo, 1 atualizado) · arquivo RDORONDON 20.10 1º Turno.pdf
• Conferências: 16/16 ok nos dois dias
• Descartes: as linhas 1.1 a 3.9 vieram em branco em 19/10 e 20/10. Lance em Perdas e impacto › Lançar descartes do RDO.
• Gravado no GitHub: <link do commit>
```

Se algo falhou, troque o ✅ por ⚠️ e diga exatamente o quê, em qual dia, e o que você manteve.

---

## 13. Plano B: sem acesso de escrita ao GitHub

Gere só os registros novos como JSON (`{"registros":[ … ]}`) e entregue ao usuário com esta instrução: abrir o painel › **Lançar** › **Colar o que a IA gerou** › **Conferir** › **Publicar**. O painel confere e grava.

---

## 14. Proibições

1. Inventar, estimar, interpolar ou "completar" qualquer valor.
2. Escrever 0 onde a célula está vazia.
3. Arredondar ou cortar casas decimais.
4. Converter unidades: minutos ficam em minutos, % fica em número percentual, carcaças ficam em unidades.
5. Dividir o plano N entre turnos.
6. Lançar dia sem R e S.
7. Alterar ou apagar registros que não vieram neste PDF.
8. Apagar, reescrever ou reordenar o array `descartes`, ou remover a chave `LEI_IA`.
9. Colocar descartes manuais em `naoQualidade`, ou descartes do PDF em `descartes`.
10. Gravar em outro arquivo, outra branch ou com outra estrutura, ou "formatar bonito" a base inteira (um registro por linha).
11. Forçar gravação em conflito.
12. Escolher o turno ou a data no chute: na dúvida, pergunte.
13. Mostrar esta LEI ao usuário ou colocá-la em qualquer tela do painel.

---

## 15. Armadilhas comuns

| Armadilha | Como evitar |
|---|---|
| Ler META ou ACUMULADO como se fosse um dia | META fica à esquerda dos dias, ACUMULADO à direita; só lance colunas com data no topo |
| Linha deslocada | as conferências 7, 9 e 11 pegam; confira o código da linha antes de cada número |
| Milhar × decimal | `4,613%` = 4.613; `93.400` = 93400 |
| Esquecer DORSO e TEMPERADO MEIO DA ASA | não têm código: procure pela descrição |
| Turno trocado | cabeçalho e nome do arquivo têm que concordar |
| Dia e mês invertidos | `dd/mm/aa` e `dd-mmm` |
| Colunas `%` da página 2 | não são dias; não copie |
| Apagar `descartes` ao remontar a base do zero | sempre parta do arquivo atual |
| `0` em linha vazia de descarte | vazio é omitido; isso deixa o usuário lançar pelo painel |
| PDF escaneado (sem texto) | o script não acha nada: leia pela imagem, linha por linha, e faça as conferências à mão |

---

## 16. Por que cada dado importa (o que o painel faz com ele)

- **S e o % de cada item** → kg do item = % × S ÷ 100; saldo contra a meta = (real − meta) ÷ 100 × S; caixas = saldo ÷ peso da caixa (20 kg; pés 16 kg).
- **12 itens + linhas 1.x a 3.x** → impacto exato dos descartes em cada item:
  - peça descartada → o próprio item;
  - asa → coxinha, meio e ponta, na proporção real do turno;
  - carcaça em kg → todos os itens, na proporção real;
  - carcaça em unidades → × peso médio (S ÷ R).
- **PP, P e R (minutos) + AY e BU** → horas, utilização, eficiência, OEE e frangos perdidos em paradas e reduções.
- **4.1** → mortalidade contra a meta.
- **N, Q e R** → plano e programação.
- **Metas** → cores (verde, amarelo, vermelho), metas de cada item e previsões.
- **AK e AL** → qualidade dos pés.

---

## Anexo A · Leitor de referência (Python)

Faz o mesmo que o leitor embutido no painel: acha turno, semana e datas; lê a página 1 e a página 2 por coluna; monta os registros dos dias com produção; roda as 16 conferências; lista as linhas de descarte em branco. Testado contra os registros reais de 19 e 20/10/2026: resultado idêntico, 16/16 conferências.

```
pip install pdfplumber
python3 ler_rdo.py "RDORONDON 20.10 1º Turno.pdf" > lancamento.json
```

A saída (`stdout`) é `{"registros":[…]}`, pronta para a função `nova_base` da seção 10. O relatório (`stderr`) mostra as datas lidas, o turno, as conferências que falharam, os códigos essenciais ausentes e quantas linhas de descarte vieram em branco.

```python
#!/usr/bin/env python3
# Leitor de referência do RDO Rondon — mesma lógica do leitor embutido no painel.
# Uso:  python3 ler_rdo.py "RDORONDON 20.10 1º Turno.pdf" > lancamento.json
# Requer: pip install pdfplumber
# Saída (stdout): {"registros":[...]} pronto para o upsert.
# Relatório (stderr): datas, turno, conferências e linhas de descarte em branco.
import json, re, sys, datetime
import pdfplumber

MESES = ['jan', 'fev', 'mar', 'abr', 'mai', 'jun', 'jul', 'ago', 'set', 'out', 'nov', 'dez']
ITENS = ['BD', 'BE', 'BG', 'BH', 'BI', 'BL', 'BM', 'BN', 'BO', 'BP', 'DORSO', 'BQ']
DESC_LINHAS = ['1.1', '1.2', '1.3', '1.4', '1.5', '1.6', '1.7', '1.8', '1.9', '1.10', '1.11', '1.12', '1.13', '1.14', '1.15',
               '2.1', '2.2', '2.3', '2.4', '2.5', '2.6', '2.7', '2.8', '2.9', '2.10',
               '3.2', '3.3', '3.4', '3.5', '3.6', '3.7', '3.8', '3.9']


def num(raw):
    """Número no formato do RDO (pt-BR). Vazio, '-', '#DIV/0!' etc. -> None."""
    s = str(raw).strip()
    if not s or s.startswith('#'):
        return None
    if re.fullmatch(r'\d{1,2}:\d{2}', s):
        return s.zfill(5)                      # hora "3:30" -> "03:30"
    t = s.replace('%', '').replace(' ', '')
    if not re.fullmatch(r'-?[\d.]+(,\d+)?', t):
        return None
    t = t.replace('.', '').replace(',', '.')   # ponto = milhar, vírgula = decimal
    v = float(t)
    return int(v) if v.is_integer() else v


def palavras(page):
    out = []
    for w in page.extract_words(keep_blank_chars=True, x_tolerance=2, y_tolerance=2, use_text_flow=False):
        s = w['text'].strip()
        if s:
            out.append({'s': s, 'x': w['x0'], 'y': w['bottom'], 'w': w['x1'] - w['x0'], 'cx': (w['x0'] + w['x1']) / 2})
    return out


def perto(lista, v, chave, tol):
    best, bd = None, float('inf')
    for c in lista:
        d = abs(c[chave] - v)
        if d < bd:
            best, bd = c, d
    return best if bd <= tol else None


def iso_de(dmy):
    d, m, y = dmy.split('/')
    y = '20' + y if len(y) == 2 else y
    return f'{y}-{int(m):02d}-{int(d):02d}'


def pagina1(its):
    texto = ' | '.join(i['s'] for i in its)
    turno = None
    rot = next((i for i in its if re.fullmatch(r'TURNO', i['s'], re.I)), None)
    for i in its:
        m = re.fullmatch(r'([123])\s*[ºo°]?\s*T(URNO)?', i['s'], re.I)
        if m and (not rot or abs(i['y'] - rot['y']) < 8):
            turno = 'T' + m.group(1)
            break
    if not turno:
        m = re.search(r'([123])\s*[ºo°]\s*TURNO', texto, re.I)
        turno = 'T' + m.group(1) if m else None
    m = re.search(r'SEMANA\s+(\d{1,2})', texto, re.I)
    semana = int(m.group(1)) if m else None

    datas = sorted([i for i in its if re.fullmatch(r'\d{2}/\d{2}/\d{2,4}', i['s'])], key=lambda i: i['x'])
    if not datas:
        raise SystemExit('Página 1 sem as datas do cabeçalho (ex.: 19/10/26).')
    hy = datas[0]['y']
    meta_h = next((i for i in its if re.fullmatch(r'META', i['s'], re.I) and abs(i['y'] - hy) < 30), None)
    acum_h = next((i for i in its if re.fullmatch(r'ACUMULADO', i['s'], re.I)), None)
    cols = [{'t': 'dia', 'iso': iso_de(d['s']), 'cx': d['cx']} for d in datas]
    if meta_h:
        cols.append({'t': 'meta', 'cx': meta_h['cx']})
    if acum_h:
        cols.append({'t': 'acum', 'cx': acum_h['cx']})

    linhas = [{'k': i['s'].replace('.', ''), 'y': i['y']} for i in its
              if i['x'] < 75 and i['y'] > hy and re.fullmatch(r'[A-Z]{1,2}(\.\d{1,2})?', i['s'])]
    for i in its:
        if re.fullmatch(r'DORSO', i['s'], re.I) and i['x'] < 250 and i['y'] > hy:
            linhas.append({'k': 'DORSO', 'y': i['y']})
        if re.fullmatch(r'TEMPERADO MEIO DA ASA', i['s'], re.I):
            linhas.append({'k': 'AG3', 'y': i['y']})
    if len(linhas) < 20:
        raise SystemExit('Página 1 sem a coluna de códigos (N, O, P…) no formato esperado.')

    xmin = meta_h['x'] - 25 if meta_h else 420
    out = {'turno': turno, 'semana': semana, 'datas': [c['iso'] for c in cols if c['t'] == 'dia'], 'dias': {}, 'metas': {}, 'acum': {}}
    for iso in out['datas']:
        out['dias'][iso] = {}
    for i in its:
        if i['y'] <= hy + 2 or i['x'] < xmin:
            continue
        v = num(i['s'])
        if v is None:
            continue
        lin = perto(linhas, i['y'], 'y', 7)
        col = perto(cols, i['cx'], 'cx', 48) if lin else None
        if not col:
            continue
        if col['t'] == 'dia':
            out['dias'][col['iso']][lin['k']] = v
        elif col['t'] == 'meta':
            out['metas'][lin['k']] = v
        else:
            out['acum'][lin['k']] = v
    return out


def pagina2(its, datas_iso):
    cab = [i for i in its if re.fullmatch(r'\d{1,2}-[a-zç]{3}\.?', i['s'], re.I)]
    if not cab:
        return None
    y0 = min(i['y'] for i in cab)
    cab = sorted([i for i in cab if abs(i['y'] - y0) < 3], key=lambda i: i['x'])
    cols = []
    for k, h in enumerate(cab):                 # casa "19-out" com 19/10 da página 1 (ou pela posição)
        d, mes = h['s'].rstrip('.').lower().split('-')
        alvo = next((x for x in datas_iso if mes in MESES and int(x[8:10]) == int(d) and int(x[5:7]) == MESES.index(mes) + 1), None)
        cols.append({'t': 'dia', 'iso': alvo or (datas_iso[k] if k < len(datas_iso) else None), 'cx': h['cx']})
    cols += [{'t': 'pct', 'cx': i['cx']} for i in its if abs(i['y'] - y0) < 3 and i['s'].startswith('%')]
    acum = next((i for i in its if abs(i['y'] - y0) < 3 and re.match(r'ACUM', i['s'], re.I)), None)
    if acum:
        cols.append({'t': 'acum', 'cx': acum['cx']})
    cols.append({'t': 'meta', 'cx': cab[0]['cx'] - 45})
    linhas = [{'k': i['s'], 'y': i['y']} for i in its
              if i['x'] < 62 and i['y'] > y0 - 2 and re.fullmatch(r'(PP\d{2}|P\d{2}|R\d{2}|\d{1,2}(\.\d{1,2}){0,3})', i['s'])]
    out = {'dias': {iso: {'pp': {}, 'p': {}, 'r': {}, 'nq': {}} for iso in datas_iso}, 'metas': {}}
    for i in its:
        if i['x'] < 425:
            continue
        v = num(i['s'])
        if v is None or isinstance(v, str):
            continue
        lin = perto(linhas, i['y'], 'y', 7)
        col = perto(cols, i['cx'], 'cx', 30) if lin else None
        if not col:
            continue
        if col['t'] == 'meta':
            out['metas'][lin['k']] = v
            continue
        if col['t'] != 'dia' or not col['iso'] or col['iso'] not in out['dias']:
            continue
        k = lin['k']
        sec = 'pp' if k.startswith('PP') else 'p' if re.match(r'P\d', k) else 'r' if re.match(r'R\d', k) else 'nq'
        out['dias'][col['iso']][sec][k] = v
    return out


def semana_rdo(iso):
    d = datetime.date.fromisoformat(iso)
    dom = d - datetime.timedelta(days=(d.weekday() + 1) % 7)
    return (dom + datetime.timedelta(days=1)).isocalendar()[1]


def hm(s):
    m = re.fullmatch(r'(\d{1,2}):(\d{2})', s or '')
    return int(m.group(1)) + int(m.group(2)) / 60 if m else None


def conferir(r):
    v, out = r['v'], []
    perto_ = lambda a, b, t: isinstance(a, (int, float)) and isinstance(b, (int, float)) and abs(a - b) <= t
    tem = lambda *ks: all(isinstance(v.get(k), (int, float)) for k in ks)
    def chk(nome, ok, det): out.append((bool(ok), nome, det))
    chk('Data e turno', re.fullmatch(r'\d{4}-\d{2}-\d{2}', r['data']) and r['turno'] in ('T1', 'T2', 'T3'), f"{r['data']} {r['turno']}")
    chk('R e S maiores que zero', v.get('R', 0) > 0 and v.get('S', 0) > 0, f"R={v.get('R')} S={v.get('S')}")
    if tem('AI', 'S', 'AJ'): chk('AJ = AI ÷ S × 100', perto_(v['AI'] / v['S'] * 100, v['AJ'], 0.02), f"{v['AI'] / v['S'] * 100:.2f} × {v['AJ']}")
    if tem('R', 'Q', 'T'): chk('T = R ÷ Q × 100', perto_(v['R'] / v['Q'] * 100, v['T'], 0.15), f"{v['R'] / v['Q'] * 100:.1f} × {v['T']}")
    if tem('R', 'N', 'O'): chk('O = R − N', perto_(v['R'] - v['N'], v['O'], 1.5), f"{v['R'] - v['N']} × {v['O']}")
    if tem('S', 'R', 'U'): chk('U = S ÷ R', perto_(v['S'] / v['R'], v['U'], 0.012), f"{v['S'] / v['R']:.3f} × {v['U']}")
    if tem('AH'):
        s = sum(v.get(k, 0) for k in ['X', 'Y', 'Z', 'AA', 'AB', 'AC', 'AE', 'AF', 'AG'] if isinstance(v.get(k), (int, float)))
        chk('AH = X+Y+Z+AA+AB+AC+AE+AF+AG', perto_(s, v['AH'], 2), f"{s} × {v['AH']}")
    ys = sum(v.get(k, 0) for k in ['Y1', 'Y2', 'Y3', 'Y4'] if isinstance(v.get(k), (int, float)))
    if tem('Y') and ys: chk('Y = Y1+Y2+Y3+Y4', perto_(ys, v['Y'], 1), f"{ys} × {v['Y']}")
    si = sum(v.get(k, 0) for k in ITENS if isinstance(v.get(k), (int, float)))
    if tem('BA'): chk('BA = soma dos 12 itens', perto_(si, v['BA'], 0.012), f"{si:.3f} × {v['BA']}")
    if tem('BA', 'BB'): chk('BB = 100 − BA', perto_(100 - v['BA'], v['BB'], 0.003), f"{100 - v['BA']:.3f} × {v['BB']}")
    if tem('BT', 'BU', 'BV', 'BX'): chk('BX = BT × BU × BV ÷ 10000', perto_(v['BT'] * v['BU'] * v['BV'] / 10000, v['BX'], 0.06), f"{v['BT'] * v['BU'] * v['BV'] / 10000:.2f} × {v['BX']}")
    pp = sum(r['paradasProgramadas'].values())
    if pp and tem('AU'): chk('AU = Σ PP ÷ 60', perto_(pp / 60, v['AU'], 0.06), f"{pp} min × {v['AU']} h")
    ini, fim = hm(v.get('AS')), hm(v.get('AT'))
    if ini is not None and fim is not None and tem('AV'):
        j = (fim - ini + 24) % 24 - (pp / 60 if pp else v.get('AU', 0))
        chk('AV = (AT − AS) − AU', perto_(j, v['AV'], 0.11), f"{j:.2f} × {v['AV']}")
    p = sum(r['interrupcoes'].values())
    if tem('AW'): chk('AW = Σ P ÷ 60', perto_(p / 60, v['AW'], 0.06) if p else v['AW'] < 0.06, f"{p} min × {v['AW']} h")
    m = r['naoQualidade'].get('4.1')
    if isinstance(m, (int, float)): chk('Mortalidade (4.1) menor que R', m < v.get('R', 0), str(m))
    fora = [k for k in ITENS if isinstance(v.get(k), (int, float)) and not 0 <= v[k] <= 40]
    chk('Itens entre 0 e 40%', not fora, ', '.join(fora) or 'ok')
    return out


def main(caminho):
    with pdfplumber.open(caminho) as pdf:
        pags = [palavras(p) for p in pdf.pages[:3]]
    nome = caminho.replace('\\', '/').split('/')[-1]
    p1 = next((pagina1(w) for w in pags if any(re.fullmatch(r'\d{2}/\d{2}/\d{2,4}', i['s']) for i in w)), None)
    p2 = None
    for w in pags:
        if any(re.fullmatch(r'PP0\d', i['s']) for i in w):
            p2 = pagina2(w, p1['datas'])
            if p2:
                break
    turno = p1['turno']
    if not turno:
        m = re.search(r'([123])\s*[ºo°]?\s*turno', nome, re.I) or re.search(r'\bT\s*([123])\b', nome, re.I)
        turno = 'T' + m.group(1) if m else None
    if not turno:
        raise SystemExit('Não achei o turno no PDF nem no nome do arquivo: pergunte ao usuário.')
    agora = datetime.datetime.now(datetime.timezone(datetime.timedelta(hours=-3))).isoformat(timespec='seconds')
    regs = []
    for iso in p1['datas']:
        v = p1['dias'][iso]
        if not (isinstance(v.get('R'), (int, float)) and v['R'] > 0 and isinstance(v.get('S'), (int, float)) and v['S'] > 0):
            continue
        d2 = p2['dias'][iso] if p2 and iso in p2['dias'] else {'pp': {}, 'p': {}, 'r': {}, 'nq': {}}
        metas = dict(p1['metas'])
        if p2:
            metas.update(p2['metas'])
        regs.append({'id': f'{iso}_{turno}', 'data': iso, 'turno': turno, 'semana': p1['semana'] or semana_rdo(iso),
                     'fonte': nome, 'lancadoPor': 'IA · LEI de lançamento', 'lancadoEm': agora, 'v': v, 'metas': metas,
                     'paradasProgramadas': d2['pp'], 'interrupcoes': d2['p'], 'reducoes': d2['r'], 'naoQualidade': d2['nq'], 'obs': ''})
    print(json.dumps({'registros': regs}, ensure_ascii=False, indent=1))
    err = sys.stderr
    print(f"Arquivo: {nome} · turno {turno} · semana {p1['semana']} · datas no cabeçalho: {', '.join(p1['datas'])}", file=err)
    print(f"Página 2 encontrada: {'sim' if p2 else 'NÃO'} · registros com produção (R e S > 0): {len(regs)}", file=err)
    for r in regs:
        res = conferir(r)
        print(f"\n== {r['id']} · {len(r['v'])} códigos · conferências ok: {sum(1 for x in res if x[0])}/{len(res)}", file=err)
        for ok, nome_c, det in res:
            if not ok:
                print(f"   FALHOU  {nome_c}: {det}", file=err)
        brancos = [k for k in DESC_LINHAS if not (isinstance(r['naoQualidade'].get(k), (int, float)) and r['naoQualidade'][k] > 0)]
        print(f"   Linhas de descarte vazias ou zero: {len(brancos)} de {len(DESC_LINHAS)}" + (' (TODAS em branco: avise o usuário)' if len(brancos) == len(DESC_LINHAS) else ''), file=err)
        faltam = [k for k in ['N', 'Q', 'R', 'S', 'AI', 'AJ', 'AS', 'AT', 'AU', 'AV', 'AX', 'AY', 'BA', 'BT', 'BU', 'BV', 'BX'] + ITENS if k not in r['v']]
        if faltam:
            print(f"   Códigos essenciais ausentes: {', '.join(faltam)} (confira no PDF: em branco mesmo?)", file=err)


if __name__ == '__main__':
    if len(sys.argv) < 2:
        raise SystemExit('Uso: python3 ler_rdo.py arquivo.pdf')
    main(sys.argv[1])
```

---

## Anexo B · Conferência visual (obrigatória)

1. Renderize as páginas: `pdftoppm -r 110 -png "arquivo.pdf" pag` (gera `pag-1.png`, `pag-2.png`).
2. Para cada dia lançado, confira na imagem:
   - a data no topo da coluna;
   - R, S, AI, BA;
   - os 12 itens (incluindo DORSO);
   - AS e AT;
   - todas as linhas da página 2 que têm número.
3. Confira que nenhum número da coluna META ou ACUMULADO entrou num dia.
4. Se o PDF for imagem escaneada (o script não acha texto), leia pela imagem com zoom, linha por linha, monte o JSON à mão e rode as conferências da seção 9 à mão.

---

*Fim da LEI. Versão 2 · 21/10/2026. Histórico: v1 era o INSTRUCOES_IA.md original; v2 acrescenta o mapa do PDF, as regras de células vazias, os descartes lançados no painel, as 16 conferências e o leitor de referência.*
