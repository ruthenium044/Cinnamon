# Bitwise operators

<img src = "https://user-images.githubusercontent.com/31730144/161545606-138e6dc5-038d-4558-9bda-29e591c69b9c.png" width = "400">


Bit operators are faster than * or / but same speed as + and -

Bit type size
```CPP
sizeof(value) * 8;
```

Cast bit value to correct bit type
```CPP
static_cast<BitType>(1) << index;
```

## Bit Flags
Can set bools into an int basically

```CPP
MAGIC = 16; //0001 0000
INTEL =  8; //0000 1000
CHAR  =  4; //0000 0100
FLY   =  2; //0000 0010
INV   =  1; //0000 0001
```

Setting attributes
```CPP
attributes = 20; // 16 + 4
attributes = MAGIC + CHARISMA;
attributes = MAGIC | CHARISMA; //10100

attributes = 0; //clear all
attributes = ~0; //set all
```

Adding
```CPP
attributes |= INTEL; //11100
attributes |= (INTEL | MAGIC | CHAR);
```

Unsetting
```CPP
attributes &= ~MAGIC; //01100
attributes &= ~(INTEL | MAGIC);
``` 

Toggling
```CPP
attributes ^= MAGIC; //01100
attributes ^= (INTEL | MAGIC);
``` 

## Bit Mask

If there's just one bit in the mask set, then they will be the same,
but if there are multiple bits set in the mask, they're different.

```CPP
mask = MAGIC;
bool match = (attributes & mask) != 0; //true if any of the bits in mask are set 
mask = (MAGIC | INTEL);
bool match = (attributes & mask) == mask; //will be true if all of the bits in mask are set
``` 

## Bit Shift

Setting flags without the numbers
```CPP
MAGIC = 1 << 4; //0001 0000
INTEL = 1 << 3; //0000 1000
CHAR  = 1 << 2; //0000 0100
FLY   = 1 << 1; //0000 0010
INV   = 1;      //0000 0001
```

Data packing
```CPP
packed |= (A << 16 - 6); // 11 0111
packed |= (B << 10 - 5); //  1 0001
packed |= (C <<  5 - 4); //    1101
//1101 1110 0011 1010
``` 

Data unpacking
```CPP
mask = A << 10;
A = (packed & mask) >> 10;
mask = B << 5;
B = (packed & mask) >> 5;
C = packed;
``` 

# Bit Board

Used to store each element on a board in a separate array. Player1, Player2, Resource1, Resource2 and so on.

Array flattening

```CPP
index = row * width + column;
//adding the index
mask = 0 << index;
board |= mask;
```

Set cell state
```CPP
long newBit = 1 << index;
board |= newBit;
```

Remove cell state
```CPP
long newBit = 1 << index;
board &= ~newBit;
```

Get cell state
```CPP
mask = 1 << index;
(board & mask) != 0;
```

Get cell count
```CPP
while(board != 0)
{
    board &= board - 1;
    count++;
}
```

# Arithmetic

Addition
```CPP
while (b != 0) 
{ 
    int c = a & b;   
    a = a ^ b;  
    b = c << 1; 
} 
return a; 
```

Subtraction
```CPP
while (b != 0) 
{ 
    int borrow = (~a) & b; 
    a = a ^ b; 
    b = borrow << 1; 
} 
return a;
```

Multiplication
```CPP
int answer = 0; 
int count = 0; 
while (m != 0) 
{ 
    if (m % 2 == 1)
    {               
        answer += n << count;
    }
    count++; 
    m /= 2; 
} 
return answer; 
```

Division
```CPP
int remainder = 0; 
 
int division(int dividend, int divisor) 
{ 
    int quotient = 1; 
    int neg = 1; 
    
    if ((dividend > 0 && divisor < 0) || (dividend < 0 && divisor > 0)) 
              neg = -1; 
 
    // Convert to positive 
    int tempdividend = Mathf.Abs((dividend < 0) ? -dividend : dividend); 
    int tempdivisor = Mathf.Abs((divisor < 0) ? -divisor : divisor); 
 
    if (tempdivisor == tempdividend) 
    { 
        remainder = 0; 
        return 1 * neg; 
    } 
    else if (tempdividend < tempdivisor) 
    { 
        if (dividend < 0) 
            remainder = tempdividend * neg; 
        else 
            remainder = tempdividend; 
        return 0; 
    } 
    
    while (tempdivisor << 1 <= tempdividend) 
    { 
        tempdivisor = tempdivisor << 1; 
        quotient = quotient << 1; 
    } 
 
    // Call division recursively 
    if (dividend < 0) 
        quotient = quotient * neg + division(-(tempdividend - tempdivisor), divisor); 
    else 
        quotient = quotient * neg + division(tempdividend - tempdivisor, divisor); 
    return quotient; 
}
```

Counter wrapping (check bounds)

```CPP
// Assumes image dimensions are powers of 2.
int widthMask = image.width - 1;
int heightMask = image.height - 1;
 
for( int x = 0; x < screen.width; x++ )
{
  for( int y = 0; x < screen.height; y++ )
  {   
    int colour = image.getPixel( x & widthMask, y & heightMask );
    screen.setPixel( x, y, colour );
  }
} 
```

Swap
```CPP
x ^= y;
y ^= x;
x ^= y;
```

Check sign
```CPP
bool haveSameSign = 0 <= (x ^ y); 
```

Min Max
```CPP
int max = x ^ ((x ^ y) & -((x < y)?1:0));
int min = y ^ ((x ^ y) & -((x < y)?1:0));
```
