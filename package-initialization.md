# PACKAGE INITIALIZATION

Package initialization begins by initializing package-level variables in the order in which they are declared, except that dependencies are resolved first:

```text
var a = b + c     // a initialized third, to 3
var b = f()       // b initialized second, to 2, by calling f
var c = 1         // c initialized first, to 1

func f() int { return c + 1 }
```

If the package has multiple `.go` files, they are initialized in the order in which the files are given to the compiler; the `go` tool sorts `.go` files by name before invoking the compiler.

Each variable declared at package level starts life with the value of its initializer expression.

```text
func init() { /* ... */ }
```

Such `init` functions can’t be called or referenced  
Within each file, `init` functions are automatically executed when the program starts, in the order in which they are declared.  
  
One package is initialized at a time, in the order of imports in the program, dependencies first, so a package `p` importing `q` can be sure that `q` is fully initialized before `p`’s initialization begins.   
Initialization proceeds from the bottom up; the `main` package is the last to be initialized.   
In this manner, all packages are fully initialized before the application’s `main` function begins.  
  


