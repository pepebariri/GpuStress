# GpuStress — Diagnóstico e Estresse de Placa de Vídeo

Ferramenta de bancada para placas AMD, NVIDIA e Intel, no Windows 10 e 11. Funciona assim:
- estressa a placa usando quase toda a VRAM;
- registra todos os sensores de um jeito que sobrevive a quedas e reinícios;
- entrega um resultado final: **SAUDÁVEL**, **INTERMEDIÁRIA** ou **PRECISA DE REPAROS**;
- gera um relatório em texto com os possíveis defeitos e a causa provável de cada um.

*Desenvolvido Por João Luis Pepe — Distribuição Gratuita.*

## Leia antes de usar

1. **Salve e feche tudo.** Um teste de estresse pode travar ou reiniciar o PC, principalmente se a placa ou a fonte tiver defeito.
2. **É um teste pesado de propósito.** Ele leva a placa ao limite para revelar defeitos. Placas com refrigeração ruim esquentam muito; evite testes longos nelas antes de revisar o cooler e a pasta térmica. O programa para sozinho em temperaturas críticas, mas nenhum teste é isento de risco.
3. **Permissão de administrador.** Ao abrir, o Windows pergunta se permite. Clique em **Sim**: sem isso o programa não abre.
4. **Tela e energia:** durante o teste, o programa impede o Windows de apagar a tela ou suspender o PC, sem alterar o plano de energia. Ao terminar, volta tudo ao normal.
5. **Não mexa no PC durante o teste** e não feche a janela do programa. Para encerrar antes, use o botão **Parar**: o relatório sai com o que já foi testado.
6. **Teste interrompido não é resultado final.** O veredito vale só para o que foi testado.

## Como usar

É um arquivo só: copie o `GpuStress.exe` para um pendrive ou para qualquer pasta e dê dois cliques. Não precisa instalar nada (nem o .NET). Os relatórios ficam na pasta `Sessoes`, criada ao lado do programa.

Escolha o **Tipo de teste**, clique em **Iniciar teste** e aguarde. No fim, o relatório abre no Bloco de Notas.

## Leitura básica e leitura completa

O botão de leitura, acima de **Comparar antes × depois**, mostra o modo em uso.

| | Leitura básica (só o GpuStress) | Leitura completa (com o HWiNFO aberto) |
|---|---|---|
| Testes de VRAM, cálculo, 3D, PCIe | sim | sim |
| Temperaturas de núcleo, hotspot e memória | sim | sim |
| Tensão do núcleo, consumo, clocks, ventoinha | sim | sim |
| Tensões de memória, SoC, VDDCI e entrada de 12 V | depende da placa | sim |
| Temperatura de VRM e limitadores medidos pela placa | depende da placa | sim |
| +12/+5/+3,3 V da placa-mãe e potência da CPU | depende do PC | sim |
| Parada automática por temperatura do VRM | só se a placa informar a temperatura | sim |

**A leitura básica serve para triagem.** Sem o HWiNFO, o resultado não avalia VRM nem fonte, e o relatório avisa isso. **Para testes mais precisos e para liberar uma placa reparada, use a leitura completa.** Em testes longos, o programa avisa se o HWiNFO não estiver conectado.

### Como ativar a leitura completa

Na primeira vez que abre, o GpuStress oferece configurar. Depois, é só clicar no botão **Leitura básica — ativar a completa**:
- se o HWiNFO não estiver instalado, o programa **pergunta** e, se você aceitar, baixa e instala pelo repositório oficial do Windows (winget). Se não der certo, oferece abrir o site oficial;
- depois o programa liga a opção de leitura no HWiNFO e abre ele. Na tela inicial do HWiNFO, escolha **somente sensores** e clique em **Iniciar**. Se ele abrir direto a janela principal, clique no ícone **Sensores**. Deixe a janela de sensores aberta; pode minimizar.

O HWiNFO é um programa de outra empresa e não vem junto com o GpuStress:
- a **versão grátis é para uso não comercial**. Para uso em oficina, confira a licença em hwinfo.com;
- nela, a leitura compartilhada funciona por até **12 horas** por vez. Depois, é preciso ligá-la de novo;
- se o HWiNFO fechar ou parar durante o teste, o GpuStress percebe, volta para a leitura básica e registra no relatório.

Se o HWiNFO já estiver aberto sem a leitura ligada, encerre-o e clique de novo no botão. Atenção: o X da janela pode só esconder o HWiNFO; para encerrar de verdade, clique com o botão direito no ícone dele, perto do relógio, e escolha **Sair**. Para ligar à mão: janela de sensores → engrenagem → **Configurações principais** → **Suporte de memória compartilhada** → OK, e depois feche e abra o HWiNFO.

### Outros programas opcionais

