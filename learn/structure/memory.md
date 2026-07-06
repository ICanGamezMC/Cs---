# Basic memory
> In Cs+++, memory management is designed to be both efficient and flexible, allowing developers to optimize their programs while maintaining control over resource allocation.

Memory has issues with not freeing up bytes, write over memory, out-of-bounds reading, and other issues. 
Cs+++ provides a unique approach to memory management.

## Memory tree model

During compile time memory is represented as a tree structure, where if memory being allocated in a block of code is not returned then it will be freed unless stated otherwise.
Also during changes to memory, or being set to another variable will always be cloned.

Here is a simple diagram of how memory is structured in Cs+++ with a tree model and code blocks:

```cs
fn main(){
    Var int("a") = 10;
    Var int("b") = 20;
    Var int("c") = a + b;  # Memory for c is allocated and will be freed after this block unless other parts of the code reference it.
    print(a) # Outputs 10
}
```
Timeline reads from left to right:
```
                          ┌*ref────────┐      ┌──────────────┐
                     ┌────►Var A prints├──────►Var A is freed│
                     │    └────────────┘      └──────────────┘
┌───────────────┐    │                                        
│ Var A created ├────┴┐  ┌*cloned────────┐    ┌──────────────┐
│ Hold int 10   │     │  │ Var C created ├────►Var C is freed│
└───────────────┘  Ref┼──► Hold int 30   │    └──────────────┘
                   A+B│  └───────────────┘                    
┌───────────────┐     │  ┌──────────────┐                     
│ Var B created │     ├──►Var─B─is─freed│                     
│ Hold int 20   ├─────┘  └──────────────┘                     
└───────────────┘                                             
```
Any reference to multiple variables and creating/assigning it a new variable will be cloned creating a new memory address.

> [!NOTE]
> Freeing code will look for any outgoing references without any processes. This all being done at compile time, so there is no garbage collection.

Here is the same code but with where the memory is being freed:
```cs
fn main(){
    Var int("a") = 10;
    Var int("b") = 20;
    Var int("c") = a + b;
    # B is freed here.
    print(a)
    # C is freed here.
    # A is freed here.
}
```