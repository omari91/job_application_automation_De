# Job Aggregator Automation 🚀

Automate your job search in Germany with ease! The **Job Aggregator Automation DE** project collects job listings from multiple sources, scores them based on your target roles, deduplicates entries, stores them in a central tracker, and prepares them for your Application Assistant workflow.  

Perfect for engineers and IT professionals applying for roles like Grid Connection Engineer, Power Systems Engineer, DevOps Engineer, or remote tech jobs in Germany. 🖥️

---

## ✨ Features

- **RSS Feed Scraping 📡**: Pulls jobs from sources like We Work Remotely and cybersecurity feeds.  
- **Keyword Scoring 📊**: Ranks jobs based on keywords (e.g., CIO, remote, cybersecurity).  
- **Deduplication 🧹**: Ensures no duplicate jobs by link or ID.  
- **Batch Processing ⚙️**: Handles feeds in batches (default: 3 per batch) to avoid timeouts.  
- **Styled Email Digest 📧**: Sends a beautiful HTML email with up to 10 top-scoring jobs.  
- **Custom Google Sheets Menu 🖱️**: Run the automation directly from your Sheet.  
- **Detailed Logging 📜**: Tracks execution in a Logs tab for easy debugging.  
- **Comprehensive Tests ✅**: Includes `testInput`, `testAppending`, and `testMain` for reliability.  

---

## 📂 Project Structure

job-aggregator-automation-de/
├── utils.gs           # Utility functions (logging, date formatting, IDs)
├── input.gs           # Reads Config and Sources tabs
├── scraper.gs         # Scrapes RSS feeds
├── output.gs          # Processes and scores jobs
├── appending.gs       # Appends jobs to Jobs tab
├── email.gs           # Sends email digests
├── menu.gs            # Custom Google Sheets menu
├── main.gs            # Orchestrates automation (entry functions)
├── appsscript.json    # Apps Script manifest (scopes/runtime)
├── emailTemplate.html # HTML email template for the digest
├── index.html         # Optional UI mock (not used by Apps Script runtime)
├── README.md          # Project documentation
├── .gitignore         # Excludes sensitive files

---

## 🛠️ Setup Instructions

Follow these steps to get the Job Aggregator running in your Google Sheets and Apps Script environment.

### 1. Create a Google Sheet 📋
- Create a new Google Sheet or use an existing one.  
- Add these tabs with headers:

**Config tab:**
| Key              | Value                        |
|------------------|------------------------------|
| emailRecipient   | your.email@gmail.com         |
| topN             | 10                           |
| minScore         | 1                            |
| dedupeBy         | link                         |
| dateFormat       | yyyy-MM-dd                   |
| coverLetterDocId |                              |
| keywords         | Grid Connection,Power Systems,DevOps,German,Berlin |

**Sources tab:**
| SourceName       | URL                                              | Notes              | Active |
|------------------|--------------------------------------------------|--------------------|--------|
| We Work Remotely | https://weworkremotely.com/remote-jobs.rss       | Remote tech jobs   | TRUE   |
| Remotive         | https://remotive.com/feed                        | Curated remote jobs| TRUE   |

**Jobs tab:**
| ID | DateFound | Title | Company | Location | Link | Source | Snippet | Score | MatchedKeywords | Status | DateNotified |

**Logs tab:**
| ID | Module | Type | Message |

- **Freeze Headers**: `View > Freeze > 1 row` for each tab.  
- **Checkboxes**: In `Sources` tab, set Active column to checkboxes (`Insert > Checkbox`).  
- Copy the **SHEET_ID** from the Sheet URL (e.g., `1fwfkdyReTRCMywq6VmQT7XN5KVu0DWcBOpY0XOxgoD0`).  

### 2. Set Up Google Apps Script ⚙️
- Open your Sheet → `Extensions > Apps Script`.  
- Copy all `.gs` and `.html` files from this repository to the Apps Script project.  
- Ensure file names match exactly (e.g., `input.gs`, not `Input.gs`).  
- Save the project (`Ctrl+S` / `Cmd+S`).  

