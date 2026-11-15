# DEFINICAO — robo-chaveiro

Criada em 15/11/2026. A placa foi comprada e ainda não chegou. Nada foi gravado nela. Este documento só
organiza as ideias para a sessão de construção.

## 1. Objetivo

Um robozinho de bolso, para pendurar como chaveiro, que conversa por voz. Para crianças e para qualquer pessoa.
Mostra um rosto na tela pequena e responde falando.

Pedido do Nei, literal (15/11/2026):
> "quero que crie um outro projeto, criando uma pasta para este projeto, pega as ideias daqui, depois vamos
> construir este projeto para as crianças e pessoas levarem como chaveiro"

"Daqui" é o `~/projetos/robo-cores3-lite` (robô de mesa). O chaveiro reaproveita o servidor de lá (opção B).
Não reinventa.

## 2. A placa comprada

**Anúncio:** https://pt.aliexpress.com/item/1005011897774164.html — título lido no HTML: "Placa ESP32-S3 com Tela
1,54 Touch". Preço pago, lido no link: **R$ 132,14** (cheio R$ 150,16). Frete e imposto não conhecidos.

**Que placa é (provável, confirmar com a placa na mão):** SpotPear **ESP32-S3 1.54" "muma"**, com o firmware
"emo XiaoZhi" e a capinha verde de chaveiro. Por quê:
- As fotos do anúncio têm a marca SpotPear e o texto "ESP32 S3 emo Xiaozhi" (`referencias/anuncio-capa.png`).
- A wiki oficial da SpotPear do "muma" lista o firmware `sp-esp32-s3-1.54-muma-emo` ("Xiaozhi AI Firmware-EMO").
- A capinha das fotos tem dois botões na lateral, como o muma (liga e boot).
- O HTML do anúncio não traz o código da variante (SKU). Por isso fica "provável".

Especificações lidas na ficha e na wiki **do muma** (SpotPear), não no anúncio:

| Item | Valor | Fonte |
|---|---|---|
| Processador | ESP32-S3, Wi-Fi 2,4 GHz, Bluetooth BLE 5, antena na placa | ficha do muma (`referencias/spotpear-muma-ficha.png`) |
| Memória | "N16R8" no nome: 16 MB de flash, 8 MB de PSRAM | título da página do muma |
| Tela | 1,54", 240 × 240, IPS, 262 mil cores, chip ST7789 (SPI) | ficha do muma |
| Toque | CST816 (I²C). Só na versão "Touch" | ficha e wiki do muma |
| Som | Um falante e microfone. A ficha diz "Microphone" (um). O chip de áudio não está na ficha | wiki e ficha do muma |
| Botões | Liga/desliga e BOOT (o BOOT também acorda, interrompe e entra no modo de gravação) | wiki do muma |
| Energia | USB-C; "gestão de bateria" na placa; LED vermelho carregando, apaga quando cheio | ficha e wiki do muma |
| Outros | Entrada de cartão SD (sem cartão); LED de conversa; furo para pendurar (numa das caixas da wiki) | wiki do muma |
| Câmera | **Não tem** (não aparece em nenhuma lista de componentes) | wiki do muma |
| Pinos da tela, SD e toque | TFT DC 47, CS 5, SCLK 4, MOSI 2, RST 38, BL 42; toque SDA 11, SCL 7, INT 12, RST 6 | wiki do muma, "Pin connection definition" |
| Esquemas | V1 e V2 em PDF, arquivo 3D `.step` | wiki do muma |
| Firmware de fábrica e de volta | Arquivos `.bin` para restaurar (V1, V2, EMO, relógio, SD) | wiki do muma, "Restore factory firmware" |

**Não verificado:** bateria da capinha de chaveiro (capacidade e se vem dentro), tamanho e peso do chaveiro,
chip de áudio e pinos do microfone e do falante, quantos microfones, se a unidade comprada é "Touch". A página
do muma cita uma bateria de 400 mAh só no conteúdo do "1.54-PCBA-BIG-KIT", não na versão de chaveiro.
Os esquemas V1/V2 respondem o áudio; ler na fase F1.

