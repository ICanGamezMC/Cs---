# Types of data
> Type syntax and the different types offered

Most programming languages provide type systems for basic primitives such as integers and booleans. 
Cs+++ extends this concept by introducing a broader and more flexible type syntax, 
including types for functions and other experimental constructs, enabling more expressive and unconventional programming patterns.

___
## How variables are built

Every variable built into the program is mutable by default, use `const` or `final` keywords to make them immutable. 
```cs
fn main(){
    const Var int("Constant") = 10;
}
```

Every variable also technically is its own function assigning binary to a pointer, with the binary itself being an assignable number of bytes.

Each default type can also be removed by using the `!//` keyword for any reason you want.
```cs
fn main(){
    Var int("hello") = 10;
    int!//;
    print(hello)  # This will give an error
}
```


___

## Integers
Integers are whole numbers, both positive and negative, without any fractional or decimal component. 