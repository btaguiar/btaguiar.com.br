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
  og.jpg              imagem de compartilhamento, 1200x630
robots.txt
sitemap.xml
vercel.json           headers de cache
CLAUDE.md             contexto, regras e os números com procedência
.claude/launch.json   preview local na porta 8123
```

Um arquivo e uma imagem. O hero em vídeo saiu no rebrand de 30/09 e levou
junto 11 MB de encode e imagem de banco: `assets/` foi de 11 MB para 100 KB.
Os vídeos ficaram em `_arquivo/`, no disco e fora do git.

Faltam duas capturas que o `index.html` referencia em comentário `TODO`:
`assets/shot-quimera.png` e `assets/shot-pmerisk.png`, 1440x900. Sem elas as
seções do quimera e do pme-risk ficam em coluna única de texto, sem quebrar.

## Publicar

```bash
npx vercel --prod
```

Ou conecte o repositório em vercel.com. Sem build: framework preset "Other",
output directory a raiz.

## Antes de publicar

As meta tags já apontam para `https://btaguiar.com.br`: `canonical`, `og:url`,
`og:image` e `twitter:image`. As de imagem precisam de URL absoluta, senão o
preview não resolve no LinkedIn e no WhatsApp.

**Reconfira os números antes de cada publicação.** Os do Cortex e do quimera
mudam toda semana, e a página inteira é vendida na premissa de que cada um tem
fonte. O `CLAUDE.md` traz os comandos de medição e as ressalvas.
