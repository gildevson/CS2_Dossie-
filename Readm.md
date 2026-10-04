# 🛡️ CS Integrity

Plataforma de **análise de integridade de partidas de Counter-Strike 2**, capaz de processar arquivos `.dem`, analisar o comportamento dos jogadores e identificar padrões estatisticamente suspeitos.

O objetivo do projeto não é afirmar automaticamente que determinado jogador utiliza cheats, mas gerar **evidências técnicas, métricas e um Dossiê de Integridade** que auxiliem jogadores, comunidades, servidores e organizadores de campeonatos na análise de partidas.

---

# 🎯 Objetivo

Criar uma plataforma capaz de:

- Receber arquivos `.dem` do Counter-Strike 2.
- Processar partidas automaticamente.
- Identificar jogadores e rounds.
- Extrair eventos e informações relevantes.
- Analisar movimentação e comportamento da mira.
- Identificar comportamentos estatisticamente suspeitos.
- Reduzir falsos positivos considerando o contexto da partida.
- Gerar evidências.
- Criar um histórico de integridade por jogador.
- Permitir revisão humana.
- Criar uma comunidade de investigadores.
- Gerar um Dossiê de Integridade.
- Disponibilizar futuramente uma API de integridade.

---

# ⚠️ Princípio do projeto

O sistema **não deve declarar automaticamente que um jogador está utilizando cheat**.

A plataforma deve apresentar:

> "Foram encontrados comportamentos estatisticamente anormais que merecem análise."

Cada detecção deve possuir evidências que expliquem por que determinado comportamento foi considerado suspeito.

---

# 🧠 Conceito

Fluxo principal:

```text
Partida CS2
     ↓
Arquivo .DEM
     ↓
Upload
     ↓
Object Storage
     ↓
Fila de processamento
     ↓
DEM Parser
     ↓
Eventos / Ticks
     ↓
Detection Engine
     ↓
Context Analyzer
     ↓
Evidence Engine
     ↓
Integrity Engine
     ↓
Dossiê de Integridade
     ↓
Revisão da Comunidade
```

---

# 🏗️ Arquitetura

```text
                         ┌──────────────────────┐
                         │       ANGULAR        │
                         │                      │
                         │ Dashboard            │
                         │ Upload .DEM          │
                         │ Dossiê               │
                         │ Replay 2D            │
                         │ Comunidade           │
                         │ Perfil do jogador    │
                         └──────────┬───────────┘
                                    │
                                  HTTPS
                                    │
                         ┌──────────▼───────────┐
                         │   ASP.NET CORE API   │
                         │                      │
                         │ Authentication       │
                         │ Players              │
                         │ Matches              │
                         │ Demos                │
                         │ Investigations       │
                         │ Evidence             │
                         │ Community Reviews    │
                         │ Integrity Scores     │
                         └──────────┬───────────┘
                                    │
                ┌───────────────────┼──────────────────┐
                │                   │                  │
                ▼                   ▼                  ▼
        ┌──────────────┐    ┌───────────────┐   ┌─────────────┐
        │ PostgreSQL   │    │ Object Storage│   │    Redis    │
        │              │    │               │   │             │
        │ Players      │    │ Arquivos DEM  │   │ Cache       │
        │ Matches      │    │ Relatórios    │   │ Jobs        │
        │ Evidence     │    │ Artefatos     │   │ Rate Limit  │
        │ Reviews      │    └───────────────┘   └─────────────┘
        │ Scores       │
        └──────────────┘
                ▲
                │
                │ Resultados
                │
        ┌───────┴────────────────────────────┐
        │                                    │
        │         ANALYSIS WORKER            │
        │                                    │
        │   DEM Parser                       │
        │        ↓                           │
        │   Detection Engine                 │
        │        ↓                           │
        │   Context Analyzer                 │
        │        ↓                           │
        │   Evidence Generator               │
        │        ↓                           │
        │   Integrity Engine                 │
        │                                    │
        └────────────────────────────────────┘
```

