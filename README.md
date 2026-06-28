# Cloud-Connected Gym Analytics Engine: Mobile Logging Application to PostgreSQL

## Executive Summary
In personal strength training and tracking, session logging is traditionally plagued by friction. Clunky generic spreadsheets, manual notebook entry, and complex commercial applications that require constant connectivity fail in fast-paced, real-world gym environments. This project resolves that operational friction by deploying a custom, mobile-first web application engineered specifically for rapid, high-impact touch logging right next to the weight rack.

The edge application functions as a live data collection tool, transmitting transactional workout records directly to a hosted PostgreSQL cloud data warehouse upon session completion. This data pipeline completely bypasses manual data entry, feeding embedded SQL reporting layers and analytical dashboards that convert raw weight actions into structured performance diagnostics, true personal records, and week-on-week progression trends.

---

## System Architecture and Data Pipeline
The end-to-end data pipeline is structured into three decoupled layers to ensure scalability and data integrity:

* **Data Collection Layer:** A lightweight HTML5 web application optimized for mobile viewports operating at the edge. It handles active volume calculations, movement inputs, and user states locally, utilizing browser `localStorage` cache fallbacks to ensure zero data loss during temporary network drops in concrete gym facilities.
* **Cloud Storage Layer:** A hosted Supabase PostgreSQL instance acting as the central data warehouse. Data transfer is handled via an authenticated REST API gateway, where database integrity is protected by structural constraints, optimized foreign key indexing, and strict token validation.
* **Analytics and Visualization Layer:** Embedded server-side SQL views that execute complex data transformations, filtering processed performance metrics directly into user-facing leaderboard components.

---

## Edge Application Logic and Live Calculators
The core challenge of tracking a live workout is managing data entry seamlessly in the background so the user can log sets instantly between recovery periods. The application handles this by running an embedded JavaScript engine that actively computes input metrics before packaging the database payload.

### 1. Live-Calculating One-Rep Max (1RM) Badge
To provide instantaneous performance feedback without cluttering the mobile viewport, the mathematical Epley formula was integrated directly into the exercise card loops. When a user enters metrics into the active input fields of any exercise row, a dynamic input listener captures the values and executes the strength calculation:

$$\text{1RM} = \text{Weight} \times \left(1 + \frac{\text{Reps}}{30}\right)$$

This script computes the theoretical maximum load instantly on the client side, displaying a live status badge directly next to the exercise name to guide training intensity before the set is even committed to memory.

### 2. Transactional History Stack and the Undo Engine
To prevent environmental gym distractions or accidental screen taps from corrupting data integrity, the application utilizes a strict Last-In, First-Out (LIFO) memory map cache. Before a local session log array is updated by the log button, the system serializes the active athlete name, exercise identity key, precise weight, and rep count into a history cache. If an incorrect weight or typo is submitted, tapping the undo command steps backward, pops the last entry off the local array, and instantly recalibrates the session volume display to maintain perfect local data synchronization.

### 3. Authenticated Batch-Loading Synchronization Pipeline
Upon concluding a workout, clicking the connection engine orchestrates a structured relational transaction to export local data up to the cloud. The script validates the token state, matches the selected profile name against the backend user table, and creates a parent workout session entry. It then extracts the logged sets for that specific athlete, structures them into a batch payload array, and executes a single network insert into the child log table, clearing the local memory cache only after a successful server handshake.

---

## Relational Database Configuration
The cloud storage layer is powered by a PostgreSQL database hosted on Supabase. Below are the production-grade SQL scripts used to establish the data warehouse architecture, enforce relational constraints, optimize performance, and build analytical data layers.

### 1. Analytical Server-Side Views
To optimize query performance, we configured pre-aggregated server-side views. This allows thin reporting clients or front-end components to pull structured analytical metrics straight from the server without wasting client-side processing power recalculating raw rows.

#### The Leaderboard View (`v_athlete_personal_records`)
Calculates the true peak strength metrics for each individual movement across all logged history, separating records by athlete:
