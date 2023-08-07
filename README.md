# radare2-cheatsheet

Radare2
=======

#### radare2 

?                       : help
? 0x6262616a            : prints whatever 0x6262616a can mean, e.g. "jabb"
? 0x10 + 6              : calculates and converts to various numbering systems etc
?v sym.imp.func_name    : Get address of func_name@PLT e.g. ?v sym.main

s?                      : help regarding seek command
a?                      : help regarding analyze command

s <tab>                 : seeks to find symbols
s main<tab>             : seeks to find symbols that start with main with completion
s <funcname>            : if <funcname> exists, seeks to its memory location
s 0x404da7              : seek to this address
s rip                   : seeks to RIP address when the app is being debugged

aaa                     : analyze binary
afl                     : analyze functions list -> list all functions
afl ~main               : list all functions and grep for "main"
af @ sym.main           : analyze main function
axt <address>           : find data/code references to this address

pdf                     : disassemble function
pdf @main               : disassemble main function
pdf @0xdeadbeef         : disassemble function at memory address
pd 10                   : disassemble 10 lines from current location (remember s 0x404da7)
pd -15                  : disassemble the previous 15 lines from current location
pd 10 @0x404bef         : disassemble 10 lines @ location

px @0x00404996          : do hexdump at address
px @rbp-0xc             : do hexdump at rbp - 0xc

iz                      : equivalent of strings command
iz ~password            : find "password" string in strings
iz ~\;\(                : find ";(" string in strings
                          NOTE: the second column is the location of the string result 
                          so you can get the address of where the string is and do an
                          `axt <address>` or `s <address>` and then `pd 10` 

CC user=jabb @location  : put comment "user=jabb" at memory address. If address is omitted, it puts at current

V                       : for visual mode
V!                      : for visual mode like ollydbg etc
VV!                     : for visual graph mode
space                   : to toggle from visual and graph mode
q                       : leaves and gives command line interpreter back
e asm.describe=1        : to describe each assembly instruction. From visual mode you need to q afterwards
e asm.describe=0        : reverts
!                       : to run system commands as system(3), e.g. !ls, or !rahash2 -E rot -S s:13 -s ‘Megabeets\n’

il                      : show the libraries that the binary uses
ii                      : shows the imports information that the binary uses

i~pic                   : check if Position Independent Code
i                       : check everything
ie                      : get address of entry point
iS                      : show sections with permissions (r/x/w)
i~canary                : check canaries
i~nx                    : check if compiled with NX

#### searching

e search.*              : Edit searching configuration
/?                      : List search subcommands
/ string                : Search string in memory/binary
/R [?]                  : Search for ROP gadgets
/R/                     : Search for ROP gadgets with a regular expressions

#### color schemes
eco                     : list available color schemes
eco theme-name          : activate color scheme (i like consonance, onedark, solarized, zenburn and twilight)
V! and then R           : go through the available color schemes

### Additional commands that run on debug mode

r2 -d ./program         : to debug program, or

ood                     : reopen binary in debug mode
oo                      : reopen current file (kill+fork in debugger)
oo+                     : reopen current file in rw

d?                      : to see all debug commands
db address              : break at address
dc                      : debug, continue execution. do d? to see what you get
ctrl +c                 : breaks
dcu main                : debug, continue execution until main function
dcu address             : debug, continue execution until address
dcr                     : continue until ret (uses step over)
dbt [?]                 : display backtrace based on dbg.btdepth and dbg.btalgo
doo [args]              : Reopen in debugger mode with args
ds                      : Step one instruction
dso                     : Step over
dbta                    : show stack frames backtrace

dr                      : show all general purpose registers
dr eax                  : show eax etc

afvd                    : show all function variables
afvn [new_name] [old]   : rename variable
wx 0x1 @address         : write 1 at the address (get address from afvd)

s                       : in Visual mode, you execute the instruction
S                       : in Visual mode, you step over the call and continue instruction
e dbg.slow=true         : and
Vpp                     : in order to get a nice stack frame view in visual mode while executing
F2                      : in Visual mode, to set a breakpoint while in visual mode. e.g.: press 'c' for cursor mode in Visual mode and then
                          tab to switch to the code section and then F2 to set a breakpoint

wx 90 @0x8000ffff       : write the 0x90 (nop) at the specific location and overwrite whatever is there
wa jmp 0x08048641 @loc  : write assembly jmp location at memory location (need to be writable with: r2 -w filename.bin)
e io.cache=1            : set so that the write is being done only in memory and not on the file

dm                      : Show memory maps
dmm                     : List modules (libraries, binaries loaded in memory)
dmi [addr|libn] [symna] : List symbols of target lib

ragg2 (outside)         : creates a De Brujin Sequence, cyclic/unique pattern.
ragg2 -P 100 -r         : creates a cyclic pattern of 100 bytes printed as raw bytes
ragg2 -q 0x414b4141     : find the offset of this return (e.g. EIP address)

afi.                    : from the current location, get the current function that this belong to
s `afi.`                : seek to the start of the function that this location belongs to
s @ `afi.`              : get the address of the start of the function that this location belongs to without seeking to it
s @ `afi. @ 0x080491a5` : get the address of the start of the function that location 0x080491a5 belongs to


### Cheatsheet? 

r2 -d megabeets_0x1
aaa
e dbg.slow=true
afl
dcu main
Vpp or V! and then | and g
c: cursor mode to move around

tab: to move from the call stack to the code below. Stack has high memory down and low memory addresses up so it grows

up as you see it in the debugger
s: to step instruction, etc.

Radare for exploitation cheatsheet from megabeets:
$ rabin2 -I ./program — Binary info (same as i from radare2 shell)
ii [q] – Imports
?v sym.imp.func_name — Get address of func_name@PLT
?v reloc.func_name — Get address of func_name@GOT
ie [q] — Get address of Entrypoint
iS — Show sections with permissions (r/x/w)
i~canary — Check canaries
i~pic — Check if Position Independent Code
i~nx — Check if compiled with NX

Debugging
dc — Continue execution
dcu addr – Continue execution until address
dcr — Continue until ret (uses step over)
dbt [?] — Display backtrace based on dbg.btdepth and dbg.btalgo
doo [args] — Reopen in debugger mode with args
ds — Step one instruction
dso — Step over

Visual Modes
pdf @ addr — Print the assembly of a function in the given offset
V — Visual mode, use p/P to toggle between different modes
VV — Visual Graph mode, navigating through ASCII graphs
V! — Visual panels mode. Very useful for exploitation

Strings equivalent, extract strings from binary:
rabin2 -zz data.bin

rabin2 -zz data.bin | grep =wide - this can be used to extract wide characters that are unprintable:w

