# Cloud-Connected Gym Analytics Engine

## Executive Summary
The Gym Tracker is a bespoke, web-based fitness logging application engineered to support high-accuracy data collection for two distinct athletes, "Ginge" and "Swift." The system solves the challenge of maintaining real-time training records by bridging local browser-based caching with a persistent, cloud-based PostgreSQL backend (Supabase). The platform is designed to prioritize performance during a live training session while ensuring all data is synchronized to a structured, relational database for long-term progress analysis.

## Technical Architecture
The system architecture was designed for robustness, scalability, and security, utilizing the following layers:

* **Frontend Layer**: A lightweight, responsive web interface built using HTML5, CSS3, and vanilla JavaScript. The UI is designed to be mobile-first for gym-floor accessibility, featuring a 64-exercise library categorized by muscle group for rapid selection.
* **Database Schema**: A sophisticated, normalized four-table structure hosted on Supabase:
    * **users**: Manages profile identity linked to authentication.
    * **exercises**: The source of truth for the library, standardized with numeric IDs (1–64) for mapping efficiency.
    * **workout_sessions**: Tracks unique training events per user.
    * **set_logs**: The granular log of every set, including weight, repetition, and order, linked to a specific session.
* **Synchronization & Reliability**: The application employs a local-first logic. User data is cached in `localStorage` throughout a workout to prevent data loss. On session completion, the application executes a two-step relational write, ensuring that set logs are correctly mapped to their corresponding parent session.
* **Security Layer**: Row Level Security (RLS) is configured to permit anonymous (guest) access to view historical data for portfolio/demonstration purposes, while restricting data modification (INSERT/UPDATE) strictly to authenticated users operating within their own account sessions.

## Analytics & Data Insights
The application provides a data-driven view of training progress through two primary mechanisms:

1. **Peak-Weight Progression**: Using Chart.js, the system renders the "top set" of each session, plotting the maximum weight moved per exercise over time, independent of repetition counts.
2. **Comparative Volume Metrics**: The application provides users with immediate visibility into their current session performance compared to their previous total volume, creating a continuous feedback loop for intensity regulation.

## Conclusion
This project successfully demonstrates the end-to-end development of a data-logging system. By aligning the frontend logic with a normalized database schema and implementing robust security policies, the Gym Tracker functions as a professional-grade tool suitable for individual training management and as a high-value technical project for your professional portfolio. This phase of development is now complete, providing a stable, scalable foundation for future enhancements.
