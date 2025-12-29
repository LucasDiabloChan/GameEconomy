# Visão Geral do Arquivo 

Este documento descreve as principais entidades de dados utilizadas pelo sistema e seus atributos, em nível lógico (independente de implementação específica).

# Sumário

- [1. Visão geral](#1-visão-geral)
- [2. Pacotes de tokens](#2-pacotes-de-tokens)
  - [2.1 Entidade `Package`](#21-entidade-package)
  - [2.2 Tipo `PaymentMethod`](#22-tipo-paymentmethod)
  - [2.3 Exemplo de pacote em JSON](#23-exemplo-de-pacote-em-json)
- [3. Entradas de cálculo](#3-entradas-de-cálculo)
  - [3.1 Entidade `CalculationInput`](#31-entidade-calculationinput)
  - [3.2 Exemplo de entrada de cálculo em JSON](#32-exemplo-de-entrada-de-cálculo-em-json)
- [4. Resultado de cálculo](#4-resultado-de-cálculo)
  - [4.1 Entidade `CalculationResult`](#41-entidade-calculationresult)
  - [4.2 Entidade `SelectedPackage`](#42-entidade-selectedpackage)
  - [4.3 Exemplo de resultado em JSON](#43-exemplo-de-resultado-em-json)
- [5. Histórico de cálculos](#5-histórico-de-cálculos)
  - [5.1 Entidade `HistoryEntry`](#51-entidade-historyentry)
  - [5.2 Estrutura de armazenamento em `localStorage`](#52-estrutura-de-armazenamento-em-localstorage)
- [6. Possíveis extensões futuras](#6-possíveis-extensões-futuras)

--- 

## 1. Visão Geral

O sistema manipula principalmente três grupos de dados:

1. **Pacotes de tokens** – ofertas de compra de bens digitais (tokens) com preço e quantidade.  
2. **Entradas de cálculo** – parâmetros fornecidos pelo usuário para realizar um cálculo.  
3. **Histórico de cálculos** – registros de resultados calculados, armazenados localmente.

Todos os modelos serão representados em TypeScript na camada de domínio.

## 2. Pacotes de Tokens

### 2.1 Entidade `Package`

Representa um pacote de tokens oferecido por uma determinada plataforma/jogo.

Campos presentes:

- `id: string`  
  Identificador único do pacote no contexto do app.

- `name: string`  
  Nome descritivo do pacote (exemplo: `"Pacote 100 tokens"`).

- `platform: string`  
  Plataforma ou jogo ao qual o pacote pertence (exemplo: `"Jogo X"`, `"Loja Y"`).

- `currency: string`  
  Código da moeda (exemplo: `"BRL"`).

- `price: number`  
  Preço do pacote na moeda especificada.

- `tokens: number`  
  Quantidade de tokens incluídos no pacote.

- `includesCardFees: boolean`  
  Indica se o preço já inclui tarifas de cartão/bandeira.

- `availablePaymentMethods: PaymentMethod[]`  
  Lista de métodos de pagamento em que esse pacote está disponível (exemplo: `[ "PIX", "CREDIT_CARD" ]`).

### 2.2 Tipo `PaymentMethod`

Enumerado simples para métodos de pagamento:

- `PIX`  
- `CREDIT_CARD`  
- (Outros podem ser adicionados futuramente)


### 2.3 Exemplo de Pacote em JSON

```json
{
  "id": "pkg-100-tokens",
  "name": "Pacote 100 tokens",
  "platform": "JogoX",
  "currency": "BRL",
  "price": 50.0,
  "tokens": 100,
  "includesCardFees": true,
  "availablePaymentMethods": ["PIX", "CREDIT_CARD"]
}
```


## 3. Entradas de Cálculo

### 3.1 Entidade `CalculationInput`

Representa os dados de entrada necessários para realizar um cálculo de combinação de pacotes.

Campos presentes:

- `requestedAmountTokens: number`  
  Quantidade de tokens desejada pelo usuário.

- `paymentMethod: PaymentMethod`  
  Método de pagamento selecionado.

- `platform?: string`  
  Plataforma/jogo escolhido (caso existam múltiplos conjuntos de pacotes).

- `extraFeePercentage?: number`  
  Taxa adicional opcional em percentual (ex.: taxa extra de cartão), aplicada sobre o valor total calculado.

### 3.2 Exemplo de entrada de cálculo em JSON

```json
{
  "requestedAmountTokens": 400,
  "paymentMethod": "CREDIT_CARD",
  "platform": "JogoX",
  "extraFeePercentage": 2.5
}
```


## 4. Resultado de Cálculo

### 4.1 Entidade `CalculationResult`

Representa a saída da lógica de cálculo para uma combinação de pacotes.

- `totalTokens: number`  
  Quantidade total de tokens obtida a partir da combinação.

- `totalPrice: number`  
  Valor total em moeda, incluindo eventuais taxas adicionais.

- `costPerToken: number`  
  Custo médio por token (`totalPrice / totalTokens`).

- `requestedTokens: number`  
  Quantidade de tokens originalmente solicitada pelo usuário.

- `excessTokens: number`  
  Tokens a mais em relação ao solicitado (`totalTokens - requestedTokens`, nunca negativo).

- `paymentMethod: PaymentMethod`  
  Método de pagamento considerado no cálculo.

- `platform: string`  
  Plataforma/jogo.

- `packages: SelectedPackage[]`  
  Lista de pacotes utilizados na combinação.

### 4.2 Entidade `SelectedPackage`

Representa um pacote específico dentro de uma combinação.

- `packageId: string`  
  Referência ao `id` de `Package`.

- `name: string`  
  Nome do pacote (denormalizado para facilitar apresentação).

- `tokens: number`  
  Quantidade de tokens desse pacote.

- `price: number`  
  Preço desse pacote.

- `quantity: number`  
  Quantidade de vezes que este pacote foi usado na combinação.

### 4.3 Exemplo de resultado em JSON

```json
{
  "totalTokens": 420,
  "totalPrice": 200.0,
  "costPerToken": 0.4762,
  "requestedTokens": 400,
  "excessTokens": 20,
  "paymentMethod": "CREDIT_CARD",
  "platform": "JogoX",
  "packages": [
    {
      "packageId": "pkg-105-tokens",
      "name": "Pacote 105 tokens",
      "tokens": 105,
      "price": 50.0,
      "quantity": 4
    }
  ]
}
```


## 5. Histórico de Cálculos

### 5.1 Entidade `HistoryEntry`

Registra um cálculo já realizado, armazenado no dispositivo do usuário.

- `id: string`  
  Identificador único da entrada de histórico (por exemplo, UUID).

- `calculationInput: CalculationInput`  
  Entrada usada no cálculo.

- `calculationResult: CalculationResult`  
  Resultado gerado.

- `createdAt: string`  
  Data e hora do cálculo em formato ISO (ex.: `"2025-12-23T15:30:00.000Z"`).

### 5.2 Estrutura de Armazenamento em `localStorage`

Sugestão de chave:

- Chave: `"tokenCalculator:history"`  
  Valor: JSON com um array de `HistoryEntry`.

Exemplo:

```json
{
  "id": "hist-001",
  "calculationInput": {
    "requestedTokens": 400,
    "paymentMethod": "CREDIT_CARD",
    "platform": "JogoX",
    "extraFeePercentage": 0
  },
  "calculationResult": {
    "totalTokens": 420,
    "totalPrice": 200.0,
    "costPerToken": 0.4762,
    "requestedTokens": 400,
    "excessTokens": 20,
    "paymentMethod": "CREDIT_CARD",
    "platform": "JogoX",
    "packages": [
      {
        "packageId": "pkg-105-tokens",
        "name": "Pacote 105 tokens",
        "tokens": 105,
        "price": 50.0,
        "quantity": 4
      }
    ]
  },
  "createdAt": "2025-12-23T15:30:00.000Z"
}
```


## 6. Possíveis Extensões Futuras

- **Perfil de usuário**:
  - Entidade `UserProfile` com preferências (plataformas favoritas, métodos de pagamento padrão, limites de gasto).

- **Configuração remota de pacotes**:
  - Entidade `RemotePackageSource` representando origem de dados (API oficial, marketplace, etc.).

- **Gamificação e metas**:
  - Entidades para metas de economia, uso recorrente, badges, etc.

Essas extensões não são necessárias na primeira versão, mas o modelo atual é projetado para permitir a evolução com impacto limitado.