---

# 💻 Stack

## Front-end

- Angular
- TypeScript
- HTML
- SCSS
- Canvas / SVG para replay
- Chart.js para gráficos

## Backend

- C#
- ASP.NET Core
- Entity Framework Core
- Background Workers

## Banco

- PostgreSQL

## Cache / Jobs

- Redis

## Storage

Possibilidades:

- Cloudflare R2
- AWS S3
- Azure Blob Storage

## Infraestrutura

- Docker
- Docker Compose
- CI/CD

---

# 📁 Estrutura da solução

```text
CSIntegrity/

├── backend/
│
│   ├── CSIntegrity.Api
│   ├── CSIntegrity.Application
│   ├── CSIntegrity.Domain
│   ├── CSIntegrity.Infrastructure
│   │
│   ├── CSIntegrity.Analysis
│   ├── CSIntegrity.DemoParser
│   └── CSIntegrity.Worker
│
├── frontend/
│
│   └── cs-integrity-web/
│
├── docker/
│
├── docs/
│
├── tests/
│
└── README.md
```

---

# 🎮 DEM Parser

O parser será responsável por transformar arquivos:

```text
.dem
```

em informações estruturadas.

Exemplo:

```json
{
    "tick": 183929,
    "playerId": "76561198000000000",
    "position": {
        "x": 1241.3,
        "y": -821.2,
        "z": 128
    },
    "view": {
        "yaw": 84.7,
        "pitch": -2.1
    },
    "weapon": "AK-47",
    "fired": true
}
```

Informações interessantes:

- Tick
- Steam ID
- Posição
- Direção da mira
- Arma
- Disparos
- Kills
- Damage
- Headshots
- Movimentação
- Rounds
- Eventos da partida

---

# 🧠 Detection Engine

O Detection Engine será responsável por procurar padrões suspeitos.

```text
DetectionEngine

├── AimDetector
├── WallDetector
├── ReactionDetector
├── TriggerDetector
├── RecoilDetector
├── MovementDetector
└── ContextAnalyzer
```

---

# 🎯 Aim Detector

Analisa possíveis padrões anormais relacionados à mira.

Exemplos:

- Mudanças extremamente rápidas de ângulo.
- Snap para a cabeça.
- Tracking excessivamente preciso.
- Correções anormais.
- Consistência estatisticamente incomum.

---

# 👁️ Wall Detector

Procura comportamentos compatíveis com conhecimento anormal da posição de adversários.

Exemplos:

- Tracking através de obstáculos.
- Prefire recorrente.
- Mira acompanhando inimigos não visíveis.
- Mudanças de mira relacionadas à movimentação de inimigos ocultos.

Um evento isolado **não deve ser considerado prova**.

---

# ⚡ Reaction Detector

Analisa o tempo entre:

```text
Alvo visível
     ↓
Movimento da mira
     ↓
Disparo
```

O sistema poderá construir uma distribuição histórica dos tempos de reação do jogador.

---

# 🔫 Trigger Detector

Analisa padrões relacionados ao momento do disparo.

Exemplo:

```text
Crosshair encontra alvo
        ↓
       35ms
        ↓
      Disparo
```

A análise deve considerar várias ocorrências antes de gerar uma evidência relevante.

---

# 🔄 Recoil Detector

Analisa padrões relacionados ao controle de recuo.

Pode observar:

- consistência;
- correções;
- comportamento da mira;
- padrões repetitivos.

---

# 🏃 Movement Detector

Analisa padrões relacionados à movimentação.

Pode observar:

- timings;
- regularidade excessiva;
- padrões repetitivos;
- comportamentos estatisticamente anormais.

---

# 🧠 Context Analyzer

Uma das partes mais importantes da plataforma.

O objetivo é evitar conclusões precipitadas.

Exemplo:

