# Windows DFIR Lab 60 — Deleted Script Recovery Investigation

## Overview

This lab investigates how a deleted PowerShell script can still leave behind useful forensic evidence after the script itself has been removed from the filesystem.

A harmless PowerShell script was created inside a dedicated investigation directory, its metadata and SHA-256 hash were recorded, and the script was executed using a process-level Execution Policy bypass. The script was then deleted to simulate attacker cleanup.

The investigation then examined the evidence that survived deletion, including Sysmon Event ID 11 for file creation, Sysmon Event ID 1 for process creation, PowerShell Event ID 4104 for Script Block Logging, Sysmon Event ID 3 for network activity, and PowerShell/PSReadLine history.

Windows Security Event ID 4688 was also investigated but was not available for the relevant activity.

## Investigation Objectives

- Establish the original state of a PowerShell script before deletion.
- Preserve file metadata and a cryptographic hash before cleanup.
- Determine when the script was created and executed.
- Identify process-creation evidence associated with the script.
- Determine whether PowerShell Script Block Logging preserved script activity after deletion.
- Investigate Sysmon file-creation telemetry.
- Examine PowerShell and PSReadLine history for surviving references.
- Review network telemetry around the execution and cleanup timeframe.
- Confirm that the script was removed from the filesystem.
- Determine what evidence remains after deletion.
- Distinguish direct recovery of a deleted file from reconstruction of its contents and activity through surviving artifacts.
- Document telemetry gaps and limitations.

## Investigation Scenario

A Windows workstation is suspected of having a PowerShell script created, executed, and subsequently deleted in an attempt to remove evidence of its activity.

The SOC/DFIR analyst needs to determine:

- When the script existed.
- What the script contained.
- When it was executed.
- Which process executed it.
- Whether Windows logging preserved evidence after deletion.
- Whether PowerShell history retained references to the script.
- Whether any related network activity occurred.
- Whether the deleted script can be reconstructed from surviving evidence.

The investigation uses only harmless laboratory data.

## Lab Environment

| Component | Details |
|---|---|
| Operating System | Windows |
| Investigation Type | Windows DFIR |
| Investigation Directory | `C:\DeletedScriptRecoveryLab` |
| Script | `recovery-test.ps1` |
| Sysmon Event ID 11 | Observed |
| Sysmon Event ID 1 | Observed |
| Sysmon Event ID 3 | Observed |
| PowerShell Event ID 4104 | Observed |
| Windows Security Event ID 4688 | Not available |
| PSReadLine History | Available |

## Controlled Script

The test script contained:

```powershell
Write-Output "LAB60-Deleted Script Recovery Test"
$LabData="CONTROLLED-LAB60-DATA"
Write-Output "Processing controlled data"
Write-Output $LabData
```

The script was created only for forensic testing and contained no malicious functionality.

## Investigation Workflow

1. Create the investigation directory.
2. Create the controlled PowerShell script.
3. Verify the script contents.
4. Collect file metadata.
5. Calculate the SHA-256 hash.
6. Record reference timestamps.
7. Execute the script with a process-level Execution Policy bypass.
8. Investigate Sysmon Event ID 11.
9. Investigate Sysmon Event ID 1.
10. Investigate PowerShell Event ID 4104.
11. Investigate Sysmon Event ID 3.
12. Investigate Security Event ID 4688.
13. Review volatile PowerShell history.
14. Review persistent PSReadLine history.
15. Delete the script.
16. Confirm that the script no longer exists.
17. Search surviving telemetry after deletion.
18. Reconstruct the script activity from the remaining evidence.
19. Document evidence gaps.
20. Build the final investigation timeline.

## File Creation Evidence

Sysmon Event ID 11 was observed for the script:

`C:\DeletedScriptRecoveryLab\recovery-test.ps1`

The event occurred at:

`24-08-2026 06:47:11`

This provides direct evidence that the file existed and was created at that time.

## File Metadata

The script metadata was recorded before deletion:

| Property | Value |
|---|---|
| Name | `recovery-test.ps1` |
| Path | `C:\DeletedScriptRecoveryLab\recovery-test.ps1` |
| Length | 151 bytes |
| Creation Time | 24-08-2026 06:47:11 |
| Last Write Time | 24-08-2026 06:47:11 |
| Last Access Time | 24-08-2026 06:49:18 |

## SHA-256

The SHA-256 hash recorded before deletion was:

`D129D500AE1C48895B967A94D9C97FBEBCD772019117090AC5327C5F272FE68`

This provides an identifier for the original script artifact.

## Script Execution

The script was executed using:

```powershell
powershell.exe -ExecutionPolicy Bypass -File "C:\DeletedScriptRecoveryLab\recovery-test.ps1"
```

The script produced:

```text
LAB60-Deleted Script Recovery Test
Processing controlled data
CONTROLLED-LAB60-DATA
```

The execution reference time recorded immediately afterward was:

`24 August 2026 06:54:25`

## Sysmon Event ID 1

Sysmon Event ID 1 was observed for PowerShell-related activity.

The search returned a matching event at:

`24-08-2026 06:54:12`

This provides process-creation evidence around the execution window.

## PowerShell Event ID 4104

PowerShell Script Block Logging Event ID 4104 was observed.

A matching event was identified at:

`24-08-2026 06:54:12`

The search used:

- `LAB60`
- `recovery-test`
- `CONTROLLED-LAB60-DATA`

This provides important surviving evidence about the PowerShell script after its deletion.

## Sysmon Event ID 3

Sysmon Event ID 3 was available and produced numerous network connection events.

The results covered the investigation period, including events around:

`06:47`

through:

`07:03`

The available output demonstrated that network telemetry was functioning.

No specific malicious network activity was attributed to the test script.