- **GPU-Z** aberto: fabricante dos chips de VRAM (Samsung, Hynix, Micron) e, nas NVIDIA, tensão e potência do slot PCIe e de cada conector de energia.
- **PawnIO** (driver instalado à parte): sem o HWiNFO, permite ler o +12/+5/+3,3 V da placa-mãe e a potência da CPU.

Para editar os limites de temperatura, coloque um `gpu_db.json` ao lado do programa; sem ele, valem os limites embutidos.

## A janela

- **Esquerda**: escolha a placa, o tipo de teste, a resolução da cena 3D e o cliente/OS.
- **Direita**: sensores ao vivo (temperaturas, consumo, clocks, ventoinha, uso, VRAM), tensões da placa de vídeo, gráfico dos últimos 10 minutos, contador de erros e o andamento de cada etapa.
- **Fundo**: a animação da placa é o fundo do painel da direita, com os quadros de informação por cima; a janela não tem transparência. Ela pode ser desligada em **Animação de fundo** e, nas etapas de repouso, roda em modo econômico para não atrapalhar a medição.
- **Tensões da placa de vídeo**: núcleo (VDDC), memória, SoC, entrada de 12 V e qualquer outra que o driver ou o HWiNFO informem. Quando nenhuma fonte informa uma delas, o quadro mostra **não informada**. Na RX 9070 XT (RDNA 4) o driver só entrega a do núcleo; as outras aparecem com o HWiNFO aberto.
- **Limitando a placa**: o limite que está segurando o clock agora (potência, corrente, temperatura do hotspot, da memória ou dos VRMs), em % do limite. "(est.)" quando é estimado pelo GpuStress em vez de medido pela placa.
- **PCIe (ao vivo)**: o link real lido pelo driver durante o teste (nas Radeon o Windows mostra a ponte interna da placa, não o slot). **Erros WHEA**: contador de erros de hardware do Windows. **CPU** e **+12 V da placa-mãe**: quando houver leitura.
- **Troca ou queda de monitor**: o programa registra quando um monitor sai ou entra. Falhas de vídeo logo depois disso aparecem como **atenção**, e não como defeito da placa; repita o teste sem mexer no monitor para confirmar. A carga dos testes não depende do monitor: na cena 3D, telas menores calculam mais amostras por pixel.
- **Ao final**: a faixa com o resultado aparece e o relatório abre no Bloco de Notas.
- **Botões extras**:
  - **Comparar antes × depois**: escolha dois testes da mesma placa e veja o que melhorou, piorou ou continua igual.
  - **Avisos pelo Telegram**: cadastro de quem recebe os avisos.
  - **Abrir pasta de relatórios**: abre a pasta `Sessoes` com todas as sessões.

## Testes

| Etapa | O que faz | Que defeito pega |
|---|---|---|
| Repouso | Mede com a GPU parada | Temperatura alta em repouso, falhas em carga baixa |
| Barramento PCIe | Envia e recebe 256 MB, compara e mede a banda; lê o link (Gen/x) | Dados corrompidos, link reduzido (x8/x4), capacitores AC, slot/riser |
| VRAM (fria/quente) | 8 padrões, "moving inversions" e martelamento em ~90% da VRAM | Erro de gravação/leitura e qual bit falhou (chip, solda, tensão da memória) |
| Cálculo (frio/quente) | Contas conferidas bit a bit com a CPU | Núcleo instável que erra antes de travar |
| Cena 3D | Desenho da placa (estilo RTX 5090 na NVIDIA, RX 9070 XT na AMD, Arc na Intel) girando com as ventoinhas e o conector PCIe detalhados; ocupa quase toda a VRAM e confere os dados o tempo todo; a cada 5 s compara um quadro pixel a pixel | Artefatos, erro de VRAM com a GPU desenhando, engasgos, perda de vídeo |
| Carga em degraus | Sobe a carga de 20% a 100% em passos de 5%; mostra hotspot, consumo, clock e tensão em cada degrau | Em que nível de carga aparece erro ou queda (VRM/fonte que só falham na média/alta, núcleo instável numa faixa) |
| Carga máxima | Cálculo + tráfego por quase toda a VRAM, conferindo as contas a cada 10 s | Hotspot, diferença hotspot−núcleo, falta de corrente, throttling |
| Fonte (CPU + GPU) | Placa em carga máxima e a CPU em todos os núcleos ao mesmo tempo | Fonte que não segura o PC inteiro: queda do +12 V (placa e placa-mãe), desligamento, CPU errando contas |
| Alternância de carga | Troca entre 25% e 100% a cada 500 ms, sem desligar a GPU | VRM e capacitores que oscilam em degraus de corrente |
| Transientes | Liga/desliga a carga de 50 ms a 5 s | Fonte fraca, VRM, capacitores (reinício do nada, tela preta) |
| Ciclos térmicos (pós-reparo) | Aquece 90 s e esfria 60 s, várias vezes | Solda/BGA fraca após reflow ou reballing |

