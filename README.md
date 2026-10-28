# PowerShell Security Log Analyzer

**PowerShell Scripting | Windows Event Logs | Security Monitoring | Cybersecurity Lab**

## Project Overview

In this lab, I wrote and executed a PowerShell script to analyze an export of Windows Security audit events. The script imports a CSV file, filters **Audit Success** and **Audit Failure** records, counts results, and identifies the most frequently occurring Event ID. This helped me practice security-focused scripting and quickly summarize large sets of Windows events rather than inspecting each record manually.

The project is based on my original university PowerShell scripting lab. The screenshots below document my original workflow. For safe public reproduction, I provide a **sanitized copy of the real exported event records** with account names, machine names, logon IDs, and event-message details removed.

## Tools and Skills

- **Windows PowerShell** — scripting, parameters, variables, conditional statements, functions, and pipelines
- **Windows Event Viewer** — security audit events exported to a delimited log file
- **CSV processing** — `Import-Csv`, `Where-Object`, `Group-Object`, `Sort-Object`, and `Select-Object`
- **Security analysis** — categorizing audit results and recognizing significant Windows Event IDs

## 1. Exploring PowerShell Commands

I started by using `Get-Command` to discover PowerShell commands available on my system. This introduced me to cmdlets and functions before I built the audit script.

```powershell
Get-Command
Get-Help Import-Csv
Get-Command *-Object
```

<img width="785" height="914" alt="image" src="https://github.com/user-attachments/assets/7f0bcec5-0d40-4222-9a59-22b44e3386bd" />


## 2. Inspecting the Windows Security Log Export

The original data was exported from Windows Security auditing. It included `Keywords`, `Date and Time`, `Source`, `Event ID`, and `Task Category`, together with event descriptions and user/system details. The public CSV intentionally retains only the five non-identifying columns.

<img width="1107" height="663" alt="image" src="https://github.com/user-attachments/assets/db503e57-1504-4b83-8321-d2778d7a3b84" />


### Event IDs worth distinguishing

| Event ID | Windows event meaning |
| --- | --- |
| **5379** | Credential Manager credentials were read; **not** a successful logon event |
| **4624** | An account was successfully logged on |
| **4625** | An account failed to log on (not present in this uploaded sample) |
| **4672** | Special privileges assigned to a new logon |
| **4799** | Local security-enabled group membership was enumerated |

An **Audit Success** label indicates the audited operation completed successfully; it does **not** mean each row is a successful login or establish that a system was intrusion-free.

## 3. Writing the PowerShell Script

I wrote a parameterized script, [`scripts/AuditLog.ps1`](scripts/AuditLog.ps1), to read a CSV file and produce an audit summary.

The script:

1. Checks whether the provided CSV file exists.
2. Imports the records with `Import-Csv`.
3. Filters events by `Audit Success` and `Audit Failure` using `Where-Object`.
4. Counts each category.
5. Uses `Group-Object`, `Sort-Object`, and `Select-Object` to identify the most frequent Event ID in each category.
6. Displays the results in the PowerShell console.

### Run the script

Clone the repository and, from the repository directory, execute:

```powershell
.\scripts\AuditLog.ps1 -Path '.\sample-data\SanitizedAuditLog.csv'
```

No administrative privileges or access to live Windows logs is required to analyze the included CSV. Run the script in a PowerShell session where local script execution is permitted; do not disable security protections system-wide just to run it.

## 4. Original Execution and Results

The following screenshot documents the output from my initial PowerShell lab, where I used a local CSV export in `C:\Logs`.

<img width="869" height="506" alt="image" src="https://github.com/user-attachments/assets/b7dc49b8-79a5-4070-99e0-f7d027c6c732" />


### Uploaded data vs. historical screenshot

The historical screenshot reports **33,292 entries, all Audit Success, zero Audit Failure, and Event ID 5379 as the most frequent**. The separately uploaded `AuditLog.txt` file has **33,266 recognizable exported event records**, including **33,266 Audit Success** and **0 Audit Failure** records. The difference of **26 records** means the screenshot and uploaded file should not be treated as identical copies of a dataset.

For the sanitized public CSV extracted from the uploaded file, the leading Event ID is **5379** (27,747 occurrences). This data can be analyzed again using the included script.

The script summarizes **audit classifications**, not actual successful/failed login counts. Investigating logins specifically would require filtering IDs **4624** and **4625**.

## 5. What I Learned

Through this project, I practiced writing a reusable `.ps1` script, working with CSV data as PowerShell objects, filtering and grouping audit records, and translating large volumes of raw events into a readable summary. I also learned why **Event ID context matters**: a repeated Credential Manager access event is different from a successful account logon.

## Future Improvements

- Add dedicated 4624/4625 logon reporting and suspicious sign-in heuristics.
- Support `.evtx` files and live collection with `Get-WinEvent` where permitted.
- Export structured summaries to CSV or JSON and add unit tests.
- Add optional thresholds for alerting on notable activity.

## Privacy and Dataset Notes

`sample-data/SanitizedAuditLog.csv` is generated from the user's actual uploaded October 2025 Windows Security export, but **it is not a byte-for-byte copy**: identifying account/host data and multiline message contents have been removed. Do **not** publish `AuditLog.txt` or other unredacted exports. The spreadsheet screenshot has been cropped to avoid revealing the original message column containing user/machine identifiers.

This project demonstrates local security-log analysis and does not claim an intrusion was detected or ruled out.


# Author

## Lucas D. Beames
Cyber Security Scholar | Lifelong learner | SOC Analyst Novice
