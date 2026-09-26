# Data & Product Analytics Projects

A collection of end-to-end analytics projects. Each one starts from a business question and goes through data validation, exploratory analysis, and modelling or forecasting. Each ends with clear, actionable recommendations.

## Projects

| Project | Summary | Skills and tools |
|---|---|---|
| [**Forecasting the Impact of a New Feature on Reminders Usage**](feature-adoption-forecast/) | Analyses 30-day activity of 1.5M messaging-app users, sizes the target audience, and forecasts incremental adoption (≈ 65.6K new users) for a "reminder from a received message" feature. | Product analytics · Feature adoption · Forecasting · Segmentation · Python, pandas, matplotlib |
| [**LLM Product Description Generator & LLM-as-a-Judge**](llm-product-description-eval/) | Generates e-commerce copy with open-weight LLMs, raises the human-rated pass rate from 73% to 93% at the same cost through prompt engineering and post-processing, and builds a Pydantic-structured LLM judge with 80% agreement with human raters. | GenAI evaluation · Prompt engineering · LLM-as-a-judge · Llama, Qwen, Gemma · OpenAI SDK, Pydantic |

## Repository layout

Each project is self-contained in its own folder, with:

- `README.md`: business context, key findings, methodology and limitations
- `notebooks/`: the full, reproducible analysis
- `data/`: the dataset, or instructions for getting it
- `requirements.txt`: the Python dependencies