O painel da cena 3D, no canto superior esquerdo, mostra:
- modelo da placa e resolução do teste;
- FPS e VRAM em uso;
- clock do núcleo e da memória;
- uso da GPU, consumo e tensão;
- temperaturas, ventoinha, verificações e tempo restante.

## Limites de temperatura por arquitetura

O programa reconhece a geração da placa pelo nome e aplica os limites dela. Os valores ficam em `gpu_db.json` e podem ser editados.

| Arquitetura | Núcleo (atenção/falha) | Hotspot (atenção/falha) | Diferença normal | Memória |
|---|---|---|---|---|
| NVIDIA RTX 50 (Blackwell) | 83 / 90 °C | sem sensor | — | GDDR7 95 / 105 °C |
| NVIDIA RTX 40 (Ada) | 83 / 90 °C | 95 / 105 °C | < 15 °C | 100 / 108 °C |
| NVIDIA RTX 30 (Ampere) | 83 / 92 °C | 95 / 105 °C | < 15 °C | 100 / 108 °C |
| NVIDIA RTX 20 / GTX 16 | 80 / 88 °C | 95 / 105 °C | < 15 °C | 92 / 100 °C |
| AMD RX 9000 (RDNA 4) | 85 / 95 °C | 100 / 108 °C | 20–30 °C | 92 / 100 °C |
| AMD RX 5000–7000 (RDNA 1–3) | 85 / 95 °C | 100 / 108 °C | < 25 °C | 92 / 100 °C |
| Intel Arc B / A | 88 / 97 · 85 / 92 °C | 95 / 105 °C | < 20 °C | 90 / 100 °C |
| NVIDIA GTX 10 (Pascal) | 83 / 92 °C | sem sensor | — | 92 / 100 °C |
| NVIDIA GTX 900/750 (Maxwell) | 83 / 93 °C | sem sensor | — | 90 / 100 °C |
| NVIDIA GTX/GT 400–700 (Kepler/Fermi) | 83 / 93 °C | sem sensor | — | 90 / 100 °C |
| AMD RX 400/500 (Polaris) | 80 / 90 °C | sem sensor | — | 90 / 100 °C |
| AMD Vega / Radeon VII | 80 / 90 °C | 95 / 105 °C | < 25 °C | HBM2 90 / 95 °C |
| AMD R9/R7/HD 7000 (GCN) | 83 / 92 °C | sem sensor | — | 90 / 100 °C |

As placas profissionais (Quadro, RTX A e Ada, Radeon Pro, Arc Pro) seguem os limites da geração delas. Placa que não está no cadastro é testada igual, com limites conservadores, e o relatório mostra **Genérica**. Nas versões de notebook, o consumo não é comparado com o da placa de mesa. O cadastro traz limites por geração e o consumo de referência de fábrica; placas de parceiros com overclock podem consumir mais.

Placas muito antigas precisam de um driver com OpenCL para os testes de VRAM e cálculo; sem isso, o programa avisa e não consegue testá-las.

O teste é interrompido sozinho quando uma destas temperaturas aparece em 2 leituras seguidas:
- **hotspot** no limite de junção: 110 °C;
- **núcleo** 3 °C acima do máximo de fábrica;
- **memória** no limite de falha;
- **VRM** em 95% do limite informado pela placa (com o HWiNFO aberto) ou, sem essa informação, em 110 °C, quando a placa informa a temperatura.

Nos modos Pós-reparo e Intermitente, o teste também para se o hotspot ficar 5 minutos seguidos a menos de 5 °C do limite de junção.

**Fontes:**
- **NVIDIA**: temperatura máxima de fábrica de 90 °C nas RTX 40/50 e 93 °C nas RTX 30.
- **AMD**: junção de 110 °C nas RDNA.
- **Intel**: alvo de 90 °C nas Arc A.
- **Memória**: a GDDR6X reduz o clock por volta de 110 °C.

## Quanto tempo de teste?

| Situação | Modo | Tempo |
|---|---|---|
| Triagem na entrada | Rápido | 20 min (não serve para liberar a placa) |
| Diagnóstico / venda de usada | Completo | 60 min |
| Placa reparada | Pós-reparo | 120 min (ideal: 2 sessões em dias diferentes) |
| Tela preta / reinício intermitente | Intermitente | 4–8 h (de preferência a noite toda) |

Rápido e Completo rodam a mesma sequência; só muda o tempo.

