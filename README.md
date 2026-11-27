# Resgate Botânico: Curador do Bio-Domo

Aplicação gráfica 3D em primeira pessoa, desenvolvida em **Java**, que utiliza **Raycasting** para converter um mapa bidimensional em uma cena tridimensional navegável em tempo real. O cenário é o **Bio-Domo**, um complexo de estufas automatizadas atingido por uma crise climática, onde o jogador deve restaurar a **Árvore Central**.

## Sumário

- [Visão geral](#visão-geral)
- [Funcionalidades](#funcionalidades)
- [Tecnologias](#tecnologias)
- [Pré-requisitos](#pré-requisitos)
- [Estrutura do repositório](#estrutura-do-repositório)
- [Como executar](#como-executar)
- [Controles](#controles)
- [Formato do mapa (.cub)](#formato-do-mapa-cub)
- [Arquitetura](#arquitetura)
- [Colisão](#colisão)
- [Testes](#testes)
- [Fora do escopo](#fora-do-escopo)
- [Autores](#autores)

---

## Visão geral

O **Resgate Botânico** permite ao jogador explorar, em primeira pessoa, o interior do Bio-Domo após um evento severo de **El Niño** que derrubou o controle climático: calor opressivo, vidros ressecados e falta de água. A **Árvore Central**, coração do ecossistema, secou por completo. O jogador assume o papel do **Curador** e deve recolher as **11 cápsulas** de nutrientes e de hidratação espalhadas pelos setores e entregá-las à árvore, fazendo o domo florescer novamente.

O ambiente é definido por um arquivo de configuração com extensão `.cub`, que descreve o mapa, as texturas, as cores de chão e teto e a posição das cápsulas, das câmaras de pressurização e da Árvore Central.

Ao ser iniciada, a aplicação:

1. lê o arquivo `.cub` informado (ou o mapa padrão `biodomo.cub`);
2. valida as configurações e o mapa;
3. posiciona o jogador de acordo com a orientação indicada no mapa;
4. renderiza a cena continuamente por meio de Raycasting com o algoritmo DDA, a partir do estado atual do jogo.

O projeto aplica os princípios de **Programação Orientada a Objetos**, com responsabilidades separadas entre leitura e validação do mapa, estado e regras do jogo, cálculos matemáticos, renderização, entrada do usuário e gerenciamento da aplicação.

## Funcionalidades

- Leitura e validação de arquivos `.cub` (texturas, cores e mapa).
- Posicionamento e orientação inicial do jogador (`N`, `S`, `E`, `W`).
- Renderização de paredes por Raycasting e DDA.
- Texturas distintas para as quatro orientações de parede.
- Cores RGB configuráveis para chão e teto.
- Movimentação (frente, trás, esquerda e direita), corrida e rotação da câmera.
- Detecção de colisão com paredes.
- Coleta de cápsulas de nutrientes e de hidratação e entrega à Árvore Central.
- Câmaras de pressurização com portas deslizantes animadas.
- Setores com níveis diferentes de estresse térmico (calor, névoa e vinheta).
- Restauração progressiva do domo: paredes, árvore, chão, teto e iluminação evoluem a cada entrega.
- Minimapa 2D com névoa de exploração e cone de visão.
- Tratamento de erros de leitura e validação por meio de exceção dedicada.

## Tecnologias

| Tecnologia | Uso |
| --- | --- |
| Java 17 ou superior | Linguagem principal |
| Swing / AWT | Janela e recursos gráficos |
| Raycasting e DDA | Geração da visão 3D e percurso dos raios pela grade |
| Arquivos `.cub` | Configuração da cena e do mapa |
| Imagens PNG | Texturas de paredes, porta, árvore e cápsulas |

## Pré-requisitos

- **JDK 17 ou superior**, configurado no `PATH`.
- **Git**, para clonar o repositório.

Para verificar a instalação:

```bash
java -version
javac -version
```

## Estrutura do repositório

```text
.
├── src/pcub3d/
│   ├── model/      # Estado e regras do jogo: Camera, GameMap, GameState, Collectible, PressureDoor, CentralTree, SceneConfig, Scene
│   ├── parser/     # Leitura e validação do .cub: SceneParser, SceneParseException
│   ├── render/     # Raycasting, texturas, HUD e construção da imagem
│   └── app/        # Entrada do usuário, coordenação da execução e inicialização
├── tests/          # Testes de mapas, configurações e funcionalidades
├── assets/
│   └── textures/   # Texturas de paredes, porta, árvore e cápsulas
├── maps/
│   ├── valid/      # Mapas .cub válidos (biodomo.cub e treino.cub)
│   └── invalid/    # Mapas .cub inválidos, usados na validação
└── README.md
```

## Como executar

### 1. Clonar o repositório

```bash
git clone https://github.com/jefferson-dias/resgate_botanico.git
cd resgate_botanico
```

### 2. Compilar

**Linux / macOS**

```bash
mkdir -p out
javac -encoding UTF-8 -d out $(find src -name "*.java")
```

**Windows (PowerShell)**

```powershell
New-Item -ItemType Directory -Force out | Out-Null
javac -encoding UTF-8 -d out (Get-ChildItem -Recurse src -Filter *.java).FullName
```

### 3. Executar

Execute a partir da **raiz do projeto**, para que os caminhos relativos de texturas e mapas sejam resolvidos corretamente.

```bash
java -Dfile.encoding=UTF-8 -cp out pcub3d.app.Main maps/valid/biodomo.cub
```

Sem argumento, o programa carrega o mapa padrão `maps/valid/biodomo.cub`. O mapa `maps/valid/treino.cub`, reduzido, é indicado para testes rápidos.

### Execução pela IDE

1. Abra a pasta do projeto no **IntelliJ IDEA** ou no **VS Code** (com o *Extension Pack for Java*).
2. Defina `src` como diretório de código-fonte e o JDK 17 (ou superior) como SDK do projeto.
3. Execute a classe `pcub3d.app.Main`, informando o caminho do arquivo `.cub` como argumento do programa.

## Controles

| Tecla | Ação |
| --- | --- |
| `W` / `↑` | Mover para frente |
| `S` / `↓` | Mover para trás |
| `A` | Mover para a esquerda |
| `D` | Mover para a direita |
| `←` | Girar a câmera para a esquerda |
| `→` | Girar a câmera para a direita |
| `SHIFT` | Correr |
| `E` | Abrir câmara de pressurização / transferir cápsulas para a árvore |
| `M` | Ligar ou desligar o minimapa |
| `F3` | Exibir FPS |
| `ENTER` | Começar / jogar novamente (tela final) |
| `ESC` | Encerrar a aplicação |

## Formato do mapa (.cub)

O arquivo `.cub` reúne as configurações da cena e o mapa. O mapa deve ser o **último** bloco do arquivo, e linhas iniciadas por `#` são comentários. Exemplo:

```text
NO assets/textures/wall_north.png
SO assets/textures/wall_south.png
WE assets/textures/wall_west.png
EA assets/textures/wall_east.png
DOOR assets/textures/door_pressure.png
TREE_0 assets/textures/tree_0.png
TREE_1 assets/textures/tree_1.png
TREE_2 assets/textures/tree_2.png
TREE_3 assets/textures/tree_3.png
CAP_N assets/textures/capsule_nutrient.png
CAP_H assets/textures/capsule_hydration.png
F 74,48,34
C 132,94,58

1111111111
10000000011111111
1000000P010000001
100000000100000P1
1000T0000G0000001
10000000010000001
10000000010010001
10N000000100000A1
10000000011111111
1111111111
```

### Identificadores de configuração

| Identificador | Significado |
| --- | --- |
| `NO` | Textura da parede voltada ao Norte |
| `SO` | Textura da parede voltada ao Sul |
| `WE` | Textura da parede voltada ao Oeste |
| `EA` | Textura da parede voltada ao Leste |
| `NO_MID` `SO_MID` `WE_MID` `EA_MID` | Texturas de parede no estado de recuperação (opcional: as 4 ou nenhuma) |
| `NO_LIVE` `SO_LIVE` `WE_LIVE` `EA_LIVE` | Texturas de parede no estado restaurado (opcional: as 4 ou nenhuma) |
| `DOOR` | Textura da câmara de pressurização (obrigatória se o mapa tiver `G`) |
| `TREE_0` a `TREE_3` | Textura da Árvore Central em cada estágio |
| `CAP_N` / `CAP_H` | Textura das cápsulas de nutrientes / hidratação |
| `F` | Cor do chão (RGB) |
| `C` | Cor do teto (RGB) |
| `F_LIVE` / `C_LIVE` | Cor do chão / teto no estado restaurado (RGB, opcionais) |

### Símbolos do mapa

| Símbolo | Significado |
| --- | --- |
| `1` | Parede |
| `0` | Espaço livre |
| `N` | Posição inicial do jogador, voltado ao Norte |
| `S` | Posição inicial do jogador, voltado ao Sul |
| `E` | Posição inicial do jogador, voltado ao Leste |
| `W` | Posição inicial do jogador, voltado ao Oeste |
| `G` | Câmara de pressurização (porta) |
| `P` | Cápsula de nutrientes |
| `A` | Cápsula de hidratação |
| `T` | Árvore Central |
| ` ` (espaço) | Vazio fora da estufa |

### Regras de validação

- Deve existir **exatamente uma** posição inicial do jogador e **exatamente uma** Árvore Central.
- Deve existir pelo menos uma cápsula.
- O mapa deve estar **totalmente fechado por paredes**: nenhuma célula livre pode encostar em espaço vazio ou na borda, inclusive nas diagonais.
- Não são aceitos caracteres fora dos símbolos acima, nem linhas vazias no meio do mapa.
- A câmara `G` deve estar entre duas paredes opostas, com passagem livre nos outros lados.
- Cápsulas e árvore devem ser alcançáveis a partir da posição inicial.
- Os valores RGB devem estar entre 0 e 255.
- Os caminhos de textura devem apontar para arquivos de imagem existentes e válidos.
- Chaves não podem ser duplicadas, desconhecidas ou sem valor, e os grupos `_MID` e `_LIVE` devem estar completos.

Qualquer violação interrompe a leitura com uma `SceneParseException`.

## Arquitetura

A aplicação é modular, baseada em Programação Orientada a Objetos e inspirada no padrão **MVC**. Leitura de arquivo, estado do jogo, cálculos de Raycasting, renderização e entrada do usuário ficam em classes distintas.

| Classe | Responsabilidade |
| --- | --- |
| `Main` | Inicia a aplicação e cria os componentes necessários |
| `SceneParser` | Lê e valida o arquivo `.cub` |
| `SceneParseException` | Representa erros de leitura ou validação |
| `Scene` | Reúne a configuração e o mapa carregados |
| `SceneConfig` | Armazena texturas, cores e configurações carregadas do `.cub` |
| `Camera` | Mantém posição, direção e plano de projeção; controla movimentação e rotação |
| `GameMap` | Representa a grade e permite consultas sobre paredes, espaços livres e setores |
| `GameState` | Mantém o estado do jogo: jogador, cápsulas, portas, árvore e nível de restauração |
| `Collectible` | Classe abstrata das cápsulas, especializada em `NutrientCapsule` e `HydrationCapsule` |
| `PressureDoor` | Representa a câmara de pressurização e sua animação de abertura |
| `CentralTree` | Representa a Árvore Central e seu estágio de florescimento |
| `RaycasterEngine` | Executa os cálculos de Raycasting/DDA e determina a projeção das paredes |
| `TextureManager` | Carrega e disponibiliza as texturas utilizadas pelo motor |
| `Texture` | Representa uma textura carregada e dá acesso aos seus dados visuais |
| `HudRenderer` | Desenha o HUD e o minimapa |
| `InputHandler` | Interpreta os comandos do teclado |
| `Engine3D` | Coordena atualização do estado, renderização e execução principal |

### Organização por camadas

| Camada | Componentes |
| --- | --- |
| **Model** | `Camera`, `GameMap`, `GameState`, `Collectible`, `PressureDoor`, `CentralTree`, `SceneConfig`, `Scene` |
| **Parser** | `SceneParser`, `SceneParseException` |
| **View / Render** | `RaycasterEngine`, `TextureManager`, `Texture`, `HudRenderer` |
| **Controller / Aplicação** | `InputHandler`, `Engine3D`, `Main` |

## Colisão

Antes de alterar a posição do jogador, a aplicação verifica se a posição de destino é uma área livre. Se for uma parede ou uma porta fechada, o deslocamento é bloqueado, impedindo que o jogador a atravesse. A colisão considera um círculo ao redor do jogador e permite deslizar ao longo das paredes.

## Testes

Os testes cobrem cenários válidos e cenários que devem ser rejeitados:

- Carregamento de mapa válido e abertura da cena.
- Identificação correta da posição inicial.
- Movimentação nas quatro direções e rotação da câmera.
- Bloqueio ao tentar atravessar paredes.
- Texturas das quatro orientações.
- Valores RGB válidos e inválidos.
- Textura inexistente ou caminho inválido.
- Mapa aberto.
- Caracteres inválidos.
- Ausência ou duplicidade da posição inicial.
- Fluxo completo do jogo: abertura de porta, coleta, entrega e vitória.
- Encerramento por `ESC` e pelo fechamento da janela.

Os mapas utilizados nos testes ficam em `maps/valid/` e `maps/invalid/`. A suíte principal é a classe `AllTests`, em `tests/`, e pode ser executada em modo headless (sem abrir janela):

```bash
javac -encoding UTF-8 -d out $(find src tests -name "*.java")
java -Djava.awt.headless=true -Dfile.encoding=UTF-8 -cp out pcub3d.tests.AllTests
```

## Fora do escopo

O projeto não inclui inimigos, áudio, salvamento de progresso ou outros elementos além dos descritos neste documento.

## Autores

- **Jefferson da Silva Dias**
- **Livia Rodrigues Dantas**

Projeto desenvolvido na **Faculdade Senac**, no curso de Análise e Desenvolvimento de Sistemas.
