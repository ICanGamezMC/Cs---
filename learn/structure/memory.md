# Basic memory
> In Cs+++, memory management is designed to be both efficient and flexible, allowing developers to optimize their programs while maintaining control over resource allocation.

Memory has issues with not freeing up bytes, write over memory, out-of-bounds reading, and other issues. 
Cs+++ provides a unique approach to memory management.

## Memory tree model

During compile time memory is represented as a tree structure, where if memory being allocated in a block of code is not returned then it will be freed unless stated otherwise.
During compilation, every allocation becomes a node in a memory tree. Direct assignments create child references to an existing allocation, 
while expressions that produce new values create new allocations.

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

## Auto referencing
When an assignment does not modify a value, Cs+++ automatically creates a reference to the existing allocation instead of cloning it. 
If an expression produces a new value, a new allocation is created.
Take this example of an automatic reference:
```cs
fn main(){
    Var int("a") = 10;
    Var int("b") = a;
    print(b)  # Outputs 10
}
```
Diagram of what's going on in memory:
```
┌──────────────┐     ┌*reference────┐   ┌*reference──┐   ┌──────────────┐
│Var A created ├─┬───►Var B created ├───►Var B prints├───►Var A is freed│
│Hold int 10   │ │   │Hold int 10   │   └────────────┘   └──────────────┘
└──────────────┘ │   └──────────────┘                                    
                 │   ┌──────────────┐                                    
                 └───►Var A is freed│                                    
                     └──────────────┘                                    
```
Here is the same code but with where the memory is being freed:
```cs
fn main(){
    Var int("a") = 10;
    Var int("b") = a;
    # A is freed here.
    print(b)  # Outputs 10
    # B is freed here.
}
```

> [!NOTE]
> Variables assigned from a root variable become child references. Child references do not own memory; they reference the same allocation as the root.
> Any modification made through the root or a child reference is visible to every variable referencing that allocation.
```cs
fn main(){
    Var int("a") = 10;
    Var int("b") = a;
    a = 20;
    print(b)  # Outputs 20
}
```

### Branching
When you assign a variable but then change a child reference, that child reference will be branching and a new allocation will be created. 
This is to prevent accidental changes to the original value. This also makes the branching child variable the new root of the allocation, 
and any future child references will reference this new allocation.

```cs
fn main(){
    Var int("a") = 10;
    Var int("b") = a;
    Var int("c") = b;

    c = 50;
    print(a);  # Outputs 10
}
```
Another interesting diagram (because we got to love those) is how this branching works in memory:
```
Referenced all to the same address:
      [10]
     / | \
    A  B  C

After c = 50:
      [10]         [50]
      /  \           |
     A    B          C
```



## Summary of tree model
Cs+++ uses a memory tree model, where allocations form a tree of references created during compilation.
Each allocation is a node in the tree. Variables are references that point into these nodes.
When an allocation has no remaining references (no variables pointing to it and no child references), 
Deallocation occurs after the current statement has completed execution.


```

                             ┌─────────────────────┐                                                                                    
                       ┌─────►Child Node           │                                                                                    
                       │     │Reference Parent Root│                                                                                    
                       │     └─────────────────────┘                                                                                    
   ┌───────────────┐   │                                                                                                                
   │Parent Root    ├───┤     ┌─────────────────────┐                                                                                    
   │Contain Var int│   ├─────►Child Node           │                                                                                    
   └───────┬───────┘   │     │Reference Parent Root│                                                                                    
           │           │     └─────────────────────┘                                                                                    
           │           │                                                                                                                
           │           │     ┌─────────────────────┐                                                                                    
           ▼           └─────►Child Node           ├──────────────┐                                                                     
       Change in             │Reference Parent Root│              │                                                                     
      data contained         └─────────────────────┘              │                                                                     
      in parent root                                              ▼                                                                     
           │                                                 Operation in           ┌───────────────────┐        ┌─────────────────────┐
           │                                                  data using   ─────────►New Parent Root    ├────────►Child Node           │
┌──────────▼────────┐        ┌─────────────────────┐        two child nodes         │    Contain Var int│        │Reference Parent Root│
│New Parent Root    ├────────►Child Node           │              ▲                 └───────────────────┘        └─────────────────────┘
│    Contain Var int│        │Reference Parent Root│              │                                                                     
└───────────────────┘        └─────────────────────┴──────────────┘                                                                     
```