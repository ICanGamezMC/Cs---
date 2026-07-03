# Cs+++ Esolang

> A context-driven, time-sensitive esoteric programming language

Cs+++ is an experimental programming language in which meaning is determined by context, structure, and execution timing rather than by fixed syntax rules.

### Features

* Symbols change behavior depending on surrounding context
* Temporal and structural computation model
* Highly flexible (and intentionally unpredictable) evaluation system

> [!WARNING]
> This language could have context loops where there is no limitations, so data leaks and infinite loops are prone.

### Why the name?
The name references cesium, a highly reactive chemical element, reflecting the language’s unstable and context-sensitive behavior.
The suffix “+++” signifies an escalation beyond traditional programming models, where symbols, structure, and execution timing all influence meaning.


### Example code

This example creates ten constant variables with unique names and integer values.

```cs
Var int("i")
for i if(i<10){
Const Var int("number_"+i) = i;
i++;
}
```