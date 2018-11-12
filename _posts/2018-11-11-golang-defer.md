---
layout: post
title:  "How does defer works"
categories: golang language
tag: defer
---

* toc
{:toc}

## basic usage

Defer is an important functionality in golang, it can be used to do some
extra clean up before the function returns.  
A simple example below shows
the common usage of defer:
```go
func readFromFile(filename string) ([]byte, error) {
    fileHandler, err := os.Open(filename)
    if err != nil {
        return nil, err
    }
    defer fileHandler.Close()

    return ioutil.ReadAll(fileHandler)
}
```
> This is just an example of defer, golang provides `ioutil.ReadFile` which does the same thing.

Of course you can do the clean up manually right before the function
returns, but using `defer` to declare the cleanup logic right after the
allocation makes it hard to forget about freeing up resources.

It's nice that golang provides a nice way to declare how to cleanup the resource, but it can be tricky in some cases.

## multiple defer
Here is a example of multiple defers:
```go
func main() {
    defer print("a")
    defer print("b")
    defer print("c")
}
```

The output of the above example is `cba`. 

This might be a suprise if you
didn't know, but it actually makes sense to execute the defer from bottom
up.  
Most cases defer is used to free up resources. And in a program the 
execution sequence is line-by-line from up to the bottom, so it is 
**not unlikely** the latter resource is depended on the resource created 
before it.  
If this is the case, clean up the depended resource before the 
depending resource (cleanup in the same order as creation) may cause an 
issue. But if the clean up happens in the reverse order (cleanup in the
reverse order as creation), the problem should be avoided.  
But it some cases, it might not be the behavior you would expect. So when
using multiple defer statements, you need to evaluate if it is doing the same behavior you are looking for.

## defer in a loop
Thinking about the following example:
```go
func main() {
    guests := []string{"Alice", "Bob"}

    for _, guest := range guests {
        fmt.Printf("Hello %s, ", guest)
        defer fmt.Println("welcome to the party")
    }
}
```

It outputs:
```
Hello Alice, Hello Bob, welcome to the party
welcome to the party
```

Why? Because the loop is not a function. Defer statements will defer the
execution until the surrounding function returns.

It might be OK in some cases, but it is definitely a point of confusion and
potential problem, so avoiding `defer` in a loop if possible. For example,
the above example can be rewritten as:
```go
func main() {
    guests := []string{"Alice", "Bob"}

    for _, guest := range guests {
        func() {
            fmt.Printf("Hello %s, ", guest)
            defer fmt.Println("welcome to the party")
        }()
    }
}
```

It outputs:
```
Hello Alice, welcome to the party
Hello Bob, welcome to the party
```

The anonymous function provides a context for the defer to execute when
the function returns.

## defer a function with params
For example:
```go
func main() {
    var str string
    defer func(v string) { print(v) }(str)

    str = "hello"
}
```

What is the output? Nothing.

This is a very important thing to remember about `defer`:

> The deferred call's arguments are evaluated immediately

Back to our example, when `print(str)` is evaluated, the value of `str`
is actually an empty string.

This is most likely an unexpected behavior, if one is not familiar with
how defer works. In fact, I encountered this in one of my projects, and I
could not figure out why it acted that way.

One way to fix the example is changing the parameter to a pointer:
```go
func main() {
    var str string
    param := &str
    defer func(v *string) { print(*v) }(param)

    str = "hello"
}
```

Because the parameters are passed in by value, if the string is given,
it will use the value at the time when the string is passed in. If the pointer
to the string is given, it will read the value of the pointer points to during
the execution.

<!-- Example to footnote Some text[^1].

Some other text[^2].

[^1]: Some footnote.
[^2]: Other footnote. -->