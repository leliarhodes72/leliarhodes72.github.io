---
title: "Reflection Sentiment Analyzer for Academic Support"
excerpt: "A custom Google Sheets plugin that flags tutor-written student reflections using sentiment analysis, keyword detection, and cloud NLP services."
collection: portfolio
---

This project was completed as part of my internship with the SALT Center (Strategic Alternative Learning Techniques Center) at the University of Arizona. I independently designed and implemented a Google Sheets plugin to support early intervention and reflection monitoring among academic support staff. The plugin automatically evaluates student reflections written by tutors and flags them as either “green flags” (positive) or “red flags” (potential concerns) using both sentiment analysis and custom heuristics.

## Project Overview

At the SALT Center, a nationally recognized program for students who learn differently, tutors write short reflections after each session with a student. These reflections help staff track student progress, identify emerging concerns, and ensure that tutoring strategies are effective and supportive. However, as the number of students and reflections grows, it becomes increasingly difficult to review them all in a timely and systematic way.

The aim of this project was to enhance the visibility of key patterns in these reflections — both positive indicators of student engagement, and concerning signals that may point to emotional distress, burnout, or disengagement. The Reflection Sentiment Analyzer was developed as a lightweight, non-invasive way to augment these existing processes. It provides an intelligent layer of sentiment and keyword-based flagging that allows staff to triage which reflections might deserve closer attention.

Rather than replace human oversight, the tool was designed to enhance it — surfacing the reflections that are most likely to require follow-up while also highlighting moments of success and growth. It respects the SALT Center’s strengths-based approach and integrates smoothly into their existing Google Sheets-based workflows.

## Tools and Technologies Used

The project used a combination of Google-native tools and external APIs to power the plugin:

- **Google Apps Script** — for building the Sheets add-on, handling UI, and communicating with APIs
- **Google Cloud Natural Language API** — to analyze the sentiment of reflections at the sentence and document level
- **JavaScript + HTML/CSS** — for building a dynamic, interactive sidebar that allows users to choose which columns to analyze
- **Regex pattern matching** — to flag reflections that contain predefined “concern words” such as “overwhelmed,” “panic,” or “burnout”
- **Keyword classification** — to detect positive or negative phrases and append emoji-style labels
- **Google `PropertiesService`** — for storing and accessing the API key securely within the script
- **Scheduled time-based triggers** — to allow automatic batch processing of reflections every 6 hours

By combining these tools, I was able to build a standalone plugin that is secure, scalable, and usable by non-technical staff members.

## Implementation Details

The system is designed to be modular and intuitive. Here’s how it works in practice:

### 1. **Sidebar-Based Column Selection**

Upon launching the plugin from the custom “Reflection Sentiment Analyzer” menu, the user is presented with a sidebar that displays all column headers in the active spreadsheet. The user can check one or more columns (e.g., "Reflection 1", "Session Notes") for analysis. The selection is saved using `ScriptProperties`, allowing the analysis script to persist settings across runs.

### 2. **Sentiment Analysis Using Google NLP**

For each selected column, the tool pulls student reflection text and sends it to the Google Cloud Natural Language API via a secure URLFetch request. The API returns:
- An overall **sentiment score** from –1.0 (very negative) to +1.0 (very positive)
- A **magnitude score** indicating emotional intensity
- Sentence-level sentiment breakdowns, which enable additional logic such as trend detection

### 3. **Custom Flagging Logic**

Once sentiment data is received, I apply custom heuristics to label the reflection:

- **Positive/Neutral/Negative sentiment tags** are assigned using thresholds (e.g., >0.6 = ✅ Positive, <–0.6 = ❗ Negative)
- If sentiment improves significantly between the first and last sentence, the reflection is marked with “↗️ Positive Progress”
- If known concern words appear (e.g., “panic,” “self-harm,” “numb”), the reflection receives a 🚨 Concern Flag
- If a reflection is unusually short (e.g., under 50–100 words), a ⚠️ is appended to indicate potential lack of detail or disengagement

