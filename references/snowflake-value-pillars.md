# Snowflake Value Pillars

Use this reference to map extracted project capabilities to the correct Snowflake platform pillar(s).
Match by scanning the **trigger keywords** column against the project's tech stack, features used, and outcomes described.
Select the 2–4 most relevant pillars. Each pillar becomes a capability chip on the slide.

---

## Pillar Mapping Table

| Pillar | Trigger Keywords | Business Outcome Statement | Chip Labels |
|--------|-----------------|---------------------------|-------------|
| **AI & ML (Cortex)** | classify, predict, generate, LLM, embedding, sentiment, summarize, extract, AI_COMPLETE, AI_CLASSIFY, AI_EXTRACT, AI_SUMMARIZE, Cortex, claude, llm, prompt, natural language, NLP | Automate decisions at scale with built-in AI on your data | `Cortex AI`, `AI_COMPLETE`, `AI_CLASSIFY`, `LLM Inference`, `Embeddings`, `Cortex Analyst` |
| **Data Engineering** | pipeline, ingest, transform, ETL, ELT, stream, CDC, dynamic table, task, Snowpipe, Kafka, dbt, staging, loading, batch, real-time | Unified, governed data foundation ready for analytics and AI | `Dynamic Tables`, `Snowpipe`, `Tasks`, `Streams`, `dbt`, `Data Pipeline` |
| **Analytics & BI** | dashboard, BI, query, report, insight, chart, visualization, Streamlit, Tableau, Power BI, Sigma, metrics, aggregate | Self-serve analytics for all users on a single platform | `Streamlit in Snowflake`, `Cortex Analyst`, `Semantic Views`, `BI Integration` |
| **Developer Platform** | Snowpark, Python, Java, Scala, UDF, stored procedure, SPCS, container, native app, app development, deploy, API | Deploy data products and apps without leaving Snowflake | `Snowpark`, `SPCS`, `Native App`, `UDFs`, `Python`, `Stored Procedures` |
| **Data Collaboration** | share, marketplace, listing, clean room, data product, external, cross-account, partner, consumer | Securely monetize and collaborate on data at scale | `Data Sharing`, `Marketplace`, `Data Clean Rooms`, `Listings` |
| **Governance & Security** | policy, mask, PII, compliance, GDPR, CCPA, row access, tag, classification, audit, lineage, data quality, governance, role, RBAC | Trusted data with fine-grained control and full auditability | `Horizon Governance`, `Data Masking`, `Row Access Policy`, `Data Classification`, `Lineage` |
| **CoCo / AI Dev Tools** | skill, CoCo, Cortex Code, agent, cortex agent, prompt, automation, workflow, AI-powered, intelligent assistant | Accelerate engineering workflows with AI-native development tools | `Cortex Code`, `CoCo Skills`, `Cortex Agents`, `AI-Native Dev` |
| **Iceberg & Open Formats** | iceberg, parquet, delta, open table format, external catalog, data lake, lake house | Unify data lake and warehouse on open standards | `Iceberg Tables`, `External Catalog`, `Open Formats` |

---

## Outcome Icons

Map each business outcome to one of these icons for the outcome panel:

| Theme | Icon |
|-------|------|
| Speed / Performance | ⚡ |
| Cost Savings | 💰 |
| Automation | 🤖 |
| Accuracy / Quality | 🎯 |
| Scale | 📈 |
| Security / Trust | 🔒 |
| Collaboration | 🤝 |
| Developer Productivity | 🛠️ |
| Time Savings | ⏱️ |
| Simplification | ✨ |

---

## Persona Labels (for slide badge)

Use the target persona of the solution to populate the `{{PERSONA}}` badge:

- `Data Engineer`
- `Data Scientist / ML`
- `Analytics Engineer`
- `Business Analyst`
- `Revenue Operations`
- `Sales Engineering`
- `Data Platform Team`
- `CIO / CTO`
- `Security & Governance`
- `App Developer`

---

## How to Use During Slide Generation

1. Read the extracted tech stack and feature list from the GitHub repo.
2. Scan trigger keywords for each pillar — mark any pillar where 2+ keywords match.
3. Pick the top 2–4 pillars ranked by relevance density.
4. For each selected pillar, pull 2–3 chip labels that most precisely match what the project actually uses.
5. Do NOT include pillars that have no matching evidence in the documentation.
6. Map the primary outcome of the project to the matching outcome icon from the table above.
