# Value Realization Slide

> Generates a Snowflake-branded, print-ready HTML value realization slide from any GitHub repository in under 60 seconds.

A [Cortex Code Desktop](https://docs.snowflake.com/en/user-guide/cortex-code/cortex-code) skill for Michael Lemke's RevOps workflows at Snowflake.

## What It Does

Given a GitHub repository URL, the skill reads the README, docs, and examples to extract the business problem, solution approach, Snowflake capabilities used, and key outcomes. It generates a polished, Snowflake-branded HTML slide, saves it to `~/Downloads`, opens it automatically in the browser, and writes the structured data plus HTML to the `AFE.TEST.VALUE_REALIZATION_CATALOG` table in Snowflake — building a reusable, searchable library of value stories across the SE team's entire GitHub portfolio.

## Business Value

| Metric | Impact |
|--------|--------|
| Time saved | 2–3 hours per slide (reduced to ~60 seconds) |
| Systems integrated | GitHub, Snowflake (catalog table), local browser |
| Manual steps eliminated | README analysis, slide design, Snowflake branding application, catalog entry creation |

Turns every GitHub project into an exec-ready leave-behind asset instantly, and scales across the entire SE team's GitHub portfolio to build a reusable value story library that compounds over time.

## How It Works

1. User provides a GitHub repository URL.
2. Skill fetches the README, docs directory, and example files using `urllib`.
3. Snowflake Cortex AI extracts: business problem, solution approach, Snowflake capabilities used, key outcomes, and a one-line value headline.
4. Skill renders a Snowflake-branded HTML slide with the extracted content and saves it to `~/Downloads`.
5. Slide opens automatically in the browser; structured data + full HTML are written to `AFE.TEST.VALUE_REALIZATION_CATALOG` in Snowflake.

## Prerequisites

- Cortex Code Desktop (CoCo)
- Snowflake connection with write access to `AFE.TEST.VALUE_REALIZATION_CATALOG`
- Public GitHub repository (or a PAT for private repos)
- Snowflake Cortex AI access (for LLM extraction step)

## Usage

Invoke by typing the skill name in CoCo, or with natural language triggers listed in the skill description (e.g., "create a value realization slide from this repo", "generate slide from GitHub", "build a one-pager from this project").

## Technologies

- **Snowflake Cortex Code Desktop** — AI-powered IDE and skill runtime
- **Python (urllib)** — fetching README and docs from GitHub without external dependencies
- **Snowflake Cortex AI** — LLM-powered extraction of business problem, solution, capabilities, and outcomes
- **HTML/CSS** — Snowflake-branded, print-ready slide output
- **Snowflake SQL** — writing structured output and HTML to `AFE.TEST.VALUE_REALIZATION_CATALOG`

## Value Realization

This skill is part of a broader **AI-driven RevOps automation system** that eliminates manual work across the Snowflake Sales Engineering and RevOps teams. Every SE builds valuable GitHub projects, but turning those projects into customer-facing assets has always required hours of manual slide work — this skill closes that gap instantly, ensuring that the full value of the SE team's technical work is visible in every field conversation.
