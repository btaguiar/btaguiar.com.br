# Portfólio — Bruno Aguiar

Landing page de portfólio de AI Engineer. HTML, CSS e JS puros. Sem build.

## Rodar

```bash
python3 -m http.server 8000
```

Abre `http://localhost:8000`. Precisa de servidor HTTP — em `file://` os
acentos quebram, porque o charset vem do header da resposta.

`.claude/launch.json` sobe o mesmo servidor na **porta 8123**, usada porque a
8000 costuma estar ocupada. Qualquer porta serve; o site não depende disso.

## Estrutura

```
index.html            página inteira: estilos, markup e script
assets/
  hero-v2.webm        hero, 19,6s — neurônios + globo + explosão (VP9, 4,2 MB)
  hero-v2.mp4         mesmo vídeo em H.264, para o Safari (6,1 MB)
  hero-poster.jpg     primeiro quadro, evita piscar preto no carregamento
  hero.jpg            globo estático, reserva se o vídeo não tocar
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
