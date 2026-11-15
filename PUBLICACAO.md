# PUBLICACAO — robo-chaveiro

Ordem do Nei (15/11/2026): "pode publicar no portal no inema.robot no lives6".

portal: feito
telegram: pendente
lives6: pendente

## Guia (GitHub Pages, via Actions, branch main)

| Idioma | URL |
|---|---|
| PT | https://inematds.github.io/robo-chaveiro/guia/ |
| EN | https://inematds.github.io/robo-chaveiro/guia/en/ |
| ES | https://inematds.github.io/robo-chaveiro/guia/es/ |

- As três responderam 200 em 15/11/2026 (e a raiz, que redireciona para `guia/`).
- Repo: https://github.com/inematds/robo-chaveiro (público desde 15/11/2026).
- O guia tem a marca `<!-- VIDEO: entra o explicavideo do projeto quando publicado -->` nos 3 idiomas, logo
  depois do `#resumo`: o vídeo do lives6 entra ali (e uma linha "🎬 Vídeo:" nos 3 READMEs).
- Revisor independente: 1ª rodada reprovado (6), 2ª aprovado (8); as correções das duas entraram. Notas em
  `~/projetos/wifi/scripts/revisor-nota.py`.

## Portal (skill atualiza-portal)

| Repo | Commit |
|---|---|
| NeiMaldaner/portal (inema.club) | `4351030` |
| inematds/inemabuscas | `e99681c` |
| inematds/inemapro (inemapro-mono, catálogo PRO) | `61f0763` |

- Card em `communityProjects` (badge Guia), topo de `projectUpdatesData`:
  "robo-chaveiro — robozinho de bolso que conversa com seu filho, com você no controle".
- EN/ES em `translated-courses.ts` (id 10232). Enrichment `projeto:robo-chaveiro-guia` (robotica-ia-fisica).
- No inema.club aparece só no quadro "Últimas Atualizações de Projetos"; no PRO entra no catálogo e na busca.
- O teste `titulos-quadro` do portal já falhava antes desta publicação, por um título de vídeo de 13/11 com 101
  caracteres (não é deste projeto).
