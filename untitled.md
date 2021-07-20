# VARIABLES

A `var` declaration  
  
1. creates a variable of a particular type,   
2. attaches a name to it, and   
3. sets its initial value.   
  
Each declaration has the general form

```text
var name type = expression
```

Either the type or the `=` `expression` part may be omitted, but not both. If the type is omitted, it is determined by the initializer expression.   
If the expression is omitted, the initial value is the _zero value_ for the type, which is `0` for numbers, `false` for booleans, `""` for strings, and `nil` for interfaces and reference types \(slice, pointer, map, channel, function\).   
The zero value of an aggregate type like an array or a struct has the zero value of all of its elements or fields.  
  
SHORT VARIABLE DECLARATIONS  
  
Within a function, an alternate form called a _short variable declaration_ may be used to declare and initialize local variables. It takes the form `name` `:=` `expression`, and the type of `name` is determined by the type of `expression`.  
  
NOTE: A `var` declaration tends to be reserved for local variables that need an explicit type that differs from that of the initializer expression, or for when the variable will be assigned a value later and its initial value is unimportant.

Keep in mind that `:=` is a declaration, whereas `=` is an assignment.  
  
NOTE: A short variable declaration does not necessarily _declare_ all the variables on its left-hand side. If some of them were already declared in the _same_ lexical block, then the short variable declaration acts like an _assignment_ to those variables.

In the code below, the first statement declares both `in` and `err`. The second declares `out` but only assigns a value to the existing `err` variable.

```text
in, err := os.Open(infile)
// ...
out, err := os.Create(outfile)
```

A short variable declaration must declare at least one new variable.  
  
POINTERS:  
  
A _pointer_ value is the _address_ of a variable. A pointer is thus the location at which a value is stored.   
Not every value has an address, but every variable does.   
With a pointer, we can read or update the value of a variable _indirectly_, without using or even knowing the name of the variable, if indeed it has a name.  
  
If a variable is declared `var x int`, the expression `&x` \(“address of `x`”\) yields a pointer to an integer variable, that is, a value of type `*int`, which is pronounced “pointer to int.”   
If this value is called `p`, we say “`p` points to `x`,” or equivalently “`p` contains the address of `x`.”   
The variable to which `p` points is written `*p`.   
The expression `*p` yields the value of that variable, an `int`, but since `*p` denotes a variable, it may also appear on the left-hand side of an assignment, in which case the assignment updates the variable.  
  
Each component of a variable of aggregate type—a field of a struct or an element of an array—is also a variable and thus has an address too.

Variables are sometimes described as _addressable_ values. Expressions that denote variables are the only expressions to which the _address-of_ operator `&` maybe applied.  
  
The zero value for a pointer of any type is `nil`. The test `p != nil` is true if `p` points to a variable. Pointers are comparable; two pointers are equal if and only if they point to the same variable or both are nil.  
  
a pointer contains the address of a variable, passing a pointer argument to a function makes it possible for the function to update the variable that was indirectly passed.  
  
THE `NEW` FUNCTION:  
  
Another way to create a variable is to use the built-in function `new`. The expression `new(T)` creates an _unnamed variable_ of type `T`, initializes it to the zero value of `T`, and returns its address, which is a value of type `*T`.  
  
A variable created with `new` is no different from an ordinary local variable whose address is taken, except that there’s no need to invent \(and declare\) a dummy name, and we can use `new(T)` in an expression. Thus `new` is only a syntactic convenience, not a fundamental notion.  
  
Each call to `new` returns a distinct variable with a unique address.  
  
Since `new` is a predeclared function, not a keyword, it’s possible to redefine the name for something else within a function.  
  
A compiler may choose to allocate local variables on the heap or on the stack but, perhaps surprisingly, this choice is not determined by whether `var` or `new` was used to declare the variable.  
  


