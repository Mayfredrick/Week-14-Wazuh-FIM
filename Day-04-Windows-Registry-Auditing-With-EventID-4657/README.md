# Week 14 – Day 4: Windows Registry Auditing with Event ID 4657

## Objective

Configure Windows Security auditing to monitor registry value modifications and verify whether Wazuh can collect and process Event ID 4657.

## Lab Environment

- **Endpoint:** Windows Server 2022
- **Hostname:** WinServ22
- **Domain:** HOMELAB.LOCAL
- **Wazuh Agent ID:** 003
- **Wazuh Manager:** wazuh-server
- **Registry Key:** `HKLM\SOFTWARE\FIM-Lab`
- **Test Value:** `InvestigationTest`

## 1. Configure Windows Registry Auditing

Enabled successful Registry auditing using:

```powershell
auditpol /set /subcategory:"Registry" /success:enable
```

Verified the setting:

```powershell
auditpol /get /subcategory:"Registry"
```

Applied a successful audit rule to the test registry key for the `SetValue` permission, using the `Everyone` security principal.

Verified the audit rule:

- Identity: Everyone
- Registry Rights: SetValue
- Audit Flags: Success

## 2. Generate a Registry Modification Event

Modified the `InvestigationTest` registry value using PowerShell:

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\FIM-Lab" -Name "InvestigationTest" -Value "Week14-Day4-ArchiveTest"
```

Windows Security recorded Event ID **4657 – A registry value was modified**.

### Windows Event Details

| Field | Observation |
|---|---|
| Event ID | 4657 |
| Account | HOMELAB\Administrator |
| Object | `\REGISTRY\MACHINE\SOFTWARE\FIM-Lab` |
| Value Name | InvestigationTest |
| Process | PowerShell 7 (`pwsh.exe`) |
| Old Value | Week14-Day4-FinalTest |
| New Value | Week14-Day4-ArchiveTest |
| Operation | Existing registry value modified |

## 3. Verify Wazuh Event Collection

Initially, Event ID 4657 did not appear in Wazuh Threat Hunting.

The Windows Security event channel was configured in the Wazuh agent configuration. Event ID 4657 was not listed among the excluded event IDs.

The agent was connected to the manager, and the Wazuh service was running.

To investigate further, JSON archival logging was temporarily enabled on the Wazuh manager.

```xml
<logall_json>yes</logall_json>
```

After restarting the manager, another registry modification was generated.

The event was found in:

```text
/var/ossec/logs/archives/archives.json
```

### Archive Results

The archived event confirmed:

- Agent ID: 003
- Agent Name: WinServ22
- Manager: wazuh-server
- Event ID: 4657
- Decoder: windows_eventchannel
- Location: EventChannel
- Account: Administrator
- Process: PowerShell 7

**Result:** Wazuh successfully received and decoded the Windows registry auditing event.

## 4. Restore Manager Logging Configuration

After confirming event collection, temporary JSON archival logging was disabled.

```xml
<logall_json>no</logall_json>
```

The Wazuh manager was restarted and verified as:

```text
Active: active (running)
```

## 5. Findings

| Test | Result |
|---|---|
| Enable Registry auditing | Successful |
| Configure registry audit rule | Successful |
| Generate Windows Event 4657 | Successful |
| Verify event in Windows Security | Successful |
| Verify agent connection | Successful |
| Find event in Wazuh archives | Successful |
| Find corresponding alert in Threat Hunting | Not observed |
| Restore manager configuration | Successful |

## 6. Lessons Learned

This lab demonstrated the difference between Windows event generation, Wazuh event collection, and Wazuh alert generation.

The archive confirmed that Event ID 4657 reached the Wazuh manager and was decoded by the Windows Event Channel decoder.

However, no corresponding alert appeared in Threat Hunting during testing.

The exact reason for the missing alert has not yet been established. Further investigation of Wazuh rules and alert generation is needed.

## Conclusion

Windows Registry auditing was successfully configured, and a controlled registry modification generated Event ID 4657.

The Wazuh manager successfully received and decoded the event, as confirmed by the JSON archive.

Although the event was not visible as an alert in Threat Hunting, the archive test established that event forwarding was working.

This exercise provided practical experience with Windows audit policies, registry permissions, Security event analysis, Wazuh agent configuration, and manager-side event verification.