Outros modos:
- **Combinado**: liga ao mesmo tempo o que estiver marcado (cena 3D, cálculo conferido e CPU), pelo tempo escolhido. Bom para reproduzir travamento de uso pesado real.
- **Monitorar uso normal**: só grava sensores e eventos. Se escolher um programa (jogo ou benchmark), o GpuStress abre o programa e encerra o monitoramento quando ele fechar.

## Atualização

Ao abrir, o GpuStress consulta na internet se há uma versão nova. Essa consulta não envia dados do computador nem dos testes. Se houver versão nova, ele mostra o que mudou e pergunta se deseja atualizar. Ao aceitar, o programa baixa o arquivo, confere se ele está íntegro, troca o programa e reabre. A atualização nunca acontece durante um teste. Sem internet, o programa funciona normalmente.

## Compartilhamento de dados (opcional)

Ao final de cada teste, o GpuStress pergunta se você deseja compartilhar os dados técnicos, para formar uma base de comparação entre placas. A janela mostra o que será enviado, e nada é enviado se você responder **Não**. O diagnóstico aparece nela de forma resumida, e o conteúdo completo fica no arquivo `diagnostico.json`, na pasta do teste.

- **Enviado:** modelo e arquitetura da placa, tipo e duração do teste, resultado, temperaturas máximas, consumo, clock, tensão, quantidade de erros e de eventos do Windows, versão do programa e os motivos do resultado, como códigos fixos (por exemplo, `f:delta_hotspot`).
- **Diagnóstico do programa, para corrigir bugs:** mínimo, média e máximo de cada sensor, resultado e números de cada etapa, tipos de evento do Windows sem o texto da mensagem, leituras descartadas por serem impossíveis, tipo e local de erros internos do programa, etapa, momento, duração e uso da CPU em cada queda do HWiNFO, versões do Windows e do driver, link PCIe e modo de leitura.
- **Código anônimo da instalação:** um código sorteado na primeira vez em que você aceita compartilhar, para saber em quantas instalações o programa é usado. Ele não tem relação com o computador nem com você, mas liga entre si os testes enviados pela mesma instalação. Fica no arquivo `%APPDATA%\GpuStress\instalacao.txt`; apagar esse arquivo faz o programa sortear outro.
- **Nunca enviado:** cliente, número da OS, nome do computador, nome do usuário, caminhos de pastas, nome de programa escolhido para monitorar, dados do Telegram e o relatório em texto.
- **Gravado:** só o dia do recebimento. O endereço IP é usado apenas para limitar envios em excesso e não é guardado.

O modo "Monitorar uso normal" não faz essa pergunta.

## Avisos pelo Telegram

1. Crie um bot uma vez: no Telegram, procure **@BotFather**, envie `/newbot` e copie o token.
2. No GpuStress, abra **Avisos pelo Telegram**, cole o token e clique em **Verificar**.
3. No celular de quem vai receber, abra o bot e toque em **Iniciar**. Depois clique em **Procurar cadastros** no programa. Dá para cadastrar várias pessoas e anotar o telefone de cada uma.

O Telegram não deixa um programa mandar mensagem direto para um número de telefone. Por isso o cadastro é feito tocando em **Iniciar** no bot.

O programa avisa quando:
- o teste começa;
- aparece o primeiro erro;
- a temperatura fica crítica;
- a GPU perde o vídeo;
- o Windows registra um evento grave (tela azul, reinício, erro de hardware);
- o teste termina. Esse aviso leva o resultado, a data e hora, o modelo, o tempo em teste e o **relatório em anexo**.

No modo Intermitente, se o PC reiniciar sozinho, o GpuStress abre de novo depois do login, analisa a queda e manda o aviso.

## Relatórios

Cada teste cria uma pasta em `Sessoes\` com estes arquivos:

| Arquivo | Conteúdo |
|---|---|
| `Relatorio_AAAA-MM-DD_HH-MM_Modelo.txt` | Resultado, defeitos com causa provável, dados da placa, tempo em teste, temperaturas, consumo, linhas de tensão, sensores por etapa e eventos do Windows |
| `sensores.csv` | Todas as leituras |
| `eventos.log` | Linha do tempo |
| `resumo.json` | Usado na comparação antes × depois |
| `analise_queda.txt` | Só existe se o PC caiu durante o teste |

## Linha de comando

```
GpuStress listar
GpuStress testar --modo completo --cliente "OS 1234" --resolucao 1920x1080
GpuStress testar --modo intermitente --horas 8
GpuStress testar --minutos 45
GpuStress testar --modo combinado --minutos 15 --com 3d,gpu,cpu
GpuStress monitorar
GpuStress monitorar --programa "C:\Jogos\jogo.exe"
GpuStress cena3d --minutos 5
GpuStress sensores-amd
```

`sensores-amd` lista tudo o que o driver AMD, o GPU-Z e o HWiNFO informam sobre a placa (útil para conferir uma placa nova).