## Windows Security Event ID 4688

Security Event ID 4688 was investigated using PowerShell.

No matching event was returned for:

- `powershell.exe`
- `recovery-test`

Therefore, Event ID 4688 was treated as an unavailable telemetry source for this investigation.

The absence of 4688 does not prove that the process did not execute.

## PSReadLine History

Persistent PowerShell history was available.

The history contained references to:

- Creation of `C:\DeletedScriptRecoveryLab`
- Creation of `recovery-test.ps1`
- File metadata collection
- SHA-256 calculation
- Script execution
- Cleanup
- Event-log searches

This provided an additional surviving evidence source after the script was deleted.

## Volatile PowerShell History

The current PowerShell session history also contained the investigation commands.

Important entries included:

- Script creation
- Script inspection
- File metadata collection
- SHA-256 calculation
- Script execution
- Cleanup
- Event queries

This demonstrates that command history can preserve investigative context even after the target file disappears.

## Script Deletion

After the execution and evidence collection, the test script was deleted using:

```powershell
Remove-Item "C:\DeletedScriptRecoveryLab\recovery-test.ps1"
```

A subsequent directory check returned no files.

This confirmed that the script was no longer present in the filesystem.

## Post-Deletion Evidence

After deletion, the filesystem no longer contained the script.

However, evidence remained in:

- Sysmon Event ID 11
- Sysmon Event ID 1
- PowerShell Event ID 4104
- Sysmon Event ID 3
- PSReadLine history
- Volatile PowerShell history
- Previously collected metadata
- Previously collected SHA-256 hash

This demonstrates:

> Deleted file does not necessarily mean deleted evidence.

## Evidence Correlation

The investigation followed this sequence:

```text
Script Created
     |
     v
File Metadata
     |
     v
SHA-256
     |
     v
PowerShell Execution
     |
     +---- Sysmon Event ID 1
     |
     +---- PowerShell Event ID 4104
     |
     +---- Sysmon Event ID 3
     |
     v
Script Deleted
     |
     v
Filesystem Artifact Gone
     |
     v
Historical Telemetry Survives
     |
     v
Script Activity Reconstructed
```

## Key Findings

- `recovery-test.ps1` was created successfully.
- Sysmon Event ID 11 recorded the file creation at 06:47:11.
- The script was 151 bytes.
- The pre-deletion SHA-256 was recorded.
- The script executed successfully using `-ExecutionPolicy Bypass`.
- Sysmon Event ID 1 was observed at 06:54:12.
- PowerShell Event ID 4104 was observed at 06:54:12.
- Sysmon Event ID 3 was available.
- Security Event ID 4688 was not available for the relevant activity.
- PSReadLine history preserved references to the script and investigation commands.
- The script was later removed from the filesystem.
- Historical telemetry remained available after deletion.
- The original file itself was not recovered from disk during the lab.
- The script's activity and content could be reconstructed from preserved evidence sources.

## Recovery vs Reconstruction

This lab did not perform forensic recovery of deleted disk sectors.

Instead, it demonstrated **artifact reconstruction**.

The distinction is:

```text
File Recovery
     |
     +-- Attempt to recover the deleted bytes from storage

Artifact Reconstruction
     |
     +-- Use logs/history/hash/metadata to reconstruct what happened
```

For this lab, reconstruction was sufficient to establish the script path, contents, execution, and deletion sequence.

## DFIR Interpretation

The strongest lesson is that deletion should not be treated as the end of an investigation.

A script can disappear from the filesystem while:

- Script Block Logging preserves its content.
- Process logs preserve its execution.
- Sysmon preserves its creation.
- PowerShell history preserves the commands used.
- Previously collected metadata preserves its original state.

This allows an investigator to reconstruct the activity without recovering the original file itself.

## MITRE ATT&CK Relevance

Potentially relevant techniques include:

**T1070.004 — File Deletion**

Relevant to the deletion of artifacts intended to remove evidence.

**T1059.001 — PowerShell**

Relevant because PowerShell was used to execute the controlled script.

The lab demonstrates these behaviors in a controlled environment and does not represent an actual attack.

## Evidence Limitations

- Security Event ID 4688 was unavailable for the relevant PowerShell activity.
- The original deleted file was not recovered directly from disk.
- Network events were available, but no specific malicious network activity was established.
- Historical evidence depends on log retention and configuration.
- PSReadLine history is session/user dependent and should not be treated as complete forensic history.

## Conclusion

This investigation demonstrated that a deleted PowerShell script can leave multiple surviving traces on a Windows endpoint.

Sysmon Event ID 11 established that the file existed. Sysmon Event ID 1 and PowerShell Event ID 4104 established its execution. PSReadLine and PowerShell history preserved references to the activity. File metadata and SHA-256 preserved the original artifact state before deletion.

Although Security Event ID 4688 was unavailable and the original file was not directly recovered, the surviving artifacts were sufficient to reconstruct the script's creation, contents, execution, and deletion sequence.

The key lesson is:

> Deleting a script can remove the file, but it does not necessarily remove the forensic story surrounding it.

## Skills Demonstrated

- Deleted Script Investigation
- Windows DFIR
- PowerShell Forensics
- Sysmon Event ID 11
- Sysmon Event ID 1
- Sysmon Event ID 3
- PowerShell Event ID 4104
- PSReadLine History Analysis
- File Metadata Analysis
- SHA-256 Hashing
- Artifact Reconstruction
- Timeline Construction
- Evidence Preservation
- Telemetry Gap Analysis

## Disclaimer

This lab used a harmless PowerShell script created specifically for DFIR testing. The script was deleted only from `C:\DeletedScriptRecoveryLab`. No Windows logs, system files, or security infrastructure were intentionally modified or deleted.
