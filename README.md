# Token Calculator

A lightweight web-based calculator to estimate AI API costs based on token usage and model selection.

## Overview

Token Calculator is a simple, single-page HTML application that helps estimate the cost of AI API calls across 13 different models. Input the number of input and output tokens along with your chosen model, and get an instant cost breakdown.

**Stack:** HTML + CSS + JavaScript (no frameworks, no build step)  
**Execution:** Simply open `index.html` in a modern web browser

## Features

- **Quick Cost Estimation**: Calculate input, output, and total costs in real-time
- **13 Pre-configured Models**: 3 custom models + 10 models with current OpenRouter pricing
- **Model Comparison** (planned): Compare costs across multiple models side-by-side
- **Responsive Design**: Works on desktop and mobile devices
- **Clear Error Handling**: Friendly validation messages for invalid inputs
- **Accessible**: Basic ARIA support for improved screen reader compatibility

## Available Models

### Custom Models
- **FastMini** — 0.10 / 0.40 €
- **BalancedPro** — 1.00 / 3.00 €
- **ReasonMax** — 5.00 / 15.00 €

### OpenRouter Models (Current Pricing)
- Ling-2.6-flash (inclusionAI) — 0.01 / 0.03 €
- Mistral Small 3.2 24B — 0.075 / 0.2 €
- Hermes 4 70B (Nous) — 0.13 / 0.4 €
- Qwen3 Coder Next — 0.11 / 0.8 €
- Hermes 3 70B Instruct (Nous) — 0.7 / 0.7 €
- GPT-4.1 Mini (OpenAI) — 0.4 / 1.6 €
- Nova 2 Lite (Amazon) — 0.3 / 2.5 €
- GLM 5V Turbo (Z.ai) — 1.2 / 4 €
- Gemini 2.5 Pro (Google) — 1.25 / 10 €
- GPT-5.4 (OpenAI) — 2.5 / 15 €

*Pricing is per 1 million tokens. Input/Output prices listed as `inputPrice / outputPrice`.*

## How to Use

1. **Open the calculator**: Simply open `index.html` in your web browser
2. **Enter token counts**:
   - **Input tokens**: Number of tokens in your prompt
   - **Output tokens**: Expected or actual number of tokens in the response
3. **Select a model**: Choose from the available 13 models
4. **Calculate**: Click the "Calculate" button
5. **View results**: See the breakdown of input cost, output cost, and total cost in euros

## Input Validation

All numeric inputs must be:
- Positive numbers (greater than 0)
- Finite and valid
- Non-empty

Invalid inputs will display a clear error message without performing the calculation.

## Cost Calculation Formula

For any selected model:

```
inputCost = (inputTokens / 1,000,000) × inputPrice
outputCost = (outputTokens / 1,000,000) × outputPrice
totalCost = inputCost + outputCost
```

## Technical Architecture

### Main Components

#### HTML Structure
- Calculator form with input fields and model selector
- Result display area with ARIA live region for accessibility
- Comparison feature (planned) with model checkboxes

#### JavaScript API (`window.AppCalculator`)
- `MODELS`: Object containing all model definitions with pricing
- `formatEuro(value)`: Formats numbers as euro currency (6 decimal places)
- `calculateCost(inputTokens, outputTokens, modelKey)`: Computes cost breakdown
- `readPositiveNumber(value, fieldName)`: Validates and converts numeric input
- `handleSubmit(event)`: Orchestrates form submission and result rendering

#### Key Functions
- `calculateCost()` — Core calculation engine
- `showResult()` — Renders cost breakdown
- `showError()` — Displays validation errors
- `initCalculator()` — Initializes form listeners on page load

### Styling
- Responsive CSS with mobile-first approach
- Media queries for smaller screens
- Clear visual distinction for errors and results
- Professional color scheme with accent highlights for important information

## Development

### Extending Models

To add or modify models:

1. Update the `MODELS` object in `index.html`
2. Sync the `<select id="model-select">` options
3. Ensure consistent pricing format (per 1M tokens)

Example:
```javascript
MODELS: {
  'my-model': {
    name: 'My Model',
    inputPrice: 0.50,
    outputPrice: 1.50
  }
}
```

### Customizing Currency Format

Modify the `formatEuro()` function to change currency display (currently set to euros with 6 decimal places).

### Input Validation

All validation rules are centralized in `readPositiveNumber()`. Extend this function for new validation requirements.

## Roadmap

- [ ] **Model Comparison**: Compare 2+ models with the same token counts
- [ ] **Model Comparison UI**: Checkbox grid with side-by-side cost table
- [ ] **Highlight cheapest model**: Visual badge for best value
- [ ] Enhanced theming options
- [ ] Copy results to clipboard
- [ ] Export comparison as CSV

## Non-Functional Requirements

- **Simplicity**: Minimal dependencies, easy to understand and modify
- **Robustness**: Clear error handling and input validation
- **Accessibility**: ARIA labels and live regions for better screen reader support
- **Responsiveness**: Functional layout on all modern screen sizes
- **Performance**: Zero external API calls, instant calculations

## Out of Scope

- Real-time pricing updates from provider APIs
- User authentication or accounts
- Persistent storage or history
- Backend integration
- Full internationalization

## Browser Compatibility

Works with all modern browsers:
- Chrome/Chromium 90+
- Firefox 88+
- Safari 14+
- Edge 90+

## License

See repository for license information.

## Contributing

Contributions welcome! Please review `SPECIFICATION.md` for detailed technical requirements before making changes.

## Support

For issues, feature requests, or improvements, please create an issue in the repository.
