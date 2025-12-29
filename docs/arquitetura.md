# Visão Geral do Arquivo

Este arquivo apresenta uma visão abrangente da arquitetura do projeto GameEconomy, cobrindo desde as decisões iniciais (como a abordagem frontend-first) até às escolhas de tecnologias e a organização dos módulos, fluxos principais e decisões arquiteturais chave, com foco em orientar seu desenvolvimento.

# Sumário

- [1. Visão Geral da Arquitetura](#1-visão-geral-da-arquitetura)
- [2. Stack/Tecnologias Escolhidas](#2-stacktecnologias-escolhidas)
- [3. Organização de Camadas/Módulos](#3-organização-de-camadasmódulos)
  - [3.1 Camada de Apresentação (UI)](#31-camada-de-apresentação-ui)
  - [3.2 Camada de Domínio (Lógica de Negócio)](#32-camada-de-domínio-lógica-de-negócio)
  - [3.3 Camada de Infraestrutura (Suporte)](#33-camada-de-infraestrutura-suporte)
- [4. Diagrama dos Fluxos Principais](#4-diagramas-dos-fluxos-principais)
  - [4.1 Fluxo de cálculo:](#41-fluxo-de-cálculo)
  - [4.2 Fluxo de histórico:](#42-fluxo-de-histórico)
- [5. Decisões Arquiteturais Chave](#5-decisões-arquiteturais-chave)
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

## 3.1 Camada de Apresentação (UI)

Contém as páginas Astro (.astro) responsáveis por layout estático, meta tags, estrutura responsiva e os componentes React usados como “ilhas” para o formulário da calculadora e a listagem e ações do histórico de cálculos.

## 3.2 Camada de Domínio (Lógica de Negócio)

### Módulo de Cálculo

Possui funções puras para determinar combinações de pacotes que atinjam ou excedam a quantidade desejada e calcular custo total, custo por token e quantidade excedente, em relação a outras opções.

### Módulo de Modelos

Defime os tipos e interfaces TypeScript (Package, CalculationInput, CalculationResult, HistoryEntry e outros).

## 3.3 Camada de Infraestrutura (Suporte)

### Módulo de Armazenamento

Dispõe de funções para ler e escrever histórico e pacotes no localStorage.

### Módulo de Integração com API de Pacotes (futuro)

Deve considerar a possível (e provável) adição de Funções para buscar pacotes de um endpoint remoto e a implementação de estratégias de fallback (usar dados locais se a API falhar).

# 4. Diagramas dos Fluxos Principais 
Esse sistema possui, inicialmente, duas principais funcionalidades. Estas são o cálculo e combinação de pacotes para buscar a melhor alternativa e o histórico delas, o qual contém informações relevantes para o usuário, como quantidade de tokens, pacotes, valor total e data.

## 4.1 Fluxo de Cálculo

Nesse fluxo, elucidado no diagrama abaixo, o usuário informa quantidade desejada, através da interface gráfica, e o sistema irá calcular as alternativas possíveis. Após os cálculos, o sistema mostrará ao usuário todas as opções e evidenciará a melhor, com foco em trazer um maior retorno em tokens com preço mais acessível, se possível. Além disso, esses resultados serão armazenados no histórico.

``` mermaid
---
title: BPMN - Fluxo de Cálculo 
config:
  look: handDrawn
  theme: white
---
flowchart LR
  A@{ shape: stadium, label: "Início" }
  C@{ shape: lean-r, label: "Quantidade de Tokens
                             e Meio de Pagamento" }
  H@{ shape: stadium, label: "Término" }
  A((Inicio)) --> B([Usuário Acessa 
                      Calculadora])
  B --> C
  C --> D[Calcular 
            Combinações]
  D --> E[Calcular Preço
            e Descontos]
  E --> F[Salvar Resultados 
            no Histórico]
  F --> G[Exibir Resultados 
            ao Usuário]
  G --> H((Fim))
```

## 4.2 Fluxo de Histórico

Na inicialização do componente de histórico, ler lista de cálculos do storage → exibir ao usuário → permitir limpar → ao limpar, apagar entradas do storage.

``` mermaid
---
title: BPMN - Fluxo de Sincronismo do Histórico
config:
  look: handDrawn
  theme: white
---
flowchart LR
  A@{ shape: stadium, label: "Início" }

  B@{ shape: diamond, label: "Histórico Existe
                              na Nuvem?" }

    C@{ shape: rect,    label: "Sincronizar com 
                                Histórico da Nuvem" }
      
      D@{ shape: rect,    label: "Exibir Cálculos
                                  do Histórico" }
  
    E@{ shape: diamond, label: "Histórico Existe
                                Localmente?" }
  
      F@{ shape: rect,     label: "Recuperar 
                                  Histórico Local" }

      G@{ shape: rect,    label: "Inicializar 
                                  Histórico Vazio" }

  H@{ shape: stadium, label: "Fim" }

  A --> B
  
  B -- Sim --> C
  B -- No --> E
  
  C --> D

  D --> H

  E -- Sim --> F
  E -- Não --> G

  F --> D

  G --> H

```

# 5. Decisões Arquiteturais Chave

Nesta seção, estão as principais decisões acerca da arquitetura e método de desenvolvimento tomados durante o planejamento do projeto GameEconomy. A seguir, as decisões e suas respectivas motivações foram listadas em formato de subtópicos.

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
