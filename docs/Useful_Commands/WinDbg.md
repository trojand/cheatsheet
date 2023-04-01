# WinDbg

## Basic commands/shortcuts
* ++f6++ : Attach a process
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


* `.reload /f` : reload modules 
* `.cls` : clear screen
