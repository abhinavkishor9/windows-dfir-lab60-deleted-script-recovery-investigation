# Troubleshooting Notes — Lab 60 Deleted Script Recovery Investigation

## 1. PowerShell Script Execution Policy

### Problem

The Windows endpoint can block direct `.ps1` execution.

### Resolution

The script was executed using:

```powershell
powershell.exe -ExecutionPolicy Bypass -File "C:\DeletedScriptRecoveryLab\recovery-test.ps1"
```

The bypass was applied only to the PowerShell process.

### DFIR Note

The system-wide Execution Policy was not permanently changed.

---

## 2. Sysmon Event ID 11 Was Available

### Observation

Sysmon Event ID 11 returned:

`24-08-2026 06:47:11`

for:

`C:\DeletedScriptRecoveryLab\recovery-test.ps1`

### Importance

This provided direct evidence that the script existed and was created.

### DFIR Lesson

File-creation telemetry can preserve evidence even after the file is deleted.

---

## 3. Sysmon Event ID 1 Was Available

### Observation

Sysmon Event ID 1 returned a relevant event at:

`24-08-2026 06:54:12`

### Importance

This provided process-creation evidence around the script execution.

### DFIR Lesson

Process telemetry can preserve historical execution information after the script itself disappears.

---

## 4. PowerShell Event ID 4104 Was Available

### Observation

A matching 4104 event was found at:

`24-08-2026 06:54:12`

### Importance

This was the most valuable source for reconstructing the script's activity because Script Block Logging can preserve script content independently of the script file.

### DFIR Lesson

PowerShell logs can become especially important when the original `.ps1` file has been deleted.

---

## 5. Security Event ID 4688 Was Not Available

### Problem

The query for:

- `powershell.exe`
- `recovery-test`

returned no results.

### Interpretation

Windows Security Event ID 4688 could not be used as supporting evidence for this investigation.

### Resolution

The process investigation relied on Sysmon Event ID 1.

### DFIR Lesson

Missing 4688 should be documented rather than treated as evidence that PowerShell did not execute.

---

## 6. Sysmon Event ID 3 Was Available

### Observation

Many network events were returned between approximately 06:42 and 07:03.

### Interpretation

Network telemetry was functioning.

No specific malicious communication was attributed to the controlled script.

### DFIR Lesson

Temporal proximity to script execution does not automatically establish causation.

---

## 7. Script File Was Successfully Deleted

### Observation

Before cleanup:

`recovery-test.ps1` existed.

After:

```powershell
Get-ChildItem "C:\DeletedScriptRecoveryLab"
```

the directory returned no files.

### Importance

This confirmed the intended cleanup simulation.

---

## 8. Deleted File Was Not Directly Recovered

### Important Distinction

The lab did not perform:

- Deleted-file carving
- NTFS MFT recovery
- Raw-disk recovery
- Memory acquisition

Therefore, the original deleted file was not physically recovered.

### What Was Recovered

Useful information about the file was reconstructed from:

- Event ID 11
- Event ID 4104
- Event ID 1
- PSReadLine history
- PowerShell history
- Metadata
- SHA-256

### DFIR Lesson

Artifact reconstruction and deleted-file recovery are different forensic processes.

---

## 9. PSReadLine History Was Available

### Observation

Persistent history contained references to:

`recovery-test.ps1`

and the investigation commands.

### Importance

This provided an independent surviving source of evidence.

### Limitation

PowerShell history is user/session dependent and should not be treated as a complete record of every action performed on the machine.

---

## 10. Volatile PowerShell History Was Available

### Observation

`Get-History` returned the investigation commands.

### Importance

This preserved the chronology of the laboratory workflow.

### Limitation

Session history can disappear when the PowerShell session ends and therefore should not be relied upon as permanent evidence.

---

## 11. SHA-256 Was Collected Before Deletion

### Observation

The original hash was:

`D129D500AE1C48895B967A94D9C97FBEBCD772019117090AC5327C5F272FE68`

### Importance

The hash provides an identifier for the original file.

### DFIR Lesson

A hash collected before deletion can remain useful even when the original file is no longer available.

---

## 12. Last Access Time Was Different From Creation Time

### Observed Metadata

Creation and Last Write:

`24-08-2026 06:47:11`

Last Access:

`24-08-2026 06:49:18`

### Interpretation

The metadata provides additional timeline information.

However, file timestamps should be interpreted carefully and correlated with other evidence.

---

# Troubleshooting Summary

| Observation | Resolution |
|---|---|
| Script execution required bypass | Used process-level `-ExecutionPolicy Bypass` |
| Event ID 11 available | Used it as file-creation evidence |
| Event ID 1 available | Used it as process evidence |
| Event ID 4104 available | Used it for script reconstruction |
| Event ID 4688 unavailable | Documented telemetry gap |
| Event ID 3 available | Reviewed for network context |
| Script deleted | Used historical logs/history |
| Original file unavailable | Reconstructed activity from surviving artifacts |
| PSReadLine history available | Used as supporting evidence |
| SHA-256 preserved | Used as pre-deletion artifact identifier |

# Final Troubleshooting Lesson

The central troubleshooting lesson from Lab 60 is:

> When the original artifact disappears, the investigation should move outward to historical evidence rather than stopping at the filesystem.

A deleted script can still be reconstructed through:

```text
File Creation
    +
Process Creation
    +
PowerShell Script Logging
    +
Command History
    +
File Metadata
    +
Hash
    =
Strong Historical Context
```

The evidence must still be reported carefully. Reconstruction of script activity is not the same as physically recovering the deleted file itself.
