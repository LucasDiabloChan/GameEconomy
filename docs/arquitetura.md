# Visão Geral do Arquivo

Este arquivo apresenta uma visão abrangente da arquitetura do projeto GameEconomy, cobrindo desde a abordagem frontend-first e tecnologias escolhidas até a organização de módulos, fluxos principais e decisões arquiteturais chave, com o objetivo de orientar desenvolvedores na implementação e manutenção do sistema.

# Sumário

- [1. Visão Geral da Arquitetura](#1-visão-geral-da-arquitetura)
- [2. Stack/Tecnologias Escolhidas](#2-stacktecnologias-escolhidas)
- [3. Organização de Camadas/Módulos](#3-organização-de-camadasmódulos)
  - [3.1 Camada de apresentação (UI)](#31-camada-de-apresentação-ui)
  - [3.2 Camada de Domínio (lógica de negócio)](#32-camada-de-domínio-lógica-de-negócio)
  - [3.3 Camada de Infraestrutura (suporte)](#33-camada-de-infraestrutura-suporte)
- [4. Fluxos principais (descrever com Mermaid depois)](#4-fluxos-principais-descrever-com-mermaid-depois)
  - [4.1 Fluxo de cálculo:](#41-fluxo-de-cálculo)
  - [4.2 Fluxo de histórico:](#42-fluxo-de-histórico)
- [5. Decisões arquiteturais chave (resumo)](#5-decisões-arquiteturais-chave-resumo)
  - [Decisão 1: Uso de Astro em vez de SPA tradicional](#decisão-1-uso-de-astro-em-vez-de-spa-tradicional)
  - [Decisão 2: Uso de TypeScript](#decisão-2-uso-de-typescript)
  - [Decisão 3: PWA com @vite-pwa/astro](#decisão-3-pwa-com-vite-pwaastro)
  - [Decisão 4: Storage local em vez de backend (Fase 1)](#decisão-4-storage-local-em-vez-de-backend-fase-1)
  - [Decisão 5: Licença AGPL-3.0](#decisão-5-licença-agpl-30)

---

# 1. Visão Geral da Arquitetura

Este documento descreve a arquitetura do projeto GameEconomy, que adotou uma abordagem frontend-first na sua versão inicial. Ele detalha as decisões tecnológicas e estruturais tomadas para garantir desempenho, manutenibilidade e extensibilidade futura, com foco em uma aplicação web progressiva (PWA) que roda principalmente no cliente.

O arquivo organiza o conteúdo em seções que cobrem a stack escolhida, incluindo frameworks como Astro e TypeScript, além de módulos para lógica de negócio, armazenamento local e possíveis integrações futuras com backend. Ele também inclui fluxos principais, decisões arquiteturais chave e informações sobre licenciamento, visando fornecer uma visão abrangente para desenvolvedores e stakeholders.

Além disso, o documento serve como guia para a implementação, com ênfase em práticas como islands architecture para otimização de JavaScript e armazenamento offline via localStorage, preparando o caminho para evoluções como sincronização de dados e APIs remotas.

# 2. Stack/Tecnologias Escolhidas

- **Framework principal: Astro**  
  O Astro é o framework central adotado, permitindo a geração de HTML estático por padrão, com interatividade limitada apenas onde necessário através da arquitetura de ilhas de componentes. Sua integração nativa com React facilita a construção de partes interativas, como a calculadora e o histórico, como componentes React, garantindo eficiência e desempenho otimizado.

- **Linguagem: TypeScript**  
  TypeScript é utilizado em toda a lógica de cálculo, modelos de dados e componentes reativos, proporcionando segurança de tipos e melhorando a manutenibilidade do código ao longo do desenvolvimento do projeto.

- **UI e estilos**  
  Para a interface do usuário e estilos, o Tailwind CSS é integrado ao Astro para estilização rápida e responsiva. Além disso, uma biblioteca de componentes baseada em Tailwind, como Material Tailwind ou daisyUI para Astro, é empregada para criar uma interface semelhante a um app sem a necessidade de esforço manual excessivo em CSS.

- **PWA**  
  O plugin @vite-pwa/astro é utilizado para implementar recursos de Progressive Web App, incluindo a geração automática de manifest, a criação de service worker para cache offline, suporte à instalação do app na tela inicial e atualização controlada de versões, assegurando uma experiência nativa no navegador.

- **Storage local**  
  Inicialmente, o localStorage é usado para armazenar o histórico de cálculos e o cache de pacotes diretamente no dispositivo. Há planos para evolução futura para IndexedDB, caso haja necessidade de lidar com dados mais complexos ou volumes maiores.

- **Licença**  
  O projeto adota a licença AGPL-3.0, com um arquivo LICENSE no repositório e menção no README, permitindo o uso e redistribuição do código enquanto assegura que modificações e serviços baseados nele compartilhem melhorias e mantenham os créditos originais.

# 3. Organização de Camadas/Módulos

## 3.1 Camada de apresentação (UI)

Contém as páginas Astro (.astro) responsáveis por layout estático, meta tags, estrutura responsiva e os componentes React usados como “ilhas” para o formulário da calculadora e a listagem e ações do histórico de cálculos.

## 3.2 Camada de Domínio (lógica de negócio)

### Módulo de Cálculo

Possui funções puras para determinar combinações de pacotes que atinjam ou excedam a quantidade desejada e calcular custo total, custo por token e quantidade excedente, em relação a outras opções.

### Módulo de Modelos

Defime os tipos e interfaces TypeScript (Package, CalculationInput, CalculationResult, HistoryEntry e outros).

## 3.3 Camada de Infraestrutura (suporte)

### Módulo de Armazenamento

Dispõe de funções para ler e escrever histórico e pacotes no localStorage.

### Módulo de Integração com API de Pacotes (futuro)

Deve considerar a possível (e provável) adição de Funções para buscar pacotes de um endpoint remoto e a implementação de estratégias de fallback (usar dados locais se a API falhar).

# 4. Fluxos Principais (descrever com Mermaid depois)
Você pode futuramente complementar esta seção com diagramas Mermaid, por exemplo:

## 4.1 Fluxo de Cálculo

Usuário informa quantidade desejada → componente React chama função de cálculo → lógica gera combinação → UI exibe resultado → resultado é salvo no histórico via módulo de storage.

flowchart TD
A[Usuário acessa PWA] --> B[Formulário de cálculo]
B --> C[Usuário informa quantidade desejada de tokens]
C --> D[Usuário seleciona método de pagamento]
D --> E[Usuário clica em 'Calcular']
E --> F[Camada de domínio: lógica de cálculo]
F --> G[Determinar combinação de pacotes
que atinja ou exceda a quantidade desejada]
G --> H[Calcular métricas
(total, custo médio, excedente)]
H --> I[Exibir resultado na UI]
I --> J[Salvar entrada no histórico local]
J --> K[Exibir histórico atualizado]

## 4.2 Fluxo de Histórico

Na inicialização do componente de histórico, ler lista de cálculos do storage → exibir ao usuário → permitir limpar → ao limpar, apagar entradas do storage.

flowchart TD
A[Inicialização da página] --> B[Carregar histórico do localStorage]
B --> C{Histórico existe?}
C -- Não --> D[Inicializar histórico vazio]
C -- Sim --> E[Exibir lista de cálculos anteriores]
E --> F[Usuário pode selecionar 'Limpar histórico']
F --> G[Apagar dados do localStorage]
G --> H[Atualizar UI para histórico vazio]

# 5. Decisões arquiteturais chave (resumo)

Aqui você pode adicionar links para ADRs ou manter apenas um resumo:

## Decisão 1: Uso de Astro em vez de SPA tradicional

Motivação: melhor desempenho para conteúdo estático, islands architecture reduzindo JS no cliente, facilidade de integração com React.

## Decisão 2: Uso de TypeScript

Motivação: tipagem forte para regras de cálculo e modelos de dados, melhorando segurança e manutenibilidade.

## Decisão 3: PWA com @vite-pwa/astro

Motivação: necessidade de funcionamento offline, instalação como app e atualização controlada, com configuração relativamente simples via plugin.

## Decisão 4: Storage local em vez de backend (Fase 1)

Motivação: app inicialmente stateless, sem necessidade de autenticação ou sincronização entre dispositivos; simplifica deployment e reduz complexidade inicial.

## Decisão 5: Licença AGPL-3.0

Motivação: permitir uso e redistribuição do código, assegurando que modificações e serviços baseados no código também compartilhem suas melhorias e mantenham créditos.