**Atenção, placa parecida:** a Waveshare ESP32-S3-Touch-LCD-1.54 também tem 1,54" e ST7789, mas outros chips
(ES8311, ES7210, giroscópio) e três botões. Não misturar as fichas.

**O firmware de fábrica fala com uma nuvem de fora.** A wiki manda ligar o Wi-Fi, pegar um código e ativar o
aparelho no painel "Xiaozhi". A voz da criança iria para esse serviço. Pela regra do Nei, isso é API externa:
**não ativar o XiaoZhi sem autorização dele.** Ao gravar o nosso firmware, o de fábrica some; volta pelos `.bin`
da wiki.

## 3. O que ele faz e o que não faz

| Faz | Não faz |
|---|---|
| Grava a fala enquanto a pessoa segura o botão (até 15 s) | Não entende a fala sozinho: sem servidor, fica mudo |
| Manda a gravação para o servidor de casa (opção B) e toca a resposta | Não funciona fora do Wi-Fi de casa na opção B |
| Mostra um rosto: pronto, ouvindo, pensando, falando, erro | Não tem câmera: nada de foto |
| Mostra frases de espera enquanto pensa | Não escuta o tempo todo na primeira versão |
| Carrega pelo USB-C | Autonomia não medida |

## 4. Como conversa: dois cérebros, o mesmo chaveiro

Pedido do Nei, 15/11/2026: "faça só o projeto de como poder usar ela direto numa api e também através de um
servidor (...) pegar a dica com o outro projeto (...) aqui não tem câmera, mas o nosso aqui é móvel, ele tem
avisar se a bateria está chegando ao fim". É desenho: **nada implementado, nenhuma API chamada.**

Mesma ideia do `robo-cores3-lite` (DEFINICAO de lá, seções 4, 9 e 10): o firmware tem uma camada "cérebro" com
implementações trocáveis, escolhida em `segredos.h`. Tela, botão, som e bateria ficam na camada de hardware.

```
                     ┌── B: servidor de casa (padrão) ──────────────────────────────┐
 chaveiro ──Wi-Fi──▶ │ robo-cores3-lite/servidor, porta 8770                        │
 tela, botão,        │ Whisper (inemavox) → claude -p (assinatura) → Chatterbox     │
 mic, falante,       └──────────────────────────────────────────────────────────────┘
 bateria   │
           ├──Wi-Fi──▶ A1: direto numa API de voz em tempo real (chave temporária)
           └──Wi-Fi──▶ A2: servidor de casa como ponte → API (a chave fica no PC)
```

| Cérebro | Onde funciona | Demora | Chave da API no chaveiro | Precisa de |
|---|---|---|---|---|
| **B, servidor** | Wi-Fi de casa (ou de fora, com túnel até o PC) | 10–14 s quente, 33 s frio (medido no outro projeto) | Nenhuma | PC ligado |
| **A1, direto** | Qualquer Wi-Fi, inclusive o do celular | perto de 1 s (estimativa do outro projeto, não medida) | Sim, por isso só temporária | Autorização do Nei, custo por uso |
| **A2, ponte** | Onde o servidor alcançar | perto de 1 s + a ida ao PC (não medido) | Nenhuma | Autorização do Nei, PC ligado, rota nova no servidor de lá |

### 4.1 Cérebro B: servidor (padrão)

Reaproveitado sem mudar o servidor. O chaveiro usa a rota principal de lá, `POST /v1/conversa` (resposta por
partes: `O` ouvido, `T` texto, `A` áudio, `E` frase de espera, `F` fim, `X` erro), e `POST /v1/aquecer` quando o
botão é apertado, para os modelos subirem enquanto a pessoa fala. `/v1/foto` não é usada: não tem câmera.

```
chaveiro ──Wi-Fi──▶ servidor do robo-cores3-lite, porta 8770
                      Whisper (inemavox) → claude -p (assinatura, sem ferramentas) → Chatterbox
```

### 4.2 Cérebro A1: direto numa API

