# Especificação do Projeto — Calculadora de Custo de IA

## 1. Objetivo

Disponibilizar uma calculadora web simples para estimar o custo de uma chamada de IA com base em:

- tokens de input
- tokens de output
- modelo selecionado

O resultado deve mostrar custo de input, custo de output e custo total.

## 2. Escopo atual

O projeto atual é uma aplicação estática de uma única página:

- **Arquivo único:** `index.html`
- **Stack:** HTML + CSS + JavaScript (sem framework, sem build)
- **Execução:** abrir o `index.html` num navegador moderno

Não há backend, persistência de dados, autenticação ou chamadas de API externas.

## 3. Interface funcional

### 3.1 Entradas

1. **Tokens de input** (`#input-tokens`)
2. **Tokens de output** (`#output-tokens`)
3. **Modelo** (`#model-select`)

Modelos disponíveis no estado atual:

- `fastmini` (FastMini)
- `balancedpro` (BalancedPro)
- `reasonmax` (ReasonMax)

### 3.2 Ação principal

- **Botão:** `Calcular`
- **Evento:** submit do formulário `#calculator-form`

### 3.3 Saídas

A área `#result` deve apresentar:

1. Modelo escolhido
2. Custo de input
3. Custo de output
4. Custo total

Formato monetário atual: `toFixed(6) + ' €'`.

## 4. Regras de negócio

### 4.1 Tabela de preços

Os preços são extraídos da API do OpenRouter (openrouter.ai/api/v1/models) e representam valores por **1 milhão de tokens**:

- `inputPrice` — custo por 1M tokens de input (prompt)
- `outputPrice` — custo por 1M tokens de output (completion)

Os 12 modelos disponíveis incluem uma seleção diversa de provedores:
- **Modelos económicos:** inclusionAI Ling-2.6-flash, Qwen3 30B, Meta Llama 3.3
- **Modelos intermediários:** OpenAI GPT-4o-mini, ReMM SLERP, NVIDIA Nemotron
- **Modelos premium:** Magnum v4, OpenAI GPT-5.4, OpenAI GPT Audio

Os preços são obtidos da API pública do OpenRouter e refletem os preços reais praticados pelos provedores.

### 4.2 Validação

Para os campos de tokens:

- valor deve ser numérico finito
- valor deve ser maior que zero
- em caso de erro, lançar exceção com mensagem amigável

Para o modelo:

- chave deve existir em `MODELS`
- em caso de chave inválida, lançar exceção

### 4.3 Fórmula

Para um modelo com preços `inputPrice` e `outputPrice`:

- `inputCost = (inputTokens / 1_000_000) * inputPrice`
- `outputCost = (outputTokens / 1_000_000) * outputPrice`
- `totalCost = inputCost + outputCost`

## 5. Estrutura técnica atual

### 5.1 Funções principais (JavaScript)

- `formatEuro(value)`  
  Formata número para string monetária em euros (6 casas decimais).

- `readPositiveNumber(value, fieldName)`  
  Converte e valida número positivo; lança erro se inválido.

- `calculateCost(inputTokens, outputTokens, modelKey)`  
  Calcula custos e retorna objeto com:
  - `modelName`
  - `inputCost`
  - `outputCost`
  - `totalCost`

- `showError(message)`  
  Renderiza erro em `#result` com estilo de erro.

- `showResult(cost)`  
  Renderiza resultado detalhado em `#result`.

- `handleSubmit(event)`  
  Orquestra leitura de inputs, cálculo e tratamento de erro.

- `initCalculator()`  
  Liga o listener de submit no carregamento da página.

### 5.2 API exposta globalmente

`window.AppCalculator` expõe:

- `MODELS`
- `formatEuro`
- `calculateCost`

Objetivo: facilitar testes automatizados e inspeção didática.

## 6. Requisitos não funcionais

1. **Simplicidade:** manter baixo acoplamento e fácil leitura.
2. **Acessibilidade básica:** `aria-live="polite"` para atualização de resultado.
3. **Responsividade:** layout funcional em ecrãs menores (media query existente).
4. **Robustez de input:** feedback claro para entradas inválidas.

## 7. Diretrizes para futuras modificações

### 7.1 Alterar ou adicionar modelos

1. Atualizar o objeto `MODELS`.
2. Sincronizar opções do `<select id="model-select">`.
3. Garantir nomes e preços consistentes.

### 7.2 Alterar formatação monetária

Centralizar mudanças em `formatEuro()` para evitar inconsistências.

### 7.3 Evoluir validação

Qualquer nova regra deve ser concentrada em funções específicas de validação, evitando lógica duplicada em `handleSubmit`.

### 7.4 Evoluir arquitetura

Se o projeto crescer (múltiplos ficheiros):

1. Extrair lógica de cálculo para módulo próprio (`calculator.js`).
2. Separar renderização de UI da lógica de negócio.
3. Manter API pública estável (`AppCalculator`) ou documentar breaking changes.

## 8. Critérios de aceitação (estado atual)

1. Com valores válidos, cálculo e renderização de custos devem ocorrer sem erro.
2. Com qualquer valor inválido (não numérico, zero, negativo), deve ser exibida mensagem de erro.
3. Com modelo inválido, deve ser exibida mensagem de erro apropriada.
4. Custo total deve ser exatamente a soma de input + output.

## 10. Comparação de Modelos (nova funcionalidade)

### 10.1 Objetivo

Permitir que utilizadores comparem rapidamente múltiplos modelos usando os mesmos valores de input/output tokens.

### 10.2 Interface

#### Seleção de modelos

- Checkboxes para cada modelo disponível
- Grid responsivo com todos os modelos
- Seleção mínima: 2 modelos

#### Tabela de comparação

Apresenta para cada modelo:
- Nome do modelo
- Custo de input
- Custo de output
- Custo total

Dados ordenados por custo total crescente.

#### Destaque visual

O modelo com menor custo total é destacado com:
- Fundo com cor de acento leve
- Badge "Mais barato" junto do nome

### 10.3 Comportamento

1. Renderizar checkboxes de todos os modelos no carregamento
2. Ao selecionar 2+ modelos, calcular e exibir tabela
3. Ao alterar valores de tokens, atualizar comparação automaticamente
4. Se menos de 2 modelos selecionados, mostrar mensagem apropriada
5. Se tokens vazios, pedir ao utilizador para preencher

### 10.4 Validação

- Aplicar as mesmas regras de `readPositiveNumber()` ao calcular
- Mostrar erro se valores forem inválidos

## 9. Itens fora de escopo (atual)

- Integração com preços reais de provedores (preços são exemplares)
- Persistência de histórico de cálculos
- Internacionalização completa
- Backend/API
- Autenticação de utilizadores

