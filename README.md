# Energy Value Index Porto

![Python](https://img.shields.io/badge/Python-3.11-3776AB?style=flat-square&logo=python&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-Aiven-336791?style=flat-square&logo=postgresql&logoColor=white)
![Power BI](https://img.shields.io/badge/Power%20BI-Dashboard-F2C811?style=flat-square&logo=powerbi&logoColor=black)
![SDG 11](https://img.shields.io/badge/UN%20SDG-11%20Sustainable%20Cities-4C9F38?style=flat-square)
![Status](https://img.shields.io/badge/Status-In%20active%20development-orange?style=flat-square)

**An end-to-end Data Engineering project that turns fragmented public real-estate
data into a reliable, geographically-disaggregated Energy Value Index for the city
of Porto.**

The goal is not a dashboard, it is a **pipeline**: taking messy, unreliable public
data and making it clean, traceable and decision-ready, from ingestion to reporting.
The project investigates how the energy efficiency of a property relates to its
market value, and surfaces rehabilitation opportunities, aligned with
**UN SDG 11 (Sustainable Cities and Communities)**.

---

## From academic project to personal Data Engineering project

**UrbanEco** started as a group academic project in the Big Data postgraduate
programme at **ISEP (2026)**, where I led the data-engineering work. This
repository, **energy-value-index-porto**, is my **individual continuation**,
developed with the original team's permission ([origin repository, archived](https://github.com/aavelarbelo/urbaneco-analytics)).

The purpose of this continuation is deliberate: take the working prototype and
**re-engineer it into a production-minded Data Engineering pipeline** that I can
run, test, explain and defend, end to end. This README describes what the academic
phase achieved, what I am evolving now, and where it is heading.

---

## 1. Where UrbanEco reached (the academic foundation)

The data-engineering layer of the original project was my work, and it is the
foundation this repository builds on:

- **Ingestion** — a multi-page **Selenium** scraper of public SuperCasa listings,
  scaled to **~2,776 analysed properties across 7 civil parishes (*freguesias*)**
  of Porto, with source traceability and de-duplication.
- **Standardization** — cleaned prices (€), areas (m²), price per m² and boolean
  amenities, with raw and validated bases kept separate.
- **Storage** — a 24-variable schema on **PostgreSQL (Aiven Cloud)**.
- **Energy Value Index (EVI, v1)** — a composite 1–10 score, Energy Score (55%) +
  Price Score (45%); dataset median 6.7.
- **Green Index** — a per-parish score from parks, Metro, cycle lanes and Mobi.E
  points (Open Data CMP).
- **Initial dashboard** — a first Power BI model connected to the database.

## 2. What I am evolving now (this repository)

Turning that prototype into a real, defensible pipeline. The focus is engineering
quality, not adding technologies for show:

- A clear **Medallion ETL structure** (Raw → Silver → Gold) with explicit stages.
- **Data quality & validation** — validation rules and quarantine of invalid records.
- **Reliability** — error handling, logging, and **idempotent** runs (re-running
  without duplicating or corrupting data).
- **Automated tests** for the transformations and data-quality rules.
- **Docker / docker-compose** for a reproducible database and pipeline.
- Fixing known issues from the prototype (e.g. the `€/m²` aggregation: replacing a
  direct `SUM(€/m²)` with a calculated `€/m²_clean` column and an outlier filter,
  500–25,000 €/m²).
- **Power BI** finalised as four analytical pages (market profile, EVI ranking,
  reliability & data quality, incident investigation).
- **Documentation** — data dictionary, technical decision log, run instructions.

## 3. Architecture

```mermaid
flowchart LR
    A[Public sources] --> B[Selenium ingestion]
    B --> C[RAW layer\nCSV / PostgreSQL]
    C --> D[Standardization + Data Quality\nSilver]
    D --> E[EVI + Green Index\nGold]
    E --> F[Power BI dashboard]
```

## 4. Tech stack (honest)

**Core:** Python (Selenium, Pandas) · SQL · PostgreSQL (Aiven) · Power BI · Docker · Git
**Being added, with purpose:** pytest (tests) · GitHub Actions (CI)
**Deliberately out of scope:** Kafka, Airflow, Spark, Terraform, Kubernetes — this
project does not have the volume or complexity to justify them, and I would rather
demonstrate an end-to-end pipeline I fully understand than stack tools I cannot defend.

## 5. Roadmap

Legend: ✅ done (from the academic phase, being migrated) · 🚧 in progress · 🔲 planned

| Phase | Focus | Status |
|---|---|---|
| 0 | Ingestion (Selenium, multi-page) | ✅ |
| 1 | Standardization & cleaning | 🚧 |
| 2 | PostgreSQL schema & dimensional modeling | 🚧 |
| 3 | Medallion ETL (Raw → Silver → Gold) | 🔲 |
| 4 | Data quality, validation & quarantine | 🔲 |
| 5 | Tests, logging, error handling, idempotency | 🔲 |
| 6 | Docker / reproducible environment | 🔲 |
| 7 | EVI + Green Index (Gold), weight calibration | 🚧 |
| 8 | Power BI — 4 analytical pages | 🚧 |
| 9 | CI (GitHub Actions) | 🔲 |
| 10 | Documentation & presentation | 🚧 |

## 6. Data & method caveats

- Prices are **asking prices** (listings), not transaction prices, stated for
  methodological transparency.
- Scope is the **municipality of Porto** (7 freguesias).
- Where public data was incomplete, some attributes are **synthetic** and flagged
  as such in the data dictionary.
- The EVI is an **interpretive index**, not a causal claim between energy
  performance and market value.

## 7. Authorship & ethics

- **Origin:** group academic project, Big Data postgraduate, ISEP (2026).
- **This repository:** my individual continuation, developed with the team's
  permission, re-engineering the pipeline end to end.
- **My role in the original project:** I led the data engineering — the Selenium
  ingestion, standardization, the PostgreSQL/Aiven database, the EVI and Green
  Index, and the initial dashboard.
- **AI as a tool:** AI assistants are used to accelerate development. Every
  component in this repository is one I can run, explain, and modify.

## 8. Setup

> Being finalised alongside the Docker setup and the reproducible run script
> (see Roadmap). High-level flow: create a Python 3.11 environment, install
> `requirements.txt`, copy `.env.example` to `.env` and set the database
> credentials, start the database with `docker compose up -d`, run the ingestion
> and transformation steps, then open the Power BI report connected to the database.
