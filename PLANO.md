# PLANO — robo-chaveiro

Cada fase tem um critério de pronto que se confere com um comando ou uma medida. O porquê de cada escolha está
na [DEFINICAO.md](DEFINICAO.md).

| Fase | O que entrega | Precisa de |
|---|---|---|
| F0 | Placa chegou, liga e carrega | A entrega |
| F1 | Firmware grava a fala e envia ao servidor do robo-cores3-lite | F0; aviso ao Nei antes de instalar biblioteca |
| F2 | O chaveiro toca a resposta e mostra o rosto | F1 |
| F3 | Chaveiro de verdade: argola, capinha, uso por criança | F2 e decisões do Nei |
| F4 | Opcionais | Decisão do Nei, um por um |

## F0 — Placa chegou e liga

**Não ativar o XiaoZhi** (nuvem de fora). Ligar só para ver se está viva.

Pronto quando:
- Liga pelo botão e a tela acende.
- Com o cabo USB-C, o LED vermelho de carga acende e apaga quando a carga termina (wiki do muma).
- `arduino-cli board list` mostra uma porta nova com a placa ligada.
- Fotos da placa (frente, verso, etiqueta, bateria se houver) em `referencias/` e a variante anotada na
  DEFINICAO.md, seção 2 (Touch? bateria? tamanho medido?).

## F1 — Grava e envia para o servidor

O que fazer: ler os esquemas V1/V2 da wiki para achar os pinos do microfone e do falante; escolher a placa no
compilador; escrever `firmware/chaveiro/` com o botão BOOT como "segurar para falar".

Pronto quando, com `python3 ~/projetos/robo-cores3-lite/servidor/servidor.py` no ar:

| # | Comando ou ação | Saída esperada |
|---|---|---|
| 1 | `arduino-cli compile --fqbn <placa escolhida> firmware/chaveiro` | Código 0 |
| 2 | Gravar e abrir o monitor serial | `PSRAM:` maior que zero; Wi-Fi conectado; `GET /saude` → 200 |
| 3 | Segurar o botão 3 s e dizer "um, dois, três, testando" a 30 cm, três vezes | O log do servidor mostra `OUVIDO:` com a frase certa nas três |
| 4 | `ffprobe` e `volumedetect` na gravação guardada pelo servidor | `pcm_s16le,16000,1`; volume médio acima de −35 dB |
| 5 | Segurar o botão 20 s | A gravação para sozinha em 15 s e é enviada; a placa não reinicia |

## F2 — Toca a resposta

Pronto quando:

| # | Ação | Saída esperada |
|---|---|---|
| 1 | Cinco perguntas seguidas, sem reiniciar | Cinco respostas tocadas até o fim, sem estalo |
| 2 | Ouvir a 50 cm numa sala comum | Dá para entender todas as palavras |
| 3 | Desligar o servidor e falar | Rosto de erro por 2 s e volta a pronto; não reinicia |
| 4 | 30 min parado e uma pergunta | Responde (Wi-Fi reconecta sozinho) |
| 5 | Bateria cheia, uma pergunta a cada 2 min | Minutos até desligar anotados na DEFINICAO.md, seção 7 |

O tempo do botão solto até o primeiro som vai para uma tabela `MEDIDAS.md` neste projeto.

## F3 — Chaveiro e capinha

Pronto quando:
- O chaveiro fica preso numa argola ou mosquetão e aguenta 10 sacudidas sem soltar.
- Nenhuma peça sai com a mão (teste de puxar cada parte).
- Se houver capinha impressa: o botão e o USB-C ficam acessíveis, o falante não fica abafado (teste 2 da F2 passa
  com a capinha).
- Persona de criança combinada com o Nei e testada em 10 perguntas de criança (nenhuma resposta assusta ou pede
  dado pessoal; conferido pelo Nei).

## F4 — Opcionais (um por um, só com decisão do Nei)

| Opcional | Critério de pronto |
|---|---|
| Frases de espera tocadas pela placa | Toca uma frase da banca em menos de 1 s depois de soltar o botão; não repete as 8 últimas |
| Funcionar fora de casa (celular ponte ou opção A) | Escolha e autorização do Nei; 5 perguntas fora de casa respondidas |
| Palavra de ativação | Modelo local novo: só com aprovação; 9 de 10 a 50 cm |
| Guia e publicação | Só com a ordem do Nei: repo, guia em `guia/`, portal |

## Riscos

| Risco | Como se descobre | Saída |
|---|---|---|
| Placa não é o muma | F0, etiqueta e fotos | Achar a wiki certa antes da F1 |
| Mic e falante não funcionam juntos | F1, esquema | Meio a meio, como no robô de mesa |
| Demora de 10–14 s parece longa para criança | F2 | Frases de espera; rosto de "pensando" animado |
| Bateria fraca | F2, teste 5 | Avisar o Nei; aceitar uso curto |
| Perda do chaveiro | — | Token trocável; nada guardado além do Wi-Fi e do token |
