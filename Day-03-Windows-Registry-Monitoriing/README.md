# Week 14 Day 3 – Windows Registry Monitoring

## Objective
Configure Wazuh File Integrity Monitoring (FIM) to monitor a Windows registry path and test whether registry changes are detected and reported.

## Environment
- **Endpoint:** Windows Server 2022
- **Hostname:** WinServ22
- **Wazuh Agent ID:** 003
- **Registry Path:** `HKEY_LOCAL_MACHINE\SOFTWARE\FIM-Lab`

## Lab Activities

### 1. Created a Test Registry Key

Created a dedicated registry key using PowerShell:

```powershell
New-Item -Path "HKLM:\SOFTWARE\FIM-Lab" -Force
```

Created a string value named `LabStatus`:

```powershell
New-ItemProperty -Path "HKLM:\SOFTWARE\FIM-Lab" -Name "LabStatus" -Value "Initial" -PropertyType String -Force
```

The initial value was confirmed as `Initial`.

### 2. Configured Wazuh Registry Monitoring

Added the following entry inside the existing `<syscheck>` section in the Windows agent configuration file:

```xml
<windows_registry>HKEY_LOCAL_MACHINE\SOFTWARE\FIM-Lab</windows_registry>
```

Restarted the Wazuh agent service:

```powershell
Restart-Service WazuhSvc
```

Verified that the service was running.

### 3. Tested a Registry Modification

Changed the registry value from `Initial` to `Modified`:

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\FIM-Lab" -Name "LabStatus" -Value "Modified"
```

Verified the updated value using:

```powershell
Get-ItemProperty -Path "HKLM:\SOFTWARE\FIM-Lab"
```

PowerShell confirmed that `LabStatus` contained `Modified`.

### 4. Investigated Wazuh Detection

Searched Wazuh Threat Hunting for events associated with Agent 003.

- The general Agent 003 search returned results.
- The registry-related search returned no results.
- The manager's `alerts.json` search returned no matching `FIM-Lab` entries.
- The Windows agent log confirmed that the registry monitoring path was loaded:

```text
Monitoring registry entry:
HKEY_LOCAL_MACHINE\SOFTWARE\FIM-Lab
```

The manager log review did not establish whether the registry modification event reached the manager.

## Results

| Test | Observation |
|---|---|
| Registry key creation | Successful |
| Initial registry value | Initial |
| Wazuh registry path configuration | Successful |
| Wazuh agent restart | Successful |
| Registry value modification | Successful |
| Agent log confirms monitoring path | Yes |
| Registry alert in Threat Hunting | Not found |
| Matching manager alert | Not found |

## Skills Practiced

- Windows Registry administration using PowerShell
- Wazuh agent configuration
- Windows registry monitoring configuration
- Reviewing Wazuh agent logs
- Searching Wazuh Threat Hunting
- Distinguishing configuration confirmation from successful event detection

## Conclusion

I created a test registry key and modified its value on Windows Server 2022. I added the registry path to the Wazuh agent configuration and confirmed through the agent log that the monitoring path was loaded.

However, the registry modification did not appear in Wazuh Threat Hunting or the manager's alert log during this test. The reason for the missing alert was not established.

This exercise demonstrated the importance of verifying both monitoring configuration and actual event detection before concluding that a security monitoring control is working.

**Lab status:** Configuration verified; registry event detection not confirmed.
