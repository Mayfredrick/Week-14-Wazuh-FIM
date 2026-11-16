# Week 14 – Day 1: File Integrity Monitoring (FIM)

## Objective

Configure and test Wazuh File Integrity Monitoring on Windows Server 2022 to detect file creation, modification, and deletion.

## Lab Environment

- Endpoint: Windows Server 2022
- Hostname: WinServ22
- Wazuh Agent ID: 003
- Agent IP: 192.168.114.12
- Wazuh Manager: wazuh-server
- Test directory: C:\FIM-Lab

## 1. Reviewed Existing FIM Configuration

Inspected the Windows agent configuration file:

C:\Program Files (x86)\ossec-agent\ossec.conf

Confirmed:
- FIM was enabled.
- Scan frequency was set to 43,200 seconds (12 hours).
- Existing Windows directories and registry locations were configured for monitoring.
- The Startup folder had real-time monitoring enabled.

## 2. Configured a Test Directory

Created the directory:

C:\FIM-Lab

Added the following entry inside the existing <syscheck> section:
<directories realtime="yes">C:\FIM-Lab</directories>

Restarted the Wazuh agent and verified that the service was running.

## 3. Tested File Creation

Created:

C:\FIM-Lab\fim-test.txt

Wazuh detected the new file.

Observed alert:
- Rule ID: 554
- Description: File added to the system
- Monitoring mode: Realtime
- Event: added
- File size: 0 bytes

Confirmed the alert in the Wazuh manager's alerts.json file and Threat Hunting dashboard.

## 4. Tested File Modification

Modified the test file using PowerShell:
Set-Content -Path "C:\FIM-Lab\fim-test.txt" -Value "Week 14 Day 1 - FIM modification test"


Wazuh detected the modification and displayed one matching event.

## 5. Tested File Deletion

Deleted the test file using PowerShell:

Remove-Item -Path "C:\FIM-Lab\fim-test.txt" -Confirm:$false

Wazuh detected the deletion.

Observed alert:
- Rule ID: 553
- Description: File deleted
- File path: c:\fim-lab\fim-test.txt
- Event: deleted

The Threat Hunting search returned two hits. One event was inspected and confirmed as a file deletion.

## Results

| Activity | Result |
|---|---|
| File creation | Detected |
| File modification | Detected |
| File deletion | Detected |

## Skills Practiced

- Wazuh File Integrity Monitoring
- Windows agent configuration
- Real-time file monitoring
- PowerShell file administration
- Wazuh Threat Hunting
- Security alert investigation
- Reviewing Wazuh manager alert logs

## Conclusion

This lab demonstrated how Wazuh File Integrity Monitoring detects changes to files on a Windows endpoint.

I configured a dedicated test directory and successfully verified file creation, modification, and deletion alerts.

This exercise provided practical experience with endpoint monitoring and file integrity detection in a SIEM environment.
