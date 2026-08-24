# Investigation Notes

## Investigation Directory

The controlled directory was:

`C:\DeletedScriptRecoveryLab`

It was created at approximately:

`24-08-2026 06:43`

## Controlled Script

The test file was:

`C:\DeletedScriptRecoveryLab\recovery-test.ps1`

Its contents were:

```powershell
Write-Output "LAB60-Deleted Script Recovery Test"
$LabData="CONTROLLED-LAB60-DATA"
Write-Output "Processing controlled data"
Write-Output $LabData
```

The script contained no malicious functionality.

## File Metadata

The pre-deletion metadata was:

| Property | Value |
|---|---|
| Name | `recovery-test.ps1` |
| Length | 151 bytes |
| Creation Time | 24-08-2026 06:47:11 |
| Last Write Time | 24-08-2026 06:47:11 |
| Last Access Time | 24-08-2026 06:49:18 |

## SHA-256

The pre-deletion SHA-256 hash was:

`D129D500AE1C48895B967A94D9C97FBEBCD772019117090AC5327C5F272FE68`

This value was preserved before deletion.

## Sysmon Event ID 11

Sysmon Event ID 11 was observed for the script:

`C:\DeletedScriptRecoveryLab\recovery-test.ps1`

The event timestamp was:

`24-08-2026 06:47:11`

This directly established file creation activity before the script was deleted.

## Script Execution

The script was executed using:

```powershell
powershell.exe -ExecutionPolicy Bypass -File "C:\DeletedScriptRecoveryLab\recovery-test.ps1"
```

The output was:

```text
LAB60-Deleted Script Recovery Test
Processing controlled data
CONTROLLED-LAB60-DATA
```

The execution reference time was recorded as:

`24 August 2026 06:54:25`

## Sysmon Event ID 1

Sysmon Event ID 1 was searched for:

- `recovery-test`
- `powershell.exe`

Matching results included:

`24-08-2026 06:54:12`

This provided process-creation evidence around the script execution.

## PowerShell Event ID 4104

PowerShell Event ID 4104 was searched for:

- `LAB60`
- `recovery-test`
- `CONTROLLED-LAB60-DATA`

A matching event was identified at:

`24-08-2026 06:54:12`

This was important because Script Block Logging can preserve script content even after the original script file is deleted.

## Sysmon Event ID 3

Sysmon Event ID 3 was queried and returned multiple network events.

The results included events between approximately:

`06:42`

and:

`07:03`

No specific malicious network activity was attributed to the test script.

## Security Event ID 4688

Security Event ID 4688 was queried using:

```powershell
Get-WinEvent -FilterHashTable @{
    LogName="Security"
    Id=4688
} -MaxEvents 100 |
Where-Object {
    $_.Message -match "powershell.exe|recovery-test"
} |
Select-Object TimeCreated, Id, Message
```

No matching event was returned.

Therefore, Event ID 4688 was unavailable as supporting process telemetry for this investigation.

## PowerShell History

The current session history contained entries for:

- Directory creation
- Script creation
- Script inspection
- Metadata collection
- Hash collection
- Script execution
- Event-log queries
- Script deletion
- Post-deletion checks

The history provided a chronological record of the laboratory actions.

## Persistent PSReadLine History

The PSReadLine history file also retained references to:

- `DeletedScriptRecoveryLab`
- `recovery-test.ps1`
- `LAB60-Deleted Script Recovery Test`
- Script execution
- Hash collection
- Cleanup
- Event searches

This was particularly valuable because the history survived after the script was deleted.

## Script Deletion

The script was removed using:

```powershell
Remove-item "C:\DeletedScriptRecoveryLab\recovery-test.ps1"
```

A subsequent directory check returned no files.

This confirmed that the script was no longer present in the laboratory filesystem.

## Post-Deletion Evidence

After deletion, the investigation still had:

- Sysmon Event ID 11
- Sysmon Event ID 1
- PowerShell Event ID 4104
- Sysmon Event ID 3
- PSReadLine history
- Current PowerShell history
- File metadata
- SHA-256 hash

These artifacts allowed the analyst to reconstruct the script's history without directly recovering the deleted file from disk.

## Evidence Correlation

The evidence chain was:

```text
Script Created
      |
      v
Event ID 11
      |
      v
Metadata + SHA-256
      |
      v
Script Executed
      |
      +---- Sysmon Event ID 1
      |
      +---- PowerShell Event ID 4104
      |
      +---- Sysmon Event ID 3
      |
      +---- PowerShell History
      |
      v
Script Deleted
      |
      v
Filesystem Artifact Gone
      |
      v
Historical Evidence Remains
```

## Findings

1. The script was created at 06:47:11.
2. The original file was 151 bytes.
3. Its SHA-256 was recorded before deletion.
4. Sysmon Event ID 11 preserved the creation event.
5. The script executed successfully at approximately 06:54:25.
6. Sysmon Event ID 1 recorded related PowerShell process activity at 06:54:12.
7. PowerShell Event ID 4104 recorded relevant script activity at 06:54:12.
8. Sysmon Event ID 3 was available.
9. Security Event ID 4688 was not available.
10. PSReadLine history preserved references to the deleted script.
11. The script was subsequently removed.
12. The file itself was no longer present after cleanup.
13. The surviving evidence was sufficient to reconstruct the activity.

## Recovery vs Reconstruction

The lab did not perform low-level disk recovery.

The original file bytes were not recovered from deleted filesystem space.

Instead, the investigation performed:

`Artifact Reconstruction`

using:

- Script Block Logging
- Process telemetry
- Command history
- File metadata
- SHA-256
- File creation telemetry

This distinction is important when documenting forensic findings.

