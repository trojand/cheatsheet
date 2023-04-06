

# WinDbg

## Shortcut Keys
* ++f6++ : Attach a process
* ++Ctrl+Break++ : Break (Force breakpoint)


## Basic commands/shortcuts
* `g`  : Go/Continue from breakpoint
* `u` : Unassemble (View/Display the assembly translation from memory)
    * `u` : Display from EIP
    * `u <address/symbol>`
        * `u kernel32!GetCurrentThread`
* `d<X>` : Read process memory content
    * `db <args>` : Display bytes
    * `dw <args>` : Display WORD  (2 bytes)
    * `dd <args>` : Display DWORD (4 bytes)
    * `dq <args>` : Display QWORD (8 bytes)
    * `dc <args>` : Display DWORD w/ASCII (8 bytes)
    * `dW <args>` : Display WORD  w/ASCII (2 bytes)
    * `d<X> <address/symbol+0xOFFSET> <L<X>>` : Common Arguments
    * `d<X> KERNELBASE+0x40 L8`
    * `d<X> poi(esp) L4`
    * Notes:
        * `poi(X)` : Pointer to Data
        * `L<X>` : Display Length depends on the value of X in `d<X>`
* `dt <structure>`: Display Type (Display Structure)
    * `dt ntdll!_TEB`
    * `dt <structure> @$teb` : To get address if field is a Ptr(Pointer)
    * `dt <structure> <@$teb> <field>` : For specific field only
    * Notes:
        * `?? sizeof(<structure>)` : To get size of structure
* `e<X> <address/register>` : Edit memory
    * `ed esp 50505050`
    * `ea esp "hello"`
* `s -<X> 0 L?80000000 <bytes/keyword>` : Search in memory. "0 L?80000000" means whole memory space
    * `s -d L?80000000 50505050`
    * `s -a L?80000000 "trojand"`
* `r` : Inspect Registers
    * `r esp`
    * `r esp=50505050` : Editing Registers
* `b<X> <args>` : Breakpoint
    * `bp <symbol/address>` : Insert breakpoint
    * `bp kernel32!ReadFile`
    * `bl` : List breakpoints
    * `bd <#>` : Disable breakpoint <number>
    * `be <#>` : Enable breakpoint <number>
    * `be 0`
    * `bc <#>` : Clear breakpoint number
    * `bc *` : Clear all breakpoints
    * `bu <module>` : Breakpoint at an unresolved endpoint(module that is not yet loaded)
    * `ba <e/w/r> <bytes> <module/address>` : Hardware breakpoints
        * Use to monitor access and changes in memory
        * Does not alter code to put `INT 3` instruction, see it as it is
        * `ba e 1 kernel32!WriteFile`
        * `ba w 2 <address>` : monitor if first letter will be modified(edited) in memory. i.e. editing notepad without saving
    * Breakpoint-based actions:
        * `bp <symbol/address> "<args>"`
        * `bp <symbol/address> "<.if (<condition>) {<if_condition_met_args>} .else {<else_condition_met_args>}>"`
        * ```
            bp kernel32!WriteFile ".if (poi(esp + 0x0C) == 4) {.printf \"++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++\\nHey it's 4 bytes! Stopping at breakpoint now...\\n++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++\\n\"} .else {.printf \"++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++\\nThe number of bytes written is %p \\n++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++\\n\",poi(esp + 0x0C);.echo;gc;}"
            ```
* `p` : Executes single instruction on a breakpoint. Steps *over* the function call.
* `t` : Executes single instruction on a breakpoint. Steps *inTo* the function call.
* `pt` : Step to next *return* (`ret`) instruction
* `ph` : Step to next *brancHing* (`je/jne`) instruction

* `lm` : Display **L**oaded **M**odules
    * `lm m <module_keywo*>` : Browse Modules
    * `lm m kernel*`
    * `x <module>!<symbo*>` : e**X**amine symbols
    * `x kernelbase!String*`
    * `x KERNELBASE!StringCchLength`
    * `.reload /f` : force reload modules (if not yet loaded)

* `? <hex> <operand> <hex>` : WinDbg Calculation
    * `? b - 1` : Equals `a`
* `? <hex>` : From hex to hex but producing decimal value on the left of `=`.
    * `? a` : `10 = 0000000a`
* `? 0n<decimal>` : From decimal to hex.
    * `? 0n10` : `10 = 0000000a`
* `? 0n10` : From binary to hex but producing decimal value on the left of `=`. 
    * `? 0y1111` : `15 = 0000000f`
* `.formats <hex>` : Display format in different types
    * `.formats 54>` : below
        ```
        Evaluate expression:
        Hex:     00000054
        Decimal: 84
        Octal:   00000000124
        Binary:  00000000 00000000 00000000 01010100
        Chars:   ...T
        Time:    Wed Dec 31 16:01:24 1969
        Float:   low 1.17709e-043 high 0
        Double:  4.15015e-322
        ```
* Pseudo Registers(Variables) : `@$t0` to `@$t19`
    * `r @$t0 = (5454 - 54) * 0n10`
    * `r @$t0` : `$t0=00034800`
    * `r @$t1 = @$t0 >> 8 `
    * `r @$t1` : `$t1=00000348`

* `.cls` : clear screen