**Optional (recommended): Deploy with `clasp`**
1. Install `clasp`: `npm i -g @google/clasp`  
2. Login: `clasp login`  
3. Create a new Apps Script project (in this folder): `clasp create --type sheets`  
4. Push code: `clasp push`  
5. Note: `.clasp.json` contains your `scriptId` and should stay uncommitted (this repo ignores it).

### 3. Configure Script Properties 🔑
- In Apps Script → `Project Settings > Script Properties`.  
- Add: `SHEET_ID = <your_spreadsheet_id>`.  
- Save.  
  
Tip: This repo includes a helper `setupScriptProperties()` in `main.gs`, but it intentionally uses a placeholder (`YOUR_SHEET_ID_HERE`) so you don’t accidentally commit a real Sheet ID.

### 4. Authorize Permissions 🔐
- Run any function (e.g., `testInput`) in Apps Script.  
- Grant permissions for Google Sheets, Script Properties, Gmail, and Google Docs.  

### 5. Populate Sources Tab 🌐
- Add RSS feeds to the `Sources` tab.  
- Set Active = `TRUE` for feeds to scrape.  

### 6. Set Up Triggers ⏰
- Run `setupTriggers()` in Apps Script to create a daily trigger for `runJobSearch` at 8 AM.  
- Verify in Triggers: One daily trigger and temporary batch triggers.  

---

## 🚀 Usage

### Run Manually
1. Open your Google Sheet.  
2. Click **Job Aggregator > Run Job Search**.  
3. The script will:  
   - Scrape RSS feeds in batches (3 per batch).  
   - Append unique jobs to the Jobs tab (deduped by link).  
   - Send a styled email to `emailRecipient` with top jobs.  

### Run Tests
Run in Apps Script:  
- `testInput()` → Validates Config and Sources reading.  
- `testAppending()` → Tests job appending and deduplication.  
- `testMain()` → Simulates the full automation flow.  

Check results in `Logs` tab or `View > Logs`.  

### Automatic Runs
- Daily trigger runs `runJobSearch` at 8 AM.  

### View Results
- **Jobs Tab**: New jobs appended (no duplicates).  
- **Email**: Styled HTML with up to `topN` jobs (`Score >= minScore`, `Status=New`).  
- **Logs Tab**: Detailed execution logs (e.g., `["INFO-001", "main", "Automation Started", "..."]`).  

---

## 🌟 Sample Sources

Add these to your `Sources` tab:  

| SourceName              | URL                                                                 | Notes                | Active |
|--------------------------|---------------------------------------------------------------------|----------------------|--------|
| We Work Remotely         | https://weworkremotely.com/remote-jobs.rss                         | Remote tech jobs     | TRUE   |
| Remotive                 | https://remotive.com/feed                                          | Curated remote jobs  | TRUE   |
| Cybersecurity Jobs RSS   | https://rss.indeed.com/rss?q=cybersecurity&l=remote                 | Cybersecurity roles  | TRUE   |
| Cloud Operations Jobs RSS| https://rss.indeed.com/rss?q=cloud+operations&l=remote             | Cloud-focused roles  | TRUE   |
| LinkedIn – Grid Connection Engineer DE | https://your-linkedin-grid-engineer-de.rss    | Grid Connection Engineer roles in Germany (RSS) | TRUE   |
| LinkedIn – Power Systems Engineer DE   | https://your-linkedin-power-systems-de.rss    | Power / grid engineer roles in Germany (RSS)      | TRUE   |
| LinkedIn – HVDC / Grid Berlin          | https://your-linkedin-hvdc-berlin.rss         | HVDC / grid roles around Berlin (RSS)             | TRUE   |
| LinkedIn – Engineering Germany         | https://your-linkedin-engineering-de.rss      | Broad engineering roles in Germany (filtered RSS) | FALSE  |

*Note: For LinkedIn sources, perform a search on LinkedIn (e.g., "Grid Connection Engineer" in "Germany"), copy the URL, and use a tool like RSS.app to convert it to an RSS feed.*

*See `sample_sources.csv` for more examples. Disable slow feeds by setting Active = FALSE.*  


---

## ⚙️ Configuration

