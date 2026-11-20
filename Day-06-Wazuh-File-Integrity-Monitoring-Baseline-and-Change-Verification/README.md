# Week 14 – Day 6: File Integrity Monitoring and SHA-256 Verification

## Objective

Practice using SHA-256 hashes to establish a file baseline, identify file modifications, and verify that Wazuh records the updated file hash correctly.

## Lab Environment

- **Operating System:** Windows Server 2022
- **Hostname:** WinServ22
- **Wazuh Agent ID:** 003
- **Wazuh Manager:** wazuh-server
- **Test Directory:** `C:\FIM-Lab`
- **Test File:** `Day6-Baseline.txt`
- **Hash Algorithm:** SHA-256

## 1. Create the Baseline File

Created the test file using PowerShell:

```powershell
Set-Content -Path "C:\FIM-Lab\Day6-Baseline.txt" -Value "Week 14 Day 6 baseline content"
```

Calculated the original SHA-256 hash:

```powershell
Get-FileHash "C:\FIM-Lab\Day6-Baseline.txt" -Algorithm SHA256
```

### Original SHA-256 Hash

```text
42E5ED9F6ECCC6A0FC0610A414EBAFB3AFF8CDCEEBE2C4909F7F9E9F3065BE1D
```

Wazuh detected the file creation.

- **Rule ID:** 554
- **Description:** File added to the system

## 2. Modify the File

Changed the contents of the test file:

```powershell
Set-Content -Path "C:\FIM-Lab\Day6-Baseline.txt" -Value "Week 14 Day 6 modified content"
```

Calculated the new SHA-256 hash:

```powershell
Get-FileHash "C:\FIM-Lab\Day6-Baseline.txt" -Algorithm SHA256
```

### Modified SHA-256 Hash

```text
48CEFC1C92703A4541DE3EB8488F8546CECD9280C71A8552720372964194D801
```

The new hash differed from the original baseline.

## 3. Verify the Modification in Wazuh

Searched Wazuh Threat Hunting for the test file.

The modification event contained:

- **Rule ID:** 550
- **Event:** Modified
- **SHA-256 After:** `48cefc1c92703a4541de3eb8488f8546cecd9280c71a8552720372964194d801`

The SHA-256 value reported by Wazuh matched the value calculated through PowerShell.

**Result:** Successful hash verification.

## 4. Delete the Test File

Removed the test file:

```powershell
Remove-Item -Path "C:\FIM-Lab\Day6-Baseline.txt"
```

Searched Wazuh Threat Hunting for the deletion event.

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

### SHA-256 Comparison

| Measurement | Hash |
|---|---|
| Original baseline | `42E5ED9F...3065BE1D` |
| Modified file | `48CEFC1C...4194D801` |
| Wazuh SHA-256 after | `48CEFC1C...4194D801` |

The modified hash reported by Wazuh matched the PowerShell calculation.

## 6. What I Learned

During this exercise, I practiced:

- Establishing a SHA-256 baseline.
- Calculating file hashes with PowerShell.
- Comparing original and modified file hashes.
- Reviewing Wazuh FIM modification events.
- Verifying Wazuh hash values against independent PowerShell calculations.
- Identifying file creation, modification, and deletion events.

## Conclusion

This lab successfully demonstrated file integrity monitoring and SHA-256 verification using Wazuh on Windows Server 2022.

Wazuh detected file creation, modification, and deletion. The SHA-256 value recorded by Wazuh matched the hash calculated through PowerShell after the file was modified.

This exercise improved my understanding of file integrity monitoring, hash comparison, and endpoint security event analysis.

