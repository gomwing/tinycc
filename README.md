Tiny C Compiler Reference Documentation       <!-- a.summary-letter {text-decoration: none} pre.display {font-family: serif} pre.format {font-family: serif} pre.menu-comment {font-family: serif} pre.menu-preformatted {font-family: serif} pre.smalldisplay {font-family: serif; font-size: smaller} pre.smallexample {font-size: smaller} pre.smallformat {font-family: serif; font-size: smaller} pre.smalllisp {font-size: smaller} span.roman {font-family:serif; font-weight:normal;} span.sansserif {font-family:sans-serif; font-weight:normal;} ul.toc {list-style: none} -->

\[[Top](#SEC_Top "Cover (top) of document")\] 
\[[Contents](#SEC_Contents "Table of contents")\] 
\[[Index](#SEC36 "Index")\] 
\[ [?](#SEC_About "About (help)") \]

Tiny C Compiler Reference Documentation
=======================================

This manual documents version of the Tiny C Compiler.

[1\. Introduction](#SEC1)  Introduction to tcc.

[2\. Command line invocation](#SEC2) Invocation of tcc (command line, options).

[3\. C language support](#SEC5) ANSI C and extensions.

[4\. TinyCC Assembler](#SEC10) Assembler syntax.

[5\. TinyCC Linker](#SEC16) Output file generation and supported targets.

[6\. TinyCC Memory and Bound checks](#SEC21) Automatic bounds-checking of C code.

[7\. The `libtcc` library](#SEC22) The libtcc library.

[8\. Developer's guide](#SEC23) Guide for Developers.

* * *

\[ [<](#SEC_Top "Previous section in reading order") \]
\[ [>](#SEC2 "Next section in reading order") \]\[ << \]
\[ [Up](#SEC_Top "Up section") \]\[ [>>](#SEC2 "Next chapter") \]                 
\[[Top](#SEC_Top "Cover (top) of document")\]
\[[Contents](#SEC_Contents "Table of contents")\]
\[[Index](#SEC36 "Index")\]\[ [?](#SEC_About "About (help)") \]

1\. Introduction
================

TinyCC (aka TCC) is a small but hyper fast C compiler. Unlike other C compilers, it is meant to be self-relying: you do not need an external assembler or linker because TCC does that for you.

TCC compiles so _fast_ that even for big projects `Makefile`s may not be necessary.

TCC not only supports ANSI C, but also most of the new ISO C99 standard and many GNUC extensions including inline assembly.

TCC can also be used to make _C scripts_, i.e. pieces of C source that you run as a Perl or Python script. Compilation is so fast that your script will be as fast as if it was an executable.

TCC can also automatically generate memory and bound checks (see section [TinyCC Memory and Bound checks](#SEC21)) while allowing all C pointers operations. TCC can do these checks even if non patched libraries are used.

With `libtcc`, you can use TCC as a backend for dynamic code generation (see section [The `libtcc` library](#SEC22)).

TCC mainly supports the i386 target on Linux and Windows. There are alpha ports for the ARM (`arm-tcc`) and the TMS320C67xx targets (`c67-tcc`). More information about the ARM port is available at [http://lists.gnu.org/archive/html/tinycc-devel/2003-10/msg00044.html](http://lists.gnu.org/archive/html/tinycc-devel/2003-10/msg00044.html).

For usage on Windows, see also tcc-win32.txt.

* * *

\[[<](#SEC1 "Previous section in reading order") \]
\[ [>](#SEC3 "Next section in reading order") \]
\[ [<<](#SEC1 "Beginning of this chapter or previous chapter") \]
\[ [Up](#SEC_Top "Up section") \]
\[ [>>](#SEC5 "Next chapter") \]

 

 

 

 

\[[Top](#SEC_Top "Cover (top) of document")\]
\[[Contents](#SEC_Contents "Table of contents")\]
\[[Index](#SEC36 "Index")\]
\[ [?](#SEC_About "About (help)") \]

2\. Command line invocation
===========================

* * *

\[ [<](#SEC2 "Previous section in reading order") \]
\[ [>](#SEC4 "Next section in reading order") \]
\[ [<<](#SEC2 "Beginning of this chapter or previous chapter") \]
\[ [Up](#SEC2 "Up section") \]
\[ [>>](#SEC5 "Next chapter") \]

 

 

 

 

\[[Top](#SEC_Top "Cover (top) of document")\]
\[[Contents](#SEC_Contents "Table of contents")\]
\[[Index](#SEC36 "Index")\]
\[ [?](#SEC_About "About (help)") \]

2.1 Quick start
---------------

 

usage: tcc \[options\] \[infile1 infile2…\] \[‘\-run’ infile args…\]

TCC options are a very much like gcc options. The main difference is that TCC can also execute directly the resulting program and give it runtime arguments.

Here are some examples to understand the logic:

`‘tcc -run a.c’`

Compile ‘a.c’ and execute it directly

`‘tcc -run a.c arg1’`

Compile a.c and execute it directly. arg1 is given as first argument to the `main()` of a.c.

`‘tcc a.c -run b.c arg1’`

Compile ‘a.c’ and ‘b.c’, link them together and execute them. arg1 is given as first argument to the `main()` of the resulting program.

`‘tcc -o myprog a.c b.c’`

Compile ‘a.c’ and ‘b.c’, link them and generate the executable ‘myprog’.

`‘tcc -o myprog a.o b.o’`

link ‘a.o’ and ‘b.o’ together and generate the executable ‘myprog’.

`‘tcc -c a.c’`

Compile ‘a.c’ and generate object file ‘a.o’.

`‘tcc -c asmfile.S’`

Preprocess with C preprocess and assemble ‘asmfile.S’ and generate object file ‘asmfile.o’.

`‘tcc -c asmfile.s’`

Assemble (but not preprocess) ‘asmfile.s’ and generate object file ‘asmfile.o’.

`‘tcc -r -o ab.o a.c b.c’`

Compile ‘a.c’ and ‘b.c’, link them together and generate the object file ‘ab.o’.

Scripting:

TCC can be invoked from _scripts_, just as shell scripts. You just need to add `#!/usr/local/bin/tcc -run` at the start of your C source:

 

#!/usr/local/bin/tcc -run
#include <stdio.h>

int main() 
{
    printf("Hello World\\n");
    return 0;
}

TCC can read C source code from _standard input_ when ‘\-’ is used in place of ‘infile’. Example:

 

echo 'main(){puts("hello");}' | tcc -run -

* * *

\[ [<](#SEC3 "Previous section in reading order") \]
\[ [>](#SEC5 "Next section in reading order") \]
\[ [<<](#SEC2 "Beginning of this chapter or previous chapter") \]
\[ [Up](#SEC2 "Up section") \]
\[ [>>](#SEC5 "Next chapter") \]

 

 

 

 

\[[Top](#SEC_Top "Cover (top) of document")\]
\[[Contents](#SEC_Contents "Table of contents")\]
\[[Index](#SEC36 "Index")\]
\[ [?](#SEC_About "About (help)") \]

2.2 Option summary
------------------

General Options:

‘\-v’

Display current TCC version, increase verbosity.

‘\-c’

Generate an object file (‘\-o’ option must also be given).

‘\-o outfile’

Put object file, executable, or dll into output file ‘outfile’.

‘\-Bdir’

Set the path where the tcc internal libraries can be found (default is ‘PREFIX/lib/tcc’).

‘\-bench’

Output compilation statistics.

‘\-run source \[args...\]’

Compile file source and run it with the command line arguments args. In order to be able to give more than one argument to a script, several TCC options can be given _after_ the ‘\-run’ option, separated by spaces. Example:

 

tcc "-run -L/usr/X11R6/lib -lX11" ex4.c

In a script, it gives the following header:

 

#!/usr/local/bin/tcc -run -L/usr/X11R6/lib -lX11
#include <stdlib.h>
int main(int argc, char \*\*argv)
{
    ...
}

Preprocessor options:

‘\-Idir’

Specify an additional include path. Include paths are searched in the order they are specified.

System include paths are always searched after. The default system include paths are: ‘/usr/local/include’, ‘/usr/include’ and ‘PREFIX/lib/tcc/include’. (‘PREFIX’ is usually ‘/usr’ or ‘/usr/local’).

‘\-Dsym\[=val\]’

Define preprocessor symbol ‘sym’ to val. If val is not present, its value is ‘1’. Function-like macros can also be defined: ‘\-DF(a)=a+1’

‘\-Usym’

Undefine preprocessor symbol ‘sym’.

Compilation flags:

Note: each of the following warning options has a negative form beginning with ‘\-fno-’.

‘\-funsigned-char’

Let the `char` type be unsigned.

‘\-fsigned-char’

Let the `char` type be signed.

‘\-fno-common’

Do not generate common symbols for uninitialized data.

‘\-fleading-underscore’

Add a leading underscore at the beginning of each C symbol.

Warning options:

‘\-w’

Disable all warnings.

Note: each of the following warning options has a negative form beginning with ‘\-Wno-’.

‘\-Wimplicit-function-declaration’

Warn about implicit function declaration.

‘\-Wunsupported’

Warn about unsupported GCC features that are ignored by TCC.

‘\-Wwrite-strings’

Make string constants be of type `const char *` instead of `char *`.

‘\-Werror’

Abort compilation if warnings are issued.

‘\-Wall’

Activate all warnings, except ‘\-Werror’, ‘\-Wunusupported’ and ‘\-Wwrite-strings’.

Linker options:

‘\-Ldir’

Specify an additional static library path for the ‘\-l’ option. The default library paths are ‘/usr/local/lib’, ‘/usr/lib’ and ‘/lib’.

‘\-lxxx’

Link your program with dynamic library libxxx.so or static library libxxx.a. The library is searched in the paths specified by the ‘\-L’ option.

‘\-shared’

Generate a shared library instead of an executable (‘\-o’ option must also be given).

‘\-static’

Generate a statically linked executable (default is a shared linked executable) (‘\-o’ option must also be given).

‘\-rdynamic’

Export global symbols to the dynamic linker. It is useful when a library opened with `dlopen()` needs to access executable symbols.

‘\-r’

Generate an object file combining all input files (‘\-o’ option must also be given).

‘\-Wl,-Ttext,address’

Set the start of the .text section to address.

‘\-Wl,--oformat,fmt’

Use fmt as output format. The supported output formats are:

`elf32-i386`

ELF output format (default)

`binary`

Binary image (only for executable output)

`coff`

COFF output format (only for executable output for TMS320C67xx target)

Debugger options:

‘\-g’

Generate run time debug information so that you get clear run time error messages: `test.c:68: in function 'test5()': dereferencing invalid pointer` instead of the laconic `Segmentation fault`.

‘\-b’

Generate additional support code to check memory allocations and array/pointer bounds. ‘\-g’ is implied. Note that the generated code is slower and bigger in this case.

‘\-bt N’

Display N callers in stack traces. This is useful with ‘\-g’ or ‘\-b’.

Note: GCC options ‘\-Ox’, ‘\-fx’ and ‘\-mx’ are ignored.

* * *

\[ [<](#SEC4 "Previous section in reading order") \]
\[ [>](#SEC6 "Next section in reading order") \]
\[ [<<](#SEC2 "Beginning of this chapter or previous chapter") \]
\[ [Up](#SEC_Top "Up section") \]
\[ [>>](#SEC10 "Next chapter") \]

 

 

 

 

\[[Top](#SEC_Top "Cover (top) of document")\]
\[[Contents](#SEC_Contents "Table of contents")\]
\[[Index](#SEC36 "Index")\]
\[ [?](#SEC_About "About (help)") \]

3\. C language support
======================

* * *

\[ [<](#SEC5 "Previous section in reading order") \]
\[ [>](#SEC7 "Next section in reading order") \]
\[ [<<](#SEC5 "Beginning of this chapter or previous chapter") \]
\[ [Up](#SEC5 "Up section") \]
\[ [>>](#SEC10 "Next chapter") \]

 

 

 

 

\[[Top](#SEC_Top "Cover (top) of document")\]
\[[Contents](#SEC_Contents "Table of contents")\]
\[[Index](#SEC36 "Index")\]
\[ [?](#SEC_About "About (help)") \]

3.1 ANSI C
----------

TCC implements all the ANSI C standard, including structure bit fields and floating point numbers (`long double`, `double`, and `float` fully supported).

* * *

\[ [<](#SEC6 "Previous section in reading order") \]
\[ [>](#SEC8 "Next section in reading order") \]
\[ [<<](#SEC5 "Beginning of this chapter or previous chapter") \]
\[ [Up](#SEC5 "Up section") \]
\[ [>>](#SEC10 "Next chapter") \]

 

 

 

 

\[[Top](#SEC_Top "Cover (top) of document")\]
\[[Contents](#SEC_Contents "Table of contents")\]
\[[Index](#SEC36 "Index")\]
\[ [?](#SEC_About "About (help)") \]

3.2 ISOC99 extensions
---------------------

TCC implements many features of the new C standard: ISO C99. Currently missing items are: complex and imaginary numbers and variable length arrays.

Currently implemented ISOC99 features:

*   64 bit `long long` types are fully supported.
*   The boolean type `_Bool` is supported.
*   `__func__` is a string variable containing the current function name.
*   Variadic macros: `__VA_ARGS__` can be used for function-like macros:
    
     
    
        #define dprintf(level, \_\_VA\_ARGS\_\_) printf(\_\_VA\_ARGS\_\_)
    
    `dprintf` can then be used with a variable number of parameters.
    
*   Declarations can appear anywhere in a block (as in C++).
*   Array and struct/union elements can be initialized in any order by using designators:
    
     
    
        struct { int x, y; } st\[10\] = { \[0\].x = 1, \[0\].y = 2 };
    
        int tab\[10\] = { 1, 2, \[5\] = 5, \[9\] = 9};
    
*   Compound initializers are supported:
    
     
    
        int \*p = (int \[\]){ 1, 2, 3 };
    
    to initialize a pointer pointing to an initialized array. The same works for structures and strings.
    
*   Hexadecimal floating point constants are supported:
    
     
    
              double d = 0x1234p10;
    
    is the same as writing
    
     
    
              double d = 4771840.0;
    
*   `inline` keyword is ignored.
*   `restrict` keyword is ignored.

* * *

\[ [<](#SEC7 "Previous section in reading order") \]
\[ [>](#SEC9 "Next section in reading order") \]
\[ [<<](#SEC5 "Beginning of this chapter or previous chapter") \]
\[ [Up](#SEC5 "Up section") \]
\[ [>>](#SEC10 "Next chapter") \]

 

 

 

 

\[[Top](#SEC_Top "Cover (top) of document")\]
\[[Contents](#SEC_Contents "Table of contents")\]
\[[Index](#SEC36 "Index")\]
\[ [?](#SEC_About "About (help)") \]

3.3 GNU C extensions
--------------------

TCC implements some GNU C extensions:

*   array designators can be used without '=':
    
     
    
        int a\[10\] = { \[0\] 1, \[5\] 2, 3, 4 };
    
*   Structure field designators can be a label:
    
     
    
        struct { int x, y; } st = { x: 1, y: 1};
    
    instead of
    
     
    
        struct { int x, y; } st = { .x = 1, .y = 1};
    
*   `\e` is ASCII character 27.
*   case ranges : ranges can be used in `case`s:
    
     
    
        switch(a) {
        case 1 … 9:
              printf("range 1 to 9\\n");
              break;
        default:
              printf("unexpected\\n");
              break;
        }
    
*   The keyword `__attribute__` is handled to specify variable or function attributes. The following attributes are supported:
    
    *   `aligned(n)`: align a variable or a structure field to n bytes (must be a power of two).
    *   `packed`: force alignment of a variable or a structure field to 1.
    *   `section(name)`: generate function or data in assembly section name (name is a string containing the section name) instead of the default section.
    *   `unused`: specify that the variable or the function is unused.
    *   `cdecl`: use standard C calling convention (default).
    *   `stdcall`: use Pascal-like calling convention.
    *   `regparm(n)`: use fast i386 calling convention. n must be between 1 and 3. The first n function parameters are respectively put in registers `%eax`, `%edx` and `%ecx`.
    *   `dllexport`: export function from dll/executable (win32 only)
    
    Here are some examples:
    
     
    
        int a \_\_attribute\_\_ ((aligned(8), section(".mysection")));
    
    align variable `a` to 8 bytes and put it in section `.mysection`.
    
     
    
        int my\_add(int a, int b) \_\_attribute\_\_ ((section(".mycodesection"))) 
        {
            return a + b;
        }
    
    generate function `my_add` in section `.mycodesection`.
    
*   GNU style variadic macros:
    
     
    
        #define dprintf(fmt, args…) printf(fmt, ## args)
    
        dprintf("no arg\\n");
        dprintf("one arg %d\\n", 1);
    
*   `__FUNCTION__` is interpreted as C99 `__func__` (so it has not exactly the same semantics as string literal GNUC where it is a string literal).
*   The `__alignof__` keyword can be used as `sizeof` to get the alignment of a type or an expression.
*   The `typeof(x)` returns the type of `x`. `x` is an expression or a type.
*   Computed gotos: `&&label` returns a pointer of type `void *` on the goto label `label`. `goto *expr` can be used to jump on the pointer resulting from `expr`.
*   Inline assembly with asm instruction:
    
     
    
    static inline void \* my\_memcpy(void \* to, const void \* from, size\_t n)
    {
    int d0, d1, d2;
    \_\_asm\_\_ \_\_volatile\_\_(
            "rep ; movsl\\n\\t"
            "testb $2,%b4\\n\\t"
            "je 1f\\n\\t"
            "movsw\\n"
            "1:\\ttestb $1,%b4\\n\\t"
            "je 2f\\n\\t"
            "movsb\\n"
            "2:"
            : "=&c" (d0), "=&D" (d1), "=&S" (d2)
            :"0" (n/4), "q" (n),"1" ((long) to),"2" ((long) from)
            : "memory");
    return (to);
    }
    
    TCC includes its own x86 inline assembler with a `gas`\-like (GNU assembler) syntax. No intermediate files are generated. GCC 3.x named operands are supported.
    
*   `__builtin_types_compatible_p()` and `__builtin_constant_p()` are supported.
*   `#pragma pack` is supported for win32 compatibility.

* * *

\[ [<](#SEC8 "Previous section in reading order") \]
\[ [>](#SEC10 "Next section in reading order") \]
\[ [<<](#SEC5 "Beginning of this chapter or previous chapter") \]
\[ [Up](#SEC5 "Up section") \]
\[ [>>](#SEC10 "Next chapter") \]

 

 

 

 

\[[Top](#SEC_Top "Cover (top) of document")\]
\[[Contents](#SEC_Contents "Table of contents")\]
\[[Index](#SEC36 "Index")\]
\[ [?](#SEC_About "About (help)") \]

3.4 TinyCC extensions
---------------------

*   `__TINYC__` is a predefined macro to `1` to indicate that you use TCC.
*   `#!` at the start of a line is ignored to allow scripting.
*   Binary digits can be entered (`0b101` instead of `5`).
*   `__BOUNDS_CHECKING_ON` is defined if bound checking is activated.

* * *

\[ [<](#SEC9 "Previous section in reading order") \]
\[ [>](#SEC11 "Next section in reading order") \]
\[ [<<](#SEC5 "Beginning of this chapter or previous chapter") \]
\[ [Up](#SEC_Top "Up section") \]
\[ [>>](#SEC16 "Next chapter") \]

 

 

 

 

\[[Top](#SEC_Top "Cover (top) of document")\]
\[[Contents](#SEC_Contents "Table of contents")\]
\[[Index](#SEC36 "Index")\]
\[ [?](#SEC_About "About (help)") \]

4\. TinyCC Assembler
====================

Since version 0.9.16, TinyCC integrates its own assembler. TinyCC assembler supports a gas-like syntax (GNU assembler). You can desactivate assembler support if you want a smaller TinyCC executable (the C compiler does not rely on the assembler).

TinyCC Assembler is used to handle files with ‘.S’ (C preprocessed assembler) and ‘.s’ extensions. It is also used to handle the GNU inline assembler with the `asm` keyword.

* * *

\[ [<](#SEC10 "Previous section in reading order") \]
\[ [>](#SEC12 "Next section in reading order") \]
\[ [<<](#SEC10 "Beginning of this chapter or previous chapter") \]
\[ [Up](#SEC10 "Up section") \]
\[ [>>](#SEC16 "Next chapter") \]

 

 

 

 

\[[Top](#SEC_Top "Cover (top) of document")\]
\[[Contents](#SEC_Contents "Table of contents")\]
\[[Index](#SEC36 "Index")\]
\[ [?](#SEC_About "About (help)") \]

4.1 Syntax
----------

TinyCC Assembler supports most of the gas syntax. The tokens are the same as C.

*   C and C++ comments are supported.
*   Identifiers are the same as C, so you cannot use '.' or '$'.
*   Only 32 bit integer numbers are supported.

* * *

\[ [<](#SEC11 "Previous section in reading order") \]
\[ [>](#SEC13 "Next section in reading order") \]
\[ [<<](#SEC10 "Beginning of this chapter or previous chapter") \]
\[ [Up](#SEC10 "Up section") \]
\[ [>>](#SEC16 "Next chapter") \]

 

 

 

 

\[[Top](#SEC_Top "Cover (top) of document")\]
\[[Contents](#SEC_Contents "Table of contents")\]
\[[Index](#SEC36 "Index")\]
\[ [?](#SEC_About "About (help)") \]

4.2 Expressions
---------------

*   Integers in decimal, octal and hexa are supported.
*   Unary operators: +, -, ~.
*   Binary operators in decreasing priority order:
    1.  \*, /, %
    2.  &, |, ^
    3.  +, -
*   A value is either an absolute number or a label plus an offset. All operators accept absolute values except '+' and '-'. '+' or '-' can be used to add an offset to a label. '-' supports two labels only if they are the same or if they are both defined and in the same section.

* * *

\[ [<](#SEC12 "Previous section in reading order") \]
\[ [>](#SEC14 "Next section in reading order") \]
\[ [<<](#SEC10 "Beginning of this chapter or previous chapter") \]
\[ [Up](#SEC10 "Up section") \]
\[ [>>](#SEC16 "Next chapter") \]

 

 

 

 

\[[Top](#SEC_Top "Cover (top) of document")\]
\[[Contents](#SEC_Contents "Table of contents")\]
\[[Index](#SEC36 "Index")\]
\[ [?](#SEC_About "About (help)") \]

4.3 Labels
----------

*   All labels are considered as local, except undefined ones.
*   Numeric labels can be used as local `gas`\-like labels. They can be defined several times in the same source. Use 'b' (backward) or 'f' (forward) as suffix to reference them:
    
     
    
     1:
          jmp 1b /\* jump to '1' label before \*/
          jmp 1f /\* jump to '1' label after \*/
     1:
    

* * *

\[ [<](#SEC13 "Previous section in reading order") \]
\[ [>](#SEC15 "Next section in reading order") \]
\[ [<<](#SEC10 "Beginning of this chapter or previous chapter") \]
\[ [Up](#SEC10 "Up section") \]
\[ [>>](#SEC16 "Next chapter") \]

 

 

 

 

\[[Top](#SEC_Top "Cover (top) of document")\]
\[[Contents](#SEC_Contents "Table of contents")\]
\[[Index](#SEC36 "Index")\]
\[ [?](#SEC_About "About (help)") \]

4.4 Directives
--------------

All directives are preceeded by a '.'. The following directives are supported:

*   .align n\[,value\]
*   .skip n\[,value\]
*   .space n\[,value\]
*   .byte value1\[,...\]
*   .word value1\[,...\]
*   .short value1\[,...\]
*   .int value1\[,...\]
*   .long value1\[,...\]
*   .quad immediate\_value1\[,...\]
*   .globl symbol
*   .global symbol
*   .section section
*   .text
*   .data
*   .bss
*   .fill repeat\[,size\[,value\]\]
*   .org n
*   .previous
*   .string string\[,...\]
*   .asciz string\[,...\]
*   .ascii string\[,...\]

* * *

\[ [<](#SEC14 "Previous section in reading order") \]
\[ [>](#SEC16 "Next section in reading order") \]
\[ [<<](#SEC10 "Beginning of this chapter or previous chapter") \]
\[ [Up](#SEC10 "Up section") \]
\[ [>>](#SEC16 "Next chapter") \]

 

 

 

 

\[[Top](#SEC_Top "Cover (top) of document")\]
\[[Contents](#SEC_Contents "Table of contents")\]
\[[Index](#SEC36 "Index")\]
\[ [?](#SEC_About "About (help)") \]

4.5 X86 Assembler
-----------------

All X86 opcodes are supported. Only ATT syntax is supported (source then destination operand order). If no size suffix is given, TinyCC tries to guess it from the operand sizes.

Currently, MMX opcodes are supported but not SSE ones.

* * *

\[ [<](#SEC15 "Previous section in reading order") \]
\[ [>](#SEC17 "Next section in reading order") \]
\[ [<<](#SEC10 "Beginning of this chapter or previous chapter") \]
\[ [Up](#SEC_Top "Up section") \]
\[ [>>](#SEC21 "Next chapter") \]

 

 

 

 

\[[Top](#SEC_Top "Cover (top) of document")\]
\[[Contents](#SEC_Contents "Table of contents")\]
\[[Index](#SEC36 "Index")\]
\[ [?](#SEC_About "About (help)") \]

5\. TinyCC Linker
=================

* * *

\[ [<](#SEC16 "Previous section in reading order") \]
\[ [>](#SEC18 "Next section in reading order") \]
\[ [<<](#SEC16 "Beginning of this chapter or previous chapter") \]
\[ [Up](#SEC16 "Up section") \]
\[ [>>](#SEC21 "Next chapter") \]

 

 

 

 

\[[Top](#SEC_Top "Cover (top) of document")\]
\[[Contents](#SEC_Contents "Table of contents")\]
\[[Index](#SEC36 "Index")\]
\[ [?](#SEC_About "About (help)") \]

5.1 ELF file generation
-----------------------

TCC can directly output relocatable ELF files (object files), executable ELF files and dynamic ELF libraries without relying on an external linker.

Dynamic ELF libraries can be output but the C compiler does not generate position independent code (PIC). It means that the dynamic library code generated by TCC cannot be factorized among processes yet.

TCC linker eliminates unreferenced object code in libraries. A single pass is done on the object and library list, so the order in which object files and libraries are specified is important (same constraint as GNU ld). No grouping options (‘\--start-group’ and ‘\--end-group’) are supported.

* * *

\[ [<](#SEC17 "Previous section in reading order") \]
\[ [>](#SEC19 "Next section in reading order") \]
\[ [<<](#SEC16 "Beginning of this chapter or previous chapter") \]
\[ [Up](#SEC16 "Up section") \]
\[ [>>](#SEC21 "Next chapter") \]

 

 

 

 

\[[Top](#SEC_Top "Cover (top) of document")\]
\[[Contents](#SEC_Contents "Table of contents")\]
\[[Index](#SEC36 "Index")\]
\[ [?](#SEC_About "About (help)") \]

5.2 ELF file loader
-------------------

TCC can load ELF object files, archives (.a files) and dynamic libraries (.so).

* * *

\[ [<](#SEC18 "Previous section in reading order") \]
\[ [>](#SEC20 "Next section in reading order") \]
\[ [<<](#SEC16 "Beginning of this chapter or previous chapter") \]
\[ [Up](#SEC16 "Up section") \]
\[ [>>](#SEC21 "Next chapter") \]

 

 

 

 

\[[Top](#SEC_Top "Cover (top) of document")\]
\[[Contents](#SEC_Contents "Table of contents")\]
\[[Index](#SEC36 "Index")\]
\[ [?](#SEC_About "About (help)") \]

5.3 PE-i386 file generation
---------------------------

TCC for Windows supports the native Win32 executable file format (PE-i386). It generates EXE files (console and gui) and DLL files.

For usage on Windows, see also tcc-win32.txt.

* * *

\[ [<](#SEC19 "Previous section in reading order") \]
\[ [>](#SEC21 "Next section in reading order") \]
\[ [<<](#SEC16 "Beginning of this chapter or previous chapter") \]
\[ [Up](#SEC16 "Up section") \]
\[ [>>](#SEC21 "Next chapter") \]

 

 

 

 

\[[Top](#SEC_Top "Cover (top) of document")\]
\[[Contents](#SEC_Contents "Table of contents")\]
\[[Index](#SEC36 "Index")\]
\[ [?](#SEC_About "About (help)") \]

5.4 GNU Linker Scripts
----------------------

Because on many Linux systems some dynamic libraries (such as ‘/usr/lib/libc.so’) are in fact GNU ld link scripts (horrible!), the TCC linker also supports a subset of GNU ld scripts.

The `GROUP` and `FILE` commands are supported. `OUTPUT_FORMAT` and `TARGET` are ignored.

Example from ‘/usr/lib/libc.so’:

 

/\* GNU ld script
   Use the shared library, but some functions are only in
   the static library, so try that secondarily.  \*/
GROUP ( /lib/libc.so.6 /usr/lib/libc\_nonshared.a )

* * *

\[ [<](#SEC20 "Previous section in reading order") \]
\[ [>](#SEC22 "Next section in reading order") \]
\[ [<<](#SEC16 "Beginning of this chapter or previous chapter") \]
\[ [Up](#SEC_Top "Up section") \]
\[ [>>](#SEC22 "Next chapter") \]

 

 

 

 

\[[Top](#SEC_Top "Cover (top) of document")\]
\[[Contents](#SEC_Contents "Table of contents")\]
\[[Index](#SEC36 "Index")\]
\[ [?](#SEC_About "About (help)") \]

6\. TinyCC Memory and Bound checks
==================================

This feature is activated with the ‘\-b’ (see section [Command line invocation](#SEC2)).

Note that pointer size is _unchanged_ and that code generated with bound checks is _fully compatible_ with unchecked code. When a pointer comes from unchecked code, it is assumed to be valid. Even very obscure C code with casts should work correctly.

For more information about the ideas behind this method, see [http://www.doc.ic.ac.uk/~phjk/BoundsChecking.html](http://www.doc.ic.ac.uk/~phjk/BoundsChecking.html).

Here are some examples of caught errors:

Invalid range with standard string function:

 

    {
        char tab\[10\];
        memset(tab, 0, 11);
    }

Out of bounds-error in global or local arrays:

 

    {
        int tab\[10\];
        for(i=0;i<11;i++) {
            sum += tab\[i\];
        }
    }

Out of bounds-error in malloc'ed data:

 

    {
        int \*tab;
        tab = malloc(20 \* sizeof(int));
        for(i=0;i<21;i++) {
            sum += tab4\[i\];
        }
        free(tab);
    }

Access of freed memory:

 

    {
        int \*tab;
        tab = malloc(20 \* sizeof(int));
        free(tab);
        for(i=0;i<20;i++) {
            sum += tab4\[i\];
        }
    }

Double free:

 

    {
        int \*tab;
        tab = malloc(20 \* sizeof(int));
        free(tab);
        free(tab);
    }

* * *

\[ [<](#SEC21 "Previous section in reading order") \]
\[ [>](#SEC23 "Next section in reading order") \]
\[ [<<](#SEC21 "Beginning of this chapter or previous chapter") \]
\[ [Up](#SEC_Top "Up section") \]
\[ [>>](#SEC23 "Next chapter") \]

 

 

 

 

\[[Top](#SEC_Top "Cover (top) of document")\]
\[[Contents](#SEC_Contents "Table of contents")\]
\[[Index](#SEC36 "Index")\]
\[ [?](#SEC_About "About (help)") \]

7\. The `libtcc` library
========================

The `libtcc` library enables you to use TCC as a backend for dynamic code generation.

Read the ‘libtcc.h’ to have an overview of the API. Read ‘libtcc\_test.c’ to have a very simple example.

The idea consists in giving a C string containing the program you want to compile directly to `libtcc`. Then you can access to any global symbol (function or variable) defined.

* * *

\[ [<](#SEC22 "Previous section in reading order") \]
\[ [>](#SEC24 "Next section in reading order") \]
\[ [<<](#SEC22 "Beginning of this chapter or previous chapter") \]
\[ [Up](#SEC_Top "Up section") \]
\[ [>>](#SEC36 "Next chapter") \]

 

 

 

 

\[[Top](#SEC_Top "Cover (top) of document")\]
\[[Contents](#SEC_Contents "Table of contents")\]
\[[Index](#SEC36 "Index")\]
\[ [?](#SEC_About "About (help)") \]

8\. Developer's guide
=====================

This chapter gives some hints to understand how TCC works. You can skip it if you do not intend to modify the TCC code.

* * *

\[ [<](#SEC23 "Previous section in reading order") \]
\[ [>](#SEC25 "Next section in reading order") \]
\[ [<<](#SEC23 "Beginning of this chapter or previous chapter") \]
\[ [Up](#SEC23 "Up section") \]
\[ [>>](#SEC36 "Next chapter") \]
 

 

 

 

\[[Top](#SEC_Top "Cover (top) of document")\]
\[[Contents](#SEC_Contents "Table of contents")\]
\[[Index](#SEC36 "Index")\]
\[ [?](#SEC_About "About (help)") \]

8.1 File reading
----------------

The `BufferedFile` structure contains the context needed to read a file, including the current line number. `tcc_open()` opens a new file and `tcc_close()` closes it. `inp()` returns the next character.

* * *

\[ [<](#SEC24 "Previous section in reading order") \]
\[ [>](#SEC26 "Next section in reading order") \]
\[ [<<](#SEC23 "Beginning of this chapter or previous chapter") \]
\[ [Up](#SEC23 "Up section") \]
\[ [>>](#SEC36 "Next chapter") \]

 

 

 

 

\[[Top](#SEC_Top "Cover (top) of document")\]
\[[Contents](#SEC_Contents "Table of contents")\]
\[[Index](#SEC36 "Index")\]
\[ [?](#SEC_About "About (help)") \]

8.2 Lexer
---------

`next()` reads the next token in the current file. `next_nomacro()` reads the next token without macro expansion.

`tok` contains the current token (see `TOK_xxx`) constants. Identifiers and keywords are also keywords. `tokc` contains additional infos about the token (for example a constant value if number or string token).

* * *

\[ [<](#SEC25 "Previous section in reading order") \]
\[ [>](#SEC27 "Next section in reading order") \]
\[ [<<](#SEC23 "Beginning of this chapter or previous chapter") \]
\[ [Up](#SEC23 "Up section") \]
\[ [>>](#SEC36 "Next chapter") \]

 

 

 

 

\[[Top](#SEC_Top "Cover (top) of document")\]
\[[Contents](#SEC_Contents "Table of contents")\]
\[[Index](#SEC36 "Index")\]
\[ [?](#SEC_About "About (help)") \]

8.3 Parser
----------

The parser is hardcoded (yacc is not necessary). It does only one pass, except:

*   For initialized arrays with unknown size, a first pass is done to count the number of elements.
*   For architectures where arguments are evaluated in reverse order, a first pass is done to reverse the argument order.

* * *

\[ [<](#SEC26 "Previous section in reading order") \]
\[ [>](#SEC28 "Next section in reading order") \]
\[ [<<](#SEC23 "Beginning of this chapter or previous chapter") \]
\[ [Up](#SEC23 "Up section") \]
\[ [>>](#SEC36 "Next chapter") \]

 

 

 

 

\[[Top](#SEC_Top "Cover (top) of document")\]
\[[Contents](#SEC_Contents "Table of contents")\]
\[[Index](#SEC36 "Index")\]
\[ [?](#SEC_About "About (help)") \]

8.4 Types
---------

The types are stored in a single 'int' variable. It was choosen in the first stages of development when tcc was much simpler. Now, it may not be the best solution.

 

#define VT\_INT        0  /\* integer type \*/
#define VT\_BYTE       1  /\* signed byte type \*/
#define VT\_SHORT      2  /\* short type \*/
#define VT\_VOID       3  /\* void type \*/
#define VT\_PTR        4  /\* pointer \*/
#define VT\_ENUM       5  /\* enum definition \*/
#define VT\_FUNC       6  /\* function type \*/
#define VT\_STRUCT     7  /\* struct/union definition \*/
#define VT\_FLOAT      8  /\* IEEE float \*/
#define VT\_DOUBLE     9  /\* IEEE double \*/
#define VT\_LDOUBLE   10  /\* IEEE long double \*/
#define VT\_BOOL      11  /\* ISOC99 boolean type \*/
#define VT\_LLONG     12  /\* 64 bit integer \*/
#define VT\_LONG      13  /\* long integer (NEVER USED as type, only
                            during parsing) \*/
#define VT\_BTYPE      0x000f /\* mask for basic type \*/
#define VT\_UNSIGNED   0x0010  /\* unsigned type \*/
#define VT\_ARRAY      0x0020  /\* array type (also has VT\_PTR) \*/
#define VT\_BITFIELD   0x0040  /\* bitfield modifier \*/

#define VT\_STRUCT\_SHIFT 16   /\* structure/enum name shift (16 bits left) \*/

When a reference to another type is needed (for pointers, functions and structures), the `32 - VT_STRUCT_SHIFT` high order bits are used to store an identifier reference.

The `VT_UNSIGNED` flag can be set for chars, shorts, ints and long longs.

Arrays are considered as pointers `VT_PTR` with the flag `VT_ARRAY` set.

The `VT_BITFIELD` flag can be set for chars, shorts, ints and long longs. If it is set, then the bitfield position is stored from bits VT\_STRUCT\_SHIFT to VT\_STRUCT\_SHIFT + 5 and the bit field size is stored from bits VT\_STRUCT\_SHIFT + 6 to VT\_STRUCT\_SHIFT + 11.

`VT_LONG` is never used except during parsing.

During parsing, the storage of an object is also stored in the type integer:

 

#define VT\_EXTERN  0x00000080  /\* extern definition \*/
#define VT\_STATIC  0x00000100  /\* static variable \*/
#define VT\_TYPEDEF 0x00000200  /\* typedef definition \*/

* * *

\[ [<](#SEC27 "Previous section in reading order") \]
\[ [>](#SEC29 "Next section in reading order") \]
\[ [<<](#SEC23 "Beginning of this chapter or previous chapter") \]
\[ [Up](#SEC23 "Up section") \]
\[ [>>](#SEC36 "Next chapter") \]

 

 

 

 

\[[Top](#SEC_Top "Cover (top) of document")\]
\[[Contents](#SEC_Contents "Table of contents")\]
\[[Index](#SEC36 "Index")\]
\[ [?](#SEC_About "About (help)") \]

8.5 Symbols
-----------

All symbols are stored in hashed symbol stacks. Each symbol stack contains `Sym` structures.

`Sym.v` contains the symbol name (remember an idenfier is also a token, so a string is never necessary to store it). `Sym.t` gives the type of the symbol. `Sym.r` is usually the register in which the corresponding variable is stored. `Sym.c` is usually a constant associated to the symbol.

Four main symbol stacks are defined:

`define_stack`

for the macros (`#define`s).

`global_stack`

for the global variables, functions and types.

`local_stack`

for the local variables, functions and types.

`global_label_stack`

for the local labels (for `goto`).

`label_stack`

for GCC block local labels (see the `__label__` keyword).

`sym_push()` is used to add a new symbol in the local symbol stack. If no local symbol stack is active, it is added in the global symbol stack.

`sym_pop(st,b)` pops symbols from the symbol stack st until the symbol b is on the top of stack. If b is NULL, the stack is emptied.

`sym_find(v)` return the symbol associated to the identifier v. The local stack is searched first from top to bottom, then the global stack.

* * *

\[ [<](#SEC28 "Previous section in reading order") \]
\[ [>](#SEC30 "Next section in reading order") \]
\[ [<<](#SEC23 "Beginning of this chapter or previous chapter") \]
\[ [Up](#SEC23 "Up section") \]
\[ [>>](#SEC36 "Next chapter") \]

 

 

 

 

\[[Top](#SEC_Top "Cover (top) of document")\]
\[[Contents](#SEC_Contents "Table of contents")\]
\[[Index](#SEC36 "Index")\]
\[ [?](#SEC_About "About (help)") \]

8.6 Sections
------------

The generated code and datas are written in sections. The structure `Section` contains all the necessary information for a given section. `new_section()` creates a new section. ELF file semantics is assumed for each section.

The following sections are predefined:

`text_section`

is the section containing the generated code. ind contains the current position in the code section.

`data_section`

contains initialized data

`bss_section`

contains uninitialized data

`bounds_section`

`lbounds_section`

are used when bound checking is activated

`stab_section`

`stabstr_section`

are used when debugging is actived to store debug information

`symtab_section`

`strtab_section`

contain the exported symbols (currently only used for debugging).

* * *

\[ [<](#SEC29 "Previous section in reading order") \]
\[ [>](#SEC31 "Next section in reading order") \]
\[ [<<](#SEC23 "Beginning of this chapter or previous chapter") \]
\[ [Up](#SEC23 "Up section") \]
\[ [>>](#SEC36 "Next chapter") \]

 

 

 

 

\[[Top](#SEC_Top "Cover (top) of document")\]
\[[Contents](#SEC_Contents "Table of contents")\]
\[[Index](#SEC36 "Index")\]
\[ [?](#SEC_About "About (help)") \]

8.7 Code generation
-------------------

* * *

\[ [<](#SEC30 "Previous section in reading order") \]
\[ [>](#SEC32 "Next section in reading order") \]
\[ [<<](#SEC23 "Beginning of this chapter or previous chapter") \]
\[ [Up](#SEC30 "Up section") \]
\[ [>>](#SEC36 "Next chapter") \]
 

 

 

 

\[[Top](#SEC_Top "Cover (top) of document")\]
\[[Contents](#SEC_Contents "Table of contents")\]
\[[Index](#SEC36 "Index")\]
\[ [?](#SEC_About "About (help)") \]

### 8.7.1 Introduction

The TCC code generator directly generates linked binary code in one pass. It is rather unusual these days (see gcc for example which generates text assembly), but it can be very fast and surprisingly little complicated.

The TCC code generator is register based. Optimization is only done at the expression level. No intermediate representation of expression is kept except the current values stored in the _value stack_.

On x86, three temporary registers are used. When more registers are needed, one register is spilled into a new temporary variable on the stack.

* * *

\[ [<](#SEC31 "Previous section in reading order") \]
\[ [>](#SEC33 "Next section in reading order") \]
\[ [<<](#SEC23 "Beginning of this chapter or previous chapter") \]
\[ [Up](#SEC30 "Up section") \]
\[ [>>](#SEC36 "Next chapter") \]

 

 

 

 

\[[Top](#SEC_Top "Cover (top) of document")\]
\[[Contents](#SEC_Contents "Table of contents")\]
\[[Index](#SEC36 "Index")\]
\[ [?](#SEC_About "About (help)") \]

### 8.7.2 The value stack

When an expression is parsed, its value is pushed on the value stack (vstack). The top of the value stack is vtop. Each value stack entry is the structure `SValue`.

`SValue.t` is the type. `SValue.r` indicates how the value is currently stored in the generated code. It is usually a CPU register index (`REG_xxx` constants), but additional values and flags are defined:

 

#define VT\_CONST     0x00f0
#define VT\_LLOCAL    0x00f1
#define VT\_LOCAL     0x00f2
#define VT\_CMP       0x00f3
#define VT\_JMP       0x00f4
#define VT\_JMPI      0x00f5
#define VT\_LVAL      0x0100
#define VT\_SYM       0x0200
#define VT\_MUSTCAST  0x0400
#define VT\_MUSTBOUND 0x0800
#define VT\_BOUNDED   0x8000
#define VT\_LVAL\_BYTE     0x1000
#define VT\_LVAL\_SHORT    0x2000
#define VT\_LVAL\_UNSIGNED 0x4000
#define VT\_LVAL\_TYPE     (VT\_LVAL\_BYTE | VT\_LVAL\_SHORT | VT\_LVAL\_UNSIGNED)

`VT_CONST`

indicates that the value is a constant. It is stored in the union `SValue.c`, depending on its type.

`VT_LOCAL`

indicates a local variable pointer at offset `SValue.c.i` in the stack.

`VT_CMP`

indicates that the value is actually stored in the CPU flags (i.e. the value is the consequence of a test). The value is either 0 or 1. The actual CPU flags used is indicated in `SValue.c.i`.

If any code is generated which destroys the CPU flags, this value MUST be put in a normal register.

`VT_JMP`

`VT_JMPI`

indicates that the value is the consequence of a conditional jump. For VT\_JMP, it is 1 if the jump is taken, 0 otherwise. For VT\_JMPI it is inverted.

These values are used to compile the `||` and `&&` logical operators.

If any code is generated, this value MUST be put in a normal register. Otherwise, the generated code won't be executed if the jump is taken.

`VT_LVAL`

is a flag indicating that the value is actually an lvalue (left value of an assignment). It means that the value stored is actually a pointer to the wanted value.

Understanding the use `VT_LVAL` is very important if you want to understand how TCC works.

`VT_LVAL_BYTE`

`VT_LVAL_SHORT`

`VT_LVAL_UNSIGNED`

if the lvalue has an integer type, then these flags give its real type. The type alone is not enough in case of cast optimisations.

`VT_LLOCAL`

is a saved lvalue on the stack. `VT_LLOCAL` should be eliminated ASAP because its semantics are rather complicated.

`VT_MUSTCAST`

indicates that a cast to the value type must be performed if the value is used (lazy casting).

`VT_SYM`

indicates that the symbol `SValue.sym` must be added to the constant.

`VT_MUSTBOUND`

`VT_BOUNDED`

are only used for optional bound checking.

* * *

\[ [<](#SEC32 "Previous section in reading order") \]
\[ [>](#SEC34 "Next section in reading order") \]
\[ [<<](#SEC23 "Beginning of this chapter or previous chapter") \]
\[ [Up](#SEC30 "Up section") \]
\[ [>>](#SEC36 "Next chapter") \]

 

 

 

 

\[[Top](#SEC_Top "Cover (top) of document")\]
\[[Contents](#SEC_Contents "Table of contents")\]
\[[Index](#SEC36 "Index")\]
\[ [?](#SEC_About "About (help)") \]

### 8.7.3 Manipulating the value stack

`vsetc()` and `vset()` pushes a new value on the value stack. If the previous vtop was stored in a very unsafe place(for example in the CPU flags), then some code is generated to put the previous vtop in a safe storage.

`vpop()` pops vtop. In some cases, it also generates cleanup code (for example if stacked floating point registers are used as on x86).

The `gv(rc)` function generates code to evaluate vtop (the top value of the stack) into registers. rc selects in which register class the value should be put. `gv()` is the _most important function_ of the code generator.

`gv2()` is the same as `gv()` but for the top two stack entries.

* * *

\[ [<](#SEC33 "Previous section in reading order") \]
\[ [>](#SEC35 "Next section in reading order") \]
\[ [<<](#SEC23 "Beginning of this chapter or previous chapter") \]
\[ [Up](#SEC30 "Up section") \]
\[ [>>](#SEC36 "Next chapter") \]

 

 

 

 

\[[Top](#SEC_Top "Cover (top) of document")\]
\[[Contents](#SEC_Contents "Table of contents")\]
\[[Index](#SEC36 "Index")\]
\[ [?](#SEC_About "About (help)") \]

### 8.7.4 CPU dependent code generation

See the ‘i386-gen.c’ file to have an example.

`load()`

must generate the code needed to load a stack value into a register.

`store()`

must generate the code needed to store a register into a stack value lvalue.

`gfunc_start()`

`gfunc_param()`

`gfunc_call()`

should generate a function call

`gfunc_prolog()`

`gfunc_epilog()`

should generate a function prolog/epilog.

`gen_opi(op)`

must generate the binary integer operation op on the two top entries of the stack which are guaranted to contain integer types.

The result value should be put on the stack.

`gen_opf(op)`

same as `gen_opi()` for floating point operations. The two top entries of the stack are guaranted to contain floating point values of same types.

`gen_cvt_itof()`

integer to floating point conversion.

`gen_cvt_ftoi()`

floating point to integer conversion.

`gen_cvt_ftof()`

floating point to floating point of different size conversion.

`gen_bounded_ptr_add()`

`gen_bounded_ptr_deref()`

are only used for bounds checking.

* * *

\[ [<](#SEC34 "Previous section in reading order") \]\[ [>](#SEC36 "Next section in reading order") \]
\[ [<<](#SEC23 "Beginning of this chapter or previous chapter") \]
\[ [Up](#SEC23 "Up section") \]
\[ [>>](#SEC36 "Next chapter") \]
 

 

 

 

\[[Top](#SEC_Top "Cover (top) of document")\]
\[[Contents](#SEC_Contents "Table of contents")\]
\[[Index](#SEC36 "Index")\]
\[ [?](#SEC_About "About (help)") \]

8.8 Optimizations done
----------------------

Constant propagation is done for all operations. Multiplications and divisions are optimized to shifts when appropriate. Comparison operators are optimized by maintaining a special cache for the processor flags. &&, || and ! are optimized by maintaining a special 'jump target' value. No other jump optimization is currently performed because it would require to store the code in a more abstract fashion.

* * *

\[ [<](#SEC35 "Previous section in reading order") \]\[ > \]\[ [<<](#SEC23 "Beginning of this chapter or previous chapter") \]\[ [Up](#SEC_Top "Up section") \]\[ >> \]

 

 

 

 

\[[Top](#SEC_Top "Cover (top) of document")\]\[[Contents](#SEC_Contents "Table of contents")\]\[[Index](#SEC36 "Index")\]\[ [?](#SEC_About "About (help)") \]

Concept Index
=============

Jump to:  

[**\_**](#SEC36_0)    
[**A**](#SEC36_1)   [**B**](#SEC36_2)   [**C**](#SEC36_3)   [**D**](#SEC36_4)   [**E**](#SEC36_5)   [**F**](#SEC36_6)   [**G**](#SEC36_7)   [**I**](#SEC36_8)   [**J**](#SEC36_9)   [**L**](#SEC36_10)   [**M**](#SEC36_11)   [**O**](#SEC36_12)   [**P**](#SEC36_13)   [**Q**](#SEC36_14)   [**R**](#SEC36_15)   [**S**](#SEC36_16)   [**T**](#SEC36_17)   [**U**](#SEC36_18)   [**V**](#SEC36_19)   [**W**](#SEC36_20)  

Index Entry

Section

* * *

\_

[\_\_asm\_\_](#IDX11)

[3.3 GNU C extensions](#SEC8)

* * *

A

[align directive](#SEC14)

[4.4 Directives](#SEC14)

[aligned attribute](#IDX1)

[3.3 GNU C extensions](#SEC8)

[ascii directive](#SEC14)

[4.4 Directives](#SEC14)

[asciz directive](#SEC14)

[4.4 Directives](#SEC14)

[assembler](#SEC15)

[4.5 X86 Assembler](#SEC15)

[assembler directives](#SEC14)

[4.4 Directives](#SEC14)

[assembly, inline](#IDX10)

[3.3 GNU C extensions](#SEC8)

* * *

B

[bound checks](#SEC21)

[6\. TinyCC Memory and Bound checks](#SEC21)

[bss directive](#SEC14)

[4.4 Directives](#SEC14)

[byte directive](#SEC14)

[4.4 Directives](#SEC14)

* * *

C

[caching processor flags](#SEC35)

[8.8 Optimizations done](#SEC35)

[cdecl attribute](#IDX5)

[3.3 GNU C extensions](#SEC8)

[code generation](#SEC30)

[8.7 Code generation](#SEC30)

[comparison operators](#SEC35)

[8.8 Optimizations done](#SEC35)

[constant propagation](#SEC35)

[8.8 Optimizations done](#SEC35)

[CPU dependent](#SEC34)

[8.7.4 CPU dependent code generation](#SEC34)

* * *

D

[data directive](#SEC14)

[4.4 Directives](#SEC14)

[directives, assembler](#SEC14)

[4.4 Directives](#SEC14)

[dllexport attribute](#IDX8)

[3.3 GNU C extensions](#SEC8)

* * *

E

[ELF](#SEC17)

[5.1 ELF file generation](#SEC17)

* * *

F

[FILE, linker command](#SEC20)

[5.4 GNU Linker Scripts](#SEC20)

[fill directive](#SEC14)

[4.4 Directives](#SEC14)

[flags, caching](#SEC35)

[8.8 Optimizations done](#SEC35)

* * *

G

[gas](#IDX12)

[3.3 GNU C extensions](#SEC8)

[global directive](#SEC14)

[4.4 Directives](#SEC14)

[globl directive](#SEC14)

[4.4 Directives](#SEC14)

[GROUP, linker command](#SEC20)

[5.4 GNU Linker Scripts](#SEC20)

* * *

I

[inline assembly](#IDX9)

[3.3 GNU C extensions](#SEC8)

[int directive](#SEC14)

[4.4 Directives](#SEC14)

* * *

J

[jump optimization](#SEC35)

[8.8 Optimizations done](#SEC35)

* * *

L

[linker](#SEC16)

[5\. TinyCC Linker](#SEC16)

[linker scripts](#SEC20)

[5.4 GNU Linker Scripts](#SEC20)

[long directive](#SEC14)

[4.4 Directives](#SEC14)

* * *

M

[memory checks](#SEC21)

[6\. TinyCC Memory and Bound checks](#SEC21)

* * *

O

[optimizations](#SEC35)

[8.8 Optimizations done](#SEC35)

[org directive](#SEC14)

[4.4 Directives](#SEC14)

[OUTPUT\_FORMAT, linker command](#SEC20)

[5.4 GNU Linker Scripts](#SEC20)

* * *

P

[packed attribute](#IDX2)

[3.3 GNU C extensions](#SEC8)

[PE-i386](#SEC19)

[5.3 PE-i386 file generation](#SEC19)

[previous directive](#SEC14)

[4.4 Directives](#SEC14)

* * *

Q

[quad directive](#SEC14)

[4.4 Directives](#SEC14)

* * *

R

[regparm attribute](#IDX7)

[3.3 GNU C extensions](#SEC8)

* * *

S

[scripts, linker](#SEC20)

[5.4 GNU Linker Scripts](#SEC20)

[section attribute](#IDX3)

[3.3 GNU C extensions](#SEC8)

[section directive](#SEC14)

[4.4 Directives](#SEC14)

[short directive](#SEC14)

[4.4 Directives](#SEC14)

[skip directive](#SEC14)

[4.4 Directives](#SEC14)

[space directive](#SEC14)

[4.4 Directives](#SEC14)

[stdcall attribute](#IDX6)

[3.3 GNU C extensions](#SEC8)

[strength reduction](#SEC35)

[8.8 Optimizations done](#SEC35)

[string directive](#SEC14)

[4.4 Directives](#SEC14)

* * *

T

[TARGET, linker command](#SEC20)

[5.4 GNU Linker Scripts](#SEC20)

[text directive](#SEC14)

[4.4 Directives](#SEC14)

* * *

U

[unused attribute](#IDX4)

[3.3 GNU C extensions](#SEC8)

* * *

V

[value stack](#SEC33)

[8.7.3 Manipulating the value stack](#SEC33)

[value stack, introduction](#SEC32)

[8.7.2 The value stack](#SEC32)

* * *

W

[word directive](#SEC14)

[4.4 Directives](#SEC14)

* * *

Jump to:  

[**\_**](#SEC36_0)    
[**A**](#SEC36_1)   [**B**](#SEC36_2)   [**C**](#SEC36_3)   [**D**](#SEC36_4)   [**E**](#SEC36_5)   [**F**](#SEC36_6)   [**G**](#SEC36_7)   [**I**](#SEC36_8)   [**J**](#SEC36_9)   [**L**](#SEC36_10)   [**M**](#SEC36_11)   [**O**](#SEC36_12)   [**P**](#SEC36_13)   [**Q**](#SEC36_14)   [**R**](#SEC36_15)   [**S**](#SEC36_16)   [**T**](#SEC36_17)   [**U**](#SEC36_18)   [**V**](#SEC36_19)   [**W**](#SEC36_20)  

* * *

\[[Top](#SEC_Top "Cover (top) of document")\]\[[Contents](#SEC_Contents "Table of contents")\]\[[Index](#SEC36 "Index")\]\[ [?](#SEC_About "About (help)") \]

Table of Contents
=================

*   [1\. Introduction](#SEC1)
*   [2\. Command line invocation](#SEC2)
    *   [2.1 Quick start](#SEC3)
    *   [2.2 Option summary](#SEC4)
*   [3\. C language support](#SEC5)
    *   [3.1 ANSI C](#SEC6)
    *   [3.2 ISOC99 extensions](#SEC7)
    *   [3.3 GNU C extensions](#SEC8)
    *   [3.4 TinyCC extensions](#SEC9)
*   [4\. TinyCC Assembler](#SEC10)
    *   [4.1 Syntax](#SEC11)
    *   [4.2 Expressions](#SEC12)
    *   [4.3 Labels](#SEC13)
    *   [4.4 Directives](#SEC14)
    *   [4.5 X86 Assembler](#SEC15)
*   [5\. TinyCC Linker](#SEC16)
    *   [5.1 ELF file generation](#SEC17)
    *   [5.2 ELF file loader](#SEC18)
    *   [5.3 PE-i386 file generation](#SEC19)
    *   [5.4 GNU Linker Scripts](#SEC20)
*   [6\. TinyCC Memory and Bound checks](#SEC21)
*   [7\. The `libtcc` library](#SEC22)
*   [8\. Developer's guide](#SEC23)
    *   [8.1 File reading](#SEC24)
    *   [8.2 Lexer](#SEC25)
    *   [8.3 Parser](#SEC26)
    *   [8.4 Types](#SEC27)
    *   [8.5 Symbols](#SEC28)
    *   [8.6 Sections](#SEC29)
    *   [8.7 Code generation](#SEC30)
        *   [8.7.1 Introduction](#SEC31)
        *   [8.7.2 The value stack](#SEC32)
        *   [8.7.3 Manipulating the value stack](#SEC33)
        *   [8.7.4 CPU dependent code generation](#SEC34)
    *   [8.8 Optimizations done](#SEC35)
*   [Concept Index](#SEC36)

* * *

\[[Top](#SEC_Top "Cover (top) of document")\]\[[Contents](#SEC_Contents "Table of contents")\]\[[Index](#SEC36 "Index")\]\[ [?](#SEC_About "About (help)") \]

About This Document
===================

This document was generated by _gr_ on _May, 18 2009_ using [_texi2html 1.78_](http://www.nongnu.org/texi2html/).

The buttons in the navigation panels have the following meaning:

|  Button        |  Name       |  Go to                                        |  From 1.2.3 go to |
|----------------|-------------|-----------------------------------------------|-------------------|
|  [ &lt; ]      | Back        | Previous section in reading order             | 1.2.2             |
|  [ &gt; ]      | Forward     | Next section in reading order                 | 1.2.4             |
|  [ &lt;&lt; ]  | FastBack    | Beginning of this chapter or previous chapter | 1                 |
|  [ Up ]        | Up          | Up section                                    | 1.2               |
|  [ &gt;&gt; ]  | FastForward | Next chapter                                  | 2                 |
|  [Top]         | Top         | Cover (top) of document                       |  &nbsp;           |
|  [Contents]    | Contents    | Table of contents                             |  &nbsp;           |
|  [Index]       | Index       | Index                                         |  &nbsp;           |
|  [ ? ]         | About       | About (help)                                  |  &nbsp;           |

 

where the **Example** assumes that the current position is at **Subsubsection One-Two-Three** of a document of the following structure:

*   1\. Section One
    *   1.1 Subsection One-One
        *   ...
    *   1.2 Subsection One-Two
        *   1.2.1 Subsubsection One-Two-One
        *   1.2.2 Subsubsection One-Two-Two
        *   1.2.3 Subsubsection One-Two-Three     **<== Current Position**
        *   1.2.4 Subsubsection One-Two-Four
    *   1.3 Subsection One-Three
        *   ...
    *   1.4 Subsection One-Four

* * *

This document was generated by _gr_ on _May, 18 2009_ using [_texi2html 1.78_](http://www.nongnu.org/texi2html/).