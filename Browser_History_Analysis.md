# Practical No. 7
## Aim: Analyze Visited Websites from the Browser History Database and Log Them for Monitoring and Security Auditing

---

## What is Browser History Analysis?
Browser history analysis is the process of extracting and examining websites visited by a user from their browser's history database. It is used in digital forensics, security auditing, and parental monitoring.

---

## Step 1: Download Browser History Examiner

1. Go to the official website: https://www.foxtonforensics.com/browser-history-examiner/
2. Download **BrowserHistoryExaminer_v1.23.0** (or latest version).
3. The downloaded folder contains:
   - `bhe` (Windows Installer, ~27,222 KB)
   - `Readme` (Text Document, 1 KB)
   - `Setup` (Application, 553 KB)
   - `WebView2 Runtime` (File folder)

---

## Step 2: Install the Application

1. Double-click on `Setup` application.
2. The **Browser History Examiner Setup Wizard** opens.
3. Click **Next** to proceed.
4. **License Agreement** screen appears.
   - Read the EULA (End-User Licence Agreement).
   - Select **I Agree** radio button.
   - Click **Next**.
5. **Select Installation Folder** screen:
   - Default path: `C:\Program Files (x86)\Foxton Software\Browser History Examiner\`
   - Click **Browse** to change location if needed.
   - Select **Just me** (or Everyone) under install options.
   - Click **Next**.
6. **Confirm Installation** screen: Click **Next** to start installation.
7. Wait for installation to complete.
8. **Installation Complete** screen appears: "Browser History Examiner has been successfully installed."
9. Click **Close** to exit the installer.

Note: The installer may prompt you to install **.NET Framework** updates — allow it.

---

## Step 3: Launch the Application

1. Open **Browser History Examiner** from the Start Menu or Desktop.
2. A dialog appears: **"Browser History Examiner is currently running in trial mode."**
3. Click **Continue Trial** to proceed without purchasing a license.

---

## Step 4: Capture Browser History

The main window shows two buttons:
- **Capture History** — Extract history from this computer or a remote one.
- **Load History** — Load previously saved history files.

Click **Capture History**.

### Capture History Options:
1. **Capture history from this computer** — (Select this option)
2. **Capture history from logical drive** — For forensic images
3. **Capture history from a remote computer** — Requires Computer Name/IP, Admin Username, Admin Password

Select **Capture history from this computer** and click **Next**.

---

## Step 5: Configure Capture Settings

On the next screen, configure:

- **User Profile**: Select the user profile (e.g., `Sairaj`)
- **Browsers** (check all that apply):
  - ✅ Chrome
  - ✅ Edge
  - ☐ Firefox (optional)
  - ✅ Internet Explorer & Edge Legacy
- **Data** (check):
  - ✅ History
  - ✅ Cache
  - ☐ Deleted History (from Volume Shadow Copies) — requires more permissions
- **Destination**: Type `D:\keylogger` (or any folder you created)
  - Click the `...` button to browse and select the destination folder.
- Click **Capture** button.

---

## Step 6: View the Captured History

After capture completes, the results are displayed. The display shows visited websites grouped by domain:

Example output:
```
▶ ilovepdf.com   8 visits
▶ google.com     7 visits
▶ notegpt.io     3 visits
▶ kali.org       2 visits
▶ virtualbox.org 2 visits
```

At the bottom of the screen, a bar chart shows **Website Visit Count** over a date range (e.g., 03-02-2026 to 21-02-2026).

### Two views available:
- **Summary View (Top 1000)** — Shows domains and visit counts.
- **Detailed View** — Shows individual URLs with timestamp, title, and visit count.

---

## Step 7: Save the Summary Report

1. Click the **Save Summary Report** button (top right).
2. A report is generated and saved to the destination folder.
3. The report is an HTML file showing:
   - **Web Browser History Report**
   - Created: (date and time)
   - Created using: Browser History Examiner v1.23
   - **Website Visits (by domain)** table:
     | Domain | Visit Count |
     |---|---|
     | ilovepdf.com | 8 |
     | google.com | 7 |
     | notegpt.io | 3 |
     | kali.org | 2 |
     | virtualbox.org | 2 |
   - Bar chart showing visit counts by date.

---

## Files Created in D:\keylogger\

After both Practical 6 and 7 are done, the keylogger folder contains:
- `keylogger_log.txt` — keyboard keystroke log (1 KB)
- `log_mouse.txt` — mouse movement log (90 KB)
- Browser History Examiner output files (history report)

---

## Alternative: Python Script to Extract Chrome History

Chrome stores history in an SQLite database. Here is a Python script to extract it:

```python
import sqlite3
import os
import shutil
from datetime import datetime, timedelta

# Path to Chrome history database
history_path = os.path.expanduser(
    r"~\AppData\Local\Google\Chrome\User Data\Default\History"
)

# Make a copy (Chrome locks the file when open)
copy_path = "chrome_history_copy"
shutil.copy2(history_path, copy_path)

# Connect to the database
conn = sqlite3.connect(copy_path)
cursor = conn.cursor()

# Query visited URLs
cursor.execute("SELECT url, title, visit_count, last_visit_time FROM urls ORDER BY last_visit_time DESC")

print(f"{'URL':<60} {'Title':<30} {'Visits':<10} {'Last Visit'}")
print("-" * 120)

for row in cursor.fetchall():
    url, title, visit_count, last_visit_time = row
    # Convert Chrome timestamp to readable date (microseconds since 1601-01-01)
    if last_visit_time:
        epoch_start = datetime(1601, 1, 1)
        visit_time = epoch_start + timedelta(microseconds=last_visit_time)
        visit_time_str = visit_time.strftime('%Y-%m-%d %H:%M:%S')
    else:
        visit_time_str = "N/A"
    
    print(f"{str(url)[:60]:<60} {str(title)[:30]:<30} {visit_count:<10} {visit_time_str}")

conn.close()
os.remove(copy_path)
```

---

## Security and Audit Use Cases

| Use Case | Description |
|---|---|
| **Employee Monitoring** | IT departments check if employees visit non-work sites |
| **Parental Control** | Parents monitor children's browsing activity |
| **Digital Forensics** | Investigators recover browsing history as evidence |
| **Insider Threat Detection** | Detect if employees visit data leak or competitor sites |
| **Compliance Auditing** | Ensure users comply with acceptable use policies |

---

## Key Points for Exam
- Browser History Examiner is a tool for extracting browsing history from Chrome, Edge, Firefox, IE.
- It can capture history from local computer, logical drive (forensic images), or remote computers.
- History is stored in SQLite databases in the browser's profile folder.
- The tool generates a report showing visited domains and visit counts with date charts.
- This is used in security auditing, digital forensics, and monitoring.
