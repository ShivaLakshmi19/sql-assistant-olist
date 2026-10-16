# AI SQL Assistant for E-Commerce Analytics (Olist)

By Shiva Lakshmi Padala

## What it does
Ask a business question in plain English. An AI model writes the SQL, a safety check validates it, and the query runs on the Olist e-commerce database. The result and the SQL are both shown, so a person can check the logic.

## Why this project
Using AI for analysis only helps if the answers can be trusted. This project focuses on the parts that make AI-written SQL usable: business context, safety, error recovery, and measured accuracy.

## How it works
1. **Business context in the prompt:** the table structure plus plain definitions (delivered orders only, what "late" means, revenue excludes freight, a customer is customer_unique_id).
2. **Safety guardrails (3 layers):** the AI is told to refuse changes, a validator allows only a single read-only SELECT on approved tables, and the database is opened in read-only mode.
3. **Self-repair:** if a query fails, the error is sent back to the AI for one retry.
4. **Measured accuracy:** 12 test questions with known answers from my earlier SQL analysis of the same data, scored automatically.

## Results
- 12 of 12 test questions answered correctly, with 0 retries needed (model: openai/gpt-oss-120b on Groq's free tier)
- All 3 harmful test requests (delete rows, drop a table, read a table outside the approved list) were stopped
- **A failure I found and fixed:** before adding a rule, the AI summed product revenue across all orders (including undelivered ones) and returned health_beauty revenue of 1,258,681 instead of the correct 1,233,132. The ranking looked right, but the numbers were wrong. I added a business rule requiring a join to the delivered-orders table, and the stricter test (which compares revenue values, not just names) confirmed the fix.

## Limitations
- 12 questions is a small test set, and matching is lenient (extra columns or different row order are allowed)
- AI output can vary between runs, and a correct-looking answer can still use wrong logic, so a person should check the SQL for important decisions
- Free-tier models, names and limits change over time

## Tools
Python, SQLite, Groq API (free tier), pandas, Google Colab

## How to run
1. Download the Olist dataset from Kaggle (keep the zip)
2. Get a free API key at console.groq.com and save it as a Colab secret named GROQ_API_KEY
3. Open the notebook in Google Colab, upload the zip, and run the cells one at a time
