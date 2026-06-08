---
title: "Building a Financial Data Platform for Student Government"
excerpt: "Designed a financial data platform that transformed decades of ASUC budget, registration, and funding records into analytics-ready datasets through deterministic document processing, cloud ETL pipelines, and developer-facing tooling."
collection: portfolio
---

## Overview

UC Berkeley is home to the Associated Students of the University of California (ASUC), the oldest and largest student government in the US. The ASUC is also one of the few student governments in the US to hold a large degree of financial autonomy and as a result of this the ASUC processes large amounts of operational and financial data, but much of that information remains trapped inside spreadsheets, meeting records, funding requests, and administrative documents. During my time as Chief Financial Officer of the ASUC, I found that answering even basic financial questions often required manually searching through years of records spread across multiple systems.

To address this problem, I developed a family of tools that evolved through three stages:

1. **ASUCExplore** — a collection of fundamental document processing and financial analysis functions and tools. Developed May 2025.
2. **OCFO-ETL** — a cloud-based ETL platform that automated extraction, transformation, and loading of ASUC financial records that implements the functions and tools from ASUCExplore. Developed June/July 2025
3. **ASFIN** — a lightweight desktop application that packages the same ASUCExplore functionalities into an dev toolkit for the CFO's in-house engineering teams. Developed September 2025.

Together, these projects transformed institutional knowledge stored in documents into a structured and reproducible financial data platform.

---

## The Problem

The ASUC maintains decades of publibly available records covering:

* Annual budgets
* Student organization registration records
* Funding committee decisions
* Contingency funding requests
* Meeting agendas
* Financial reports
* Legal contracts

Although these records are highly structured, they exist across multiple formats including Google Docs, spreadsheets, PDFs, and manually maintained reports. 

Information is excessively denormalized into different documents and tables based on date, committee and other kinds of metadata making even simple questions like "how much did student organizations spend over the last two years" necessitate hours of manual document searching without automated tooling. On top of that naming conventions often change over time, organizations merge or disappear, and some digital records are simply incomplete. Record styling changes dramatically and and information integrity deminishes once we start looking at COVID and pre-COVID era records. Thus the historical records are rarely stored in a consistent format, adding to the difficulty for finding and aggregating information without some sort of computer assistance. 

This outlines the initial theoretic properties that our financial record processing system must have. First it must be able to handle large quantities (years worth) of multimodal records documents containing very different kinds of highly structured information (bylaws, ordered meeting notes, official budget spreadsheets). Second because of the nature of the data being financial information, the system must be highly accurate. On a data systems level this means designing an Available and Partition Tolerant (AP) system whereas on the user level it just means that the output of our record processing system must either be true to the record or throw an error when it's not sure about a document. 

To tackle this I designed a library of processing functions for different types of documents then designed processing pipelines that adaptively read and processed different records accordingly. 

One final engineering note I wanted to make was the absence of AI, there is both a timing and engineering reason. First the engineering reason why no AI is used in the processing functions is because consistency and accuracy is suport important with a financial record processing system and I couldn't risk hallucinations or catastriophic forgetfulness. Second the timing reason is simply that when this project first began development AI could build rudimentary functions but not whole systems reliably. While the ASUCExplore wouldn't exist until May 2025 and wouldn't be on github until June 2025, the actual functions were developed as early as November of 2024. Thus part of while AI was not used to develop these tools initially was simply because the AI tools were not consistent enough back then. 

---

## Phase I: ASUCExplore

The first version of the project began as a collection of Python utilities for processing ASUC financial records.

The goal was simple: automate repetitive analysis that previously required manual review of committee agendas and funding documents.

Key functionality included:

* Contingency funding extraction from meeting records
* Student organization registration analysis
* Historical funding trend analysis
* Organization matching and normalization
* Budget reporting workflows

Many of these tools relied on carefully designed regular expressions and document parsers that converted semi-structured records into machine-readable datasets. The idea is to take structured but consistent records and either turn them into neat CSVs that a sklearn model could learn on or log an error asking for human intervention. 

While effective, ASUCExplore remained a collection of scripts and utilities rather than a unified system.

---

## Phase II: OCFO-ETL

As the scope of analysis grew, maintaining individual scripts became increasingly difficult. I redesigned the project as a production-oriented ETL platform capable of automatically ingesting, processing, and warehousing financial data.

### Architecture

The system follows a traditional Extract-Transform-Load workflow:

**Extract**

* Google Drive API integration
* Automated document discovery
* Historical record retrieval

**Transform**

* Dataset-specific processing pipelines
* Regular expression extraction
* Fuzzy entity resolution
* Schema standardization
* Validation checks

**Load**

* Google BigQuery warehouse
* Automated dataset publishing
* Versioned archival storage

### Engineering Highlights

#### Processor Abstraction

Different datasets required different extraction logic while sharing a common workflow.

To solve this, I implemented a processor architecture where each dataset type exposes a common interface while encapsulating dataset-specific transformations internally. This reduced code duplication and simplified future maintenance.

#### Entity Resolution

Student organizations frequently change names over time, creating challenges when linking records across datasets.

Using fuzzy string matching and custom validation thresholds, the platform automatically resolves historical naming inconsistencies and creates unified organizational records.

#### Observability and Logging

Because the platform runs automatically, debugging visibility became critical.

I implemented structured logging, execution tracking, and dataset-level reporting to ensure failures could be diagnosed quickly without manual intervention.

#### Cloud-Native Deployment

The platform was containerized using Docker and connected to GitHub Actions for automated execution.

This enabled repeatable deployments, automated updates, and scheduled processing without requiring manual intervention from future finance teams.

---

## Impact

The resulting platform consolidated financial information from multiple independent systems into a centralized warehouse capable of supporting reporting, budgeting, and policy analysis. It turned simple questions that used to take CFO staff hours to answer like "has this club applied to funding this past year" or "how much have we given to cultural clubs so far" into something that takes less than 5 seconds across two function calls. 

The project demonstrated that many organizational data challenges are not fundamentally machine learning problems. Instead, they require careful data engineering and software engineering put together reliable and scaleable pipelines for creating transparent and useable datasets. 

---

## Phase III: ASFIN

While OCFO-ETL successfully automated financial processing in the cloud, deployment and maintenance still required technical infrastructure. There was also more of a push to have a dev toolkit version of ASUCExplore and OCFO-ETL more geared towards analysis and exploration of specific datasets. 

Thus I coded up the initial implementation of  ASFIN and led a team of 3-4 engineers to develop a lightweight desktop application built on the same core processing framework.

The goal was to shift from a cloud-operated system to a user-friendly application that non-technical CFO staff could use to read out figures quickly and technical data scientists could use to analyze data in a more surgical end-to-end manner from raw CSV to cleaned dataset in the warehouse. 

This represents the next step in the project's evolution:

**Research Scripts → Production ETL Platform → User-Facing Financial Software**

---

## Technologies

* Python
* Google Drive API
* Google BigQuery
* Docker
* GitHub Actions
* Pandas
* RapidFuzz
* Regular Expressions
* Cloud ETL Architecture
* Data Warehousing
* Financial Data Engineering

