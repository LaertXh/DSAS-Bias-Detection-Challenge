# DSAS Hiring Dataset – Normalized Tables

This dataset consists of several normalized tables that capture different stages and entities in a fictional hiring process. Use this README to understand the relationships and how to join tables for analysis.

## Table of Contents
1. [Overview](#overview)  
2. [Entity Tables](#entity-tables)  
   - [Jobs](#jobs)  
   - [Candidates](#candidates)  
   - [Recruiters](#recruiters)  
3. [Process Tables](#process-tables)  
   - [Applications](#applications)  
   - [AI Screening](#ai-screening)  
   - [Recruiter Screening](#recruiter-screening)  
   - [Tech Screening](#tech-screening)  
   - [Management Screening](#management-screening)  
4. [Relationships & Joining](#relationships--joining)  
5. [Usage Notes](#usage-notes)  

---

## Overview
The DSAS Hiring Dataset captures applicant journey data through a four-stage hiring pipeline:

1. **AI Screening**  
2. **Recruiter Screening**  
3. **Technical Interview Screening**  
4. **Management Decision**  

Additionally, we have reference tables for **jobs**, **candidates**, and **recruiters**. Analysts and data scientists can use these tables to explore potential biases and identify bottlenecks or disparities in the hiring process.

---

## Entity Tables

### Jobs
- **File**: `jobs.csv`
- **Columns**:  
  - `job_id` (PK)  
  - `job_title`  
  - `department`

This table is mostly reference data for what positions the company has open. It can be joined to the `applications` table by `job_id`.

---

### Candidates
- **File**: `candidates.csv`
- **Columns**:  
  - `candidate_id` (PK)  
  - `gender`  
  - `ethnicity`  
  - `education_level`  
  - `age`  
  - `university_rank`  
  - `years_experience`

Details about each candidate’s demographics, background, and experience. Use this table to analyze whether certain groups face disadvantages at different hiring stages.

---

### Recruiters
- **File**: `recruiters.csv`
- **Columns**:  
  - `recruiter_id` (PK)  
  - `gender`  
  - `ethnicity`  
  - `age`  
  - `education_level`  
  - `university_rank`

This table contains demographic info for recruiters. Some columns mirror those in the **Candidates** table (e.g., `gender`, `ethnicity`, `education_level`). **When joining to the `rec_screening` table, use aliases** for clarity—for instance, `c.gender` vs. `r.gender`.

---

## Process Tables

### Applications
- **File**: `applications.csv`
- **Columns**:  
  - `application_id` (PK)  
  - `candidate_id`  
  - `job_id`  
  - `referred` (indicator of referral source, e.g., 1 if referred, 0 otherwise)

Every time a candidate applies for a job, we have a unique row here. This is the starting point for linking candidates to positions.

---

### AI Screening
- **File**: `ai_screening.csv`
- **Columns**:  
  - `application_id` (FK to `applications.application_id`)  
  - `ai_screening` (value is `1` for pass, `0` for fail)

As en example, you can join this table to **`applications`** and then join the result to **`candidates`**. Then you can figure out potential bias.

---

### Recruiter Screening
- **File**: `rec_screening.csv`
- **Columns**:  
  - `application_id` (FK to `applications.application_id`)  
  - `recruiter_id` (FK to `recruiters.recruiter_id`)  
  - `rec_screening` (value is `1` for a pass, `0` for a fail)

Contains records of applications that went through the recruiter screening. The `recruiter_id` helps link which recruiter handled the screening. Missing applications means that they never passed the ai screening so they never got to this stage.

---

### Tech Screening
- **File**: `tech_screening.csv`
- **Columns**:  
  - `application_id` (FK to `applications.application_id`)  
  - `tech_interview_score` (numeric score from the technical interview)  
  - `tech_screening` (value is `1` for a pass, `0` for a fail, `1` for a pass)

This table tracks the technical interview. Notice that there is a score for each interview depending on the performance of the candidate. Missing applications means that they never passed the recruiter screening so they never got to this stage.

---

### Management Screening
- **File**: `management_screening.csv`
- **Columns**:  
  - `application_id` (FK to `applications.application_id`)  
  - `management_screening` (value is `1` for a pass, `0` for a fail)

Represents final decision by management.

---

## Relationships & Joining
Below is a basic relationship schema:

- **`applications`** (PK `application_id`)
  - Many-to-one with **`candidates`** via `candidate_id`
  - Many-to-one with **`jobs`** via `job_id`
- **`ai_screening`** links to **`applications`** on `application_id`
- **`rec_screening`** links to **`applications`** on `application_id`; also links to **`recruiters`** on `recruiter_id`
- **`tech_screening`** links to **`applications`** on `application_id`
- **`management_screening`** links to **`applications`** on `application_id`
- **`applications`** links to **`candidates`** on `candidate_id`
- **`applications`** links to **`jobs`** on `job_id` --> there is no bias related to jobs, its just here for aesthetic 


---

## Usage Notes
1. **Aliases for Clarity**  
   - `recruiters` and `candidates` share columns like `gender`, `ethnicity`, etc. Always alias them in your queries (`cand.gender`, `rec.gender`) to differentiate.
2. **Data is Fictional**  
   - The records are synthetic, intended to illustrate a hiring pipeline. Do not treat this data as real.
