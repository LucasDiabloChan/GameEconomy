# Introdução 

Este documento apresenta os requisitos para o desenvolvimento de uma aplicação PWA (Progressive Web App) destinada ao cálculo de pacotes de bens digitais em jogos, como diamantes e moedas, com foco em usuários móveis. A aplicação permite calcular combinações otimizadas de pacotes para atingir quantidades desejadas, considerando ofertas e métodos de pagamento, enquanto mantém armazenamento local para histórico e configurações.

# Sumário

- [Introdução](#introdução)
- [1. Visão geral](#1-visão-geral)
- [2. Atores e contexto](#2-atores-e-contexto)
  - [Ações dos Usuários no site (ou PWA)](#ações-dos-usuários-no-site-ou-pwa)
  - [Ações do Administrador](#ações-do-administrador)
- [3. Requisitos de Regras de Negócio](#3-requisitos-de-regras-de-negócio)
- [4. Requisitos funcionais](#4-requisitos-funcionais)
- [5. Requisitos não funcionais](#5-requisitos-não-funcionais)

--- 

# 1. Visão geral
O sistema será um PWA de calculadora de pacotes de bens digitais de jogos (diamantes, moedas, rubis e etc), focado em usuários mobile. Essa aplicação permite calcular rapidamente quantos pacotes comprar para atingir uma quantidade desejada desses bens, cálculo o qual considera diferentes ofertas e métodos de pagamento. O aplicativo será stateless do lado do servidor, com armazenamento apenas local para histórico e configurações de pacotes.

# 2. Atores e contexto

Esse projeto, inicialmente, conta com apenas dois atores, sendo eles o administrador e o usuário. 

- Administrador: é quem realizará a manutenção e monitoramento do sistema e corrigirá eventuais bugs.
- Usuário: são todos aqueles que utilizarão o sistema.

Dentro dessa aplicação, cada um têm seu papel e suas respectivas interações, onde somente as do `usuário` serão descritas, visto que o administrador possuirá todas as funcionalidades de um usuário comum, com o adicional de poder auditar o sistema e aprimorá-lo. 

## Ações dos Usuários no site (ou PWA)

Ao acessar a plataforma (ou PWA - versão instalada localmente no celular), ele informa quantos tokens deseja adquirir e, após os cálculos internos, ele pode visualizar as combinações de pacotes sugeridas. Após isso, ele poderá avaliar o custo total, custo médio por token e impacto de diferentes métodos de pagamento. Adicionalmente, ele também pode consultar histórico de cálculos realizados no dispositivo.

## Ações do Administrador

O administrador, além de todos as responsabilidades citadas, terá acesso a insights e dashboards, futuramente. Além dessas visualizações, ele também poderá gerir o uso das APIs externas usadas para atualizar ou consultar os pacotes e lojas disponíveis (futuramente). Essas APIs serão responsaveis por fornecer a lista atualizada de pacotes, preços, descontos e quantidades de bens digitais.

# 3. Requisitos de Regras de Negócio

As regras de negócio afetam como os comportamentos devem ser implementados, principalmente no que tange ao "como" manipular e retornar os resultados do sistema. A seguir, confira a lista de regras levantadas:

| # | Descrição | Prioridade |
|:----------:|:----------|:----------:|
| RN01 | O sistema deve permitir que o cálculo de pacotes ultrapasse a quantidade desejada de tokens, desde que: (1) traga o menor custo e, no caso de empates de preço, (2) destaque a opção que traga o maior número de tokens. | Alta |
| RN02 | O sistema deve, após os cálculos, considerar as tarifas e descontos atuais, de acordo com cada tipo de pagamento (Pix, Boleto, Cartão de Crédito e entre outros). | Alta | 

# 4. Requisitos funcionais

Os Requisitos Funcionais descrevem comportamentos que o sistema deve entregar. Confira na tabela a seguir os requisitos levantados: 

| # | Descrição | Prioridade |
|:----------:|:----------|:----------:|
| RF01 | O sistema deve permitir que o usuário insira a quantidade de tokens desejados | Alta |
| RF02 | O sistema deve permitir que o usuário selecione pelo menos um meio de pagamento. | Alta |
| RF03 | O sistema deve calcular combinações de pacotes de tokens que atinjam pelo menos a quantidade de tokens desejada pelo usuário, minimizando o custo total. | Alta |
| RF04 | Após cada cálculo, o sistema deve exibir ao usuário a quantidade total de tokens, valor total, custo médio por token e a diferença entre outras opções. | Alta |
| RF05 | O sistema deve manter um histórico, de forma local, de cálculos realizados no dispositivo, contendo, para cada entrada: (1) quantidade desejada de tokens; (2) combinação de pacotes selecionada (lista de pacotes, preço, tokens); (3) valor total e custo médio por token; (4) o método de pagamento usado e (5) a data e hora do cálculo. | Alta |
| RF06 | O sistema deve restaurar e exibir o histórico de cálculos realizados e armazenados previamente, a fim de que o usuário possa visualizar ou limpar os cálculos recentes. | Alta | 
| RF07 | O sistema deve permitir que o administrador possa gerenciar a lista de pacotes disponíveis, em que cada um contém: (1) preço, (2) quantidade de tokens e (3) o tipo de moeda (exemplo: BRL). | Alta |
| RF08 | O sistema deve permitir que o usuário selecione o idioma da aplicação e, após selecionado, exiba a interface de acordo com o idioma escolhido. | Baixa |
| RF09 | O sistema deve sincronizar sua base de dados local (dos pacotes) toda vez que for aberto, caso tenha acesso à internet. | Baixo |


# 5. Requisitos não funcionais

Os Requisitos Não Funcionais descrevem atributos de qualidade que o sistema deve atender. Confira na tabela a seguir os requisitos levantados:

| # | Descrição | Prioridade |
|:----------:|:----------|:----------:|
| RNF01 | O sistema deve fornecer uma experiência de tela única, onde o usuário informe a quantidade desejada de tokens, visualize o resultado da combinação de pacotes e consulte o histórico na mesma página. | Alta |
| RNF02 | O sistema deve funcionar como um PWA, suportando instalação no dispositivo com ícone e nome configurados, além de funcionamento offline após o primeiro carregamento via cache de assets em um service worker. | Alta |
| RNF03 | O layout deve ser responsivo, com foco em dispositivos móveis, mantendo boa usabilidade em telas maiores, com aparência "app-like", componentes adequados a toque, botões claros e fontes legíveis. | Alta |
| RNF04 | O tempo de carregamento inicial deve ser baixo, aproveitando o fato de o app ser majoritariamente estático, com uso de JavaScript limitado às partes interativas (calculadora e histórico), seguindo o conceito de “ilhas de interatividade”. | Alta |
| RNF05 | A interface deve ser intuitiva, com poucos campos obrigatórios (quantidade de tokens e método de pagamento), exibindo informações de resultado (custo total, custo por token, quantidade excedente) de forma clara, com bom contraste visual. | Alta |
| RNF06 | O código deve ser escrito em TypeScript, com tipagem explícita dos modelos (pacotes, histórico, etc.), lógica de cálculo separada em módulos puros, e estrutura de internacionalização que permita adicionar novos idiomas com impacto mínimo. | Alta |
| RNF07 | O projeto deverá ser disponibilizado como open source sob a licença AGPL-3.0, garantindo reutilização condicionada à abertura do código de serviços derivados e preservação de créditos ao autor. | Alta |