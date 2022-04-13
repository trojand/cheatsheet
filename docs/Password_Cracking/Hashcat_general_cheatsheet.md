# Common hashcat commands

## Rulesets
### My Practice Ruleset
* [Practical Ruleset](Hashcat_Practical_Ruleset.html)
### Popular Rulesets
* [Clem9669 - Hashcat-Rule](https://github.com/clem9669/hashcat-rule)
* [Kaonashi](https://github.com/kaonashi-passwords/Kaonashi)
* [Pantagrule](https://github.com/rarecoil/pantagrule) by @rarecoil
* [nsa-rules](https://github.com/NSAKEY/nsa-rules) by @_NSAKEY


## Popular Wordlists
* ROCKYOU.txt!!!!!
* Try create a custom password list containing all the passwords either
  * Leaked from that country (public and private sector breaches)
  * What you have encountered yourself in that country. (Remove PII or personally identifying ones)
* [Rockyou2021.txt](https://github.com/ohmybahgosh/RockYou2021.txt)
* [Kaonashi.txt](https://github.com/kaonashi-passwords/Kaonashi)

## Popular hashcat modes
* `-m 1000`  : NTLM - From sam dump, secretsdump, ntdsutil dump
* `-m 5600` : NetNTLMv2 (i.e. Captured hash from Responder)
* `-m 13100` : Kerberoast output


## Practical Wordlist Cracking
```powershell
.\hashcat.exe -a 0 -m 1000 --potfile-path company.potfile --session=company-wordlist-rule --debug-mode=4 --debug-file=matched.rule --force -O .\dumped_hashes.txt .\rockyou.txt -r rules\trojand.rule
.\hashcat.exe -a 0 -m 1000 --potfile-path company.potfile --session=company-wordlist-rule --debug-mode=4 --debug-file=matched.rule --force -O .\dumped_hashes.txt .\rockyou2021.txt -r rules\trojand.rule
.\hashcat.exe -a 0 -m 1000 --potfile-path company.potfile --session=company-wordlist-rule --debug-mode=4 --debug-file=matched.rule --force -O .\dumped_hashes.txt .\kaonashi.txt -r rules\trojand.rule
.\hashcat.exe -a 0 -m 1000 --potfile-path company.potfile --session=company-wordlist-rule --debug-mode=4 --debug-file=matched.rule --force -O .\dumped_hashes.txt .\rockyou.txt -r rules\hashcat-rule\clem9669_large.rule
.\hashcat.exe -a 0 -m 1000 --potfile-path company.potfile --session=company-wordlist-rule --debug-mode=4 --debug-file=matched.rule --force -O .\dumped_hashes.txt .\rockyou2021.txt -r rules\hashcat-rule\clem9669_large.rule
.\hashcat.exe -a 0 -m 1000 --potfile-path company.potfile --session=company-wordlist-rule --debug-mode=4 --debug-file=matched.rule --force -O .\dumped_hashes.txt .\kaonashi.txt -r rules\hashcat-rule\clem9669_large.rule
.\hashcat.exe -a 0 -m 1000 --potfile-path company.potfile --session=company-wordlist-rule --debug-mode=4 --debug-file=matched.rule --force -O .\dumped_hashes.txt .\kaonashi.txt -r rules\Kaonashi\rules\haku34K.rule
.\hashcat.exe -a 0 -m 1000 --potfile-path company.potfile --session=company-wordlist-rule --debug-mode=4 --debug-file=matched.rule --force -O .\dumped_hashes.txt .\kaonashi.txt -r rules\Kaonashi\rules\kamaji34K.rule
.\hashcat.exe -a 0 -m 1000 --potfile-path company.potfile --session=company-wordlist-rule --debug-mode=4 --debug-file=matched.rule --force -O .\dumped_hashes.txt .\kaonashi.txt -r rules\Kaonashi\rules\yubaba64.rule
```

## Practical Brute-Force
```powershell
.\hashcat.exe -a 3 -m 1000 --potfile-path company.potfile --debug-mode=4 --debug-file=matched.rule --session=company-bruteforce --force -O .\dumped_hashes.txt ?u?l?l?l?d?d?d?d
.\hashcat.exe -a 3 -m 1000 --potfile-path company.potfile --debug-mode=4 --debug-file=matched.rule --session=company-bruteforce --force -O .\dumped_hashes.txt ?u?l?l?l?d?d?d?d?s
.\hashcat.exe -a 3 -m 1000 --potfile-path company.potfile --debug-mode=4 --debug-file=matched.rule --session=company-bruteforce --force -O .\dumped_hashes.txt ?u?l?l?l?s?d?d?d?d
.\hashcat.exe -a 3 -m 1000 --potfile-path company.potfile --debug-mode=4 --debug-file=matched.rule --session=company-bruteforce --force -O .\dumped_hashes.txt ?u?l?l?s?d?d?d?d
.\hashcat.exe -a 3 -m 1000 --potfile-path company.potfile --debug-mode=4 --debug-file=matched.rule --session=company-bruteforce --force -O .\dumped_hashes.txt ?u?l?l?d?d?d?d?s
```
