# Monolith

## Sobre o Projeto

**Monolith** é um sistema modular desenvolvido para realizar a **extração do núcleo melódico** de qualquer fonte de áudio musical.

O sistema processa a **polifonia** (múltiplos sons e instrumentos simultâneos) de uma faixa musical e a reduz a uma **monofonia** estrita. O resultado é uma sequência de notas limpa, ideal para a execução como um **solo** por um único instrumento.

## Saídas e Aplicações

O Monolith visa fornecer três entregáveis principais a partir de uma faixa de áudio de entrada:

1.  **Solo Sintetizado:** Arquivo de áudio (`.wav` ou `.mp3`) contendo o solo gerado, renderizado com o timbre do instrumento especificado pelo usuário.
2.  **Transcrição MIDI:** Arquivo `MIDI` limpo e **monofônico** para uso em Digital Audio Workstations (DAWs) e software de notação.
3.  **Representação Estruturada:** Dados em formato de texto/JSON contendo a sequência de notas, duração e *pitch* (altura) para análise e estudo.

## Arquitetura do Processamento (Quatro Fases)

O processo de transformação é executado em quatro fases sequenciais de **Processamento de Sinal Digital (DSP)** e **Aprendizado de Máquina (ML)**:

### Fase 1: Separação de Fontes (Stem Separation)
* **Função:** Isolamento da trilha de áudio que contém o componente melódico primário (geralmente o vocal principal).
* **Método:** Utilização de modelos de Deep Learning (como o **Demucs**) para decompor a mixagem em *stems* instrumentais isolados.
* **Saída:** Uma trilha de áudio isolada contendo o vocal/melodia.

### Fase 2: Transcrição Automática (AMT)
* **Função:** Conversão do sinal de áudio isolado em uma sequência de dados musicais discretos.
* **Método:** Aplicação de algoritmos de **Pitch Tracking ($\text{F}_0$ Tracking)** para detecção da frequência fundamental, seguida de **Quantização** para mapear as frequências para as notas da escala temperada, definindo o *início* e *duração* de cada evento.
* **Saída:** Estrutura de dados inicial em formato de eventos **MIDI** (Nota, Tempo, Duração).

### Fase 3: Ensaio Monofônico (Monophonization Engine)
* **Função:** Garantir a estrita monofonia e a coerência melódica do solo.
* **Método:** Aplicação de lógica de filtragem para resolver **conflitos de notas sobrepostas** (polifonia residual da transcrição). O *engine* prioriza a nota com maior **velocidade (volume)** e/ou **duração** em cada instante de tempo. Opcionalmente, aplica suavização de **saltos melódicos** (grandes intervalos) para otimizar a tocabilidade.
* **Saída:** Arquivo MIDI final, garantidamente monofônico.

### Fase 4: Síntese e Retorno
* **Função:** Renderizar o solo para áudio e formatar os dados para o usuário.
* **Método:** O arquivo MIDI monofônico é processado por um **Sintetizador de Software** (`Fluidsynth`) utilizando um **SoundFont** (banco de sons) específico do instrumento selecionado, gerando o arquivo de áudio final.

## Requisitos de Sistema

* **Linguagem:** Python ($\geq 3.9$)
* **Bibliotecas:** `Librosa`, `Demucs` (ou alternativa de *stem separation*), `Midy`, `Fluidsynth` (e SoundFonts).

## Uso

```bash
python monolith.py --input "path/to/audio_file.mp3" --instrument "flute" --output_dir "./exports"