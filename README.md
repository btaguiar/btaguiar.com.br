# Portfólio — Bruno Aguiar

Landing page de portfólio de AI Engineer. HTML, CSS e JS puros. Sem build.

## Rodar

```bash
python3 -m http.server 8000
```

Abre `http://localhost:8000`. Precisa de servidor HTTP — em `file://` os
acentos quebram, porque o charset vem do header da resposta.

## Estrutura

```
index.html            página inteira: estilos, markup e script
assets/
  hero.mp4            hero, 16,5s — neurônios + globo emendados (H.264, 8,1 MB)
  hero.webm           mesmo vídeo em VP9, reserva (3,9 MB)
  hero-poster.jpg     primeiro quadro, evita piscar preto no carregamento
  hero.jpg            globo estático, reserva do canvas
  cortex.jpg          faixa da seção Cortex
  pauta.jpg           card do pauta
  grifo.jpg           card do grifo
  reach.jpg           card do Agent-Reach
  og.jpg              imagem de compartilhamento, 1200x630
CLAUDE.md             contexto e regras do projeto
vercel.json           headers de cache
```

## Publicar

```bash
npx vercel --prod
```

Ou conecte o repositório em vercel.com. Sem build: framework preset "Other",
output directory a raiz.

## Antes de publicar

Trocar `SEU-DOMINIO.com.br` pelas URLs reais nas meta tags do `<head>` —
`canonical`, `og:url` e `og:image`. O Open Graph precisa de URL absoluta.

As regras de conteúdo e os números verificados estão no `CLAUDE.md`.
