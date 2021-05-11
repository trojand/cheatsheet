# My Practical Hashcat Ruleset

1. Download maskprocessor from [here](https://github.com/hashcat/maskprocessor/releases/)
1. Rules (Just paste this whole (or portions only) section to your cmd.exe terminal. It gets weird in powershell due to the '$' character. No need to remove the tabs and comments/descriptions)

      ```
      # Realistic years only (For smaller rules)
        ## Uppercase First Letter, Append 4 Digits
          mp64.exe -o trojand.rule "c $1$9$?d$?d"
          mp64.exe -o trojand.rule "c $2$0$?d$?d"
        ## Uppercase First Letter, Symbol, Append 4 Digits
          mp64.exe -o trojand.rule "c $?s$1$9$?d$?d"
          mp64.exe -o trojand.rule "c $?s$2$0$?d$?d"
        ## Uppercase First Letter, Append 4 Digits,Symbol
          mp64.exe -o trojand.rule "c $1$9$?d$?d$?s"
          mp64.exe -o trojand.rule "c $2$0$?d$?d$?s"
        ## Uppercase First Letter, 2 Symbols, Append 4 Digits
          mp64.exe -o trojand.rule "c $?s$?s$1$9$?d$?d"
          mp64.exe -o trojand.rule "c $?s$?s$2$0$?d$?d"
        ## Uppercase First Letter, Append 4 Digits, 2 Symbols
          mp64.exe -o trojand.rule "c $1$9$?d$?d$?s$?s"
          mp64.exe -o trojand.rule "c $2$0$?d$?d$?s$?s"
        ## Append 4 Digits
          mp64.exe -o trojand.rule "$1$9$?d$?d"
          mp64.exe -o trojand.rule "$2$0$?d$?d"
        ## Symbol, Append 4 Digits
          mp64.exe -o trojand.rule "$?s$1$9$?d$?d"
          mp64.exe -o trojand.rule "$?s$2$0$?d$?d"
        ## Append 4 Digits,Symbol
          mp64.exe -o trojand.rule "$1$9$?d$?d$?s"
          mp64.exe -o trojand.rule "$2$0$?d$?d$?s"
        ## 2 Symbols, Append 4 Digits
          mp64.exe -o trojand.rule "$?s$?s$1$9$?d$?d"
          mp64.exe -o trojand.rule "$?s$?s$2$0$?d$?d"
        ## Append 4 Digits, 2 Symbols
          mp64.exe -o trojand.rule "$1$9$?d$?d$?s$?s"
          mp64.exe -o trojand.rule "$2$0$?d$?d$?s$?s"
        ## ALL UPPERCASE, Append 4 Digits
          mp64.exe -o trojand.rule "u $1$9$?d$?d"
          mp64.exe -o trojand.rule "u $2$0$?d$?d"
        ## ALL UPPERCASE, Symbol, Append 4 Digits
          mp64.exe -o trojand.rule "u $?s$1$9$?d$?d"
          mp64.exe -o trojand.rule "u $?s$2$0$?d$?d"
        ## ALL UPPERCASE, Append 4 Digits,Symbol
          mp64.exe -o trojand.rule "u $1$9$?d$?d$?s"
          mp64.exe -o trojand.rule "u $2$0$?d$?d$?s"
        ## ALL UPPERCASE, 2 Symbols, Append 4 Digits
          mp64.exe -o trojand.rule "u $?s$?s$1$9$?d$?d"
          mp64.exe -o trojand.rule "u $?s$?s$2$0$?d$?d"
        ## ALL UPPERCASE, Append 4 Digits, 2 Symbols
          mp64.exe -o trojand.rule "u $1$9$?d$?d$?s$?s"
          mp64.exe -o trojand.rule "u $2$0$?d$?d$?s$?s"
        ## all lowercase, Append 4 Digits
          mp64.exe -o trojand.rule "l $1$9$?d$?d"
          mp64.exe -o trojand.rule "l $2$0$?d$?d"
        ## all lowercase, Symbol, Append 4 Digits
          mp64.exe -o trojand.rule "l $?s$1$9$?d$?d"
          mp64.exe -o trojand.rule "l $?s$2$0$?d$?d"
        ## all lowercase, Append 4 Digits,Symbol
          mp64.exe -o trojand.rule "l $1$9$?d$?d$?s"
          mp64.exe -o trojand.rule "l $2$0$?d$?d$?s"
        ## all lowercase, 2 Symbols, Append 4 Digits
          mp64.exe -o trojand.rule "l $?s$?s$1$9$?d$?d"
          mp64.exe -o trojand.rule "l $?s$?s$2$0$?d$?d"
        ## all lowercase, Append 4 Digits, 2 Symbols
          mp64.exe -o trojand.rule "l $1$9$?d$?d$?s$?s"
          mp64.exe -o trojand.rule "l $2$0$?d$?d$?s$?s"
      ```
      ```
      # Aggresive Numbers
        ## Uppercase First Letter, Append <1-4> Digits
          mp64.exe -o trojand.rule "c $?d"
          mp64.exe -o trojand.rule "c $?d$?d"
          mp64.exe -o trojand.rule "c $?d$?d$?d"
          mp64.exe -o trojand.rule "c $?d$?d$?d$?d"
        ## Uppercase First Letter, Symbol, Append <1-4> Digits
          mp64.exe -o trojand.rule "c $?s$?d"
          mp64.exe -o trojand.rule "c $?s$?d$?d"
          mp64.exe -o trojand.rule "c $?s$?d$?d$?d"
          mp64.exe -o trojand.rule "c $?s$?d$?d$?d$?d"
        ## Uppercase First Letter, Append <1-4> Digits,Symbol
          mp64.exe -o trojand.rule "c $?d$?s"
          mp64.exe -o trojand.rule "c $?d$?d$?s"
          mp64.exe -o trojand.rule "c $?d$?d$?d$?s"
          mp64.exe -o trojand.rule "c $?d$?d$?d$?d$?s"
        ## Uppercase First Letter, 2 Symbols, Append <1-3> Digits
          mp64.exe -o trojand.rule "c $?s$?s$?d"
          mp64.exe -o trojand.rule "c $?s$?s$?d$?d"
          mp64.exe -o trojand.rule "c $?s$?s$?d$?d$?d"
        ## Uppercase First Letter, Append <1-3> Digits, 2 Symbols
          mp64.exe -o trojand.rule "c $?d$?s$?s"
          mp64.exe -o trojand.rule "c $?d$?d$?s$?s"
          mp64.exe -o trojand.rule "c $?d$?d$?d$?s$?s"
        ## Append <1-4> Digits
          mp64.exe -o trojand.rule "$?d"
          mp64.exe -o trojand.rule "$?d$?d"
          mp64.exe -o trojand.rule "$?d$?d$?d"
          mp64.exe -o trojand.rule "$?d$?d$?d$?d"
        ## Symbol, Append <1-4> Digits
          mp64.exe -o trojand.rule "$?s$?d"
          mp64.exe -o trojand.rule "$?s$?d$?d"
          mp64.exe -o trojand.rule "$?s$?d$?d$?d"
          mp64.exe -o trojand.rule "$?s$?d$?d$?d$?d"
        ## Append <1-4> Digits,Symbol
          mp64.exe -o trojand.rule "$?d$?s"
          mp64.exe -o trojand.rule "$?d$?d$?s"
          mp64.exe -o trojand.rule "$?d$?d$?d$?s"
          mp64.exe -o trojand.rule "$?d$?d$?d$?d$?s"
        ## 2 Symbols, Append <1-3> Digits
          mp64.exe -o trojand.rule "$?s$?s$?d"
          mp64.exe -o trojand.rule "$?s$?s$?d$?d"
          mp64.exe -o trojand.rule "$?s$?s$?d$?d$?d"
        ## Append <1-3> Digits, 2 Symbols
          mp64.exe -o trojand.rule "$?d$?s$?s"
          mp64.exe -o trojand.rule "$?d$?d$?s$?s"
          mp64.exe -o trojand.rule "$?d$?d$?d$?s$?s"
        ## ALL UPPERCASE, Append <1-4> Digits
          mp64.exe -o trojand.rule "u $?d"
          mp64.exe -o trojand.rule "u $?d$?d"
          mp64.exe -o trojand.rule "u $?d$?d$?d"
          mp64.exe -o trojand.rule "u $?d$?d$?d$?d"
        ## ALL UPPERCASE, Symbol, Append <1-4> Digits
          mp64.exe -o trojand.rule "u $?s$?d"
          mp64.exe -o trojand.rule "u $?s$?d$?d"
          mp64.exe -o trojand.rule "u $?s$?d$?d$?d"
          mp64.exe -o trojand.rule "u $?s$?d$?d$?d$?d"
        ## ALL UPPERCASE, Append <1-4> Digits,Symbol
          mp64.exe -o trojand.rule "u $?d$?s"
          mp64.exe -o trojand.rule "u $?d$?d$?s"
          mp64.exe -o trojand.rule "u $?d$?d$?d$?s"
          mp64.exe -o trojand.rule "u $?d$?d$?d$?d$?s"
        ## ALL UPPERCASE, 2 Symbols, Append <1-3> Digits
          mp64.exe -o trojand.rule "u $?s$?s$?d"
          mp64.exe -o trojand.rule "u $?s$?s$?d$?d"
          mp64.exe -o trojand.rule "u $?s$?s$?d$?d$?d"
        ## ALL UPPERCASE, Append <1-3> Digits, 2 Symbols
          mp64.exe -o trojand.rule "u $?d$?s$?s"
          mp64.exe -o trojand.rule "u $?d$?d$?s$?s"
          mp64.exe -o trojand.rule "u $?d$?d$?d$?s$?s"
        ## all lowercase, Append <1-4> Digits
          mp64.exe -o trojand.rule "l $?d"
          mp64.exe -o trojand.rule "l $?d$?d"
          mp64.exe -o trojand.rule "l $?d$?d$?d"
          mp64.exe -o trojand.rule "l $?d$?d$?d$?d"
        ## all lowercase, Symbol, Append <1-4> Digits
          mp64.exe -o trojand.rule "l $?s$?d"
          mp64.exe -o trojand.rule "l $?s$?d$?d"
          mp64.exe -o trojand.rule "l $?s$?d$?d$?d"
          mp64.exe -o trojand.rule "l $?s$?d$?d$?d$?d"
        ## all lowercase, Append <1-4> Digits,Symbol
          mp64.exe -o trojand.rule "l $?d$?s"
          mp64.exe -o trojand.rule "l $?d$?d$?s"
          mp64.exe -o trojand.rule "l $?d$?d$?d$?s"
          mp64.exe -o trojand.rule "l $?d$?d$?d$?d$?s"
        ## all lowercase, 2 Symbols, Append <1-3> Digits
          mp64.exe -o trojand.rule "l $?s$?s$?d"
          mp64.exe -o trojand.rule "l $?s$?s$?d$?d"
          mp64.exe -o trojand.rule "l $?s$?s$?d$?d$?d"
        ## all lowercase, Append <1-3> Digits, 2 Symbols
          mp64.exe -o trojand.rule "l $?d$?s$?s"
          mp64.exe -o trojand.rule "l $?d$?d$?s$?s"
          mp64.exe -o trojand.rule "l $?d$?d$?d$?s$?s"
      ```
      ```
      # Specific to a country's mobile number format
        ## i.e for Mongolia
          mp64.exe -o trojand.rule "$d$?d$?d$?d$?d$?d$?d$?d"
          mp64.exe -o trojand.rule "$9$7$6$d$?d$?d$?d$?d$?d$?d$?d"
      ```

1. How to use this
	* It is NOT recommended to paste all of the above to generate one massive rule file as this would either take ages to load in hashcat
		* Try to paste portions of it only
		* Keep the size of the rule file to 50MB-100MB MAX. A 100MB Rule file took 
			* A 100MB Rule file took ~5mins to load in hashcat on my PC
		* But you may have a beast of a PC, dedicated cracking rig or a cluster of rigs. If yes, disregard this recommendation
	* It is recommended to use a custom wordlists containing a few words
	* You can of course use `cewl` but I recommend you make it simpler
	* A simple wordlist might include
		* Country
		* Capital City
		* Current City
		* Top 3 other famous/major cities within the country
		* Company Name (No Space)
		* Company Abbreviation
		* Other observed password formats of users (After post-exploitation)
	* You can use this with bigger wordlists, it would take a long time though
	* Nowadays, Passwords usually need an Uppercase character. It is recommeded to prioritize the rules that converts the first character to Uppercase
		* Try to crack some passwords using the basic Uppercase first letter rule and the rule without changing any case
			* If none gets cracked in the all lowercase rule, this MIGHT be a hint that an Uppercase character is enforced in that organization.
		* The "# Realistic years only (For smaller rules)" section is quick to generate, load and gives the most results.

1. Example commands after build the wordlist:
    * `.\hashcat.exe -a 0 -m 1000 --force -O .\dumped_hashes.txt .\uber_targeted_custom_wordlist.dict -r .\trojand.rule`
