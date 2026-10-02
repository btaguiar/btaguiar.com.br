# Portfólio — Bruno Aguiar

Landing page estática de portfólio. Vende o Bruno como **AI Engineer que coloca
sistemas de agentes em produção**. Público: recrutadores tech, tech leads e
gestores de contratação. Idioma: **português do Brasil, só**.

## A tese

O diferencial vendido aqui não é stack, é **postura**: todo número publicado
tem procedência conferível, incluindo os que não favorecem o Bruno. A mesma
regra aparece no `BRIEF.md` do pme-risk, no README do quimera e na landing do
quimera ("números medidos, não prometidos"). A página inteira é construída
sobre isso, e a seção `#numeros` é a tese literal: valor, o que é, como foi
medido.

**Consequência prática:** a AUC de 0,5845 do pme-risk fica à vista, ao lado
dos 23 agentes. Esconder a AUC atrás do F1 da extração destruiria o
argumento da página inteira.

Site de uma página. HTML, CSS e JS puros, sem build, sem framework, sem
dependência em `node_modules`. Tudo vive em `index.html` mais `assets/`.

---

## Regras que não podem ser quebradas

1. **Nenhum número sem fonte.** Todo número da página está na tabela abaixo.
   Não arredonde para cima, não invente métrica nova, não invente nome de agente.
   Um avaliador que confere um número inflado descarta o candidato.
2. **A grade de horários é agenda, não status ao vivo.** A página não sabe se um
   job rodou. Nunca escreva "rodando agora", "online" ou "ao vivo" sobre o
   scheduler. O HUD diz "próximo na agenda", que é verdade porque é calculado
   da tabela de horários.
