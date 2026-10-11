# Game Data Collector

> **Navigation / Repository Structure:**
> * 📊 [Architecture Diagram & Assets](./architecture/)
> * 📄 [Architecture Rationale](./docs/architecture-rationale.md)
> * 🗂️ [Data-Source Register](./docs/data-source-register.md)
> * 🔒 [Governance, Privacy & Access Matrix](./docs/governance-privacy-access.md)
> * 📈 [Analytical Questions](./analysis/analytical-questions.md)

# Capstone proposal content

## Topic and title

Project Title: "Game Data Collector"

Description: Big data architecture for collecting, processing and analyzing payment transactions and game events of a free to play (F2P) mobile game in order to optimize monetization and to monitor stability

## Problem Statement

The current situation: The studio is launching a mobile project on Android, but data on user purchases is delayed.

Affected stakeholders: Game designers, backend developers, marketers, finance department, and technical support.

Decision to improve: Implementation of a single data pipeline, as well as automation of daily financial reporting.

Measurable outcome: A 15% reduction in the outflow of players at early levels.

Guardrail (Risk measure): Strict compliance with privacy standards (GDPR), preventing leaks of sensitive player information.

## Scope

Population: All active mobile game players worldwide.

Geography: Global market (CIS, EU, LATAM).

Time horizon: 1 year historical data + continuous stream of real-time data.

Unit of analysis: One game event or one purchase transaction.

Inclusions: Payment webhooks of app stores.

Exclusions: Data from third-party advertising networks (stored in external partner accounts).

Expected users: Live-ops managers, data analysts, finance department.

## Dataset(s)

### Game Events

Source: Client logs in JSON format 

Access method: API gateway

Grain: One player action (click, death, level completion)

Important fields: timestamp and player_id

Format: JSON that parses into TIMESTAMPZ, BIGINT

Scale estimate: Real-time stream (up to 5,000 events/seconds during peak hours)

### In-App Purchases

Source: Webhooks from Google Play and the App Store in JSON format

Access method: API gateway

Grain: One successful or cancelled purchase transaction

Important fields: purchase_id, status

Format: UUID, TEXT ENUM

Scale estimate: Batch upload every hour as purchases are made.

### User Profiles

Source: PostgreSQL relational database

Access: SQL methods in backend

Grain: One row per registered user

Important fields: user_id

Format: BIGINT

Scale estimate: Daily Incremental Backup

### ---

License/Permission: the data is modeled, license is educational, there are no restrictions on use.

Sensitivity/Privacy: IP addresses of the players, email addresses (when linking the account), payment tokens of the device. They require masking and pseudonymization at the stage of getting into the repository.

Known limitations: Peak loads during major game updates or esports events

## Analytical questions

### Operational question (Freshness: 5 minutes): 

What are the current indicators of customer crashes and network delays by region over the past 15 minutes?

### Financial question (Freshness: once a day): 

What is the total gross revenue and average receipt for the segments of paying players over the past day?

### Product question (Historical analysis): 

At what specific campaign level is the largest churn rate among players from the EU region over the last quarter?

## Preliminary Architecture

--- Using Storage and Compute Separation based on cloud object storage (AWS S3) 
--- Organization of data zones according to the medallion architecture (Bronze -> Silver -> Gold)
--- A message broker (Kafka)
--- Streaming engine (Apache Flink/Spark Streaming) to process the telemetry stream
--- Distributed SQL engine for analytical queries

# Dataset evidence and safety

### Proof of feasibility: 

The repository uses a mini-sample data structure (JSON schemas and non-sensitive logs) to demonstrate the functionality of the scripts.

### Load assessment: 

The production load is estimated at 50 GB of raw logs per day (the training sample has been reduced to megabytes for repository security).

### Security: 

According to the security rules, to the repository are NOT uploaded: real personal user data, cloud access tokens, database connection strings, API keys. All secrets are stored in environment variables (.env.example), and in .gitignore added rules for blocking temporary files.