```text
Jogador acompanhou inimigo pela parede
                 ↓
      Viu o inimigo anteriormente?
                 ↓
                NÃO
                 ↓
        Existia pista sonora?
                 ↓
                NÃO
                 ↓
       Era um ângulo previsível?
                 ↓
                NÃO
                 ↓
      Tracking continuou oculto?
                 ↓
                SIM
                 ↓
        EVENTO SUSPEITO
```

O contexto pode reduzir ou aumentar a confiança da detecção.

---

# 🔎 Evidence Engine

Cada comportamento relevante gera uma evidência.

Exemplo:

```text
Evidence

Tipo:
PossibleWallTracking

Round:
17

Tempo:
01:23

Confiança:
94%

Descrição:
Tracking prolongado de adversário não visível.

Contexto:
Sem contato visual recente.
```

---

# 🗄️ Entidade Evidence

```text
Evidence

Id
PlayerId
MatchId
RoundId

Type

StartTick
EndTick

Confidence

ContextScore

Description

CreatedAt
```

---

# 📊 Integrity Engine

Responsável por calcular o histórico de integridade.

O cálculo NÃO deve simplesmente utilizar:

```text
Média das evidências
```

Deve considerar:

```text
Quantidade
+
Repetição
+
Intensidade
+
Contexto
+
Histórico
+
Diversidade das evidências
```

---

# 📁 Dossiê de Integridade

Cada jogador poderá possuir um relatório.

Exemplo:

```text
PLAYER_X

INTEGRIDADE

37 / 100

RISCO ELEVADO

────────────────────────

Partidas analisadas: 48

Rounds analisados: 1.247

────────────────────────

AIM

91% suspeito

WALL

82% suspeito

TRIGGER

41% suspeito

MOVEMENT

18% suspeito

────────────────────────

EVIDÊNCIAS

🔴 Round 17
Tracking suspeito
Confiança: 94%

[VER REPLAY]

🟠 Round 21
Prefire suspeito
Confiança: 79%

[VER REPLAY]
```

---

# 🗺️ Replay 2D

O sistema poderá reconstruir partes da partida.

Exemplo:

```text
             MAPA

┌─────────────────────────────┐
│                             │
│          PLAYER A           │
│             ●               │
│              \              │
│               \ MIRA        │
│                \            │
│           █████████         │
│           █ PAREDE █        │
│           █████████         │
│                    ●        │
│                 PLAYER B    │
│                             │
└─────────────────────────────┘

◀────────────●──────────────▶

Tick 183929
```

O replay poderá mostrar:

- jogadores;
- posições;
- direção da mira;
- tiros;
- kills;
- movimentação;
- eventos suspeitos.

---

# 👥 Comunidade

A plataforma poderá possuir um sistema comunitário de revisão.

Uma evidência será apresentada de maneira neutra.

```text
CASO #19382

Round 17

Assista ao trecho.

Como você classificaria?

[ NORMAL ]

[ SUSPEITO ]

[ ALTAMENTE SUSPEITO ]

[ INCONCLUSIVO ]
```

O resultado automático não deve ser mostrado antes do voto para evitar influenciar o revisor.

---

# 🕵️ Sistema de Investigadores

Os usuários poderão ganhar reputação conforme participam das análises.

Possíveis níveis:

```text
Revisor

Investigador

Investigador Experiente

Investigador Sênior

Especialista
```

Exemplo:

```text
INVESTIGADOR SÊNIOR

Casos analisados:
1.823

Reputação:
94 / 100
```

---

# 🏆 Ranking

A comunidade poderá possuir rankings baseados em:

- quantidade de análises;
- consistência;
- qualidade das avaliações;
- reputação;
- experiência.

O sistema não deve recompensar simplesmente quem classifica mais jogadores como suspeitos.

---

# 🗃️ Banco de Dados

Entidades iniciais:

```text
Users

Players
SteamProfiles

Demos

Matches
Rounds
MatchPlayers

Evidence

Investigations

IntegrityScores
IntegrityHistory

CommunityReviews

ReviewerProfiles
ReviewerReputation
```

---

# 🔐 Segurança