Mesmas candidatas do outro projeto (seção 10 de lá): **Gemini Live** e **OpenAI Realtime**, áudio entrando e
saindo por WebSocket. Sem câmera, a vantagem da visão do Gemini não conta aqui; pesa a demora, o custo e a voz.
A API da Anthropic não é opção (Claude só pela assinatura).

- **Chave:** um chaveiro se perde fácil. A chave de verdade **nunca** vai gravada nele. Desenho: o PC de casa
  entrega ao chaveiro uma chave temporária, de vida curta (as duas APIs anunciam esse recurso; **não verificado
  nesta sessão**). Sem isso, a A1 não sai do papel.
- **Firmware:** WebSocket com TLS e áudio contínuo é mais pesado que o HTTP da B. A PSRAM de 8 MB ajuda; o peso
  real **não foi medido**.
- **Persona e segurança de criança:** a persona vai no início da sessão, a mesma combinada para a B. A conversa
  vai para a nuvem do provedor: mesma decisão de privacidade da seção 9, com mais peso.
- **Botão:** continua "segurar para falar". A API escuta só enquanto o botão está apertado; isso também segura o
  custo.

### 4.3 Cérebro A2: servidor como ponte

O chaveiro fala com o servidor de casa como na B; o servidor repassa para a API. A chave fica no PC. O firmware
é quase o mesmo da B. Precisa de uma rota nova no servidor do `robo-cores3-lite`: **mudança no outro projeto,
só com o Nei**, e só depois da autorização da API.

### 4.4 Regras para os três

- **Nenhuma chamada de API antes de o Nei autorizar por escrito o serviço e a finalidade.** Escolher A1 ou A2
  também é decisão dele.
- Os mesmos estados do outro projeto: PRONTO → OUVINDO → PENSANDO → FALANDO → PRONTO; ERRO por 2 s.
- O cérebro sem rede dá rosto de "sem internet" e volta a PRONTO; nunca reinicia a placa.
- Troca automática (opcional, F4): se a rede de casa aparece, usa B; se não, A1. Só depois de as duas
  funcionarem sozinhas.

### 4.5 Detalhes da B (medidas do outro projeto)

- Mesmo protocolo da DEFINICAO do robo-cores3-lite, seção 11: `POST /v1/fala` com WAV 16 kHz, 16 bits, mono,
  de 0,5 a 15 s; cabeçalhos `X-Robo-Token` e `X-Robo-Id` (ex.: `chaveiro-1`); volta WAV no mesmo formato com
  `X-Ouvido`, `X-Resposta`, `X-Tempos`. Sem foto: a rota `/v1/foto` não é usada.
- Medido lá em 15/11/2026: **10 a 14 s por resposta** com os modelos carregados, 33 s na primeira depois de
  10 min parado (`~/projetos/robo-cores3-lite/servidor/MEDIDAS.md`).
- O servidor é usado onde está: `python3 ~/projetos/robo-cores3-lite/servidor/servidor.py`. Este projeto não
  copia o código. Mudança no servidor (persona, novo `X-Robo-Id`) é mudança no outro projeto: só com o Nei.

## 5. Fora de casa: ele é móvel (nada decidido)

O robô de mesa fica parado ao lado do PC; o chaveiro anda no bolso. Por isso ele guarda **mais de uma rede**
(casa e o ponto de acesso do celular, em `segredos.h`) e tenta na ordem. O ponto de acesso precisa ser 2,4 GHz.

| Ideia | Como | Contra |
|---|---|---|
| Celular + cérebro A1 | Ponto de acesso do celular; o chaveiro fala direto com a API (seção 4.2) | **Exige autorização do Nei**; custo; chave temporária obrigatória |
| Celular + cérebro B com túnel | Ponto de acesso do celular + túnel/VPN até o PC de casa | Ferramenta nova (avisar o Nei); o PC continua ligado; a porta do servidor fica alcançável de fora: token forte |
| Celular + cérebro A2 | Igual ao túnel, com o servidor repassando para a API | Soma os contras dos dois |
| App no celular | O celular grava e fala; o chaveiro só mostra o rosto (Bluetooth) | Outro projeto inteiro |
| Só em casa | Aceitar o limite: é "robô de casa que cabe no bolso" | Menos mágico |

