# Week 14 – Day 2: File Integrity Monitoring and Hash Analysis

## Objective

Investigate how Wazuh File Integrity Monitoring detects file modifications and records cryptographic hashes.

## Lab Environment

- Endpoint: Windows Server 2022
- Hostname: WinServ22
- Wazuh Agent ID: 003
- Wazuh Manager: wazuh-server
- Monitored directory: C:\FIM-Lab
- Test file: hash-test.txt

## 1. Created the Baseline File

Created a test file using PowerShell:

powershell
Set-Content -Path "C:\FIM-Lab\hash-test.txt" -Value "Original Week 14 Day 2 content"


Calculated its SHA-256 hash:

powershell
Get-FileHash "C:\FIM-Lab\hash-test.txt" -Algorithm SHA256


Original SHA-256:

1B39F0465427086DD7BD6BC7BFAEA96C6750B2EF14FBF31A978181EAFB48C2E9

## 2. First File Modification

Modified the file:

powershell
Set-Content -Path "C:\FIM-Lab\hash-test.txt" -Value "Modified Week 14 Day 2 content"


Calculated the new SHA-256 hash:

B1DE5690289EC707FBB9D455EEA7A5C66C301AE28530A653246056787AE900B3

Wazuh detected the modification.

Observed event:
- Rule ID: 550
- Description: Integrity checksum changed
- Event: modified
- File: c:\fim-lab\hash-test.txt

The SHA-256 hash recorded by Wazuh matched the PowerShell result.

## 3. Second File Modification

Modified the file again:

powershell
Set-Content -Path "C:\FIM-Lab\hash-test.txt" -Value "Second modification - Week 14 Day 2"


Calculated the second modified SHA-256 hash:

4D68AC2E3CA746A3822BEDC817BC3AC11E81D10719D1897D28BF3773A7CA9D67

Wazuh generated another Event ID 550.

The SHA-256 hash recorded by Wazuh matched the PowerShell result.

## 4. File Deletion

Deleted the test file:

powershell
Remove-Item -Path "C:\FIM-Lab\hash-test.txt" -Confirm:$false


Wazuh detected the deletion.

Observed event:
- Rule ID: 553
- Description: File deleted
- Event: deleted
- File: c:\fim-lab\hash-test.txt

## Results

| Activity | Wazuh Rule | Result |
|---|---:|---|
| First modification | 550 | Detected; hash matched |
| Second modification | 550 | Detected; hash matched |
| File deletion | 553 | Detected |

## Skills Practiced

- File Integrity Monitoring
- SHA-256 hash calculation
- PowerShell file administration
- Wazuh checksum investigation
- Windows endpoint monitoring
- Comparing endpoint-generated hashes with SIEM records
- Security event investigation

## Conclusion

This lab demonstrated how Wazuh detects file modifications and records updated cryptographic checksums.

I independently calculated SHA-256 hashes using PowerShell and compared them with the values recorded by Wazuh. Both modification tests produced matching hashes.

The final deletion test also generated a Wazuh alert.

This exercise strengthened my understanding of file integrity monitoring and the use of cryptographic hashes during endpoint investigations.
