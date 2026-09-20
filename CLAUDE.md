# Portfólio — Bruno Aguiar

Landing page estática de portfólio. Vende o Bruno como **AI Engineer que coloca
sistemas de agentes em produção**. Público: recrutadores tech, tech leads e
gestores de contratação. Idioma: **português do Brasil, só**.

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

Medidos em 18 e 19/09/2026 direto no servidor de produção.

| Número | Valor | Como foi medido |
|---|---|---|
| Agentes | **23** | Pastas em `src/cortex/agents/*/*` com `runner.py` ou `agent.py` |
| Jobs agendados | **38** | Jobs registrados na última inicialização do `scheduler.service` |
| Testes | **1.963** | `pytest --collect-only` |
| Servidor | **1 VPS**, sem supervisão | systemd; API, painel e scheduler |

> O README público (`btaguiar/btaguiar`) ainda diz "27 agentes", "mais de 40
> jobs" e "1.950 testes". **Os números certos são os da tabela.** O README
> precisa ser corrigido em separado.

Para medir de novo, na VPS dentro de `/var/www/app`:

```bash
for d in src/cortex/agents/*/*/; do [ -f "$d/runner.py" ] || [ -f "$d/agent.py" ] && echo "$d"; done | wc -l
journalctl -u scheduler.service --no-pager -o cat | awk '/Configurando schedule/{n=0} /job adicionado/{n++} END{print n}'
python -m pytest --collect-only -q | tail -1
```

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

**Se for mexer nas animações, mantenha esse desenho.** Qualquer coisa que possa
deixar conteúdo invisível precisa de um caminho que o torne visível de novo.

### Hero

Palco de `360vh` com um painel `sticky` de `100dvh` dentro. O progresso do
scroll (0 → 1) controla:

- `video.currentTime` nos primeiros **80%** (`VID_SPAN`)
- a entrada dos cinco blocos de texto, pelo array `CUES`
- o véu de contraste (`.hero-scrim`) e a barra de scrub

O vídeo é **um arquivo só** com dois atos emendados por dissolve no ffmpeg:
neurônios (0–10,5s) → dissolve 1,5s → globo (12–16,5s), 16,54s no total. Dois
`<video>` sincronizados dessincronizam no seek; um arquivo só não tem como.

Encodado com **keyframe a cada 4 quadros** (`-g 4`). Sem isso o navegador
decodifica desde o keyframe anterior a cada seek e o scrub trava. Se
reencodar, mantenha `-g` baixo.

O `<canvas id="globe">` é **reserva**: se o vídeo não tocar, ele desenha um
globo de partículas. Assim que o vídeo carrega, o canvas se desliga sozinho
(`globeStop`) para não gastar CPU.

### HUD

Barra fixa no topo com relógio de São Paulo (`Intl.DateTimeFormat` com
`timeZone: "America/Sao_Paulo"`) e o próximo job, calculado do array
`SCHEDULE`. Pula os jobs "seg a sex" no fim de semana e vira para o primeiro do
dia seguinte quando passa de todos.

---

## Identidade visual

Tokens em `:root`. **Nunca escreva hex direto num componente** — use o token.

| Papel | Token | Valor |
|---|---|---|
| Fundo | `--void` | `#060608` |
| Painel | `--panel` | `#0E0F13` |
| Texto | `--ink` | `#F2F3F6` |
| Texto secundário | `--ink-2` | `#989EAA` |
| Rótulos mono | `--ink-3` | `#757C8A` |
| Acento | `--signal` | `#4D7CFE` |
| Sinal positivo | `--mint` | `#3DD68C` |

`--ink-3` dá **4,83:1** sobre `--void`. O valor anterior (`#5C616B`) dava 3,26:1
e reprovava no mínimo de 4,5:1 da WCAG. **Não escureça esse token.**

| Papel | Fonte |
|---|---|
| Display | Archivo 700–800 |
| Acento itálico | Bodoni Moda italic |
| Texto | IBM Plex Sans |
| Dados e rótulos | IBM Plex Mono |

Tema único escuro, de propósito. Tudo é pintado explicitamente.

## Acessibilidade — não regrida

- Contraste mínimo 4,5:1 em texto pequeno
- Alvos de toque: 44px no rail, 38px nos links de card
- `prefers-reduced-motion` desliga o scrub, o boot e as revelações
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

**Não hospedar nem buildar na VPS do Cortex.** O scheduler ocupa cerca de
1,9 GB de RAM e a máquina tem pouca folga; a porta 80 já serve o painel privado
e o nginx serve o olhonomundo.com.br com TLS.

## Pendências

- [ ] Escolher o domínio e trocar `SEU-DOMINIO.com.br` nas meta tags do `<head>`
- [ ] Confirmar "Disponível para começar em 30 dias" e se o e-mail fica visível
- [ ] Criar o repositório e conectar à Vercel
- [ ] Testar em celular real, nos dois sentidos, e com movimento reduzido ligado