Arquivos `.dem` enviados pelos usuários devem ser considerados conteúdo não confiável.

O processamento deve ocorrer isoladamente.

```text
Internet
   ↓
API
   ↓
Storage
   ↓
Queue
   ↓
Isolated Worker
   ↓
DEM Parser
```

O Worker deverá possuir:

- limite de memória;
- limite de CPU;
- timeout;
- validação de tamanho;
- validação do arquivo;
- isolamento;
- permissões mínimas.

---

# 🚀 MVP

O projeto será desenvolvido progressivamente.

## V1 — Demo Parser

Objetivo:

```text
Upload .DEM
      ↓
Parser
      ↓
Partida
      ↓
Jogadores
      ↓
Rounds
      ↓
Kills
```

---

## V2 — Análise básica

Adicionar:

```text
Aim Analysis

Reaction Analysis
```

---

## V3 — Contexto

Adicionar:

```text
Context Analyzer

Possible Wall Tracking

Prefire Analysis
```

---

## V4 — Evidências

Adicionar:

```text
Evidence Engine

Integrity Score

Dossiê

Replay 2D
```

---

## V5 — Comunidade

Adicionar:

```text
Community Review

Investigadores

Reputação

Ranking
```

---

## V6 — Machine Learning

Depois da obtenção de uma quantidade significativa de dados classificados:

```text
Dados das demos
        +
Evidências
        +
Avaliações humanas
        ↓
Dataset
        ↓
Machine Learning
        ↓
Melhoria do Detection Engine
```

---

# 💰 Possibilidades de monetização

A plataforma poderá trabalhar com modelo gratuito + premium.

## Gratuito

- Upload limitado de demos.
- Análise básica.
- Perfil de integridade.
- Participação na comunidade.

## Premium

- Mais análises.
- Histórico completo.
- Dossiê avançado.
- Replay detalhado.
- Comparação entre partidas.
- Estatísticas avançadas.

## Comunidades e servidores

Possível plano profissional para:

- servidores privados;
- comunidades;
- ligas;
- campeonatos;
- organizadores de eventos.

---

# 🔌 API de Integridade

No futuro poderá existir uma API.

Exemplo:

```http
GET /api/integrity/{steamId}
```

Resposta:

```json
{
    "integrityScore": 94,
    "riskLevel": "LOW",
    "matchesAnalyzed": 184,
    "strongEvidence": 0
}
```

Essa pontuação deverá ser tratada como um **indicador adicional**, nunca como prova definitiva de utilização de cheat.

---

# 🧬 Diferencial

O principal diferencial do projeto não será apenas detectar comportamentos suspeitos.

Será combinar:

```text
Detecção
    +
Contexto
    +
Evidências
    +
Histórico
    +
Replay
    +
Comunidade
    +
Reputação
```

---

# 📈 Ativo do projeto

Com o crescimento da plataforma, quatro ativos importantes poderão surgir:

```text
Algoritmos de detecção

+

Histórico de partidas

+

Dataset de comportamentos classificados

+

Comunidade de investigadores
```

Isso poderá permitir que o sistema melhore progressivamente.

---

# 🌎 Visão de longo prazo

```text
                   CS INTEGRITY

                        │

        ┌───────────────┼───────────────┐
        │               │               │

    DETECTOR         DOSSIÊ         COMUNIDADE

        │               │               │

   Evidências        Histórico       Revisores

        └───────────────┼───────────────┘

                        ↓

                INTEGRITY ENGINE

                        ↓

                 INTEGRITY API

                        ↓

          ┌─────────────┼─────────────┐
          │             │             │

      Jogadores     Comunidades   Campeonatos
```

---

# 🛡️ Missão

Criar uma plataforma de **integridade competitiva baseada em dados, contexto, transparência e evidências**, auxiliando a comunidade a identificar comportamentos anormais sem depender exclusivamente de acusações ou decisões automatizadas.

> **Não acusar. Analisar, contextualizar e apresentar evidências.**