These decisions are made row-by-row and result in the creation of a new “Sentiment Score” column next to each analyzed reflection.

### 4. **Keyword Highlighting**

To provide visual indicators of important emotional language, key terms are tagged inline with emoji. For example:
- “Motivated” → “Motivated✅”
- “Overwhelmed” → “Overwhelmed❗”
- “Self-harm” → “Self-harm🚨”

This allows staff to quickly scan the spreadsheet and identify phrases of interest without opening each individual row.

### 5. **Batch Processing and Tracking**

The script can run in two modes:
- **Manually**, via the custom menu (great for testing or on-demand checks)
- **Automatically**, every 6 hours (via time-driven trigger)

To avoid duplicate processing, a hidden column called `__Processed__` tracks which rows have already been analyzed. This makes the plugin safe to rerun multiple times on the same sheet.

---

## Results and Impact

The Reflection Sentiment Analyzer significantly reduces the time needed to review tutor reflections. What once required a manual, labor-intensive review of each row can now be triaged in minutes. The sentiment labels and emoji flags create an at-a-glance summary that highlights which students might need additional attention, and which reflections indicate positive growth or success.

During testing with sample tutor data, the plugin reliably flagged concerning patterns, such as:
- Students experiencing panic or academic burnout
- Session notes indicating disengagement or stress
- Sessions that were very short or vague

It also surfaced unexpectedly positive patterns, such as strong student initiative or well-organized progress, which might otherwise go unrecognized.

The tool fits seamlessly into existing workflows — it does not require any third-party installation, and because it's built using native Google tools, it runs directly inside Google Sheets where tutors are already working.

Feedback from staff emphasized that the tool could help identify struggling students sooner and support stronger, data-driven coaching and tutoring.

---

## Challenges and Future Improvements

This project offered plenty of opportunities to solve real-world deployment challenges:

### Deployment Challenges

Because I chose not to publish the add-on to the Google Workspace Marketplace, deployment to test users required extra care. Each user had to:
- Copy the script manually into their own Google Sheets
- Link their script to a Cloud Console project
- Enable the correct APIs and create their own API key
- Store that key using `PropertiesService`

To streamline this, I wrote two custom PDF guides:
- One for Google Cloud Console setup
- One for script installation and configuration

These guides walked users through each step, including enabling APIs, creating credentials, and inserting the API key safely. While this approach worked for my internship, it would not scale well without publishing the add-on for broader access.

### Logic Design Trade-offs

- Emoji tagging makes the reflection text less “clean,” which may annoy users who prefer untouched logs
- Hardcoded keyword lists work for the SALT Center, but would need customization for broader educational contexts
- The thresholds for concern (e.g., 100 words = too short) are heuristic and could be adjusted over time with more data

### Planned Enhancements

In a future version, I would consider:
- Adding a user-editable keyword manager
- Logging results to a separate summary sheet for weekly reporting
- Creating a one-click installer via Workspace Marketplace for organization-wide deployment
- Including a dashboard to display overall sentiment trends over time

---

## Learning Outcomes

This project helped me put nearly every skill from my MS in Human Language Technology program into real-world use. Specific outcomes include:

1. **End-to-end system development**: I built a complete solution — UI, backend logic, external API integration, and user deployment — from scratch.
2. **Secure API integration**: I used the Google Cloud NLP API in a secure and modular way, storing credentials with `PropertiesService`.
3. **Regex and linguistic heuristics**: I applied my linguistics training to write smart, transparent heuristics for tagging emotional and concern-based content.
4. **User-centered design**: I worked backward from the staff’s actual workflow to create something intuitive and minimally disruptive.
5. **Documentation and deployment support**: I wrote polished documentation for installation and testing, ensuring others could use the tool effectively.

The Reflection Sentiment Analyzer not only strengthened my technical skills but also helped me better understand how NLP tools can be used ethically and responsibly in education settings. It was deeply rewarding to know the tool could directly support staff in helping students thrive.

---

## See the Code

[View the code on Github](https://github.com/leliarhodes72/Tutoring-Analysis)