### Config Tab
| Key              | Description                | Example                    |
|------------------|----------------------------|----------------------------|
| emailRecipient   | Email for digests          | your.email@gmail.com       |
| topN             | Max jobs in email          | 10                         |
| minScore         | Min score for emailed jobs | 1                          |
| dedupeBy         | Deduplication field        | link                       |
| dateFormat       | Date format                | yyyy-MM-dd                 |
| coverLetterDocId | Google Doc ID for cover    |                            |
| keywords         | Scoring keywords           | CIO,CTO,cybersecurity,remote |

### Sources Tab
- Add valid RSS feed URLs.  
- Use checkboxes in the Active column to enable/disable feeds.  

---

## 🛡️ Troubleshooting

- **"input module is not defined"**  
  - Ensure `input.gs` exists and is named exactly.  
  - Rename to `a_input.gs` if it loads after `main.gs`.  
  - Run `testInput()` to verify.  

- **Timeouts**  
  - Reduce `BATCH_SIZE` in `main.gs` (e.g., 2).  
  - Disable slow feeds in Sources tab.  

- **No Email Sent**  
  - Verify `emailRecipient` in Config tab.  
  - Check Gmail permissions in Apps Script.  
  - Review Logs tab for errors.  

- **Logs**  
  - Check Logs tab for detailed execution logs (e.g., `["ERROR-001", "main", ...]`).  

---

## 🔄 Integration with Job Application Assistant

For engineers, think of these two repositories as separate stages in your **Job Search CI/CD Pipeline**:

1.  **Stage 1: Automated Discovery (This Repo)**  
    *   **Role:** The "Backend" / "Scraper".  
    *   **Function:** Runs a cron job (via Google Apps Script triggers) to ingest data from RSS feeds, normalize it, and push it to a database (Google Sheets).  
    *   **Use Case:** High-volume lead generation. Automate the "hunting" so you don't waste CPU cycles refreshing job boards.

2.  **Stage 2: Manual Processing (omari91/Application-Assistant)**  
    *   **Role:** The "Frontend" / "Client".  
    *   **Function:** A local-first Single Page Application (SPA) that helps you tailor your resume and track state (Applied, Interviewing, Offer).  
    *   **Use Case:** High-value conversion. Once you find a signal in the noise from Stage 1, use this tool to craft the perfect payload (your application).

| Feature | Job Aggregator (This Repo) | Application Assistant (omari91) |
| :--- | :--- | :--- |
| **Architecture** | Serverless Script (Apps Script) | Client-side SPA (HTML/JS) |
| **Data Store** | Cloud (Google Sheets) | Local (Browser `localStorage`) |
| **Primary Op** | `CRON` -> `FETCH` -> `FILTER` | `USER_INPUT` -> `GENERATE_DOCS` |

### 🛠 Recommended Engineering Workflow

1.  **Automate Ingestion**: Configure this repo to scrape sources like "We Work Remotely" or "Hacker News Jobs". Let it run in the background.
2.  **Triage**: Review the daily email digest or Google Sheet. Identify the top 5% of interesting roles.
3.  **Context Switch**: Open your hosted instance of **Job Application Assistant**.
4.  **Execute**: Copy the job details from the Sheet into the Assistant. Use its templating engine to generate a tailored cover letter and interview prep notes.
5.  **Monitor**: Use the Assistant's dashboard to track the lifecycle of your applications.

---

---

## 💻 Developer Setup (Terminal)

Prefer the command line? You can manage and execute this project using **clasp** (Command Line Apps Script Projects).

## 🤝 Contributing

We welcome contributions!  

1. Fork the repository.  
2. Create a branch: `git checkout -b feature/your-feature`.  
3. Commit changes: `git commit -m "Add your feature"`.  
4. Push: `git push origin feature/your-feature`.  
5. Open a Pull Request.  

*Please include tests and update this README for new features.*  

---

## 📜 License
This project is licensed under the **MIT License**.  

---

## 📬 Contact
For questions or suggestions, reach out to **wachirakungu@gmail.com** or open an issue on GitHub.  

Happy job hunting! 🎉
