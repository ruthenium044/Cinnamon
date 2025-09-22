## Memory layout

#### Text segment

The text segment (also known as code segment) is where the executable code of the program is stored. It contains the compiled machine code of the program's functions and instructions.

#### Data segment

The data segment stores global and static variables that are created by the programmer.

Can be initialised or uninitialised. Uninitialised variables are automatically initialised to zero at runtime by the operating system.

#### Heap

Allocating on heap: new and malloc

Heap segment is where dynamic memory allocation usually takes place. It is managed by functions such as malloc(), realloc(), and free()

#### Stack

Allocating on stack: scope declaration

The stack is a region of memory used for local variables and function call management. Each time a function is called, a stack frame is created to store local variables, function parameters, and return addresses. 

https://www.geeksforgeeks.org/c/memory-layout-of-c-program/

## New and Delete

New gives you memory.

In C++, doing a new does the following:

- Requests memory from the OS
- Organises and tracks said memory (See malloc and how much work it is doing compared to the OS solutions such as VirtualAlloc)
- Calls the constructor if applicable (if it has constructor and is not primitive type, but for the sake of C++ it is always applicable to some degree)
- Finally gives user pointer to T where T is the resource

For primitive value types such as int, double, etc. the default constructor becomes a zero-init constructor. In other words, it zeroes out that

The delete is same in reverse!

So what is happening in these delete versions (and the default by the compiler) is the following:

- The user passes in the pointer to delete
- Since delete pointer; becomes: void operator delete(T* ptr); when the keyword delete gets expanded into the function and then generated.
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

https://cplusplus.com/reference/new/operator%20new/

## Smart pointers

#### Unique

Owns a dynamically allocated object and makes sure it gets deleted when it goes out of scope. Once you hand it off, you no longer own it.

Key points:

- Exclusive ownership → only one unique_ptr can own a resource at a time.

- Automatic cleanup → no need to call delete, it happens automatically.

- Non-copyable → can’t copy a unique_ptr (prevents two owners).

- Movable → you can transfer ownership using std::move().

- Avoids memory leaks and dangling pointers.

#### Shared

It allows multiple smart pointers to share ownership of the same resource. The resource stays alive as long as someone still holds the contract.

Key points:

- Shared ownership → multiple shared_ptrs can point to the same object.

- Reference counting → internally keeps track of how many shared_ptrs own the resource.

- Automatic cleanup → when the last shared_ptr goes out of scope, the resource is deleted.

- Copyable & movable → you can copy or move shared_ptrs freely.

- Slightly slower than unique_ptr (because of reference counting overhead).

#### Weak pointer

It observes a shared_ptr’s resource without owning it. You can look at the resource, but you don’t keep it alive.

Key points:

- Non-owning → doesn’t affect the resource’s lifetime.

- Avoids cycles → used to break circular references between shared_ptrs.

- Must be locked → to use the resource, convert it back to shared_ptr with .lock().

- Check validity → .expired() tells if the resource has been deleted.

- Lightweight → no heavy reference counting like shared_ptr.

## Some optimisation notes

#### Optimisation with lock and unlock

One of the techniques you used to optimise a slow system

If it is not a highly contested resource, it is fine to use. Often works in an editor context

#### Optimising vector writing

When a vector is being written to every frame, for temporary storage

Just serize it to max on initialise c:

## Notes

- Render passes

- 3 kinds of buffers (d3d or something? I forgot)

- Rendering barrier. When render targets are being written to one after another, and it's not done so, it flickers

- Different kinds of allocators

Linear allocator used every loop to store data from dynamic objects

- Rendered things can be constant (textures of entities or similar) or dynamic if they're changing. And the frequency would be every frame unlesss special cases

- Heard of bindless?

Yes
