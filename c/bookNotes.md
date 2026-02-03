# Basics 

Compile with command cc hello.c 

Run with a.out 

```C
#include <stdio.h>
main()
{
	int fahr, celsius;
	int lower, upper, step;
	lower = 0; /* lower limit of temperature scale */
	upper = 300; /* upper limit */
	step = 20; /* step size */
	fahr = lower;
	printf("%s %s\n", Fahr, Celsius);
	while (fahr <= upper)
	{
		celsius = (5.0/9.0) * (fahr-32.0);
 		printf("%3.0f %6.1f\n", fahr, celsius);
 		fahr = fahr + step;
	}
}
```
OR
```
#include <stdio.h>

#define LOWER 0 /* lower limit of table */
#define UPPER 300 /* upper limit */
#define STEP 20 /* step size */ 

main()
{
	int fahr;
	for (fahr = LOWER; fahr <= UPPER; fahr = fahr + STEP)
	{
 		printf("%3d %6.1f\n", fahr, (5.0/9.0)*(fahr-32));
	}
}
```

## Data types

C provides data types: int, float, char, short, long, double. 
The size of these objects is machine-dependent. There are also arrays, structures and
unions of these basic types, pointers to them, and functions that return them.

Integer division truncates: any fractional part is discarded. 5/9 would be truncated to zero

## printf

* %d print as decimal integer
* %6d print as decimal integer, at least 6 characters wide
* %f print as floating point
* %6f print as floating point, at least 6 characters wide
* %.2f print as floating point, 2 characters after decimal point
* %6.2f print as floating point, at least 6 wide and 2 after decimal point 
* %o for octal
* %x for hexadecimal
* %c for character
* %s for character string
* %% for itself

//TODO
Among the others that C provides are \t for tab, \b for backspace, \" for the double quote
and \\ for the backslash itself. There is a complete list in Section 2.3. 


