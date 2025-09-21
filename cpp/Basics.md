## Compiler

The name "compiler" is primarily used for programs that translate source code from a high-level programming language to a low-level programming language (e.g. assembly language, object code, or machine code) to create an executable program

## Translation unit:

One source file, after including all its headers and processing all macros, that the compiler compiles at once.

In simple words:

- You write a .c or .cpp file.

- It may include other files using #include.

- The compiler takes your file + all included code and turns it into a single "big file" internally.

- That big file is the translation unit.

Each translation unit is compiled separately into an object file (.o or .obj), and then the linker combines all those object files into your final program.


<img width="420" height="1024" alt="image" src="https://github.com/user-attachments/assets/c80c348e-7080-42ee-bdb8-7b157452062b" />
