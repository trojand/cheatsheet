# Base64 encode powershell commands

## Windows

```powershell
$MYCOMAND = "Invoke-WebRequest -Uri 'http://www.contoso.com' -OutFile 'C:\path\file'"
$ENCODED = [Convert]::ToBase64String([Text.Encoding]::Unicode.GetBytes($MYCOMMAND))
Write-Output $ENCODED
```

## Linux

```bash
echo -n 'Invoke-WebRequest -Uri "http://www.contoso.com" -OutFile "C:\path\file"' | iconv -f UTF8 -t UTF16LE | base64 -w 0
# Remove the % character at the end
```

## Execute

```powershell
powershell -e <Base64_output_earlier>
```
            
[^1]: [Tech Expert](https://techexpert.tips/powershell/powershell-base64-encoding/)