3. **Não citar "BetLab"** e não usar a expressão "transição de carreira". A
   origem em comunicação aparece como qualidade ("ainda meço tudo em
   resultado"), nunca como mudança de área.
4. **Nada de dado privado:** sem IP, caminho de servidor, nome de variável de
   ambiente ou qualquer detalhe do `.env`.
5. **AWS fica de fora de propósito.** O Bruno considera o próprio conhecimento
   de AWS básico. Não adicione à ficha técnica.

## Números verificados

Reconferidos em **01/10/2026**. Vários mudaram desde 20/09.

| Número | Valor | Como foi medido |
|---|---|---|
| Agentes | **24** | Pastas em `src/cortex/agents/*/*` com `runner.py` ou `agent.py` |
| Jobs agendados | **39** | 42 chamadas ativas em `run_scheduler.py`, menos 3 do Reddit |
| Testes | **2.146** | `pytest --collect-only`, **sem erro de coleta** |
| Servidor | **1 VPS**, sem supervisão | systemd; API, painel e scheduler |

**Os testes subiram de 1.963 para 2.146 e os 8 erros sumiram.** O import path
de `nexus_global` foi corrigido. A página guarda isso como histórico ("eram
1.963 com 8 arquivos que nem eram lidos"), porque o movimento é a prova de
que o piso declarado era piso mesmo.

**O comando antigo de contar jobs morreu.** O scheduler virou
`UnifiedScheduler` e não loga mais `Configurando schedule` / `job adicionado`
do jeito antigo; o journal também rotaciona e perde o start. Contagem atual
vem da fonte, e **tem que descontar o kill-switch**:

```bash
for d in src/cortex/agents/*/*/; do [ -f "$d/runner.py" ] || [ -f "$d/agent.py" ] && echo "$d"; done | wc -l
grep -cE '^[[:space:]]*self\.add_(cron|interval)_job\(' scripts/run_scheduler.py   # 42
grep -c 'Reddit desligado' logs/scheduler.log                                       # confirma os 3 fora
python -m pytest --collect-only -q | tail -1
```

### quimera

Medido em **01/10/2026, árvore limpa**. Três conjuntos, e eles não são
intercambiáveis.

| Conjunto | n | Aprovação | Precisão de linha | Commit |
|---|---|---|---|---|
| Inéditos (o que vale) | **1.000** | **95,4%** | — | `6f39566` |
| Golden principal | 28 | 92,9% | 98,1% | `4d79a1b` |
| Holdout, sem curadoria | 33 | **87,9%** | **91,0%** | `4d79a1b` |

Metas: principal 90% / 98% (batida). Holdout 90% / 92% (**abaixo nas duas**).
Recusa correta 1,0 em todos. 27,8 M estabelecimentos (`dados.py:98`).

Três regras:

1. **O número de capa é o de 1.000**, com margem de 1,3 ponto. Em 28 ou 33
   casos uma única falha move 3 pontos: não é medição, é ruído.
2. **O holdout abaixo da meta fica na página.** É o custo da régua, do mesmo
   jeito que os 17% do grifo.
3. **Nunca publicar número de eval rodado com árvore suja.** Houve uma medição
   com commit `6f39566+alterações`: o sufixo é o script avisando que havia
   arquivo modificado fora de qualquer commit, então o valor não é
   reproduzível por hash. Rodar limpo e reconferir.

### pme-risk

| Número | Valor | Fonte |
|---|---|---|
| AUC | **0,5845** | `logreg_v3`, n=63.502. Sorteio = 0,50 |
| Brier | **0,0638** | baseline ingênuo = 0,0640 |
| F1 da extração | **0,9868** | `laudo_eval_v7`, 2 rodadas iguais, golden de 80 itens |

**A AUC é fraca e isso vai na página.** O modelo praticamente não discrimina
neste dataset. A entrega é a engenharia em volta.

**O F1 é 0,9868, nunca "0,99".** Arredondar para cima é o que a regra 1 proíbe
em uma linha. E ele carrega ressalva própria, registrada no
`PLANO-pme-risk.md`: parte do ganho de `finalidade` (0,86 → 0,99) veio de
método, e o lote novo foi escrito junto com as regras do prompt, então **não é
holdout independente**. O `docs/eval/avaliacao-2026-09-28.md` tem um achado
chamado "F1 da extração inflado por defeitos metodológicos". A ressalva anda
com o número.

### grifo

| Número | Valor | Fonte |
|---|---|---|
| fonte@5 | **0,79** | **53 itens em escopo** (`EVALUATION.md:86`) |
| Recusas corretas | **11/11** | 11 fora de escopo |
| Trechos indexados | **6.551** | 24 sessões (`EVALUATION.md:3`) |
| Falsas recusas | **17%** (9 perguntas) | `README.md:116` |

O golden set tem **64 itens = 53 em escopo + 11 fora**. Por isso 11/11 e 0,79
têm denominadores diferentes e os dois estão certos. **Os 6.551 vivem no
`EVALUATION.md`, não no README** — procurar só no README dá falso negativo,
já aconteceu.

1. **"11/11", nunca "100%".**
2. **Os 17% andam junto** do 11/11.

O chip do card **não** diz "Reranker": o cross-encoder foi desligado por
medição. O que roda é híbrido vetorial + BM25 com RRF.

### Não reconferido

**1.645% de ROI (SCS) e −30% de CPA (BK-DEP).** Os repositórios não estão em
`C:\Dev`, só no GitHub, em notebook. Ninguém reconferiu esses dois nesta
rodada.

> O README público (`btaguiar/btaguiar`) e a descrição do `cortex-multi-agent`
> ainda têm números velhos. Os certos são os desta seção.

---

## Arquitetura da página

Arquivo único, `index.html`, em três blocos: `<style>` no head, markup, e um
`<script>` IIFE no fim. Sem módulos e sem bundler — é proposital: o site tem que
renderizar igual daqui a dois anos sem ninguém rodar `npm install`.

### Motor de scroll

Não usa `IntersectionObserver`. Ele falha em contextos onde a viewport não é a
que se espera, e deixava elementos presos em `opacity: 0` — buracos na página.

Em vez disso há um `tick()` que mede `getBoundingClientRect()` contra a
viewport real, alimentado por:

- `scroll` em **fase de captura** no `document` (pega o scroll de qualquer
  container, inclusive de um host que embuta a página)
- `wheel`, `touchmove`, `resize`
- um heartbeat de `setInterval(tick, 180)` como rede de segurança
- `visibilitychange`, que força um tick ao voltar para a aba

**Se for mexer nas animações, mantenha esse desenho.** Qualquer coisa que possa
deixar conteúdo invisível precisa de um caminho que o torne visível de novo.
A armadilha concreta está em "Motor de revelação", abaixo.

### Hero

Sem vídeo. Sem canvas. Sem scrub.

Existiu um hero de 200vh com vídeo de 19,58s controlado por `playbackRate`,
mais um globo de partículas em canvas de reserva. **Saiu no rebrand de
30/09/2026**, por contradição com a tese: um argumento sobre não inflar nada
não pode gastar a primeira tela e 4,2 MB em espetáculo gerado. Os arquivos
estão em `_arquivo/` (fora do git) caso alguém queira de volta. `assets/`
caiu de 11 MB para 100 KB.

O hero agora é `min-height:100dvh`, grade assimétrica de duas colunas:
título e CTAs à esquerda, ficha do sistema principal à direita, separada por
hairline. Quatro elementos de texto no total, que é o teto: selo, título,
lede, CTAs.

**O título tem que caber em 2 linhas no desktop.** Título de 4 linhas é erro
de corpo de fonte, nunca de tamanho de texto. `max-width:26ch` com
`clamp(30px,4.3vw,50px)` dá 2 linhas; se mexer num, confira o outro.

### Fundo do hero: campo de sonar

Canvas de pontos onde ondas se expandem a partir de um ponto. Portado à mão
de um componente React (shadcn + Tailwind + `motion`) para JS puro: **este
projeto não tem build**, então o arquivo da referência não serve, só a
técnica.

**Duas cores, não uma.** Na referência os pontos e a onda compartilham a cor
do tema. Aqui os pontos ficam em `--ink-3` a 16% de alpha e **só a frente de
onda acende em `--accent`**. O dourado desta página significa "valor medido";
se o fundo inteiro for dourado, o acento vira papel de parede e a tabela de
números perde força. O sinal é a onda que passa, não o campo.

Disciplina mantida do original, e medida:

- para de repintar quando nenhuma onda está viva (volta por `setTimeout`)
- para quando o hero sai da tela (conferido: o canvas congela)
- para em aba oculta, acorda em `visibilitychange`
- sob `prefers-reduced-motion` desenha a grade **estática**, sem laço e sem ping
- DPR limitado a 2

Não usa `IntersectionObserver`: o projeto já mede com `getBoundingClientRect`
e não vale manter dois mecanismos para a mesma pergunta.

**Regra 2 vale aqui.** O ping é ambiente e não representa job rodando. Não
ligue a origem nem o intervalo ao array `SCHEDULE`, e não rotule o campo: a
página não sabe se um job rodou, e sugerir que sabe quebra a regra 2 de um
jeito difícil de desfazer depois.

### Fundo do site: duas camadas fixas

`.bggrid` (z -4): grade de pontos de 30px, mesmo passo do campo vivo do hero.
Papel quadriculado. **Sem máscara.** Já teve uma `mask-image` que apagava o
terço inferior de toda tela e o fundo simplesmente sumia fora do hero.

`.bgfx` (z -3): **um** brilho dourado que migra entre as telas. Um elemento
só, animando apenas `transform` e `opacity`, trocado por
`html[data-screen="..."]`, que o `tick()` escreve elegendo a seção mais
próxima do centro da viewport. Nove alvos, todos distintos, conferidos.

O hero tem `background: var(--void)` para tapar a grade estática: senão ela
some por baixo do campo vivo e os dois se somam.

Isto **não** é um tema por seção. Continua escuro único; o que muda é posição,
escala e opacidade de um brilho dentro da mesma família. Nenhuma seção inverte.

### Lente de cursor no hero

Os pontos perto do cursor afastam (`LENS_PUSH`) e acendem em `--ink`, com teto
de alpha mais baixo que o da onda: **o cursor não compete com o sinal**. A onda
continua sendo a única coisa dourada.

Veio de uma referência React que ligava partículas com linhas e pintava tudo de
roxo. **As linhas não foram portadas**: malha de partículas conectadas é a cara
do `particles.js` e é o fundo mais genérico que existe em site de IA. O roxo
quebraria o acento único.

Só liga com `(pointer: fine)`: em toque não existe hover, e o `pointerdown`
já dispara onda. O laço fica vivo enquanto o cursor está dentro
(`rings.length || mouseIn`) e um `pointerleave` dá o quadro final de repouso.

### Verificar isto com o painel oculto não funciona

Com `document.hidden` o navegador **suspende transições CSS e estrangula o
rAF** (medido: 2 quadros em 600ms). Consequências para quem for testar:

- `getComputedStyle(...).opacity` devolve o valor inicial, não o alvo
- capturas de tela saem **velhas**, e duas seguidas podem vir idênticas
- o campo do hero não repinta, então lente e onda parecem não funcionar

Para conferir de verdade: leia o alvo com `style.transition='none'`, confira
`data-screen` por atributo, e olhe o resultado visual num navegador de verdade.

### Motor de revelação: sem requestAnimationFrame

`tick()` mede `getBoundingClientRect()` contra a viewport real, alimentado
por `scroll` em fase de captura, `wheel`, `touchmove`, `resize`, um heartbeat
de 180ms e `visibilitychange`.

**Não reintroduza `requestAnimationFrame` aqui.** A versão anterior fazia
`queued = true`, agendava o rAF e só limpava `queued` dentro dele. **O rAF não
dispara em aba oculta**, então `queued` travava em `true` e todo tick seguinte
saía na primeira linha: o motor morria e a página ficava com 18 de 21 blocos
presos em `opacity: 0`. Foi medido exatamente assim. O trabalho é um punhado
de `getBoundingClientRect`; roda direto, limitado por timestamp de 60ms.

Detalhe de teste: com a página oculta o navegador **suspende transições CSS**,
então `getComputedStyle(el).opacity` devolve `0` mesmo com a classe `.in`
aplicada e a cascata apontando para `1`. Isso é artefato de medição, não bug.
Para conferir de verdade, leia o alvo da cascata com `transition:none`.

### HUD

Barra fixa no topo com relógio de São Paulo (`Intl.DateTimeFormat` com
`timeZone: "America/Sao_Paulo"`) e o próximo job, calculado do array
`SCHEDULE`. Pula os jobs "seg a sex" no fim de semana e vira para o primeiro do
dia seguinte quando passa de todos.

---

## Identidade visual

Rebrand de 30/09/2026. Linguagem de **relatório de engenharia**: a prova é o
design. Dials usados: `DESIGN_VARIANCE 7`, `MOTION_INTENSITY 4`,
`VISUAL_DENSITY 6`. Movimento baixo e densidade alta de propósito, porque
procedência ocupa espaço: `0,5845` sozinho é ruim, `0,5845 · n=63.502 ·
brier 0,0638 contra 0,0640 ingênuo` é engenharia.

Tokens em `:root`. **Nunca escreva hex direto num componente.**

| Papel | Token | Valor |
|---|---|---|
| Fundo | `--void` | `#0B0C0E` |
| Painel | `--panel` | `#121317` |
| Texto | `--ink` | `#ECEDEF` |
| Texto secundário | `--ink-2` | `#9BA1AB` |
| Rótulos mono | `--ink-3` | `#757C8A` |
| Acento | `--accent` | `#E3B341` |

**Um acento só, travado na página inteira.** O dourado marca valor medido e
nada mais. Não introduza segunda cor: não há verde de "bom" nem vermelho de
"ruim". Número abaixo da meta usa `--ink-2` (classe `.mcell.under`), sem
teatro de semáforo. O azul e o verde da marca anterior saíram.

`--ink-3` dá 4,83:1 sobre o fundo. **Não escureça esse token.**

| Papel | Fonte |
|---|---|
| Títulos e texto | IBM Plex Sans 400-700 |
| Dados e rótulos | IBM Plex Mono 400-600 |

**Duas famílias, não quatro.** Archivo e Bodoni Moda saíram. O itálico serifado
dentro do `h1` sans também saiu: emphasis de família misturada dentro de um
título é amador, e se precisar destacar palavra use itálico ou peso da mesma
família.

**Uma escala de canto: 0px.** Tudo reto, hairlines fazendo o trabalho
estrutural. Não introduza raio arredondado em componente isolado.

Tema escuro único e travado. Nenhuma seção inverte.

## Acessibilidade — não regrida

- Contraste mínimo 4,5:1 em texto pequeno
- Alvos de toque: 44px no rail, 38px nos links de card
- **O rail é `position:fixed` e não ocupa espaço no fluxo.** Ele vai de 22px a
  90px. Acima de 1240px o `.wrap` encolhe por `--rail-safe` (114px) dos dois
  lados, senão o texto passa por baixo dele — já aconteceu, com 37px de
  sobreposição em 1265px. Encolher os dois lados mantém a página centralizada.
  Se mexer no tamanho ou na posição do rail, refaça essa conta.
- `prefers-reduced-motion` desliga o scrub e as revelações (`finalCopy` +
  `revealAll` pintam tudo de uma vez)
- Link "pular para o conteúdo" como primeiro elemento focável
- Hierarquia de títulos sem pular nível (h1 → h2 → h3)
- Sem scroll horizontal em 390px

---

## Rodar

```bash
python3 -m http.server 8000
# abre http://localhost:8000
```

Precisa de servidor HTTP: em `file://` o navegador erra a codificação e os
acentos quebram, porque o charset vem do header.

## Publicar

Vercel, site estático, sem build. `vercel.json` já traz os headers de cache
para `assets/`.

**Não hospedar nem buildar na VPS do Cortex.** A máquina é dedicada ao
scheduler e não tem folga para mais nada. O site vai para a Vercel.

## Pendências

Estado conferido em 01/10/2026.

**Bloqueia publicar:**

- [ ] **Capturas reais das duas demos.** Dois `TODO` no `index.html`:
      `assets/shot-quimera.png` e `assets/shot-pmerisk.png`, 1440x900. Chrome
      e Edge headless abortam o renderer nesta máquina, então é à mão.
      Sem elas, quimera e pme-risk ficam em coluna única de texto.
- [ ] **Domínio.** `btaguiar.com.br` aparece 4 vezes nas meta tags.
      O Open Graph precisa de URL absoluta.
- [ ] Criar o repositório e conectar à Vercel.

**Risco fora do site:**

- [ ] **Turnstile do quimera continua com a chave de teste**
      `1x00000000000000000000AA`, que sempre passa, numa demo aberta que gasta
      BigQuery e Gemini. Conferido no `/config` ao vivo em 01/10.
- [ ] **A descrição do `cortex-multi-agent` no GitHub diz "15 specialized
      agents"** e o site diz 24. É o link que a página chama de espelho
      público: quem clicar vê os dois números.

**Verificação:**

- [ ] Reconferir 1.645% de ROI (SCS) e −30% de CPA (BK-DEP). Nunca foram
      checados; os repositórios não estão em `C:\Dev`.
- [ ] Testar em celular real e com movimento reduzido ligado.
- [ ] Confirmar "Disponível para começar em 30 dias" e se o e-mail fica visível.

**Armadilha de git, já resolvida mas fácil de repetir:** os vídeos
arquivados foram para `_arquivo/` com `git mv`, o que os deixou **staged como
rename**. `.gitignore` não vale para arquivo já rastreado, então o commit
levaria 11 MB de vídeo morto para o histórico de um repositório que vai ser
público. Resolvido com `git rm --cached -f`. Se arquivar asset de novo, use
`mv` e confira `git ls-files --cached _arquivo/`.
