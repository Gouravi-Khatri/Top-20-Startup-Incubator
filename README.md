# Module 1: Indian Startup Incubators Data Collection & Historical Archive Pipeline

## 📌 Overview
This repository contains the automated web scraping, domain resolution, and historical archive extraction pipeline for analyzing the **Top 20 Startup Incubators in India**. 

To overcome common Web Data Mining challenges such as dynamic domain migrations (e.g., NASSCOM transitioning from `10000startups.com` to `nasscom.in`), website restructurings, DNS timeouts, and archive rate limiting this module utilizes an **Automated Internet Archive (Wayback Machine CDX API) Scraper** configured with custom retry logic and exponential backoffs.

---

## 🎯 Objectives
1. **Identify & Categorize Incubators:** Map leading Indian startup incubators using two complementary frameworks:
   - **Methodology A (Scale & Reach):** Cohort size, regional coverage, and incubation footprint.
   - **Methodology B (Impact & Capital):** Total venture funding raised by alumni, IP density, and unicorn yield.
2. **Live Scraping Engine:** Collect current active HTML DOM snapshots from live homepages and "About Us" portals.
3. **Historical CDX Extraction:** Retrieve monthly historical snapshots across 57 time checkpoints spanning **January 2022 to September 2026**.
4. **Fault Tolerance & Resilience:** Prevent connection aborts (`ConnectTimeoutError`, 429 rate limits) via exponential retry backoffs.

---

## 🛠️ Data Pipeline Architecture

```text
 ┌──────────────────────────┐
 │ Incubator Target Dataset │
 │  (20 Active Incubators)  │
 └────────────┬─────────────┘
              │
      ┌───────┴────────┐
      ▼                ▼
┌──────────────┐   ┌──────────────────────────────┐
│ Live Scraper │   │ Wayback CDX API Query Engine │
│ (Current DOM)│   │ (Jan 2022 – Sep 2026 Target) │
└──────┬───────┘   └──────────────┬───────────────┘
       │                          │
       │           ┌──────────────┴──────────────┐
       │           │ Retry & Exponential Backoff │
       │           │  (Handles 429s & Timeouts)   │
       │           └──────────────┬──────────────┘
       │                          │
       ▼                          ▼
┌─────────────────────────────────────────────────┐
│ Local HTML File Persistence & Metadata Storage  │
│          (./scraped_incubators_html/)           │
└─────────────────────────────────────────────────┘

scraped_incubators_html/
│
├── NASSCOM_10000_Startups/
│   ├── live_homepage.html
│   ├── live_about.html
│   ├── archive_202201_homepage.html
│   ├── archive_202201_about.html
│   └── ... (Monthly Snapshots 2022-2026)
│
├── Kerala_Startup_Mission/
├── T_Hub/
├── IIT_Madras_IITMIC/
├── IIT_Bombay_SINE/
└── ... (All 20 Target Incubators)
