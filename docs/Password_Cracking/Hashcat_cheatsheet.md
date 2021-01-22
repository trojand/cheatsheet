# Common hashcat commands

Just a few for now
___
## Popular Rulesets
   * [Pantagrule](https://github.com/rarecoil/pantagrule) by @rarecoil
   * [nsa-rules](https://github.com/NSAKEY/nsa-rules) by @_NSAKEY


## Popular Wordlists
   * ROCKYOU.txt!!!!!
___

## Kerberoast
```bash
./hashcat -a 0 -m 13100 kerberoasted.txt rockyou.txt -r pantagrule.popular.rule -O`
./hashcat -a 0 -m 13100 kerberoasted.txt rockyou.txt -r _NSAKEY.v1.dive.rule -O`
```


