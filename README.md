# gpt-oss-20b Text-to-SQL (QLoRA)

Fine-tuning **gpt-oss-20b** to turn a plain-English business question plus a database schema into a SQL query. Training data comes from industry domains (manufacturing, automotive, energy, mining, and more), and every training row is validated by **executing its SQL in SQLite**. Built with Unsloth on a free Google Colab T4.

> **Status: experimental, work in progress.** A 30-step proof-of-pipeline run is finished. Evaluation against the base model is still in progress, so this repo makes no accuracy claims yet.

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Ameer-pasha/gpt-oss-text2sql-lora/blob/main/notebooks/gpt_oss_text2sql_finetune.ipynb)

- **Notebook:** `notebooks/gpt_oss_text2sql_finetune.ipynb`
- **Adapter / model card:** [ameer00712/gpt-oss-20b-text2sql-lora](https://huggingface.co/ameer00712/gpt-oss-20b-text2sql-lora) on Hugging Face (private until evaluation is finished)

## Why this project

Analysts and plant managers ask questions like "Which machine had the most downtime in 2024?" that need SQL over a private database. A small fine-tuned model can run on-prem so schemas and data never leave the company, can be trained to answer with SQL only, and can be measured objectively with execution accuracy.

## Pipeline

1. Load [`gretelai/synthetic_text_to_sql`](https://huggingface.co/datasets/gretelai/synthetic_text_to_sql) (100,000 train rows).
2. Keep industry domains, `analytics and reporting` tasks and short schemas: **9,138 rows**.
3. Run each row's SQL in SQLite and keep rows that execute and return results: **6,307 rows**.
4. Sample **1,500 rows** and format them with the GPT-OSS Harmony chat template (schema and question in, reasoning and SQL out).
5. QLoRA fine-tune with Unsloth and TRL, with loss on the final answer only.
6. Evaluate execution accuracy on held-out test rows, base model versus fine-tuned.
7. Publish the LoRA adapter to Hugging Face.

## Results

| Model | Valid SQL rate | Execution accuracy |
|---|---|---|
| gpt-oss-20b (base) | TBD | TBD |
| + LoRA adapter | TBD | TBD |

## Run it

1. Open the notebook in Colab and select a **T4 GPU** runtime.
2. Run the cells top to bottom.
3. To push to Hugging Face, store your token as a Colab **Secret** named `HF_TOKEN`. Never paste a token directly into a cell.

## Notes

- The notebook sets `UNSLOTH_COMPILE_DISABLE=1` and `TORCHDYNAMO_DISABLE=1` before importing Unsloth, because torch.compile failed on the Colab T4 setup used here.
- The training data is synthetic and generic. It has not been tested on real production schemas.
- SQL was validated in SQLite only, so MySQL or Postgres specific functions may not transfer.

## Credits and licenses

- Notebook adapted from Unsloth's gpt-oss-20B fine-tuning notebook (LGPL-3.0).
- Dataset: Gretel `synthetic_text_to_sql` (Apache-2.0).
- Base model: OpenAI gpt-oss-20b (Apache-2.0).
