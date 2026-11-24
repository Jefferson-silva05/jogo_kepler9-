# P-cub3D — Estação Kepler-9

Aplicação gráfica 3D em primeira pessoa, desenvolvida em **Java**, que utiliza **Raycasting** para transformar um mapa bidimensional em uma representação tridimensional navegável. O cenário é uma estação espacial abandonada, a **Estação Kepler-9**.

Projeto acadêmico desenvolvido na **Faculdade Senac** — Análise e Desenvolvimento de Sistemas (noite, S2), sob orientação do Professor Tibério.

> **Status:** em desenvolvimento (escopo da AV1).

---

## Sumário

- [Sobre o projeto](#sobre-o-projeto)
- [Funcionalidades](#funcionalidades)
- [Tecnologias utilizadas](#tecnologias-utilizadas)
- [Pré-requisitos](#pré-requisitos)
- [Estrutura do repositório](#estrutura-do-repositório)
- [Como executar](#como-executar)
- [Controles](#controles)
- [Formato do mapa (.cub)](#formato-do-mapa-cub)
- [Arquitetura](#arquitetura)
- [Regras de colisão](#regras-de-colisão)
- [Testes planejados](#testes-planejados)
- [Escopo](#escopo)
- [Autores](#autores)

---

## Sobre o projeto

O **P-cub3D — Estação Kepler-9** é uma aplicação em que o jogador explora, em primeira pessoa, uma estação espacial abandonada. O ambiente é construído a partir de um mapa definido em um arquivo de configuração com extensão `.cub`.

Ao iniciar, o programa:

1. lê o arquivo `.cub` informado;
2. valida as configurações e o mapa;
3. posiciona o jogador conforme a orientação indicada no mapa;
4. calcula a imagem tridimensional em tempo real por meio do motor de Raycasting.

O foco do projeto é a aplicação prática de **Programação Orientada a Objetos**, com separação clara de responsabilidades entre leitura e validação do mapa, estado do jogador, cálculos matemáticos, renderização, entrada do usuário e gerenciamento da aplicação.

## Funcionalidades

- Leitura e validação de arquivos `.cub` (texturas, cores e mapa).
- Posicionamento e orientação inicial do jogador (`N`, `S`, `E`, `W`).
- Renderização de paredes por Raycasting e algoritmo DDA.
- Texturas distintas para as quatro orientações de parede.
- Cores RGB configuráveis para chão e teto.
- Movimentação (frente, trás, esquerda, direita) e rotação da câmera.
- Detecção de colisão com paredes.
- Tratamento de erros de leitura e validação com exceção dedicada.

## Tecnologias utilizadas

| Tecnologia | Uso |
| --- | --- |
| Java 17 ou superior | Linguagem principal do projeto |
| Swing / AWT | Criação da janela e recursos gráficos |
| Raycasting e DDA | Geração da visão 3D e percurso dos raios pela grade |
| Arquivos `.cub` | Configuração da cena e do mapa |
| Imagens de textura | Representação das quatro orientações das paredes |
| VS Code ou IntelliJ IDEA | Ambiente de desenvolvimento |

## Pré-requisitos

- **JDK 17 ou superior** instalado e configurado no `PATH`.
- **Git** para clonar o repositório.

Para verificar a instalação do Java:

```bash
java -version
javac -version
```

## Estrutura do repositório

```text
.
├── src/pcub3d/
│   ├── model/      # Estado da cena: Camera, GameMap, SceneConfig
│   ├── parser/     # Leitura e validação do .cub: SceneParser, SceneParseException
│   ├── render/     # Raycasting, texturas e construção da imagem
│   └── app/        # Entrada, coordenação da execução e inicialização
├── tests/          # Testes de mapas, configurações e funcionalidades
├── assets/
│   └── textures/   # Texturas das quatro orientações de parede
├── maps/
│   ├── valid/      # Mapas .cub válidos
│   └── invalid/    # Mapas .cub inválidos (testes de validação)
└── README.md
```

## Como executar

### 1. Clonar o repositório

```bash
git clone <URL-DO-REPOSITORIO>
cd <NOME-DO-REPOSITORIO>
```

### 2. Compilar

**Linux / macOS**

```bash
mkdir -p out
javac -d out $(find src -name "*.java")
```

**Windows (PowerShell)**

```powershell
New-Item -ItemType Directory -Force out | Out-Null
javac -d out (Get-ChildItem -Recurse src -Filter *.java).FullName
```

### 3. Executar

Execute sempre a partir da **raiz do projeto**, para que os caminhos relativos das texturas e dos mapas sejam resolvidos corretamente.

```bash
java -cp out pcub3d.app.Main maps/valid/<nome-do-mapa>.cub
```

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
| `ESC` | Encerrar a aplicação |

## Formato do mapa (.cub)

O arquivo `.cub` reúne as configurações da cena e o mapa. Exemplo:

```text
NO assets/textures/wall_north.png
SO assets/textures/wall_south.png
WE assets/textures/wall_west.png
EA assets/textures/wall_east.png
F 70,70,70
C 20,30,45

1111111111
1000000001
1011101001
100N000001
1111111111
```

### Identificadores de configuração

| Identificador | Significado |
| --- | --- |
| `NO` | Textura da parede voltada ao Norte |
| `SO` | Textura da parede voltada ao Sul |
| `WE` | Textura da parede voltada ao Oeste |
| `EA` | Textura da parede voltada ao Leste |
| `F` | Cor do chão (RGB) |
| `C` | Cor do teto (RGB) |

### Símbolos do mapa

| Símbolo | Significado |
| --- | --- |
| `1` | Parede |
| `0` | Espaço livre |
| `N` | Posição inicial do jogador, olhando para o Norte |
| `S` | Posição inicial do jogador, olhando para o Sul |
| `E` | Posição inicial do jogador, olhando para o Leste |
| `W` | Posição inicial do jogador, olhando para o Oeste |

### Regras de validação

- Deve existir **exatamente uma** posição inicial do jogador.
- O mapa deve estar **totalmente fechado por paredes**.
- Não são aceitos caracteres fora dos símbolos acima.
- Os valores RGB devem ser válidos.
- Os caminhos de textura devem apontar para arquivos existentes.

Em caso de violação, a leitura é interrompida com uma `SceneParseException`.

## Arquitetura

A estrutura é modular, baseada em Programação Orientada a Objetos e inspirada no padrão **MVC**, evitando concentrar leitura de arquivo, estado do jogador, cálculos de Raycasting, renderização e entrada em uma única classe.

| Classe | Responsabilidade |
| --- | --- |
| `Main` | Inicia a aplicação e cria os componentes necessários |
| `SceneParser` | Lê e valida o arquivo `.cub` |
| `SceneParseException` | Representa erros de leitura ou validação |
| `SceneConfig` | Armazena texturas, cores e configurações carregadas do `.cub` |
| `Camera` | Mantém posição, direção e plano de projeção; controla movimentação e rotação |
| `GameMap` | Representa a grade e permite consultas sobre paredes e espaços livres |
| `RaycasterEngine` | Executa os cálculos de Raycasting/DDA e determina a projeção das paredes |
| `TextureManager` | Carrega e disponibiliza as texturas utilizadas pelo motor |
| `Texture` | Representa uma textura carregada e dá acesso aos seus dados visuais |
| `InputHandler` | Interpreta os comandos do teclado |
| `Engine3D` | Coordena atualização do estado, renderização e execução principal |

### Organização por camadas

| Camada | Componentes |
| --- | --- |
| **Model** | `Camera`, `GameMap`, `SceneConfig` |
| **Parser** | `SceneParser`, `SceneParseException` |
| **View / Render** | `RaycasterEngine`, `TextureManager`, `Texture` |
| **Controller / Aplicação** | `InputHandler`, `Engine3D`, `Main` |

> Os nomes das classes podem ser ajustados durante a implementação, desde que as responsabilidades sejam mantidas.

## Regras de colisão

Antes de alterar a posição do jogador, o programa verifica se a posição de destino é uma área livre. Se for uma parede, o deslocamento é bloqueado, impedindo que o jogador a atravesse.

## Testes planejados

Os testes cobrem situações válidas e situações que devem ser rejeitadas:

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
- Encerramento por `ESC` e pelo fechamento da janela.

Mapas de teste ficam em `maps/valid/` e `maps/invalid/`.

## Escopo

O escopo atual (AV1) contempla: leitura e validação do `.cub`, movimentação, colisão, renderização de paredes, texturas e cores de chão e teto.

**Fora do escopo:** mecânica narrativa, sistema de inimigos, portas e demais elementos além dos descritos acima.

## Autores

- **Jefferson da Silva Dias**
- **Livia Rodrigues Dantas**

Faculdade Senac — Análise e Desenvolvimento de Sistemas (Noite, S2) · Professor Tibério
