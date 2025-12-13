# Linkage

Recall that in C/C++ program, to generate a machine code given a program, there are 4 steps: `preprocessing`, `compilation`, `assembler`, and `linker`. 

Consider the case when we have multiple source code, for example, `one.c` and `two.c`, that we want to compile! In the preprocessing step, for each source code, all headers and dependencies will be expanded independently. The product of this process is called the __translation unit__. So, in the examples, `one.c` and `two.c` will result in two different translation units.

These translation units from multiple source code will be compiled one at a time, and converted to machine code by the assembler. As a result, there are multiple machine code produced. It is the job of the linker to combine all these files into one executable object. 

Now, back to topic, __linkage__ is the property that describes how variables should be linked by the linker, that is, the last step of generating machine code. 

## Internal Linkage

If internal linkage is implemented, variables will not be accessible outside the translational unit it is implemented. To enable internal linkage, we need to use the `static` keyword. 

### Interview Implication 1

__Question__: How to prevent naming conflict in global variables between multiple files in C++?

__Answer__: Declare the global variables as `static`. Global static variable has internal linkage, i.e., it is accessible only within the file where it is defined. 

## External Linkage

On the other hand, when external linkage is implemented, variables will be accessible in every translational unit. 

### Linking Happen After Compilation

If variables will be accessible in every translation unit, why does `animals` in `test2.cpp` said to be not declared?

```c++
// test1.cpp
#include <stdio.h>

int animals = 8;
```

```c++
// test2.cpp
#include <stdio.h>

int main() {
    printf("%d", animals);
    return 0;
}
```

This is because compilation happen first before linking. Before linking together the machine codes from both files, both of these source files need to be compiled __independently__. During the stage of compilation, `test2.cpp` will crashed out because `animals` is not declared. So, linking will not happen. 

### Consequence of External Linkage

Problems in linkage will occur in the case below. 

```c++
// test1.cpp
#include <stdio.h>

int animals = 8;
```

```c++
// test2.cpp
#include <stdio.h>

int animals = 7;

int main() {
    printf("%d", animals);
    return 0;
}
```

Both `test1.cpp` and `test2.cpp` define the variable `animals` with external linkage. Thus, `animals` from `test1.cpp` is accessible in the translation unit of `test1.cpp` and `test2.cpp`; this also applies for `test2.cpp`. 

Thus, running `gcc test1.cpp test2.cpp -o test` will result in the following error. 

```
/usr/bin/ld: /tmp/ccgtBjkE.o:(.data+0x0): multiple definition of `animals'; /tmp/ccODaDN9.o:(.data+0x0): first defined here
collect2: error: ld returned 1 exit status
```