## 6. Voz, ativação, tela

- **Voz:** a do servidor (Chatterbox local, voz `rachel` no `.env` de lá). Voz de criança ou outra: decisão do Nei.
- **Ativação:** segurar o botão BOOT para falar, soltar para enviar (a wiki diz que ele serve para acordar e
  interromper). Se for a versão Touch, o toque pode ser o "parar". Palavra de ativação: opcional, modelo local
  novo, só com aprovação do Nei.
- **Microfone e falante ao mesmo tempo:** não verificado nesta placa. No robô de mesa não podiam. Conferir no esquema.
- **Tela:** rosto de olhos grandes, como o "emo" das fotos. Legenda curta talvez: 240 × 240 é pouco espaço.

## 7. Energia e bateria

### 7.1 Aviso de bateria acabando (pedido do Nei, 15/11/2026)

O robô de mesa vive no cabo; o chaveiro vive na bateria. Ele precisa avisar antes de apagar.

**Como medir:** a wiki do muma (lida em 15/11/2026) lista os pinos da tela, do SD e do toque, mas **nenhum pino
de bateria**. Falta conferir no esquema V1/V2 se a bateria chega a uma entrada analógica do ESP32-S3 (divisor de
tensão) e se o carregador tem um pino que diz "carregando". Três casos:

| O esquema mostra | O firmware faz |
|---|---|
| Divisor de tensão num pino analógico | Lê a tensão a cada 30 s (média de várias leituras, fora da hora em que o falante toca, porque o falante derruba a tensão) |
| Também um pino de "carregando" | Mostra um raio na tela e não avisa de bateria fraca enquanto carrega |
| Nenhum dos dois | Não dá para medir pela placa. Saída: adaptação de hardware (dois resistores) ou só um aviso por tempo de uso. Decidir com o Nei |

**Os limites saem da medida, não de chute.** Na F2 (teste 5) o chaveiro descarrega de cheio até apagar,
gravando a tensão a cada minuto. Dessa curva saem os três degraus:

| Degrau | Quando (calibrado na F2) | O chaveiro faz |
|---|---|---|
| Cansado | ~20% do tempo de uso restante | Ícone de bateria na tela; olhos de sono no rosto PRONTO |
| Pedindo carga | ~10% restante | Fala uma vez "estou ficando com sono, me coloca para carregar" e repete a cada nova conversa; o falante baixa o volume |
| Dormindo | ~5% restante | Diz tchau, desliga o Wi-Fi e a tela e entra em sono profundo; só volta com o cabo ou com o botão |

- **O aviso funciona sem rede.** As frases de bateria são WAVs gravados uma vez pelo Chatterbox local
  (inemavox, rota `/api/jobs/tts/upload`), guardados no flash do chaveiro. Valem para os três cérebros.
- Na B, o chaveiro pode mandar o nível no cabeçalho `X-Bateria` (o servidor de hoje ignora; usar exige mudar o
  outro projeto, só com o Nei). Serve para o servidor dar respostas mais curtas com a bateria baixa.
- Economia: tela apaga após 30 s em PRONTO; Wi-Fi em modo de economia entre conversas; sono profundo depois de
  5 min parado, acordando pelo BOOT. O tempo de reconectar ao acordar entra na MEDIDAS.md.

### 7.2 Carga e segurança

- A placa tem gestão de bateria e LED de carga (wiki). A bateria da versão de chaveiro: **não verificada**.
- Bateria de lítio num objeto de criança: só com proteção, dentro da capinha, sem fio solto. Nunca carregar
  sem um adulto por perto na primeira vez. Não deixar no sol nem molhar.
- Autonomia: medir na F2 (minutos de conversa e horas parado). Desligar pelo botão quando não usar.

## 8. Tamanho e chaveiro

