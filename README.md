# PowerShell Security Log Analyzer

**Cybersecurity home lab | PowerShell | Windows Event Logs | Security Monitoring**

## Overview

This project demonstrates how I used **PowerShell scripting** to automate the analysis of Windows Security event logs exported to CSV. Instead of manually reviewing thousands of records, my script filters audit events, counts successes and failures, and reports the most frequently occurring Event ID in each category.

The project grew out of my university PowerShell scripting lab. This repository presents the practical scripting workflow and a reproducible, privacy-safe example.

## Objectives

- Practice PowerShell fundamentals, including variables, parameters, conditional statements, functions, and pipelines.
- Import and process structured security data with `Import-Csv`.
- Filter **Audit Success** and **Audit Failure** records with `Where-Object`.
- Identify frequent event identifiers with `Group-Object`, `Sort-Object`, and `Select-Object`.
- Produce a simple console summary that can support security triage.

## Tools and skills

| Tool / concept | How I used it |
| --- | --- |
| Windows PowerShell / PowerShell | Ran an audit-analysis script (`.ps1`) |
| Windows Event Viewer | Source of exported security-event records in the original lab |
| CSV files | Portable input format for log analysis |
| `Import-Csv` | Converted CSV rows into PowerShell objects |
| `Where-Object` | Filtered by audit result |
| `Group-Object` | Counted occurrences by Event ID |
| `Sort-Object` / `Select-Object` | Selected the most frequent Event ID |
| `if`/`else`, functions, parameters | Added validation and reusable analysis logic |

## Repository contents

```text
PowerShell-Security-Log-Analyzer/
├── scripts/
│   └── AuditLog.ps1
├── sample-data/
│   └── SampleSecurityLog.csv
├── screenshots/
│   └── README.md
├── docs/
│   └── LAB_NOTES.md
├── .gitignore
└── README.md
```

## How to run

1. On Windows, open **PowerShell**.
2. Clone the repository and navigate to it:

   ```powershell
   git clone https://github.com/YOUR-USERNAME/PowerShell-Security-Log-Analyzer.git
   cd PowerShell-Security-Log-Analyzer
   ```

3. Run the included script against the included **synthetic** test data:

   ```powershell
   .\scripts\AuditLog.ps1 -Path .\sample-data\SampleSecurityLog.csv
   ```

4. To analyze your own CSV export, use a local path:

   ```powershell
   .\scripts\AuditLog.ps1 -Path 'C:\Logs\SecurityLog.csv'
   ```

> **Note:** If your environment blocks script execution, check `Get-ExecutionPolicy -List` and follow your organization's approved execution policy. Do not weaken managed security settings to run this lab.

### Expected output for the included sample CSV

```text
=== Windows Security Audit Log Analysis ===
Total entries: 6

Number of Audit Failures: 2 of 6 entries
Most common Event ID: 4625 (2 occurrences)

Number of Audit Successes: 4 of 6 entries
Most common Event ID: 5379 (2 occurrences)

Analysis complete.
```

This output is **illustrative** and comes from synthetic events, not from a live system.

## Methodology

1. **Explore commands:** I began by using `Get-Help` and `Get-Command`, then researched `Get-ChildItem`, `Select-String`, `Get-Content`, and `Get-Process`.
2. **Prepare a CSV:** I worked with security-audit data in a CSV format so that `Import-Csv` could treat each record as an object.
3. **Write the script:** I implemented filtering, counts, Event ID grouping, and readable console output.
4. **Debug and execute:** I corrected the file extension to `.ps1`, supplied a CSV path, and ran the script.
5. **Interpret results:** I used the audit category counts and top Event IDs as a starting point for understanding log activity.

## Results from my original class lab

The lab report described **zero Audit Failure records** and Audit Success events dominated by **Event ID 5379**. The original narrative and screenshot have different total counts (33,323 and 33,292, respectively); those figures are preserved as reported rather than presented as a single verified result. See [lab notes](docs/LAB_NOTES.md).

**Important interpretation:** Event ID **5379** indicates credentials were read from Windows Credential Manager; it is **not a successful user logon**. A successful logon is commonly indicated by Event ID **4624**, while a failed logon is commonly **4625**. Zero failed events in an export does not prove an absence of unauthorized access; conclusions depend on collection scope, audit configuration, and event context.

## Security relevance

Security analysts and system administrators can use scripts like this to establish baseline activity, rapidly summarize exported logs, and identify event types requiring closer investigation. Real incident response also requires event timestamps, hostnames, user accounts, correlation with other sources, and validation of logging coverage.

## Future improvements

- Read logs directly with `Get-WinEvent` (with appropriate authorization).
- Add date-range and Event ID filters.
- Export findings as CSV or JSON.
- Alert on configurable failed-logon thresholds.
- Correlate related events across hosts and accounts.
- Add automated tests for different CSV formats and missing values.

## Privacy and responsible use

The public repository contains **synthetic test data only**. Do not commit real Security logs containing account names, machine identifiers, domain details, or other sensitive information. Analyze only systems you own or are authorized to inspect.

## Reference

- [Microsoft Learn — What is PowerShell?](https://learn.microsoft.com/en-us/powershell/scripting/overview)
- [Microsoft Learn — Import-Csv](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/import-csv)
- [Microsoft Learn — Windows security auditing events](https://learn.microsoft.com/en-us/windows/security/threat-protection/auditing/basic-audit-logon-events)
