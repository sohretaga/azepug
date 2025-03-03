# Global Variables and Their Impact on Performance

Sometimes we create global variables in our code and use them in various places (functions, loops, etc.). But is this approach correct? Let's discuss this and explore whether it's performant or how we can use global variables more efficiently.

## A Simple Example

Let's say we have a global variable called `global_var` with a value of 10. We also have a function with a loop that iterates 1000 times:

```python
global_var = 10

def func():
    ans = 0
    for i in range(1000):
        ans += global_var * i
    return ans
    
func()
```

This function iterates from 0 to 999 and adds 10 * the index value to `ans` in each iteration.

## Identifying the Problem

At first glance, the code seems fine, but upon closer inspection, we can see that it accesses the global variable in each iteration. In Python, accessing global variables can be slower than accessing local variables.

## Why Are Global Variable Accesses Slower?

To understand this, let's look at how Python searches for variables. When a variable is accessed in Python, the language follows an internal mechanism known as the **"LEGB rule"**. LEGB represents the order in which Python searches for a variable: **Local, Enclosing, Global, Built-in**.

### The LEGB Rule

1. **Local**: First, Python looks for the variable in the current function's or block's local scope.
2. **Enclosing**: If the variable isn't found in the local scope, Python checks the enclosing functions' scopes. This is particularly relevant when dealing with nested functions.
3. **Global**: If the variable isn't found in the previous two scopes, Python looks at the global scope – variables defined at the top level of the module.
4. **Built-in**: If the variable isn't found anywhere, Python finally checks its built-in functions and variables (like `print`, `len`, etc.).

### Example

```python
x = 10  # Global variable

def outer():
    y = 20  # Enclosing variable
    
    def inner():
        z = 30  # Local variable
        print(x)  # x comes from the global scope
        print(y)  # y comes from the enclosing scope
        print(z)  # z is found locally
    
    inner()

outer()
```

## Optimizing the Code

Now that we understand the LEGB rule, let's optimize our code. To avoid accessing the global variable in each iteration, we can simply assign the global variable to a local variable at the beginning of the function:

```python
global_var = 10

def func():
    ans = 0
    local_var = global_var  # Copy the global variable to a local variable
    for i in range(1000):
        ans += local_var * i
    return ans
    
func()
```

## The Question of Additional Memory Usage

You might wonder, "Doesn't `local_var` take up additional memory?"

No. In Python, variables don't store values directly; they store references to objects. When you write `local_var = global_var`, `local_var` doesn't create a new value; it simply points to the same object (in this case, the integer 10) that `global_var` points to. So, no additional copy of 10 is created in memory; only a new reference (pointer) is added.

## Performance Difference

In the previous example, the for loop runs 1000 times. If we directly access `global_var` in each iteration, Python would need to search for `global_var` in the global scope each time. But after copying it to `local_var` once, the loop only accesses the local variable, which is faster.

The performance difference is very small — on the order of nanoseconds — and usually not noticeable in ordinary cases. However, in large loops or applications where performance is critical, this optimization can be beneficial.


Written by **[Sohret Aga](https://www.linkedin.com/in/sohretaga/)**
