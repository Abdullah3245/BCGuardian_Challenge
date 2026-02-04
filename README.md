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
- To help design a regex for identifying valid YouTube channel IDs <br>
<img width="623" height="103" alt="Screenshot 2026-02-03 at 10 49 05 PM" src="https://github.com/user-attachments/assets/cefed9f8-b8dc-4bfa-b943-bb49e65bb290" /> <br>
- To understand YouTube Data API usage and pagination patterns <br>
<img width="624" height="122" alt="Screenshot 2026-02-03 at 10 49 29 PM" src="https://github.com/user-attachments/assets/52f11640-64e4-4c87-9ef5-1e8407a60885" /> <br>
- To draft helper functions for fetching playlist items and video metadata <br>
<img width="595" height="154" alt="Screenshot 2026-02-03 at 10 49 41 PM" src="https://github.com/user-attachments/assets/39e49556-baa7-4856-bc12-568deedf6818" /> <br>
- Switched to OPENAI ChatGPT to keep the challenge confidential. Used ChatGPT to set up the Machine Learning Pipeline

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
