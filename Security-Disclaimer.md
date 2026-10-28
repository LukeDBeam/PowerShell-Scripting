# Never commit unredacted Windows Security logs or local exports
AuditLog.txt
*SecurityLog.csv
!sample-data/SanitizedAuditLog.csv
*.evtx
*.log
private-data/
