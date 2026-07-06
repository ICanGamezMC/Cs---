# Basic memory
> In Cs+++, memory management is designed to be both efficient and flexible, allowing developers to optimize their programs while maintaining control over resource allocation.

Memory has issues with not freeing up bytes, write over memory, out-of-bounds reading, and other issues. 
Cs+++ provides a unique approach to memory management.

## Memory tree model

During compile time memory is represented as a tree structure, where if memory being allocated in a block of code is not returned then it will be freed unless stated otherwise.
Assigning one variable to another does not transfer ownership. Instead, a new allocation is created by cloning the original value.

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
                          ┌*reference──┐      ┌──────────────┐
                     ┌────►Var A prints├──────►Var A is freed│
                     │    └────────────┘      └──────────────┘
┌───────────────┐    │                                        
│ Var A created ├────┴┐  ┌*clone─────────┐    ┌──────────────┐
│ Hold int 10   │     │  │ Var C created ├────►Var C is freed│
└───────────────┘  Ref┼──► Hold int 30   │    └──────────────┘
                   A+B│  └───────────────┘                    
┌───────────────┐     │  ┌──────────────┐                     
│ Var B created │     ├──►Var─B─is─freed│                     
│ Hold int 20   ├─────┘  └──────────────┘                     
└───────────────┘                                             
```

> [!NOTE]
> During compilation, every allocation becomes a node in a memory tree. The compiler tracks how memory 
> flows between variables and scopes. If an allocation has no outgoing references after a scope ends, it is automatically freed.

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