- A capinha verde das fotos tem furo para cordão (`referencias/anuncio-capa.png`). Tamanho não lido.
- **Argola de chaveiro ou mosquetão, sim. Cordão no pescoço de criança, não** (risco de enforcamento). Se for
  cordão, só com fecho que se solta sozinho quando puxado.
- Capinha impressa própria: opcional (F3). Sem peça pequena que solte (risco de engolir). Tela protegida contra queda.

## 9. Crianças: segurança e privacidade

- **O que a criança fala vai para o PC do Nei.** O servidor guarda cada gravação em
  `servidor/dados/turnos/` e as últimas trocas em `dados/conversa-<robo-id>.json` (no robo-cores3-lite).
  Decidir com o Nei: guardar, guardar por X dias ou não guardar.
- Persona para criança: frases curtas, sem assustar, sem pedir dado pessoal (nome completo, endereço, escola).
  Hoje o `persona.txt` de lá é do robô de mesa ("Lite"); a persona do chaveiro é pergunta aberta.
- O `claude -p` continua **sem ferramentas**: o que a criança diz nunca vira comando na máquina.
- O chaveiro guarda a senha do Wi-Fi de casa e o token do servidor. Perdeu o chaveiro: trocar o token.
- Nenhuma nuvem de terceiros (XiaoZhi, APIs) sem autorização do Nei.

## 10. Ideias trazidas do robo-cores3-lite

| Ideia | De onde | Uso aqui |
|---|---|---|
| Servidor local (opção B) | `servidor/servidor.py`, porta 8770 | Usado como está, com outro `X-Robo-Id` |
| Frases de espera sem repetir | `servidor/espera.py`: categorias (frio, hora, conta, difícil, geral), não repete as 8 últimas, pula 1 em 3, nunca pula no frio | Gravadas uma vez e tocadas pela placa (cabe no flash de 16 MB; não medido) |
| Persona curta | `servidor/persona.txt`: 1–2 frases, sem markdown, números por extenso | Versão para criança (pergunta aberta) |
| Resposta 204 em silêncio | servidor, seção 11 | Rosto de "não entendi" |
| Limites (FAQ) | O ESP32 não pensa sozinho; Claude só pela assinatura; opção A só com autorização | Mesmos limites |
| Estados | PRONTO → OUVINDO → PENSANDO → FALANDO → PRONTO; ERRO por 2 s | Mesmos estados |

O que **não** vem de lá: câmera e foto, toque como botão de falar, barramento I²S da CoreS3, bateria de 200 mAh,
placa `m5stack_cores3` no compilador, M5Unified.

## 11. Firmware (a decidir)

- `arduino-cli` já está instalado (`~/.local/bin/arduino-cli`). Placa no compilador: **não verificada**
  (candidata genérica ESP32-S3 com PSRAM OPI ou QSPI; conferir na F1 pela serial).
- Bibliotecas de tela (ex.: Arduino_GFX ou LovyanGFX): download novo, **avisar o Nei antes**.
- Antes de gravar qualquer coisa, guardar o firmware de fábrica (`esptool read_flash`, se o Nei aprovar) e ter
  os `.bin` da wiki à mão.

## 12. Fontes lidas em 15/11/2026

- Anúncio (título e 6 fotos): https://pt.aliexpress.com/item/1005011897774164.html
- SpotPear muma, wiki: https://spotpear.com/wiki/ESP32-S3-AI-1.54-inch-LCD-Display-TouchScreen-N16R8-muma-DeepSeek.html
- SpotPear muma, loja e ficha: https://spotpear.com/shop/ESP32-S3-AI-1.54-inch-LCD-Display-TouchScreen-N16R8-muma-DeepSeek.html
- Placa parecida (não é a nossa): https://spotpear.com/wiki/ESP32-S3-1.54inch-LCD-Display-TouchScreen-AI-DeepSeek.html
- Base de ideias: `~/projetos/robo-cores3-lite/` (DEFINICAO, FAQ, PLANO, servidor/README, MEDIDAS, espera.py,
  arquivo/variante-xiao/DEFINICAO.md)
