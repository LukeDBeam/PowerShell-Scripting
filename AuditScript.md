
param (
    [Parameter(Mandatory = $true)]
    [string]$Path
)

# === Load the CSV file ===
if (-not (Test-Path -Path $Path -PathType Leaf)) {
    Write-Host "File not found: $Path" -ForegroundColor Red
    exit
}

try {
    $logs = @(Import-Csv -Path $Path -ErrorAction Stop)
}
catch {
    Write-Host "Error importing CSV file: $_" -ForegroundColor Red
    exit
}

$totalEntries = $logs.Count

if ($totalEntries -eq 0) {
    Write-Host "No entries found in the log file." -ForegroundColor Yellow
    exit
}

# === Verify required CSV columns ===
$requiredColumns = @('Keywords', 'Event ID')

foreach ($column in $requiredColumns) {
    if ($column -notin $logs[0].PSObject.Properties.Name) {
        Write-Host "Missing required column: $column" -ForegroundColor Red
        exit
    }
}

# === Filter for Audit Success and Audit Failure events ===
$auditSuccess = @($logs | Where-Object { $_.Keywords -match 'Audit Success' })
$auditFailure = @($logs | Where-Object { $_.Keywords -match 'Audit Failure' })

# === Count events ===
$successCount = $auditSuccess.Count
$failureCount = $auditFailure.Count

# === Find most common Event ID in each category ===
$mostCommonSuccess = $auditSuccess |
    Group-Object 'Event ID' |
    Sort-Object Count -Descending |
    Select-Object -First 1

$mostCommonFailure = $auditFailure |
    Group-Object 'Event ID' |
    Sort-Object Count -Descending |
    Select-Object -First 1

# === Display results ===
Write-Host ""
Write-Host "===== WINDOWS EVENT LOG ANALYSIS =====" -ForegroundColor Cyan
Write-Host "Total log entries: $totalEntries"

Write-Host ""
Write-Host "Number of Audit Failures: $failureCount failures of $totalEntries entries"

if ($mostCommonFailure) {
    Write-Host "Most common Event ID: $($mostCommonFailure.Name)"
    Write-Host "Occurrences: $($mostCommonFailure.Count)"
}
else {
    Write-Host "Most common Event ID: None found"
}

Write-Host ""
Write-Host "Number of Audit Successes: $successCount successes of $totalEntries entries"

if ($mostCommonSuccess) {
    Write-Host "Most common Event ID: $($mostCommonSuccess.Name)"
    Write-Host "Occurrences: $($mostCommonSuccess.Count)"
}
else {
    Write-Host "Most common Event ID: None found"
}

Write-Host ""
Write-Host "Analysis complete." -ForegroundColor Green
