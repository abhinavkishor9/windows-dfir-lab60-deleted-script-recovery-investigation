# Timeline 

## Investigation Timeline

| Time | Source | Activity | Significance |
|---|---|---|---|
| 06:43 | File System | Investigation directory created | `C:\DeletedScriptRecoveryLab` established |
| 06:47:11 | File System | `recovery-test.ps1` created | Controlled script created |
| 06:47:11 | Sysmon Event ID 11 | File creation recorded | Confirms script creation |
| 06:47:11 | File System | Creation and LastWrite recorded | Baseline metadata established |
| 06:49:18 | File System | LastAccess recorded | Additional timeline evidence |
| Before execution | File System | SHA-256 calculated | Original file hash preserved |
| 06:53:04 | PowerShell | Reference timestamp collected | Pre-execution reference |
| 06:54:12 | Sysmon Event ID 1 | PowerShell process event observed | Process execution evidence |
| 06:54:12 | PowerShell Event ID 4104 | Script Block event observed | Script activity evidence |
| 06:54:25 | PowerShell | Script execution completed | Execution timestamp recorded |
| 06:47–07:03 | Sysmon Event ID 3 | Network events observed | Network telemetry available |
| After execution | PowerShell | File listed before deletion | Pre-cleanup confirmation |
| After execution | PowerShell | `recovery-test.ps1` deleted | Cleanup simulated |
| After deletion | File System | Directory checked | Script no longer present |
| After deletion | PowerShell | PSReadLine history reviewed | Historical references survived |
| After deletion | PowerShell | Volatile history reviewed | Session evidence survived |
| After deletion | Event Logs | 4104 searched again | Historical script evidence available |
| Final | DFIR Analysis | Evidence correlated | Activity reconstructed |

