# Week 14 – Day 5: Windows File Integrity Monitoring (FIM)

## Objective

Practice monitoring file creation, modification, and deletion on Windows Server 2022 using Wazuh File Integrity Monitoring (FIM).

The goal was to generate controlled file changes and verify that Wazuh detected each activity.

## Lab Environment

- **Operating System:** Windows Server 2022
- **Hostname:** WinServ22
- **Wazuh Agent ID:** 003
- **Wazuh Manager:** wazuh-server
- **Test Directory:** `C:\FIM-Lab`
- **Test File:** `Day5-Test.txt`

## 1. Create the Test File

Created a test file using PowerShell:

```powershell
New-Item -Path "C:\FIM-Lab\Day5-Test.txt" -ItemType File -Force
```

Added initial content:

```powershell
Set-Content -Path "C:\FIM-Lab\Day5-Test.txt" -Value "Week 14 Day 5 initial test"
```

Checked Wazuh Threat Hunting for the new file.

**Result:** Wazuh detected the file creation.

- Rule ID: 554
- Description: File added to the system

## 2. Modify the Test File

Changed the file contents:

```powershell
Set-Content -Path "C:\FIM-Lab\Day5-Test.txt" -Value "Week 14 Day 5 modified test content"
```

Checked Threat Hunting for the modification event.

**Result:** Wazuh detected the file modification.

- Rule ID: 550
- Description: Integrity checksum changed

This demonstrated how Wazuh identifies changes to file contents through integrity monitoring.

## 3. Delete the Test File

Removed the test file:

```powershell
Remove-Item -Path "C:\FIM-Lab\Day5-Test.txt"
```

Checked Threat Hunting for the deletion event.

**Result:** Wazuh detected the file deletion.

- Rule ID: 553
- Description: File deleted

## 4. Lab Results

| Activity | Wazuh Rule ID | Result |
|---|---:|---|
| File creation | 554 | Detected |
| File modification | 550 | Detected |
| File deletion | 553 | Detected |

All three activities were successfully detected by Wazuh Agent 003.

## 5. What I Learned

During this exercise, I practiced:

- Creating files for controlled security testing.
- Monitoring file creation through Wazuh.
- Identifying file modifications using integrity checksum changes.
- Detecting file deletion events.
- Searching for specific file activity in Wazuh Threat Hunting.
- Understanding the role of file hashes in integrity monitoring.

## Conclusion

This lab successfully demonstrated Wazuh File Integrity Monitoring on Windows Server 2022.

Wazuh detected the creation, modification, and deletion of the test file. The corresponding events were recorded under rules 554, 550, and 553.

This exercise provided practical experience monitoring file activity and reviewing endpoint security events through a centralized SIEM platform.

