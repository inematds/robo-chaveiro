# robo-chaveiro

**🇧🇷 [Português](README.md) · 🇺🇸 [English](README.en.md) · 🇪🇸 [Español](README.es.md)**

[![Robô Chaveiro](guia/assets/banner.jpg)](https://inematds.github.io/robo-chaveiro/guia/)

## O que é

Este é um projeto aberto para transformar uma plaquinha com tela de 1,54" numa espécie de bichinho de bolso que
conversa por voz: a criança segura um botão, fala, e ele responde em voz alta, com um rosto de olhos grandes na
tela. Ele "pensa" de um de três jeitos: pelo computador de casa, direto num serviço de IA na internet (OpenRouter),
ou pelo computador de casa repassando para esse serviço. Os pais vão configurar tudo pelo celular. Hoje existe o
projeto no papel e o servidor de casa, que já funciona num projeto irmão; a placa e o programa dela ainda não.

## 📖 Guia de uso

Guia completo (landing + passo a passo): **https://inematds.github.io/robo-chaveiro/guia/**

## Mais detalhes

A placa é a SpotPear ESP32-S3 1,54" (provável "muma", versão "emo"), comprada no AliExpress em 15/11/2026 por
R$ 132,14. O "cérebro" de casa é o servidor que já funciona no
[robo-cores3-lite](https://github.com/inematds/robo-cores3-lite) (opção B): Whisper → `claude -p` pela
assinatura → voz Chatterbox. Desenhados também: cérebro A1 (direto no OpenRouter) e A2 (servidor como ponte),
para usar longe de casa, o aviso de bateria acabando, o painel dos pais e corpos de bicho com o chaveiro como
rosto. Nada disso está implementado, e nenhuma API foi chamada.

**Estado em 15/11/2026: só a definição. Placa a caminho, nenhum firmware.**

| Para | Onde |
|---|---|
| O que é, a placa, segurança, ideias | [DEFINICAO.md](DEFINICAO.md) |
| Fases e critério de pronto | [PLANO.md](PLANO.md) |
| Regras do projeto | [CLAUDE.md](CLAUDE.md) |
| Retomar o trabalho | [handoffs/latest.md](handoffs/latest.md) |
| Fotos do anúncio e ficha da placa | [referencias/](referencias/) |
| Ideia para depois: corpos de boneco e bicho com o chaveiro como rosto | [ideias/corpos/PROPOSTAS.md](ideias/corpos/PROPOSTAS.md) |

## O processo, do início ao fim

Legenda: 🧠 sessão com o Nei · ⚙️ comando/script · 🤖 `claude -p`

| # | Passo | Comando | Quem | Existe hoje? |
|---|---|---|---|---|
| 1 | Ler a definição e responder as perguntas abertas | [handoffs/latest.md](handoffs/latest.md) | 🧠 | sim |
| 2 | Receber a placa e ligar, sem ativar o XiaoZhi | [PLANO.md](PLANO.md), F0 | 🧠 | não: placa a caminho |
| 3 | Ver a porta da placa | `arduino-cli board list` | ⚙️ | não testado: sem placa |
| 4 | Subir o servidor (no outro projeto) | `python3 ~/projetos/robo-cores3-lite/servidor/servidor.py` | ⚙️ | sim |
| 5 | Dentro de cada turno: escrever a resposta | `claude -p --model haiku --tools "" --strict-mcp-config --no-session-persistence …` (feito pelo servidor) | 🤖 | sim (no robo-cores3-lite) |
| 6 | Achar pinos de áudio nos esquemas e escolher a placa no compilador | [PLANO.md](PLANO.md), F1 | 🧠 | não |
| 7 | Instalar biblioteca de tela (avisar o Nei antes) | a definir | ⚙️ | não |
| 8 | Escrever, compilar e gravar o firmware | `arduino-cli compile/upload --fqbn <a definir> firmware/chaveiro` | ⚙️ | não |
| 9 | Conversar na bancada: 5 perguntas seguidas (cérebro B) | [PLANO.md](PLANO.md), F2 | 🧠 | não |
| 10 | Gravar as frases de bateria (Chatterbox local) e testar o aviso sem rede | [PLANO.md](PLANO.md), F2b | ⚙️ 🧠 | não |
| 11 | Configurar pelo celular (nome, Wi-Fi) e painel da família (limites, histórico) | `python3 painel/painel.py` (a criar), porta 8771; [PLANO.md](PLANO.md), F2c | ⚙️ 🧠 | não: só desenho |
| 12 | Argola, capinha, persona de criança | [PLANO.md](PLANO.md), F3 | 🧠 | não |
| 13 | Cérebro A (A1 direto ou A2 ponte), só com autorização escrita do Nei | [PLANO.md](PLANO.md), F3b | 🧠 | não: só desenho |
| 14 | Opcionais (troca automática, túnel, espera, palavra de ativação, guia) | [PLANO.md](PLANO.md), F4 | 🧠 | não |
| 15 | Guia PT/EN/ES no GitHub Pages | skill `projetos-landing-guia` | 🧠 | sim — [PUBLICACAO.md](PUBLICACAO.md) |
| 16 | Portal inema.club, inemabuscas e catálogo PRO | skill `atualiza-portal` | 🧠 | ver [PUBLICACAO.md](PUBLICACAO.md) |
| 17 | Tópico no grupo INEMA.ROBOT do Telegram + MENU | skill `postar-telegram` | 🧠 | ver [PUBLICACAO.md](PUBLICACAO.md) |
| 18 | Vídeo no canal lives6 e no guia | marca `<!-- VIDEO: -->` no guia | 🧠 | ver [PUBLICACAO.md](PUBLICACAO.md) |

O único passo com `claude -p` é o 5, feito pelo servidor do robo-cores3-lite, pela assinatura, sem ferramentas.

## Regras

- Nenhuma API externa sem autorização explícita do Nei. Isso inclui a nuvem do XiaoZhi do firmware de fábrica.
- Claude só pela assinatura (`claude -p`).
- Repo público `inematds/robo-chaveiro` desde 15/11/2026 (ordem do Nei: publicar no portal, no INEMA.ROBOT e no lives6).
