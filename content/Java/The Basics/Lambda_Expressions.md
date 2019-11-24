---
title: Lambda Expressions
---

Lambda expressions are not unknown to many of us who have worked on other popular programming languages like Scala. In Java programming language, a Lambda expression (or function) is just an anonymous function, i.e., a function with no name and without being bounded to an identifier. They are written exactly in the place where it’s needed, typically as a parameter to some other function.

The most important features of Lambda Expressions is that they execute in the context of their appearance. So, a similar lambda expression can be executed differently in some other context (i.e. logic will be same but results will be different based on different parameters passed to function).

### Synatax

```java
// This function takes two parameters and returns their sum

//(parameters) -> expression
(x, y) -> x + y
(int a, int b) -> a * b

//(parameters) -> { statements; }
(x, y) -> { x+y; }

//() -> expression
() -> z
() -> 100
```

## Functional Interface

A functional interface is an interface with a single abstract method (SAM). A class implements any interface by providing implementations for all the methods in it.

```java
@FunctionalInterface
public interface Runnable {
    public abstract void run();
}
```

{{% notice note %}}
Type in which lambda expressions are converted, are always of functional interface type.
{{% /notice %}}
