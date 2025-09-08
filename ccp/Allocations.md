
## New and Delete

In C++, doing a new does the following:

- Requests memory from the OS
- Organises and tracks said memory (See malloc and how much work it is doing compared to the OS solutions such as VirtualAlloc)
- Calls the constructor if applicable, but the sake of C++ it is always applicable to some degree
- Finally gives user pointer to T where T is the resource

For primitive value types such as int, double, etc. the default constructor becomes a zero-init constructor. In other words, it zeroes out that

The delete is same in reverse! 

The user, you, passes in the pointer to delete. 

Since delete pointer; becomes: void operator delete(T* ptr); when the keyword delete gets expanded into the function and then generated.

All the keywords become functions and all the functions become overloads and then mangled function names. Such as:

```CPP
template <typename T>
void operator delete(T* ptr);
// Code gen for various types does the following (example illustration only, different between compilers, etc):

void operator__delete__int(int* ptr);
void operator__delete__custom_struct(custom_struct* ptr);
void operator__delete__float(float* ptr);
void operator__delete__double(double* ptr);
```

So what is happening in these delete versions (and the default by the compiler) is the following:

- You pass in a pointer
- The compiler gets the type and calls the destructor
- It moves on further to remove the tracking (if any) and some other bookkeeping (See malloc/free implementation)
- Gives memory back to the OS (VirtualFree)

So when you add your custom destructor ~custom_struct() to a class, you essentially allow the meta code to simply call your custom_struct destructor.
You can think of these things as code for code: 

```CPP
if constexpr(has_destructor<custom_struct>()) 
{
    // Pointer comes from outside:
    pointer->~custom_struct();
    // And there also ways to treat it like a static function
    custom_struct::~custom_struct(pointer);
}
```

