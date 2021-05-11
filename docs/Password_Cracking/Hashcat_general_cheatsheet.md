# Common hashcat commands

## Rulesets
### My Practice Ruleset
* [Practical Ruleset](Hashcat_Practical_Ruleset.html)
### Popular Rulesets
* [Pantagrule](https://github.com/rarecoil/pantagrule) by @rarecoil
* [nsa-rules](https://github.com/NSAKEY/nsa-rules) by @_NSAKEY


## Popular Wordlists
* ROCKYOU.txt!!!!!
* Try create a custom password list containing all the passwords either
  * Leaked from that country (public and private sector breaches)
  * What you have encountered yourself in that country. (Remove PII or personally identifying ones)

## Popular hashcat modes
* `-m 1000`  : NTLM - From sam dump, secretsdump, ntdsutil dump
* `-m 13000` : Kerberoast output


## Practical Brute-Force
```powershell
.\hashcat.exe -a 3 -m 1000 --force -O .\dumped_hashes.txt ?u?l?l?l?d?d?d?d
.\hashcat.exe -a 3 -m 1000 --force -O .\dumped_hashes.txt ?u?l?l?l?d?d?d?d?s
.\hashcat.exe -a 3 -m 1000 --force -O .\dumped_hashes.txt ?u?l?l?l?s?d?d?d?d
.\hashcat.exe -a 3 -m 1000 --force -O .\dumped_hashes.txt ?u?l?l?s?d?d?d?d
.\hashcat.exe -a 3 -m 1000 --force -O .\dumped_hashes.txt ?u?l?l?d?d?d?d?s
```
