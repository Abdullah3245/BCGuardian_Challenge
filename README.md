# YouTube Copyright Risk Detection

In this challenge, I built an end-to-end system to identify and preserve YouTube videos
that may be susceptible to copyright violations, using only **free and official APIs**.
The goal is to **prioritize potentially risky content** without downloading videos or
transcripts, which would violate YouTube’s Terms of Service.

---

## Initial Research

- Downloading YouTube videos or transcripts is not permitted under YouTube’s Terms of Service.
- The YouTube Data API provides access to channel metadata and video metadata, including
  titles, descriptions, engagement metrics, and canonical URLs.
- These metadata signals can be preserved and analyzed without copying the underlying content.
- This metadata forms the basis of the entire pipeline.

---

## Pipeline (High Level)

The pipeline takes a list of YouTube channel inputs (handles or channel IDs), resolves them
using the YouTube Data API, enumerates all uploaded videos via each channel’s uploads playlist,
and builds a structured Pandas DataFrame containing channel-level and video-level metadata.
This metadata is then used to train a machine learning model that assigns a **risk probability**
to each video, allowing high-risk candidates to be ranked and exported for review.

---

## LLM Usage

LLMs were used as a development aid only:
- To help design a regex for identifying valid YouTube channel IDs
- To understand YouTube Data API usage and pagination patterns
- To draft helper functions for fetching playlist items and video metadata

All final code was reviewed, simplified, and optimized to remove unnecessary API calls.

---

## Machine Learning Approach

- A labeled dataset is constructed using:
  - **Official studio channels** (low-risk examples)
  - **Clip aggregation channels** (high-risk examples)
- Text features are created by combining:
  - Channel description
  - Video title
  - Video description
- Text is embedded using a transformer model (`all-MiniLM-L6-v2`)
- Structured features (views, likes, comments, license signals) are kept as separate numeric inputs
- A **logistic regression model** is trained to output a risk probability for each video

The model is used for **triage and prioritization**, not legal determination.

---

## Output and Preservation

- Each video receives a `clip_risk_proba` score
- Videos are ranked from highest to lowest risk
- Results are exported to an Excel file containing:
  - High-risk videos for review
  - Full scored dataset for auditability
- Preservation is achieved via metadata, timestamps, and canonical URLs

---

## Disclaimer

This system provides **risk-based prioritization only**.
Final determinations regarding copyright infringement must be made by qualified legal professionals.
