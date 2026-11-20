# Week 14 – Day 7: File Integrity Monitoring Investigation

## Objective

Complete a controlled file integrity investigation using Wazuh on Windows Server 2022.

The exercise focused on detecting file creation, modification, and deletion, verifying SHA-256 hashes, and interpreting security events without automatically assuming malicious activity.

## Lab Environment

- **Operating System:** Windows Server 2022
- **Hostname:** WinServ22
- **Wazuh Agent ID:** 003
- **Wazuh Manager:** wazuh-server
- **Test Directory:** `C:\FIM-Lab`
- **Test File:** `Day7-Investigation.txt`
- **Hash Algorithm:** SHA-256

## 1. Create the Investigation File

Created the test file:

```powershell
New-Item -Path "C:\FIM-Lab\Day7-Investigation.txt" -ItemType File -Force
```

Added the initial content:

```powershell
Set-Content -Path "C:\FIM-Lab\Day7-Investigation.txt" -Value "Approved baseline configuration"
```

Calculated the original SHA-256 hash:

```powershell
Get-FileHash "C:\FIM-Lab\Day7-Investigation.txt" -Algorithm SHA256
```

### Original SHA-256 Hash

```text
C444646E4B00CEE0741424FDFF916963D93788C42E1FD790EB4388969BC2CF59
```

Wazuh detected the file creation.

- **Rule ID:** 554
- **Description:** File added to the system

## 2. Modify the Investigation File

Changed the file contents:

```powershell
Set-Content -Path "C:\FIM-Lab\Day7-Investigation.txt" -Value "Modified configuration - investigation test"
```

Calculated the new SHA-256 hash:

```powershell
Get-FileHash "C:\FIM-Lab\Day7-Investigation.txt" -Algorithm SHA256
```

### Modified SHA-256 Hash

```text
015908F75293963860363E1DA04365CDAB8C9BD6C09748FC0DD5A8B1D543B7F2
```

The modified hash differed from the original baseline.

## 3. Verify the Modification in Wazuh

Searched Threat Hunting for the investigation file.

The event contained:

- **Rule ID:** 550
- **Event:** Modified
- **SHA-256 After:** `015908f75293963860363e1da04365cdab8c9bd6c09748fc0dd5a8b1d543b7f2`

The hash reported by Wazuh matched the hash calculated through PowerShell.

**Result:** Successful hash verification.

## 4. Delete the Investigation File

Removed the test file:

```powershell
Remove-Item -Path "C:\FIM-Lab\Day7-Investigation.txt"
```

Searched Threat Hunting for the deletion event.

**Result:**

- **Rule ID:** 553
- **Description:** File deleted

Wazuh successfully detected the deletion.

## 5. Lab Results

| Activity | Wazuh Rule ID | Result |
|---|---:|---|
| File creation | 554 | Detected |
| File modification | 550 | Detected |
| SHA-256 verification | 550 | Exact match |
| File deletion | 553 | Detected |

### Hash Comparison

| Measurement | SHA-256 |
|---|---|
| Original baseline | `C444646E...9BC2CF59` |
| Modified file | `015908F7...D543B7F2` |
| Wazuh SHA-256 after | `015908F7...D543B7F2` |

## 6. Security Analyst Knowledge Check

**Question:** Can file creation, modification, and deletion events alone establish that an activity was malicious?

**Answer:** No. Additional evidence is needed to determine whether the activity was authorized or malicious.

An analyst should consider:

- Which user performed the activity?
- Which process modified the file?
- Whether the change was authorized.
- Whether other unusual activity occurred around the same time.
- Whether the file change is related to a known security incident.

## 7. What I Learned

During this exercise, I practiced:

- Establishing a file integrity baseline.
- Calculating SHA-256 hashes with PowerShell.
- Comparing original and modified file hashes.
- Verifying Wazuh FIM events.
- Identifying file creation, modification, and deletion.
- Reviewing security events without making unsupported assumptions.

## Conclusion

This lab successfully demonstrated a controlled file integrity investigation using Wazuh.

Wazuh detected file creation, modification, and deletion. The SHA-256 value recorded by Wazuh matched the independent PowerShell calculation.

The exercise also reinforced an important security analysis principle: file changes are evidence of activity, but additional investigation is necessary before determining whether that activity is malicious.
