# Basic Egghunting

Source: CaptMeelo[^1]



## Commands learned
* Alternative to pattern create:
    * `/usr/share/metasploit-framework/tools/exploit/pattern_create.rb -l <number_of_buffer>`
    * is
    * `#!python !mona pc <number_of_buffer>`
* Alternative to pattern offset
    * `/usr/share/metasploit-framework/tools/exploit/pattern_offset.rb -l <number_of_buffer> -q <value_in_register>`
    * is
    * `#!python !mona findmsp` @ mona itself after sending the pattern
        * This can be used in conjunction with pattern_create + it tells you more details such as other registers and the size of the whole payload
* `#!python !mona jmp -r esp -m 'essfunc.dll'`
* SHORT JUMP
    * `#!asm \xEB<offset>`
       * offset = i.e. 50 bytes back so '-50' convert to hex using the calculator site is 'FFFFFFFFFFFFFFCE' so it is `#!asm \xCE` therefore making the whole command `#!asm \xEB\xCE`,
       * This would technically only move you back 48 bytes since `#!asm \xEB\xCE` itself consumes 2 byes and will be counted in walking back
           * calculator site [^2]
* `#!python !mona egg -t <keyword_you_would_like_to_use>`
    * i.e. `#!python !mona egg -t Bellend`
    * then send shellcode via other means like via other COMMAND (i.e. "STATS "+"*BellendBellend*"+shellcode)
    * make space for shellcode


[^1]: [CaptMeelo](https://captmeelo.com/exploitdev/osceprep/2018/06/29/vulnserver-kstet.html)
[^2]: [Binary Hex Converter](https://www.binaryhexconverter.com/decimal-to-hex-converter)